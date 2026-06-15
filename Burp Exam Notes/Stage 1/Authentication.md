## Stage 1 + 2
### What is it?
This is under the **business logic vulnerabilities** page. These are flaws in the design and implementation of the web app that opens up to attacks.

### Identify Vulnerabilities in Exam
- Asks for 4-digit security code
	- **Solution**: go directly to `/my-account` - [Authentication](Authentication.md#1.2.%20Lab%202FA%20simple%20bypass%20%E2%AD%95%EF%B8%8F)
	- `verify` cookie 
		- [Authentication](Authentication.md#1.8.%20Lab%202FA%20broken%20logic%20%E2%AD%95%EF%B8%8F)
- "You have made too many incorrect login attempts. Please try again in X minute(s)." *but resets when you use a valid username*
	- [Authentication](Authentication.md#1.6.%20Lab%20Broken%20brute-force%20protection%2C%20IP%20block%20%E2%AD%95%EF%B8%8F)
- "Invalid username or password."
	- [Authentication](Authentication.md#1.7.%20Lab%20Username%20enumeration%20via%20account%20lock%20%E2%AD%95%EF%B8%8F)
	- [Brute Force](Brute%20Force.md#1.4.%20Lab%20Username%20enumeration%20via%20subtly%20different%20responses%20%E2%AD%95%EF%B8%8F)
- "Admin interface is only for local users"
	- **Solution**: Proxy > Match and replace > Type: Request header > Replace: `X-Custom-IP-Authorization: 127.0.0.1`
		- If `127.0.0.1` doesn't work, [follow this guide](https://github.com/DingyShark/BurpSuiteCertifiedPractitioner#approach-7) for encoding.
		- [Content Discovery](Content%20Discovery.md#1.4.%20Lab%20Authentication%20bypass%20via%20information%20disclosure%20%E2%AD%95%EF%B8%8F)



---
#### 1.2. Lab: 2FA simple bypass ⭕️
This lab's two-factor authentication can be bypassed. You have already obtained a valid username and password, but do not have access to the user's 2FA verification code. To solve the lab, access Carlos's account page.
- Your credentials: `wiener:peter`
- Victim's credentials `carlos:montoya`
1. Login as wiener, click Email client, use 2FA code to sign in.
	1. ![197](Authentication-20260118193412988.png)
2. Login as the victim > when you see the `/login2` request asking for the 2FA code (which we do not have access to) > just change the endpoint to `/my-account` to bypass it and solve.
	1. ![451](Authentication-20260118193619986.png)
	2. ![462](Authentication-20260118193643401.png)

>[!tip] How to Identify this Vulnerability
>1. App asks for 2FA code upon logging in
>2. `/login2` request exists

---
#### 1.6. Lab: Broken brute-force protection, IP block ⭕️
This lab is vulnerable due to a logic flaw in its password brute-force protection. To solve the lab, brute-force the victim's password, then log in and access their account page.
- Your credentials: `wiener:peter`
- Victim's username: `carlos`
- [Candidate passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)

1. Enter arbitrary credentials like `aaa:aaa` and after the 3rd attempt, we get an error: "You have made too many incorrect login attempts. Please try again in 1 minute(s)." *It is possible to reset the counter for the number of failed login attempts by logging in to your own account before this limit is reached.*
	1. ![Authentication-20260118194201590](Authentication-20260118194201590.png)
2. Send `POST /login` request to Intruder. Use **Pitchfork attack**. Resource Pool maximum concurrent requests to `1`. 
3. Follow the community solution to create a script to print out wiener once, carlos twice for usernames and peter once, potential carlos password twice for passwords. This is to bypass the brute force protection back in Step 1.
	1. ![Authentication-20260118195905294](Authentication-20260118195905294.png)
	2. ![Authentication-20260118200001107](Authentication-20260118200001107.png)
```
#!/usr/bin/env python3

# This script is used to bypass the Authentication: Broken brute-force protection, 
# IP block lab. We get blocked after the 3rd invalid attempt but the blocking counter 
# resets once we have a successful login. So we try carlos with a password twice then 
# login with wiener:peter on the 3rd attempt to reset the counter. Then, try carlos again.

print("=========== Usernames ===========")
for i in range (150):
	if i % 3:
		print("carlos")
	else:
		print("wiener")

print("=========== Passwords ===========")
with open('passwords.txt', 'r') as f:
	lines = f.readlines()

i = 0
for pwd in lines:
	if i% 3:
		print(pwd.strip('\n'))
	else:
		print("peter")
		print(pwd.strip('\n'))
		i = i + 1
	i = i + 1
```
4. Pop usernames in payload position 1 and passwords in payload position 2. Look for a 302 status code for `carlos`. Login and solve.
	1. ![Authentication-20260119185531080](Authentication-20260119185531080.png)

---
#### 1.x. Lab: Inconsistent handling of exceptional input ⭕️
This lab doesn't adequately validate user input. You can exploit a logic flaw in its account registration process to gain access to administrative functionality. To solve the lab, access the admin panel and delete the user `carlos`.
Hint: You can use the link in the lab banner to access an email client connected to your own private mail server. The client will display all messages sent to `@YOUR-EMAIL-ID.web-security-academy.net` and any arbitrary subdomains. Your unique email ID is displayed in the email client.

1. Go to Target > Site map > right click on lab URL > Engagement tools > Discover content. This will crawl the site and find hidden content/functionalities like endpoints. Or just try `/admin`.
![400](Authentication-20251027213654259.png)
 ^npw924
2. Click `Session not running` to start the scan and eventually we will see `/admin`. Stop the scan.
![399](Authentication-20251027213900962.png)

3. Navigating to `/admin` will give us the following message. Only a DontWannaCry user can access the admin panel.
![377](Authentication-20251027214107962.png)

4. Register an account with the `@dontwannacry.com` email domain. Then try to see if we can login without needing to verify the email. *This is a real world scenario we can try in a pentest.* Tried to login but no dice. We need to verify the email.
![374](Authentication-20251027214450609.png)
![Authentication-20251027214523891](Authentication-20251027214523891.png)

5. Click on Email client, copy the email address, then register an account with that email. Check the Email client again and there will be a verification email. Click on the link and then login. Try to access `/admin` again but it wont work.
![301](Authentication-20251027214620344.png)
![287](Authentication-20251027214648954.png)

6. Now we want to test if the app **validates user input with excessive amount of characters**. Register a fresh account, `test3`, with an arbitrary email, then send the `/register` POST request to repeater.
7. Replace the email with the attacker email, highlight, right click, and URL encode key characters. Send to Intruder. Add the `attacker` portion of the email. Payload type: Character blocks, min length: 100, max length: 500, step: 100. So this will put 100 A's in the first request, 200 on the second one, etc.
![Authentication-20251027215632148](Authentication-20251027215632148.png)

8. Got 200 OK on all of them. Confirming that the app *does not* validate excessive amount of user input. The Email client will show 5 more verification links. Click on one of them.
![329](Authentication-20251027215741679.png)

9. After logging in, it seems that our email is truncated because when we registered the account, we supplied the email domain too. Use something online or VS Code to get the number of characters of the AAA... and it's 255. Now we want to include `@dontwannacry.com` in the email so the web app will recognize it and treat the new account as a dontwannacry account and give us the admin panel.
![Authentication-20251027220130880](Authentication-20251027220130880.png)
**PAYLOAD**
```
very-long-strings-so-very-long-string-so-very-long-string-so-very-long-string-so-very-long-string-so-very-long-string-so-very-long-string-so-very-long-string-so-very-long-string-so-very-long-string-so-very-long-string-so-very-long-strings@dontwannacry.com.exploit-0afe007b03a34169c10b8fc501510091.exploit-server.net
```

10. `@dontwannacry.com` is 17 characters, 255-17=238 so we only want 238 A's followed by `%40dontwannacry.com` (we need to encode @ hence the `%40`), then our exploit email domain. So make a fresh account (bc the csrf token might be expired) then send to Repeater. Verify the email link and login.
![Authentication-20251027221744679](Authentication-20251027221744679.png)

11. Now we see the `@dontwannacry.com` email domain and see the Admin panel. Delete `carlos` and solved.
![Authentication-20251027221852232](Authentication-20251027221852232.png)
- - - 
#### 1.x. Lab: Infinite money logic flaw ⭕️
This lab has a logic flaw in its purchasing workflow. To solve the lab, exploit this flaw to buy a "Lightweight l33t leather jacket".
You can log in to your own account using the following credentials: `wiener:peter`
>[!info] How is this useful in the exam?
>Auth Token bypass using Macros. If the authentication login is protected against brute force by using random token that is used on every login POST, a Burp Macro can be used to bypass protection. [Guide here](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study?tab=readme-ov-file#auth-token-bypass-macro).

1. Scroll to the bottom and enter email (from Email client). Will get `SIGNUP30` promo code. Use it to buy a Gift Card then redeem it to the `wiener:peter` account. Just bought a $10 gift card that cost $7 so netted $3. Now **automate this entire process** to get enough money to buy the jacket.
![154](Authentication-20251028204953392.png)
![191](Authentication-20251028205029447.png)

2. Settings > Sessions > Session handling rules > Add > Scope > select Include all URLs. Then Details > Rule actions > Add > Run a macro. Select macro > Add to open the Macro editor.
![525](Authentication-20251028205222008.png)
![216](Authentication-20251028203544873.png)

3. Hold `cmd` then add the following requests. Select the `GET /cart/order-confirmation?order-confirmed=true` request > Configure item > Add > set Parameter name: `gift-card` then highlight the actual gift card code in the box. Click OK twice.
![434](Authentication-20251028205359275.png)
![424](Authentication-20251028205705791.png)

4. Select `POST /gift-card` > Configure item > Parameter handling then use the dropdown lists for `gift-card` to say Derive from prior response and Response 4. Click OK.
![407](Authentication-20251028205917291.png)

5. Click Test Macro. It worked when `Get /cart/order-confirmation...`'s response show a new gift card code and it matches with `POST /gift-card`'s request.
![324](Authentication-20251028210034137.png)
![324](Authentication-20251028210038029.png)

6. Send `GET /my-account` to Intruder, Sniper attack, Payload type: Null payloads, Generate 412, Resource Pool, create a new one, concurrent: 1. Start attack. After it's done, there should be enough store credit to buy the jacket. Solved.
![Authentication-20251028204842483](Authentication-20251028204842483.png)

- - - 
#### 1.7. Lab: Username enumeration via account lock ⭕️
This lab is vulnerable to username enumeration. It uses account locking, but this contains a logic flaw. To solve the lab, enumerate a valid username, brute-force this user's password, then access their account page.
1. Enter arbitrary credentials in the Login feature. Send `POST /login` to Intruder.
2. Find the valid username:
	1. **Cluster Bomb attack**, set the `username` value as a position but set a position *after* the `password` param. Payload position 1, go to [here for a list of usernames](https://portswigger.net/web-security/authentication/auth-lab-usernames) then paste it in. For payload position 2, get null payloads and generate 5 payloads.
		1. ![Authentication-20260119190044368](Authentication-20260119190044368.png)
		2. ![Authentication-20260119190109606](Authentication-20260119190109606.png)
	2. Sort by length and the username without the "Invalid username or password" error message is the valid username: **att**.
		1. ![Authentication-20260119190208957](Authentication-20260119190208957.png)
3. Intruder > clear positions > set `username: att` > set `password` param as the payload position > switch to **Sniper attack** > paste the candidate password list in.
	1. ![Authentication-20260119190250295](Authentication-20260119190250295.png)
	2. Settings > Grep Extract > highlight "Invalid username or password" > OK. Because when we get a hit, the response won't have any error messages within those `<p>` tags. Start attack.
	3. ![Authentication-20260119191049148](Authentication-20260119191049148.png)
4. Sort by the grep extract column: `-warning>` and notice there is no error message so `cheese` is the valid password.
	1. ![Authentication-20260119191126772](Authentication-20260119191126772.png)
5. Login as `att:cheese` to solve.
	1. ![246](Authentication-20260119191221210.png)
>[!tip] How to Identify this Vulnerability?
>1. Login feature with different warning messages for valid username

---
#### 1.8. Lab: 2FA broken logic ⭕️
This lab's two-factor authentication is vulnerable due to its flawed logic. To solve the lab, access Carlos's account page.
- Your credentials: `wiener:peter`
- Victim's username: `carlos`
You also have access to the email server to receive your 2FA verification code.
1. Login with `wiener:peter`, Go to Email client and get your 2FA code and submit it. Notice the `GET /login2` request as a cookie `verify=wiener`. 
	1. ![Authentication-20260119192843606](Authentication-20260119192843606.png)
2. Send `GET /login2` to Repeater > set `verify=carlos` > Send.
3. Brute force 2FA:
	1. Send `POST /login2` to Intruder > set `verify=carlos` > set position to the `mfa-code` param > Payload type: Numbers > From: 0000 To: 9999 Step 1 > Start attack.
		1. ![Authentication-20260119193100403](Authentication-20260119193100403.png)
	2. Sort the Status code column and look for 302. `1879` is our 2FA code. Right click > show response in browser > paste URL in Chrome > solve.
		1. ![Authentication-20260119192651461](Authentication-20260119192651461.png)
>[!tip] How to Identify this Vulnerability?
>1. 2FA feature with 4 digits
>2. `verify` cookie header

---
#### 1.12. Lab: Password brute-force via password change ⭕️
[Password Reset](Password%20Reset.md#1.12.%20Lab%20Password%20brute-force%20via%20password%20change%20%E2%AD%95%EF%B8%8F)








