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
- Decoded `session` cookie = `O:4` = PHP serialized object
	- [[Deserialization#3.1. Lab Modifying serialized objects ⭕️]]
	- [[Deserialization#3.2. Lab Modifying serialized data types ⭕️]]
- `session` cookie = `rO0AB` = Java serialized object
	- [[Deserialization#3.5. Lab Exploiting Java deserialization with Apache Commons ⭕️]]
- `session cookie`= `%7B%22token`
	- [[Deserialization#3.6. Lab Exploiting PHP deserialization with a pre-built gadget chain ⭕️]] #MysteryLab
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

