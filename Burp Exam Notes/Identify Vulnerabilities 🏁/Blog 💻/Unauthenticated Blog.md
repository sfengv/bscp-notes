### Unauthenticated Blog
#### No Comments
##### GraphQL
- GraphQL Visualizer
	- `postPassword: String` in `BlogPost`
		- [GraphQL Endpoints](GraphQL%20Endpoints.md#2.1.%20Lab%20Accessing%20private%20GraphQL%20posts%20%E2%AD%95%EF%B8%8F)
	- `BlogPost` + `User`
		- [GraphQL Endpoints](GraphQL%20Endpoints.md#2.2%20Lab%20Accidental%20exposure%20of%20private%20GraphQL%20fields%20%E2%AD%95%EF%B8%8F)



#### Every field + File Upload in Blog
##### Page Source
- `svg` in `<img>` tag
	- [XXE](XXE.md#3.8.%20Lab%20Exploiting%20XXE%20via%20image%20file%20upload%20%E2%AD%95%EF%B8%8F)
- `indexOf('http')` + `addEventListener`
	- [DOM-Based XSS](DOM-Based%20XSS.md#1.2.%20Lab%20DOM%20XSS%20using%20web%20messages%20and%20a%20JavaScript%20URL%20%E2%AD%95%EF%B8%8F)


#### Every field
##### Page Source
- More than one result of `canonical`
	- [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20in%20canonical%20link%20tag%20%E2%AD%95%EF%B8%8F)

##### `X-Cache`
- Query string in response like `?cb=123` + `X-Cache: miss` <sup>X-Cache has to say miss and cb=123 is reflected in response</sup>
	- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.5.Lab%20Web%20cache%20poisoning%20via%20an%20unkeyed%20query%20string%20%E2%AD%95%EF%B8%8F)
	- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.6.Lab%20Web%20cache%20poisoning%20via%20an%20unkeyed%20query%20parameter%20%E2%AD%95%EF%B8%8F)
	- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.9.%20Lab%20URL%20normalization%20%E2%AD%95%EF%B8%8F)
- Param Miner > Guess query params > Parameter Cloaking
	- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.7.Lab%20Parameter%20cloaking%20%E2%AD%95%EF%B8%8F)
- `/js/geolocate.js?callback=setCountryCookie`
	- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.8.%20Lab%20Web%20cache%20poisoning%20via%20a%20fat%20GET%20request%20%E2%AD%95%EF%B8%8F)

##### Submit Feedback
- `jQuery 1.8.2`
	- `GET /feedback?returnPath=/`
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.5.%20Lab%20DOM%20XSS%20in%20jQuery%20anchor%20%60href%60%20attribute%20sink%20using%20%60location.search%60%20source%20%E2%AD%95%EF%B8%8F)
- `csrf` token in `POST /feedback/submit`
	- [Clickjacking](Clickjacking.md#1%2F2.4.%20Lab%20Exploiting%20clickjacking%20vulnerability%20to%20trigger%20DOM-based%20XSS%20%E2%AD%95%EF%B8%8F)

##### Target > Engagement tools > Search
- `returnUrl = /url`
	- [DOM-Based XSS](DOM-Based%20XSS.md#1.4.%20Lab%20DOM-based%20open%20redirection%20%E2%AD%95%EF%B8%8F)
- `html.replace`
	- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20Stored%20DOM%20XSS%20%E2%AD%95%EF%B8%8F)
- `hashchange`
	- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20DOM%20XSS%20in%20jQuery%20selector%20sink%20using%20a%20hashchange%20event%20%E2%AD%95%EF%B8%8F)

##### Post Comment
- Solved with `</p><script>alert(1)</script>`
	- [Stored XSS](Stored%20XSS.md#1.x.%20Lab%20Stored%20XSS%20into%20HTML%20context%20with%20nothing%20encoded%20%E2%AD%95%EF%B8%8F)
- `name` field is a hyperlink
	- Solve with `javascript:alert(1)`
		- [Stored XSS](Stored%20XSS.md#1.x.%20Lab%20Stored%20XSS%20into%20anchor%20%60href%60%20attribute%20with%20double%20quotes%20HTML-encoded%20%E2%AD%95%EF%B8%8F)
	- `website` field has client side validation
		- [Stored XSS](Stored%20XSS.md#1.x.%20Lab%20Stored%20XSS%20into%20%60onclick%60%20event%20with%20angle%20brackets%20and%20double%20quotes%20HTML-encoded%20and%20single%20quotes%20and%20backslash%20escaped%20%E2%AD%95%EF%B8%8F)


##### Use this HTTP Request Smuggling Payload: [README](../../Stage%201/HTTP%20Request%20Smuggling/README.md#%5Eosmqx6)
- 500 Internal Server Error
	- "Server Error: Communication timed out"
		- [CL](CL.md#1.1.%20Lab%20HTTP%20request%20smuggling%2C%20confirming%20a%20CL.TE%20vulnerability%20via%20differential%20responses%20%E2%AD%95%EF%B8%8F)
		- [CL](CL.md#1.x.%20Lab%20HTTP%20request%20smuggling%2C%20basic%20CL.TE%20vulnerability%20%E2%AD%95%EF%B8%8F)
		- `User-Agent` reflected in response in `GET /post?postId=`
			- [CL](CL.md#1.7.%20Lab%20Exploiting%20HTTP%20request%20smuggling%20to%20deliver%20reflected%20XSS%20%E2%AD%95%EF%B8%8F)
- 400 Bad Request
	- [TE](TE.md#1.2.%20Lab%20HTTP%20request%20smuggling%2C%20confirming%20a%20TE.CL%20vulnerability%20via%20differential%20responses%20%E2%AD%95%EF%B8%8F)
	- [TE](TE.md#1.x.%20Lab%20HTTP%20request%20smuggling%2C%20basic%20TE.CL%20vulnerability%20%E2%AD%95%EF%B8%8F)
	- [Others](Others.md#1.x.%20Lab%20CL.0%20request%20smuggling%20%E2%AD%95%EF%B8%8F)

##### "HTML is allowed"
- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20Exploiting%20DOM%20clobbering%20to%20enable%20XSS%20%E2%AD%95%EF%B8%8F)


#### Search Bar
##### Use the HTTP Request Smuggling Payload
- 400 Bad Request
	- "Both chunked encoding and content-length were specified"
		- [HTTP2](HTTP2.md#1.9.%20Lab%20H2.CL%20request%20smuggling%20%E2%AD%95%EF%B8%8F)

##### DOM Invader
- Inject forms > Search
	- `document.write(1)`
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20DOM%20XSS%20in%20%60document.write%60%20sink%20using%20source%20%60location.search%60%20%E2%AD%95%EF%B8%8F)
	- `element.InnerHTML(1)`
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.4.%20Lab%20DOM%20XSS%20in%20%60innerHTML%60%20sink%20using%20source%20%60location.search%60%20%E2%AD%95%EF%B8%8F)
	- `element.setAttribute.href(1)` / `ng-app` / `angular 1.7.7`
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20DOM%20XSS%20in%20AngularJS%20expression%20with%20angle%20brackets%20and%20double%20quotes%20HTML-encoded%20%E2%AD%95%EF%B8%8F)
	- `eval(1)`
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20Reflected%20DOM%20XSS%20%E2%AD%95%EF%B8%8F)

##### Enter XSS Payload
```
<script>alert(1)</script>
```
- Solved with payload
	- [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20HTML%20context%20with%20nothing%20encoded%20%E2%AD%95%EF%B8%8F)
- `&lt;script&gt;alert(1)&lt;/script&gt;`
	- <sup>Scan results: YES</sup> [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20attribute%20with%20angle%20brackets%20HTML-encoded%20%E2%AD%95%EF%B8%8F)
- `var searchTerms`
	- <sup>Scan results: YES</sup> [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20a%20JavaScript%20string%20with%20angle%20brackets%20HTML%20encoded%20%E2%AD%95%EF%B8%8F)
	- [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20a%20JavaScript%20string%20with%20single%20quote%20and%20backslash%20escaped%20%E2%AD%95%EF%B8%8F)
	- <sup>Scan results: YES</sup> [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20a%20JavaScript%20string%20with%20angle%20brackets%20and%20double%20quotes%20HTML-encoded%20and%20single%20quotes%20escaped%20%E2%AD%95%EF%B8%8F)
- `var message`
	- Payload enclosed in backticks
		- [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20a%20template%20literal%20with%20angle%20brackets%2C%20single%2C%20double%20quotes%2C%20backslash%20and%20backticks%20Unicode-escaped%20%E2%AD%95%EF%B8%8F)
- `var key`
	- [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20with%20AngularJS%20sandbox%20escape%20without%20strings%20%E2%AD%95%EF%B8%8F)
- "Tag is not allowed"
	- `<body>` / `<custom tags>` allowed
		- <sup>Scan results: YES</sup> [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20HTML%20context%20with%20most%20tags%20and%20attributes%20blocked%20%E2%AD%95%EF%B8%8F)
	- `<svg>` allowed
		- [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20with%20some%20SVG%20markup%20allowed%20%E2%AD%95%EF%B8%8F)
	- `<xss+id=x>#x';` allowed
		- <sup>Scan results: YES</sup> [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20HTML%20context%20with%20all%20tags%20blocked%20except%20custom%20ones%20%E2%AD%95%EF%B8%8F)





---

>[!info] Circle emoji ⭕️ next to Lab titles
>It means that lab has been linked to this page.





