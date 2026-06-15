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
- Decoded `session` cookie = `O:4` = PHP serialized object
	- [Deserialization](Deserialization.md#3.1.%20Lab%20Modifying%20serialized%20objects%20%E2%AD%95%EF%B8%8F)
	- [Deserialization](Deserialization.md#3.2.%20Lab%20Modifying%20serialized%20data%20types%20%E2%AD%95%EF%B8%8F)
- `session` cookie = `rO0AB` = Java serialized object
	- [Deserialization](Deserialization.md#3.5.%20Lab%20Exploiting%20Java%20deserialization%20with%20Apache%20Commons%20%E2%AD%95%EF%B8%8F)
- `session cookie`= `%7B%22token`
	- [Deserialization](Deserialization.md#3.6.%20Lab%20Exploiting%20PHP%20deserialization%20with%20a%20pre-built%20gadget%20chain%20%E2%AD%95%EF%B8%8F) #MysteryLab
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
		- [CORS](../../../Stage%202/CORS.md#2.1.%20Lab%20CORS%20vulnerability%20with%20basic%20origin%20reflection%20%E2%AD%95%EF%B8%8F)
		- [CORS](../../../Stage%202/CORS.md#2.2.%20Lab%20CORS%20vulnerability%20with%20trusted%20null%20origin%20%E2%AD%95%EF%B8%8F)
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

