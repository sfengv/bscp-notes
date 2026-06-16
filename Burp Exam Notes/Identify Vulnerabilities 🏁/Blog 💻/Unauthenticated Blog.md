### Unauthenticated Blog
#### No Comments
##### GraphQL
- GraphQL Visualizer
	- `postPassword: String` in `BlogPost`
		- [[GraphQL Endpoints#2.1. Lab Accessing private GraphQL posts ⭕️]]
	- `BlogPost` + `User`
		- [[GraphQL Endpoints#2.2 Lab Accidental exposure of private GraphQL fields ⭕️]]



#### Every field + File Upload in Blog
##### Page Source
- `svg` in `<img>` tag
	- [[XXE#3.8. Lab Exploiting XXE via image file upload ⭕️]]
- `indexOf('http')` + `addEventListener`
	- [[DOM-Based XSS#1.2. Lab DOM XSS using web messages and a JavaScript URL ⭕️]]


#### Every field
##### Page Source
- More than one result of `canonical`
	- [[Reflected XSS#1.x. Lab Reflected XSS in canonical link tag ⭕️]]

##### `X-Cache`
- Query string in response like `?cb=123` + `X-Cache: miss` <sup>X-Cache has to say miss and cb=123 is reflected in response</sup>
	- [[Web Cache Poisoning#1.5.Lab Web cache poisoning via an unkeyed query string ⭕️]]
	- [[Web Cache Poisoning#1.6.Lab Web cache poisoning via an unkeyed query parameter ⭕️]]
	- [[Web Cache Poisoning#1.9. Lab URL normalization ⭕️]]
- Param Miner > Guess query params > Parameter Cloaking
	- [[Web Cache Poisoning#1.7.Lab Parameter cloaking ⭕️]]
- `/js/geolocate.js?callback=setCountryCookie`
	- [[Web Cache Poisoning#1.8. Lab Web cache poisoning via a fat GET request ⭕️]]

##### Submit Feedback
- `jQuery 1.8.2`
	- `GET /feedback?returnPath=/`
		- [[DOM-Based XSS#1.5. Lab DOM XSS in jQuery anchor `href` attribute sink using `location.search` source ⭕️]]
- `csrf` token in `POST /feedback/submit`
	- [[Clickjacking#1/2.4. Lab Exploiting clickjacking vulnerability to trigger DOM-based XSS ⭕️]]

##### Target > Engagement tools > Search
- `returnUrl = /url`
	- [[DOM-Based XSS#1.4. Lab DOM-based open redirection ⭕️]]
- `html.replace`
	- [[DOM-Based XSS#1.x. Lab Stored DOM XSS ⭕️]]
- `hashchange`
	- [[DOM-Based XSS#1.x. Lab DOM XSS in jQuery selector sink using a hashchange event ⭕️]]

##### Post Comment
- Solved with `</p><script>alert(1)</script>`
	- [[Stored XSS#1.x. Lab Stored XSS into HTML context with nothing encoded ⭕️]]
- `name` field is a hyperlink
	- Solve with `javascript:alert(1)`
		- [[Stored XSS#1.x. Lab Stored XSS into anchor `href` attribute with double quotes HTML-encoded ⭕️]]
	- `website` field has client side validation
		- [[Stored XSS#1.x. Lab Stored XSS into `onclick` event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped ⭕️]]


##### Use this HTTP Request Smuggling Payload: [[Burp Exam Notes/Stage 1/HTTP Request Smuggling/README#^osmqx6]]
- 500 Internal Server Error
	- "Server Error: Communication timed out"
		- [[CL.TE#1.1. Lab HTTP request smuggling, confirming a CL.TE vulnerability via differential responses ⭕️]]
		- [[CL.TE#1.x. Lab HTTP request smuggling, basic CL.TE vulnerability ⭕️]]
		- `User-Agent` reflected in response in `GET /post?postId=`
			- [[CL.TE#1.7. Lab Exploiting HTTP request smuggling to deliver reflected XSS ⭕️]]
- 400 Bad Request
	- [[TE.CL#1.2. Lab HTTP request smuggling, confirming a TE.CL vulnerability via differential responses ⭕️]]
	- [[TE.CL#1.x. Lab HTTP request smuggling, basic TE.CL vulnerability ⭕️]]
	- [[Others#1.x. Lab CL.0 request smuggling ⭕️]]

##### "HTML is allowed"
- [[DOM-Based XSS#1.x. Lab Exploiting DOM clobbering to enable XSS ⭕️]]


#### Search Bar
##### Use the HTTP Request Smuggling Payload
- 400 Bad Request
	- "Both chunked encoding and content-length were specified"
		- [[HTTP2#1.9. Lab H2.CL request smuggling ⭕️]]

##### DOM Invader
- Inject forms > Search
	- `document.write(1)`
		- [[DOM-Based XSS#1.x. Lab DOM XSS in `document.write` sink using source `location.search` ⭕️]]
	- `element.InnerHTML(1)`
		- [[DOM-Based XSS#1.4. Lab DOM XSS in `innerHTML` sink using source `location.search` ⭕️]]
	- `element.setAttribute.href(1)` / `ng-app` / `angular 1.7.7`
		- [[DOM-Based XSS#1.x. Lab DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded ⭕️]]
	- `eval(1)`
		- [[DOM-Based XSS#1.x. Lab Reflected DOM XSS ⭕️]]

##### Enter XSS Payload
```
<script>alert(1)</script>
```
- Solved with payload
	- [[Reflected XSS#1.x. Lab Reflected XSS into HTML context with nothing encoded ⭕️]]
- `&lt;script&gt;alert(1)&lt;/script&gt;`
	- <sup>Scan results: YES</sup> [[Reflected XSS#1.x. Lab Reflected XSS into attribute with angle brackets HTML-encoded ⭕️]]
- `var searchTerms`
	- <sup>Scan results: YES</sup> [[Reflected XSS#1.x. Lab Reflected XSS into a JavaScript string with angle brackets HTML encoded ⭕️]]
	- [[Reflected XSS#1.x. Lab Reflected XSS into a JavaScript string with single quote and backslash escaped ⭕️]]
	- <sup>Scan results: YES</sup> [[Reflected XSS#1.x. Lab Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped ⭕️]]
- `var message`
	- Payload enclosed in backticks
		- [[Reflected XSS#1.x. Lab Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped ⭕️]]
- `var key`
	- [[Reflected XSS#1.x. Lab Reflected XSS with AngularJS sandbox escape without strings ⭕️]]
- "Tag is not allowed"
	- `<body>` / `<custom tags>` allowed
		- <sup>Scan results: YES</sup> [[Reflected XSS#1.x. Lab Reflected XSS into HTML context with most tags and attributes blocked ⭕️]]
	- `<svg>` allowed
		- [[Reflected XSS#1.x. Lab Reflected XSS with some SVG markup allowed ⭕️]]
	- `<xss+id=x>#x';` allowed
		- <sup>Scan results: YES</sup> [[Reflected XSS#1.x. Lab Reflected XSS into HTML context with all tags blocked except custom ones ⭕️]]





---

>[!info] Circle emoji ⭕️ next to Lab titles
>It means that lab has been linked to this page.





