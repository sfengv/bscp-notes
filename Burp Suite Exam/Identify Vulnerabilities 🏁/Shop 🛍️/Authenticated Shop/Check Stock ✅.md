#### Check stock ✅
##### Change Email
- `GET /accountDetails`
	- `Access-Control-Allow-Credentials: true`
		- [[Burp Suite Exam/Stage 2/CORS#2.3. Lab CORS vulnerability with trusted insecure protocols ⭕️]]

##### Login Attempt: `wiener:peter`
- "Invalid username or password."
	- `POST /product/stock` uses XML
		- [[SQLi#2.18. Lab SQL injection with filter bypass via XML encoding ⭕️]]
	- `stockAPI` in `POST /product/stock`
		- [[SSRF#3.1. Lab Basic SSRF against the local server ⭕️]]
		- [[SSRF#3.2. Lab Basic SSRF against another back-end system ⭕️]]
		- [[SSRF#3.4. Lab SSRF with blacklist-based input filter ⭕️]]

##### Filter + Submit Feedback + Live Chat
- `jquery_1-7-1.js`
	- [[Client side#2.5. Lab Client-side prototype pollution in third-party libraries ⭕️]]](<#### Check stock ✅

##### Change Email
- `GET /accountDetails`
	- `Access-Control-Allow-Credentials: true`
		- [[Burp Suite Exam/Stage 2/CORS#2.3. Lab CORS vulnerability with trusted insecure protocols ⭕️]]

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