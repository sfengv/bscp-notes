
### Mystery Labs
1. [[Deserialization#3.6. Lab Exploiting PHP deserialization with a pre-built gadget chain ⭕️]] 
2. [[HTTP2#1.8. Lab Response queue poisoning via H2.TE request smuggling ⭕️]]
3. [[CSRF#2.11. Lab CSRF where Referer validation depends on header being present ⭕️]]
4. [[Deserialization#3.4. Lab Arbitrary object injection in PHP ⭕️]]
5. [[Web Cache Poisoning#1.5.Lab Web cache poisoning via an unkeyed query string ⭕️]]
6. [[Brute Force#1.4. Lab Username enumeration via subtly different responses ⭕️]]
7. [[LFI#3.6. Lab File path traversal, validation of file extension with null byte bypass ⭕️]]
8. [[SQLi#2.5. Lab SQL injection attack, listing the database contents on non-Oracle databases ⭕️]]
9. [[DOM-Based XSS#1.x. Lab Stored DOM XSS ⭕️]]
10. [[Blind SQLi#2.17. Lab Blind SQL injection with out-of-band data exfiltration ⭕️]]

## Shop 🛍️
- Authenticated Shop
	- Add to Cart 🛒
		- Change Email
	- Check stock ❌
		- Comment in Page Source
		- `/.git`
		- `/robots.txt`
		- `/admin`
		- Login Attempt: `wiener:peter`
		- Change Address
		- Change Email
		- Change Password + Change Email
		- Forgot password?
		- Filter Categories
		- Filter Categories (No Products) <sup>Only show 2 rows of text per entry</sup>
		- Filter Categories (No Images)
		- Live Chat
		- Please select a role
	- Check stock ✅
		- Change Email
		- Login Attempt: `wiener:peter`
		- Filter + Submit Feedback + Live Chat
		- Change Email + Gift Card
- Unauthenticated Shop
	- Check stock ❌
		- Comment in Page Source
		- Target > Engagement tools > Search
		- `/robots.txt`
		- ##### Submit Feedback
		- ##### Filter Categories
		- ##### Filter Categories (No Products) <sup>Only show 2 rows of text per entry</sup>
	- #### Check stock ✅
		- ##### Submit Feedback
		- ##### Timer
---
## Blog 💻
- Authenticated Blog
	- No Comments
		- GraphQL
	- Only Comment Field in Blog
		- Change Preferred Name
		- Change Email + Upload Avatar + Delete account button
	- Every field
		- My account
		- Forgot password?
		- Asks for 4-digit security code
		- Login Attempt: `wiener:peter`
		- Stay Logged In Checkbox
		- Change Email
		- Change Email + Delete Account
		- Change Password + Change Email
	- Every field + File Upload in Blog
		- My account > File Upload
	- Search Bar
		- `/admin`
		- Change Email
		- Submit Feedback
		- Use the HTTP Request Smuggling Payload
- Unauthenticated Blog
	- No Comments
		- GraphQL
	- Every field + File Upload in Blog
		- Page Source
	- Every field
		- Page Source
		- `X-Cache`
		- Submit Feedback
		- Target > Engagement tools > Search
		- Post Comment
		- Use this HTTP Request Smuggling Payload: [[Burp Suite Exam/Stage 1/HTTP Request Smuggling/README#^osmqx6]]
		- "HTML is allowed"
	- Search Bar
		- Use the HTTP Request Smuggling Payload
		- DOM Invader
		- Enter XSS Payload