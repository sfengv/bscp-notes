
#### 1.8. Lab: Response queue poisoning via H2.TE request smuggling ⭕️
This lab is vulnerable to request smuggling because the front-end server downgrades HTTP/2 requests even if they have an ambiguous length.
To solve the lab, delete the user `carlos` by using response queue poisoning to break into the admin panel at `/admin`. An admin user will log in approximately every 15 seconds.
The connection to the back-end is reset every 10 requests, so don't worry if you get it into a bad state - just send a few normal requests to get a fresh connection.

Determine vulnerability: HTTP Request Smuggler > HTTP/2 Probe > revealed to be vulnerable to **HTTP/2 TE desync**. Using Smuggle Probe should also get us this result.
![HTTP Request Smuggling-20260105200503758](HTTP%20Request%20Smuggling-20260105200503758.png)

1. Send root `GET /` request to repeater, change request method, remove all headers except ones on screenshot, turn on Show non-printable characters, and uncheck Update Content-Length. **Replace Content-Length with Transfer-Encoding: chunked**.
	1. Name this the Attacker request and Send. Expect a 200 OK.
	2.  ![HTTP Request Smuggling-20260105201646665](HTTP%20Request%20Smuggling-20260105201646665.png)
2. Go back to HTTP History, send the same `GET /` request to Repeater, name it Victim, and click Send. 404 Not Found indicates that we've successfully smuggled the request. The victim tried to access the `/doesntExist` endpoint. *This also confirms that the app is vulnerable.*
	1. ![HTTP Request Smuggling-20260105201152929](HTTP%20Request%20Smuggling-20260105201152929.png)
3. Modify the Attacker request to look like this:
	1. Success would be a 302 Found response. If it keep saying 404 Not Found, use Intruder to help.
	2. ![HTTP Request Smuggling-20260105201741657](HTTP%20Request%20Smuggling-20260105201741657.png)
4. Intruder settings
	1. Payloads: Null payloads, Continue indefinitely (manually stop when 302 Found is reached)
	2. Settings: uncheck Update Content-Length
	3. Resource pool: max. concurrent requests: 1, delay between requests: 800.
		1. ![HTTP Request Smuggling-20260105202211956](HTTP%20Request%20Smuggling-20260105202211956.png)
	4. Start attack, view filter, uncheck 2XX, 4XX, and 5XX responses
		1. ![425](HTTP%20Request%20Smuggling-20260105202336553.png)
5. 302 Found > Response and we get the administrator's session id. They logged in and we got their session id. Pause the attack.
	1. ![HTTP Request Smuggling-20260105202649984](HTTP%20Request%20Smuggling-20260105202649984.png)
6. Refresh the browser a few times to clear the responses(?), paste the session id in cookie editor, refresh, we're logged in as the admin, delete carlos and solved.
	1. ![317](HTTP%20Request%20Smuggling-20260105202811015.png)


#### 1.9. Lab: H2.CL request smuggling ⭕️

This lab is vulnerable to request smuggling because the front-end server downgrades HTTP/2 requests even if they have an ambiguous length.

To solve the lab, perform a request smuggling attack that causes the victim's browser to load and execute a malicious JavaScript file from the exploit server, calling `alert(document.cookie)`. The victim user accesses the home page every 10 seconds.

1. Send `GET /` to Repeater > set up the request like the screenshot > name it Attacker > if see HTTP/1.1 change it to 2 > Send > get 200 OK
	1. ![491](HTTP%20Request%20Smuggling-20260201102302952.png)
```
POST / HTTP/2
Host: YOUR-LAB-ID.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-Length: 5

x=1
GET /doesntExist HTTP/1.1
X-Ignore: x
```

2. Send another `GET /` > name it Victim > if see HTTP/1.1 change it to 2 > Send > should get a 404.
	1. ![HTTP Request Smuggling-20260201102451411](HTTP%20Request%20Smuggling-20260201102451411.png)
3. From line 7, paste in the code below, send, Victim request, send, and get a 302. Notice the `Location` header reflects the arbitrary `example.com` we added in the smuggled request. **If don't see a 302 Found, send the Attacker request a few times**.
	1. This is an *offsite redirect*?
	2. ![438](HTTP%20Request%20Smuggling-20260201103328717.png)
	3. ![HTTP Request Smuggling-20260201103421209](HTTP%20Request%20Smuggling-20260201103421209.png)
```
GET /resources/js HTTP/1.1
Host: example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 3

x=
```

4. Exploit server:
	1. file: `/resources/js/` > Head: change application to text > body: `alert(document.cookie);` then Store
	2. ![246](HTTP%20Request%20Smuggling-20260201103935263.png)
5. Attacker request: change the host header to your exploit server domain > Send > Victim request > Send > did not solve. Need to automate this with Intruder.
	1. ![HTTP Request Smuggling-20260201104038432](HTTP%20Request%20Smuggling-20260201104038432.png)
6. Intruder
	1. Sniper > Null payloads > generate 1000, *uncheck Update Content-Length* > resource pool: concurrent requests 1, delay 300 milliseconds. The lab will solve when we see 2 back to back 200 responses (attacker 200 and victim 200).
		1. ![HTTP Request Smuggling-20260201104413546](HTTP%20Request%20Smuggling-20260201104413546.png)
		2. ![HTTP Request Smuggling-20260201104408972](HTTP%20Request%20Smuggling-20260201104408972.png)

>[!tip] How to Identify this Vulnerability
>1. 

---

#### 1.10. Lab: HTTP/2 request smuggling via CRLF injection ⭕️
This lab is vulnerable to request smuggling because the front-end server downgrades HTTP/2 requests and fails to adequately sanitize incoming headers.
To solve the lab, use an HTTP/2-exclusive request smuggling vector to gain access to another user's account. The victim accesses the home page every 15 seconds.
If you're not familiar with Burp's exclusive features for HTTP/2 testing, please refer to [the documentation](https://portswigger.net/burp/documentation/desktop/http2) for details on how to use them.

HTTP Smuggler > HTTP/2 Probe show us this is vulnerable to HTTP/2 TE desync vulnerability?
![HTTP Request Smuggling-20260105155519960](HTTP%20Request%20Smuggling-20260105155519960.png)

1. Send `/` to Repeater, click Send, notice HTTP/1.1 changes to HTTP/2 (that's fine, keep this), then set up the request like the screenshot. **Remove the Content-Length header**. HTTP/2 keeps track of it. This will be the Attacker Request.
	1. ![HTTP Request Smuggling-20260105155354723](HTTP%20Request%20Smuggling-20260105155354723.png)
2. Send another `/` to Repeater, this will be the Victim Request. 
3. Send Attacker request then Victim request. If the smuggling is successful, we should see a 404 Not Found when we send the Victim request. We continue to see 200 OK. Gotta find a way to smuggle. 
	1. ![HTTP Request Smuggling-20260105155819594](HTTP%20Request%20Smuggling-20260105155819594.png)
4. Attacker request: remove `Transfer-Encoding: chunked` header, Inspector > Request headers > + (add a header) and set it like the screenshot. Shift + Enter after `bar` to add `Transfer-Encoding`. *This will convert the request to HTTP/1.1??*
	1. ![HTTP Request Smuggling-20260105160430908](HTTP%20Request%20Smuggling-20260105160430908.png)
5. You will see that the request is *kettled* and this is fine.
	1. ![351](HTTP%20Request%20Smuggling-20260105163959774.png)
6. Repeat Step 3 and we should see 404 Not Found on the Victim's request. The attacker's request has been successfully smuggled.
	1. ![HTTP Request Smuggling-20260105164156353](HTTP%20Request%20Smuggling-20260105164156353.png)
7. Use the Search feature and we see a history of recent searches. Most likely tied to our session id. Send the `POST` request to repeater.
	1. ![HTTP Request Smuggling-20260105164311082](HTTP%20Request%20Smuggling-20260105164311082.png)
8. Downgrade this request to HTTP/1.1, remove every header except ones in the screenshot, and remove the unnecessary lab_analytics cookie, click Send to confirm it works.
	1. ![HTTP Request Smuggling-20260105164607100](HTTP%20Request%20Smuggling-20260105164607100.png)
9. Copy + Paste this request onto the Attacker's request.
	1. ![HTTP Request Smuggling-20260105164831634](HTTP%20Request%20Smuggling-20260105164831634.png)
10. Modify the Content-Length:
	1. Send `GET /` from HTTP History to Repeater and select all on the request: `764`
		1. ![HTTP Request Smuggling-20260105164923169](HTTP%20Request%20Smuggling-20260105164923169.png)
	2. The Content-Length in the Attacker's request should be more than the `GET /` request so start with `1000` to be safe.
11. Send the attacker request. Description says a user visits the app every 15 seconds so wait a bit and then click Home. Will see an invalid error, this is expected. Refresh and we should see the Victim's request and their session id. If it doesn't work, send the attacker request and try again.
	1. ![HTTP Request Smuggling-20260105165949277](HTTP%20Request%20Smuggling-20260105165949277.png)
12. Paste the session id in cookie editor, save, and refresh to solve.
- - -

#### 1.11. Lab: HTTP/2 request splitting via CRLF injection ⭕️
This lab is vulnerable to request smuggling because the front-end server downgrades HTTP/2 requests and fails to adequately sanitize incoming headers.

To solve the lab, delete the user `carlos` by using [response queue poisoning](https://portswigger.net/web-security/request-smuggling/advanced/response-queue-poisoning) to break into the admin panel at `/admin`. An admin user will log in approximately every 10 seconds.

The connection to the back-end is reset every 10 requests, so don't worry if you get it into a bad state - just send a few normal requests to get a fresh connection.

1. Send `GET /` to Repeater > ensure it is on HTTP/2 > send > 200 OK.
2. Inspector > Request headers > follow the screenshot. Tip: shift + enter will get `\r\n`. Send.
	1. ![HTTP Request Smuggling-20260201110017743](HTTP%20Request%20Smuggling-20260201110017743.png)
3. Refresh the browser and got a 404. Smuggling successful.
	1. ![HTTP Request Smuggling-20260201110109369](HTTP%20Request%20Smuggling-20260201110109369.png)
4. Inspector > change `:path` to something like `/doesntExist` > send > should get a 302 with the admin's session cookie. *If doesn't get a 302, wait few or 10 seconds, send again OR change to back to `/` then send*.
	1. ![HTTP Request Smuggling-20260201110428527](HTTP%20Request%20Smuggling-20260201110428527.png)
5. Put the cookie in Cookie Editor > Save > click My account > Admin panel > delete carlos to solve.
	1. ![330](HTTP%20Request%20Smuggling-20260201110450114.png)

>[!tip] How to Identify this Vulnerability
>1. 

---

