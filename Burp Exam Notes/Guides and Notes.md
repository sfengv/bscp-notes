### Guides
- https://github.com/DCKento/Burp-Suite-Certified-Practitioner-Notes
- https://github.com/DingyShark/BurpSuiteCertifiedPractitioner
- https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study
- https://bscp.guide/

### Burp Practice Exam
> In the [Burp Practice exam stage 2](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study?tab=readme-ov-file#practice-exam-postgresql-timedelay) the SQL injection is escaped not using single quote `'` but using a semicolon `;` and then URL encoding it as `%3B`.
>Stage 3 of the Burp Practice exam admin portal require exploitation of an [insecure deserialization](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study?tab=readme-ov-file#ysoserial) cookie value.

### Must Do's
1. Find hidden directories: use `dirsearch`, `gobuster`, Burp's crawl feature, etc. to discover hidden directories
2. Use the Burp browser and turn on DOM Invader, messages, and prototype pollution.
3. Filter settings: enable *images* for path traversal
4. FoxyProxy: don't use patterns, proxy everything
5. Fuzzing
	1. Use directory enum tools like `ffuf` and [wordlists from github](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study?tab=readme-ov-file#content-discovery) or use Burp crawl / Discover content
	2. Change param data types e.g., `productId=1` > productId=**a**
	3. Page source comments
	4. `/robots.txt` + `/sitemap.xml` + `/.git` + `/cgi-bin/phpinfo.php`
6. Searching for keywords: Target > right click on domain > Engagement tools > Search 


First thing to do for Stage 1 to get an user account:
- [Authentication](Authentication.md#1.7.%20Lab%20Username%20enumeration%20via%20account%20lock%20%E2%AD%95%EF%B8%8F)

### For Deliver exploit to victim payloads
If fetch or document.location doesn't work, try this simpler one:
```
<script>
location="https://TARGET.NET/#__proto__[hitCallback]=alert%28document.cookie%29"
</script>  
```



