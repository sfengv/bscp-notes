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