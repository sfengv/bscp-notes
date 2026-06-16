## **_Forgot password functionality_**
### * **HTTP host header attacks**
- [[Host Headers#1.1 Lab Basic password reset poisoning ⭕️]]
### * **Brute-force**
- [[Brute Force#1.1 Lab Username enumeration via different responses ⭕️]]
- [[Brute Force#1.4. Lab Username enumeration via subtly different responses ⭕️]]
### * **2-Factor-Authentication**
- [[Authentication#1.2. Lab 2FA simple bypass ⭕️]]
- [[Authentication#1.8. Lab 2FA broken logic ⭕️]]
### * **Race conditions**
- [[Password Reset#2.5. Lab Exploiting time-sensitive vulnerabilities ⭕️]]
### * **Authentication Issues**
- [[Password Reset#2.3. Lab Password reset broken logic ⭕️]]
- [[Host Headers#1.11 Lab Password reset poisoning via middleware ⭕️]]
### * **Server-side parameter pollution**
- [[API Testing#2.2. Lab Exploiting server-side parameter pollution in a query string ⭕️]]
### * Flawed Access Control
- [[Access Control#2.3. Lab User role controlled by request parameter ⭕️]]
>Tip: In this lab by changing the Admin:false cookie to Admin:true the server grants us Admin privileges right away. However, some servers have validation techniques that don't accept Admin:true unless it was issued by the server itself. In this case we search for app functionalities that aren't expecting already authenticated users to use them (like pre-login forgot password functionality). If vulnerable they might only validate the username part of the cookie and not the login status part. And so they issue authenticated cookies for random users.
---
