
### Identify Vulnerabilities in Exam
- `username` in `POST /forgot-password`
	- `user` + `token` URL param in password reset email
		- [Password Reset](Password%20Reset.md#2.5.%20Lab%20Exploiting%20time-sensitive%20vulnerabilities%20%E2%AD%95%EF%B8%8F)
	- Remove `current-password`
		- **Solution:** remove that + `change username=administrator` to change their password
		- [Password Reset](Password%20Reset.md#2.7.%20Lab%20Weak%20isolation%20on%20dual-use%20endpoint%20%E2%AD%95%EF%B8%8F)
	- `temp-forgot-password-token` in `POST` + source code
		- **Solution:** change `username=administrator`
			- If that doesn't work, try just removing the `temp-forgot-password-token` value OR remove the token altogether
			- [Password Reset](Password%20Reset.md#2.3.%20Lab%20Password%20reset%20broken%20logic%20%E2%AD%95%EF%B8%8F)
	- Enter different values for `new-password-1` + `new-password-2` == "New passwords do not match"
		- [Password Reset](Password%20Reset.md#1.12.%20Lab%20Password%20brute-force%20via%20password%20change%20%E2%AD%95%EF%B8%8F)


- - - 
#### 2.3. Lab: Password reset broken logic ⭕️
#Authentication #Does-Not-Require-User-Interaction
This lab's password reset functionality is vulnerable. To solve the lab, reset Carlos's password then log in and access his "My account" page.
- Your credentials: `wiener:peter`
- Victim's username: `carlos`

1. Click on Forgot Password? link, enter `wiener`, click Email client, click link, change password to something like `password`, and login.
2. Observe the `POST /forgot-password?temp-forgot-password-token=` request and notice the `username` field in the request body. Send to Repeater.
	1. ![Password Reset-20260108095402299](Password%20Reset-20260108095402299.png)
3. Send the request again, got a 302 -- success -- this means the server isn't validating the token at all. 
	1. ![Password Reset-20260108095559009](Password%20Reset-20260108095559009.png)
4. Set `username=carlos`, Send, and the lab is solved.
	1. ![Password Reset-20260108095715672](Password%20Reset-20260108095715672.png)
>In the exam, if this doesn't work, can also try removing the `temp-forgot-password-token` from the URL + the body. Or just remove the value and keep the key.
>![Password Reset-20260108095840599](Password%20Reset-20260108095840599.png)

>[!tip] How to Identify this Vulnerability
>1. Forgot Password feature
>2. `temp-forgot-password-token`

- - -

#### 2.7. Lab: Weak isolation on dual-use endpoint ⭕️
#Business-logic-vulnerabilities #Does-Not-Require-User-Interaction 
This lab makes a flawed assumption about the user's privilege level based on their input. As a result, you can exploit the logic of its account management features to gain access to arbitrary users' accounts. To solve the lab, access the `administrator` account and delete the user `carlos`.
You can log in to your own account using the following credentials: `wiener:peter`

1. Login, use the Change password feature and send the `POST` change password request to repeater. Change the password and Send. It will give you a 200 OK but using the new password won't work because the `csrf` token is a one-time use.
	1. ![Password Reset-20260108100533123](Password%20Reset-20260108100533123.png)
	2. ![Password Reset-20260108100541996](Password%20Reset-20260108100541996.png)
2. Fill out the Change password fields > Intercept ON > Click Change password > set `username=administrator` > **remove `current-password`** > Forward.
	1. ![Password Reset-20260108101357796](Password%20Reset-20260108101357796.png)
	2. ![Password Reset-20260108101410172](Password%20Reset-20260108101410172.png)
3. To solve the lab, login as administrator with the new password `aaa` > Admin panel > delete carlos.

>[!tip] How to Identify this Vulnerability
>1. Change Password feature
>2. `current-password` field
>3. `csrf` token may/may not need to be present

- - -

#### 2.5. Lab: Exploiting time-sensitive vulnerabilities ⭕️
#Race-conditions
This lab contains a password reset mechanism. Although it doesn't contain a race condition, you can exploit the mechanism's broken cryptography by sending carefully timed requests.
To solve the lab:
1. Identify the vulnerability in the way the website generates password reset tokens.
2. Obtain a valid password reset token for the user `carlos`.
3. Log in as `carlos`.
4. Access the admin panel and delete the user `carlos`.
You can log into your account with the following credentials: `wiener:peter`.

5. Use the Forgot Password? feature > Email client > notice in the password reset URL there is a `user` param and a `token` param.
	1. ![Password Reset-20260108110614004](Password%20Reset-20260108110614004.png)
6. Send the `POST /forgot-password` request to Repeater + Send. Notice every time we send a new forgot password request, we get a new `token` value. It might be using a **timestamp** to generate this token. But we don't know what mechanisms it's using to generate this token.
	1. ![Password Reset-20260108110807248](Password%20Reset-20260108110807248.png)
7. Go to HTTP History > find the `GET /forgot-password` request > *remove the Cookie header* > Send > We will get a new session cookie and a new csrf token. 
	1. ![Password Reset-20260108112330068](Password%20Reset-20260108112330068.png)
	2. ![Password Reset-20260108112340410](Password%20Reset-20260108112340410.png)
8. #Useful Create a tab group in Repeater one with the original session cookie + csrf token and another one with the new values. Send the requests in parallel. Notice the response time is identical (*turns out doesn't need to match to exploit this vulnerability*).
	1. ![Password Reset-20260108112611498](Password%20Reset-20260108112611498.png)
	2. ![Password Reset-20260108112630168](Password%20Reset-20260108112630168.png)
	3. ![Password Reset-20260108112640184](Password%20Reset-20260108112640184.png)
9. Check Email client and we get 2 reset links with the same token and the same timestamp.
	1. ![Password Reset-20260108112802080](Password%20Reset-20260108112802080.png)
10. **If we change  the `username` param to `carlos` and so the token value for both usernames will be identical**. Copy the password reset link, paste it in the browser, change username to carlos, enter, and we can change carlos's password! Changed to `aaa`.
	1. ![469](Password%20Reset-20260108113648355.png)
11. Login as `carlos:aaa` > Admin panel > delete carlos and solved!

>[!tip] How to Identify this Vulnerability
>1. Forgot Password feature
>2. `user` and `token` param in password reset URL
>3. new `token` value every time we request a password reset link
>4. response time identical when sent in parallel?

- - -

#### 1.12. Lab: Password brute-force via password change ⭕️
#Authentication 
This lab's password change functionality makes it vulnerable to brute-force attacks. To solve the lab, use the list of candidate passwords to brute-force Carlos's account and access his "My account" page.
- Your credentials: `wiener:peter`
- Victim's username: `carlos`
- [Candidate passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords)

**NOT JUST FOR STAGE 1, CAN USE THIS TO BRUTE FORCE THE ADMIN'S PASSWORD**

1. Login and use the Change password feature. Notice a `username` param in the request body.
	1. ![Password Reset-20260119193842617](Password%20Reset-20260119193842617.png)
2. Testing the Change password feature
	1. When you enter the wrong current password, it logs you out and has account lockout.
	2. When you enter different values for New password (`new-password-1`) and Confirm new password (`new-password-2`) you get the error message "Current password is incorrect"
		1. ![Password Reset-20260119194402741](Password%20Reset-20260119194402741.png)
	3. When you enter a valid current password and different values for the new password params, you get the error message "New passwords do not match"
		1. ![Password Reset-20260119194559030](Password%20Reset-20260119194559030.png)
	4. So we need to use Intruder to enumerate the candidate password list and *grep extract* the `<p class=is-warning>` tag and *we'll find the victim's password when we see the error message "New passwords do not match"*
3. Brute force current password:
	1. Send `POST /my-account/change-password` to Intruder. Set `username=carlos` > set the value for `current-password` as a position > paste in the candidate password list.
		1. ![Password Reset-20260119195026211](Password%20Reset-20260119195026211.png)
	2. Setting > Grep Extract > highlight "New passwords do not match" > OK > Start attack.
		1. ![389](Password%20Reset-20260119194836822.png)
	3. Sort the `-warning>` column and see that `superman` is the correct password.
		1. ![Password Reset-20260119195128931](Password%20Reset-20260119195128931.png)
4. Login as `carlos:superman` to solve.
>[!tip] How to Identify this Vulnerability?
>1. Change password feature when authenticated
>2. `username` param in the change password `POST` request
>3. Difference in error messages with valid and invalid current password

---
