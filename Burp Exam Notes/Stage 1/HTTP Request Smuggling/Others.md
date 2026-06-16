
#### 1.5. Lab: Exploiting HTTP request smuggling to reveal front-end request rewriting ⭕️
This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding.

There's an admin panel at `/admin`, but it's only accessible to people with the IP address 127.0.0.1. The front-end server adds an HTTP header to incoming requests containing their IP address. It's similar to the `X-Forwarded-For` header but has a different name.

To solve the lab, smuggle a request to the back-end server that reveals the header that is added by the front-end server. Then smuggle a request to the back-end server that includes the added header, accesses the admin panel, and deletes the user `carlos`.

1. Navigate to `/admin` and notice an error: "Admin interface only available if logged in as an administrator, or if requested from 127.0.0.1"
	1. `X-Forwarded-For` doesn't work
	2. ![[HTTP Request Smuggling-20260201095813240.png|522]]
2. Use the search bar feature and notice user input is reflected back on the page
	1. ![[HTTP Request Smuggling-20260201095541236.png]]
	2. ![[HTTP Request Smuggling-20260201095558503.png]]
3. Send `GET /` to Repeater. Paste the following code snippet into the request (change YOUR-LAB-ID). Send the request **TWICE**. Look for "search" in the response and you should see something unusual being reflected and has a `X-*-Ip` header in this case it's `IptqQM`. 
	1. ![[HTTP Request Smuggling-20260201100322132.png]]
```
POST / HTTP/1.1 Host: YOUR-LAB-ID.web-security-academy.net Content-Type: application/x-www-form-urlencoded Content-Length: 124 Transfer-Encoding: chunked 0 POST / HTTP/1.1 Content-Type: application/x-www-form-urlencoded Content-Length: 200 Connection: close search=test
```
4. Add that `X-IptqQM-Ip` header > set it to `127.0.0.1` > change line 16 to `x=1`, change line 9 to `GET /admin` > Send > notice a 200 OK meaning we successfully accessed the admin page.
	1. ![[HTTP Request Smuggling-20260201100539314.png]]
	2. Right click > request in browser > in original session and see that we have access to it
		1. ![[HTTP Request Smuggling-20260201100936562.png|135]]
5. Change the endpoint in line 9 to `GET /admin/delete?username=carlos` to solve the lab.
	1. ![[HTTP Request Smuggling-20260201101028247.png]]

>[!tip] How to Identify this Vulnerability
>1. Search bar + My account
>2. 

---

#### 1.6. Lab: Exploiting HTTP request smuggling to capture other users' requests ⭕️
This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding.
To solve the lab, smuggle a request to the back-end server that causes the next user's request to be stored in the application. Then retrieve the next user's request and use the victim user's cookies to access their account.
>[!info] Hint
> If you encounter a timeout, this may indicate that the number of bytes you're trying to capture is greater than the total number of bytes in the subsequent request. Try reducing the `Content-Length` specified in the smuggled request prefix.

##### Determine if the app is vulnerable to TE.CL or CL.TE
**Manual Method**
1. Similar to Step 1 from the previous lab: send `/` request to repeater, change request method, remove every headers except `POST, Host, Content-Type, and Content-Length`. Uncheck Update content length and enable show non-printable characters `\n`. Add `Transfer-Encoding: chunked` and other details like so then send:
	1. ![[HTTP Request Smuggling-20251111194255490.png]]
	2. It took a bit of time to load and got a **500 error** saying timed out and this is an indicator that this app is **vulnerable to CL.TE**.
- - - 
**Automated Method**
- Right click on the `GET` request > Extensions > HTTP Request Smuggler > Smuggle probe
- Target > Site Map > select the domain > and see that it says the app is vulnerable to CL.TE.
	- ![[HTTP Request Smuggling-20251111194118604.png|325]]
- - - 
2. Duplicate the request (this will be the victim's request) and set it like the image and should get a 200 OK.
	1. ![[HTTP Request Smuggling-20251111194442077.png|420]]
3. Attacker request: check update content length (do this bc this is a **CL.TE** vulnerability) and set everything else like so and send and should get a 200 OK
	1. ```
	   
	   0
	   
	   GET /something HTTP/1.1
	   Content-Type: ...
	   Content-Length: 3
	   
	   x=
	   ```
	2. ![[HTTP Request Smuggling-20251111194830985.png]]
4. Victim's request, send, and should get a 400 not found
	1. ![[HTTP Request Smuggling-20251111195142054.png]]
5. Go to the app and post a comment. Grab the `POST` request, paste it in the attacker's request but only keep the following headers but **make sure the `comment` key is at the end**
```
POST /post/comment ...
Host: ...
Cookie: ...
Content-Length: ...
Content-Type: ...

csrf...&comment=...
```
> The cookie and csrf token is very important for this step.
6. We now need to modify the `Content-Length`: highlight everything from `csrf` onward and look at Inspector: `122` (your number might be diff). Then go to proxy history, find the `/` request, select all, get the byte length from Inspector and add `122`. Then we have our new content length.
	1. ![[HTTP Request Smuggling-20260105142627199.png]]
		1. **IMPORTANT: Make sure the paddings `\r\n` are correct and matches the screenshots above.**
	2. Now go back to HTTP History, find the original GET request `/`, select all, and find the number of bytes: `844`.
		1. ![[HTTP Request Smuggling-20260105142016564.png]]
	3. 844 + 122 = `966` so `Content-Length: 966`
		1. ![[HTTP Request Smuggling-20260105142053696.png]]
>[!tip] If this doesn't work
>If at the end we can't retrieve the victim's session id, keep increasing the `Content-Length`.
7. Victim's request: now we need to *pad* the request aka *add safe padding* so that the content length reaches `962` or a little bit over to avoid the server timing out. Solution is to just **add a whole bunch of new lines**.
	1. ![[HTTP Request Smuggling-20260105142828985.png]]
8. Attack request: Send. Should get a 200 OK.
9. Victim request: Send. Should get a 302 Found. **Now a new comment as been posted.** 
	1. ![[HTTP Request Smuggling-20260105142915806.png]]
	2. ![[HTTP Request Smuggling-20260105143052745.png|469]]
10. Continue to repeat Steps 8 and 9 to send the attacker's request, wait a few seconds, and then the victim's request until the victim's response show 200 OK. That means someone else (our target) visited the page instead of our own victim request and we should see our target's header information.
	1. ![[HTTP Request Smuggling-20260105145317795.png]]
	2. `(Victim)` in the user agent shows us that our victim visited the root endpoint `/` (which is the homepage) and we got the session id.
	3. If we only got partial values for the session id or don't see it, go back to the attacker's request and increase the `Content-Length`. 
11. Paste the session id in cookie editor, Save, and refresh the page.
	1. ![[HTTP Request Smuggling-20260105145537168.png]]
	2. ![[HTTP Request Smuggling-20260105145551941.png]]
	3. Go to My account and we can see that we are the admin now
		1. ![[HTTP Request Smuggling-20260105145612996.png]]
- - -

#### 1.x. Lab: CL.0 request smuggling ⭕️
This lab is vulnerable to CL.0 request smuggling attacks. The back-end server ignores the `Content-Length` header on requests to some endpoints.

To solve the lab, identify a vulnerable endpoint, smuggle a request to the back-end to access to the admin panel at `/admin`, then delete the user `carlos`.
![[HTTP Request Smuggling-20260201201855572.png]]
![[HTTP Request Smuggling-20260201201943861.png]]

1. Uncheck the Hide box for image files, grab an `.svg` file and send to Repeater. Change to HTTP/1.1 > change request method > remove unnecessary headers > Send and get 200 OK.
	1. ![[HTTP Request Smuggling-20260201202317176.png|489]]
2. Settings > Check HTTP/1 connection reuse > add the header: `Connection: Keep-Alive`. Set up the Attacker request like the screenshot
	1. ![[HTTP Request Smuggling-20260201202541973.png]]
	2. ![[HTTP Request Smuggling-20260201202816612.png]]
```
POST /resources/images/avatarDefault.svg HTTP/1.1
Host: 0a8a001b03fdf2a581f3dee9004a00b7.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 0
Connection: Keep-Alive

GET /doesntexist HTTP/1.1
X-Ignore: X
```

3. Add the Attacker and Victim requests (send the same `GET` request to Repeater) in a group and set it as "Send group (single connection)". Send and attacker gets 200 OK but victim gets 404 (request smuggled successfully). **This confirms client desync**.
	1. ![[HTTP Request Smuggling-20260201203008843.png]]
4. Change line 7 to `GET /admin` > Send > check victim request and got a 200 OK. Render the page to confirm.
	1. ![[HTTP Request Smuggling-20260201203232440.png]]
	2. ![[HTTP Request Smuggling-20260201203244825.png]]
5. Change line 7 again to `GET /admin/delete?username=carlos` to solve.
	1. ![[HTTP Request Smuggling-20260201203345739.png]]
	2. ![[HTTP Request Smuggling-20260201203332248.png]]
>[!tip] How to Identify this Vulnerability
>1. Admin panel exists + 403 + "Path /admin is blocked"




---
#### 1.x. Lab: HTTP request smuggling, obfuscating the TE header ⭕️
This lab involves a front-end and back-end server, and the two servers handle duplicate HTTP request headers in different ways. The front-end server rejects requests that aren't using the GET or POST method.
To solve the lab, smuggle a request to the back-end server, so that the next request processed by the back-end server appears to use the method `GPOST`.

#ExamTip Manually fixing the length fields in request smuggling attacks can be tricky. Our [HTTP Request Smuggler](https://portswigger.net/blog/http-desync-attacks-request-smuggling-reborn#demo) Burp extension was designed to help. You can install it via the BApp Store.

> This lab is susceptible to a TE.CL vulnerability where the frontend processes `Transfer-Encoding` and the backend processes `Content-Length`.

1. Send root request to Repeater
2. Inspector > set to `HTTP/1`
3. right click on request > Change request method (change it from GET to POST)
4. Can remove all headers except `Host, Content-Type, and Content-Length`
5. Click gear icon next to Send to uncheck Update Content Length
6. Click show non-printable characters `\n`
7. Send request to ensure you get 200 OK back.
> Now we need to detect which header/technique(?) the frontend is using.
8. Add `Transfer-Encoding: chunked`, new line (`\r\n`), 3, new line, abc, newline, x, newline and change `Content-Length: 6`. Setting it to 6 so the content ends after `abc`. Why, idk. Need to understand this.
	1. ![[HTTP Request Smuggling-20251104204217249.png|337]]
9. Figuring out what the frontend server is using:
	1. Send, got 400 Bad Request. This means the frontend server is rejecting the request. **This determines that the frontend server is using `Transfer-Encoding: chunked`.** This is because `X` is an *invalid chunk size* (idk what this means) and the frontend server is expecting a hexadecimal value. 
10. Figuring out what the backend server is using:
	1. Remove all values right up to `Transfer-Encoding`, newline, 0, newline, newline, X. Don't make a newline after X and keep the `Content-Length: 6`. Got 200 OK back. **This means that the backend server is using `Transfer-Encoding: chunked` too.** 
		1. ![[HTTP Request Smuggling-20251104204833350.png|296]]
11. Now we need to trick one of the servers (the backend server) so they don't process the `Transfer-Encoding` header by using `Content-Length`. For this lab, **use a duplicate of the TE header** and one of the servers will process the CL header instead.

> These are all the ways we can obfuscate the TE header so the server won't process it. #Useful 
> ![[HTTP Request Smuggling-20251104205325116.png|421]]

12. Add another TE header with an arbitrary value like this. The server will timeout -- will take long to get a response back; most likely a 500 code. Now setup 2 requests, one as the attacker and one as the victim.
	1. ![[HTTP Request Smuggling-20251104205558012.png|286]]

13. Victim request: remove all other headers like in Step 4, add an arbitrary value in the body like `foo=bar`, Send, and expect a 200 OK. Make sure the Update content length is checked on this one. 
14. Attacker request: set it like the screenshot. Send it to make sure you get a 200 OK. So now the server is *poisoned*(?). Setting the `Content-Length: 3` tells the server to only process up to `1\r\n`. So `G\r\n 0\r\n\r\n` is sent.  
	1. ![[HTTP Request Smuggling-20251104210110604.png|337]]
15. To confirm the poisoning, go back to the victim request and Send. Should see a `Unrecognized method G0POST`.
	1. ![[HTTP Request Smuggling-20251104210358620.png]]
 16. Crafting the attacker request: copy from line 4 upwards (Content-Length upwards) and paste it at the end. In the newly pasted values, remove the `Host` header. Don't need it. Add `x=1` so it should look like this for now.
	 1. ![[HTTP Request Smuggling-20251104211029658.png|471]]
 17. Add `0` and 2 newlines. Open Inspector, highlight the last values (including newlines) up to `x=1`. The length should show `10`. Need to add 1 to it so the `Content-Length` for the newly pasted values should be `11`. 
 18. Highlight from `x=1` up to `GPOST` and the hexadecimal value for that is `0x5c`. On line 7, make a newline and add `5c`. Set the original `Content-Length` to 4 because we want it to end after `5c\r\n`. Send it.
	 1. ![[HTTP Request Smuggling-20251104211353260.png|369]]
19. Victim request: Send and got `Unrecognized method GPOST` and solved. 
> What is the point or risk/impact here? #TODO For the next lab, the point is to bypass an authorization roadblock. Using HTTP Request Smuggling to gain access to the admin panel.

- - - 


