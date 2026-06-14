Tip : wkhtmltopdf is a command line tool that renders HTML into PDF and various image formats. Older versions are vulnerable to an SSRF vulnerability that allows local file inclusion. [https://www.virtuesecurity.com/kb/wkhtmltopdf-file-inclusion-vulnerability-2/](https://www.virtuesecurity.com/kb/wkhtmltopdf-file-inclusion-vulnerability-2/)

To exploit it, We simply inject our malicious code into the data intended to be rendered by wkhtmltopdf so when the tool renders the data our code is executed. This command renders all the available files on the localhost machine.

`<iframe src='http://localhost:6566/' height='500' width='500'>`

From there we can pick which specific file we want to view.


