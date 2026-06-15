
### Mystery Labs
1. [Deserialization](Deserialization.md#3.6.%20Lab%20Exploiting%20PHP%20deserialization%20with%20a%20pre-built%20gadget%20chain%20%E2%AD%95%EF%B8%8F) 
2. [HTTP2](HTTP2.md#1.8.%20Lab%20Response%20queue%20poisoning%20via%20H2.TE%20request%20smuggling%20%E2%AD%95%EF%B8%8F)
3. [CSRF](CSRF.md#2.11.%20Lab%20CSRF%20where%20Referer%20validation%20depends%20on%20header%20being%20present%20%E2%AD%95%EF%B8%8F)
4. [Deserialization](Deserialization.md#3.4.%20Lab%20Arbitrary%20object%20injection%20in%20PHP%20%E2%AD%95%EF%B8%8F)
5. [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.5.Lab%20Web%20cache%20poisoning%20via%20an%20unkeyed%20query%20string%20%E2%AD%95%EF%B8%8F)
6. [Brute Force](Brute%20Force.md#1.4.%20Lab%20Username%20enumeration%20via%20subtly%20different%20responses%20%E2%AD%95%EF%B8%8F)
7. [LFI](LFI.md#3.6.%20Lab%20File%20path%20traversal%2C%20validation%20of%20file%20extension%20with%20null%20byte%20bypass%20%E2%AD%95%EF%B8%8F)
8. [SQLi](SQLi.md#2.5.%20Lab%20SQL%20injection%20attack%2C%20listing%20the%20database%20contents%20on%20non-Oracle%20databases%20%E2%AD%95%EF%B8%8F)
9. [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20Stored%20DOM%20XSS%20%E2%AD%95%EF%B8%8F)
10. [Blind SQLi](Blind%20SQLi.md#2.17.%20Lab%20Blind%20SQL%20injection%20with%20out-of-band%20data%20exfiltration%20%E2%AD%95%EF%B8%8F)

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
		- Use this HTTP Request Smuggling Payload: [README](Stage%201/HTTP%20Request%20Smuggling/README.md#%5Eosmqx6)
		- "HTML is allowed"
	- Search Bar
		- Use the HTTP Request Smuggling Payload
		- DOM Invader
		- Enter XSS Payload