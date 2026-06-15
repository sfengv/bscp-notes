#### Check stock ✅
##### Change Email
- `GET /accountDetails`
	- `Access-Control-Allow-Credentials: true`
		- [CORS](../../../Stage%202/CORS.md#2.3.%20Lab%20CORS%20vulnerability%20with%20trusted%20insecure%20protocols%20%E2%AD%95%EF%B8%8F)

##### Login Attempt: `wiener:peter`
- "Invalid username or password."
	- `POST /product/stock` uses XML
		- [SQLi](SQLi.md#2.18.%20Lab%20SQL%20injection%20with%20filter%20bypass%20via%20XML%20encoding%20%E2%AD%95%EF%B8%8F)
	- `stockAPI` in `POST /product/stock`
		- [SSRF](SSRF.md#3.1.%20Lab%20Basic%20SSRF%20against%20the%20local%20server%20%E2%AD%95%EF%B8%8F)
		- [SSRF](SSRF.md#3.2.%20Lab%20Basic%20SSRF%20against%20another%20back-end%20system%20%E2%AD%95%EF%B8%8F)
		- [SSRF](SSRF.md#3.4.%20Lab%20SSRF%20with%20blacklist-based%20input%20filter%20%E2%AD%95%EF%B8%8F)

##### Filter + Submit Feedback + Live Chat
- `jquery_1-7-1.js`
	- [Client side](Client%20side.md#2.5.%20Lab%20Client-side%20prototype%20pollution%20in%20third-party%20libraries%20%E2%AD%95%EF%B8%8F)](<#### Check stock ✅

##### Change Email
- `GET /accountDetails`
	- `Access-Control-Allow-Credentials: true`
		- [CORS](../../../Stage%202/CORS.md#2.3.%20Lab%20CORS%20vulnerability%20with%20trusted%20insecure%20protocols%20%E2%AD%95%EF%B8%8F)

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