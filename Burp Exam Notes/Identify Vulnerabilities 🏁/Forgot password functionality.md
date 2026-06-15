## **_Forgot password functionality_**
### * **HTTP host header attacks**
- [Host Headers](Host%20Headers.md#1.1%20Lab%20Basic%20password%20reset%20poisoning%20%E2%AD%95%EF%B8%8F)
### * **Brute-force**
- [Brute Force](Brute%20Force.md#1.1%20Lab%20Username%20enumeration%20via%20different%20responses%20%E2%AD%95%EF%B8%8F)
- [Brute Force](Brute%20Force.md#1.4.%20Lab%20Username%20enumeration%20via%20subtly%20different%20responses%20%E2%AD%95%EF%B8%8F)
### * **2-Factor-Authentication**
- [Authentication](Authentication.md#1.2.%20Lab%202FA%20simple%20bypass%20%E2%AD%95%EF%B8%8F)
- [Authentication](Authentication.md#1.8.%20Lab%202FA%20broken%20logic%20%E2%AD%95%EF%B8%8F)
### * **Race conditions**
- [Password Reset](Password%20Reset.md#2.5.%20Lab%20Exploiting%20time-sensitive%20vulnerabilities%20%E2%AD%95%EF%B8%8F)
### * **Authentication Issues**
- [Password Reset](Password%20Reset.md#2.3.%20Lab%20Password%20reset%20broken%20logic%20%E2%AD%95%EF%B8%8F)
- [Host Headers](Host%20Headers.md#1.11%20Lab%20Password%20reset%20poisoning%20via%20middleware%20%E2%AD%95%EF%B8%8F)
### * **Server-side parameter pollution**
- [API Testing](API%20Testing.md#2.2.%20Lab%20Exploiting%20server-side%20parameter%20pollution%20in%20a%20query%20string%20%E2%AD%95%EF%B8%8F)
### * Flawed Access Control
- [Access Control](Access%20Control.md#2.3.%20Lab%20User%20role%20controlled%20by%20request%20parameter%20%E2%AD%95%EF%B8%8F)
>Tip: In this lab by changing the Admin:false cookie to Admin:true the server grants us Admin privileges right away. However, some servers have validation techniques that don't accept Admin:true unless it was issued by the server itself. In this case we search for app functionalities that aren't expecting already authenticated users to use them (like pre-login forgot password functionality). If vulnerable they might only validate the username part of the cookie and not the login status part. And so they issue authenticated cookies for random users.
---
