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