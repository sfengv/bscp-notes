## Look for
- Hidden paths and pages
	- Use FFUF (dirbuster alternative?) or Burp's content discovery option
- Page source
- `robots.txt` and `sitemap.xml`
### FFUF
```
wget https://raw.githubusercontent.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/main/wordlists/burp-labs-wordlist.txt

ffuf -c -w ./burp-labs-wordlist.txt -u https://LAB-ID.web-security-academy.net/FUZZ
```


### Identify Vulnerabilities in Exam
- Change integer to char like `?productId=a` to reveal info like 3rd party library version
	- [[Content Discovery#1.1 Lab Information disclosure in error messages ⭕️]]
- `/robots.txt`
	- [[Content Discovery#1.3. Lab Source code disclosure via backup files ⭕️]]
- `/.git`
	- [[Content Discovery#1.5. Lab Information disclosure in version control history ⭕️]]
- `/admin` == only available to local users > **`TRACE /admin` revealed `X-Custom-IP-Authorization: x.x.x.x` set it in the request to `GET /admin`**
	- [[Content Discovery#1.4. Lab Authentication bypass via information disclosure ⭕️]]


- - - 

Gonna go thru the [Information Disclosure labs](https://portswigger.net/web-security/all-labs#information-disclosure). Kinda suggested by the study guide.
# Information Disclosure Labs
#### 1.1 Lab: Information disclosure in error messages ⭕️
This lab's verbose error messages reveal that it is using a vulnerable version of a third-party framework. To solve the lab, obtain and submit the version number of this framework.

1. Click on an item and change the productId from 1 to a
![[Content Discovery-20251013212852816.png|575]]

2. Solution
![[Content Discovery-20251013212940147.png|390]]
- - - 
#### 1.2. Lab: Information disclosure on debug page  ⭕️
This lab contains a debug page that discloses sensitive information about the application. To solve the lab, obtain and submit the `SECRET_KEY` environment variable.

1. View page source and found a commented out link
![[Content Discovery-20251013213417068.png]]

2. Navigate to it then search for secret_key
![[Content Discovery-20251013213456051.png]]
- - - 
#### 1.3. Lab: Source code disclosure via backup files ⭕️
This lab leaks its source code via backup files in a hidden directory. To solve the lab, identify and submit the database password, which is hard-coded in the leaked source code.

1. Check `/robots.txt` and grab the disallow endpoint
![[Content Discovery-20251013213951293.png|498]]

2. Click on the `.bak` file
![[Content Discovery-20251013214027252.png|267]]

3. Solution
![[Content Discovery-20251013214045786.png|428]]
- - -
#### 1.4. Lab: Authentication bypass via information disclosure ⭕️
This lab's administration interface has an authentication bypass vulnerability, but it is impractical to exploit without knowledge of a custom HTTP header used by the front-end.

To solve the lab, obtain the header name then use it to bypass the lab's authentication. Access the admin interface and delete the user `carlos`.

You can log in to your own account using the following credentials: `wiener:peter`

1. Go to `/admin` and it will give a hint so we need to send a custom request header as `localhost`
![[Content Discovery-20251013215230335.png]]

2. Proxy > Match and replace > Type: Request header > Replace: `X-Custom-IP-Authorization: 127.0.0.1`
	1. **Now every request we send will include that custom header**
![[Content Discovery-20251013215310753.png|421]]
![[Content Discovery-20251013215143142.png|402]]

3. Refresh the page then click Delete for carlos
![[Content Discovery-20251013215056680.png|364]]

> [!tip] Lesson Learned
> Using the HTTP match and replace rules (Request header) will help add headers to every request.
> 

- - - 
#### 1.5. Lab: Information disclosure in version control history ⭕️
This lab discloses sensitive information via its version control history. To solve the lab, obtain the password for the `administrator` user then log in and delete the user `carlos`.

1. Go to `/.git` 
![[Content Discovery-20251013220542293.png|409]]

2. Download all the contents of this page `wget -r https://<LAB-ID>.web-security-academy.net/.git`. But opening it in Finder or File Explorer won't show you anything, gotta use any Git software with a GUI like GitKraken to open it.
![[Content Discovery-20251013221319771.png|417]]

3. Set the repository to the downloaded folder and click on the "Remove admin password..." and click on `admin.conf`
![[Content Discovery-20251013221539294.png|502]]

Login with `administrator:getnt59mlk3m91r9f4hx` and delete carlos
![[Content Discovery-20251013221605751.png]]

> [!tip] Lesson Learned
> Navigate to `/.git` to see if we can check an app's Git control data.
> 

- - - 



