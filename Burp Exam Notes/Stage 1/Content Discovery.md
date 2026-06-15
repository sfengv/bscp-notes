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
	- [Content Discovery](Content%20Discovery.md#1.1%20Lab%20Information%20disclosure%20in%20error%20messages%20%E2%AD%95%EF%B8%8F)
- `/robots.txt`
	- [Content Discovery](Content%20Discovery.md#1.3.%20Lab%20Source%20code%20disclosure%20via%20backup%20files%20%E2%AD%95%EF%B8%8F)
- `/.git`
	- [Content Discovery](Content%20Discovery.md#1.5.%20Lab%20Information%20disclosure%20in%20version%20control%20history%20%E2%AD%95%EF%B8%8F)
- `/admin` == only available to local users > **`TRACE /admin` revealed `X-Custom-IP-Authorization: x.x.x.x` set it in the request to `GET /admin`**
	- [Content Discovery](Content%20Discovery.md#1.4.%20Lab%20Authentication%20bypass%20via%20information%20disclosure%20%E2%AD%95%EF%B8%8F)


- - - 

Gonna go thru the [Information Disclosure labs](https://portswigger.net/web-security/all-labs#information-disclosure). Kinda suggested by the study guide.
# Information Disclosure Labs
#### 1.1 Lab: Information disclosure in error messages ⭕️
This lab's verbose error messages reveal that it is using a vulnerable version of a third-party framework. To solve the lab, obtain and submit the version number of this framework.

1. Click on an item and change the productId from 1 to a
![575](Content%20Discovery-20251013212852816.png)

2. Solution
![390](Content%20Discovery-20251013212940147.png)
- - - 
#### 1.2. Lab: Information disclosure on debug page  ⭕️
This lab contains a debug page that discloses sensitive information about the application. To solve the lab, obtain and submit the `SECRET_KEY` environment variable.

1. View page source and found a commented out link
![Content Discovery-20251013213417068](Content%20Discovery-20251013213417068.png)

2. Navigate to it then search for secret_key
![Content Discovery-20251013213456051](Content%20Discovery-20251013213456051.png)
- - - 
#### 1.3. Lab: Source code disclosure via backup files ⭕️
This lab leaks its source code via backup files in a hidden directory. To solve the lab, identify and submit the database password, which is hard-coded in the leaked source code.

1. Check `/robots.txt` and grab the disallow endpoint
![498](Content%20Discovery-20251013213951293.png)

2. Click on the `.bak` file
![267](Content%20Discovery-20251013214027252.png)

3. Solution
![428](Content%20Discovery-20251013214045786.png)
- - -
#### 1.4. Lab: Authentication bypass via information disclosure ⭕️
This lab's administration interface has an authentication bypass vulnerability, but it is impractical to exploit without knowledge of a custom HTTP header used by the front-end.

To solve the lab, obtain the header name then use it to bypass the lab's authentication. Access the admin interface and delete the user `carlos`.

You can log in to your own account using the following credentials: `wiener:peter`

1. Go to `/admin` and it will give a hint so we need to send a custom request header as `localhost`
![Content Discovery-20251013215230335](Content%20Discovery-20251013215230335.png)

2. Proxy > Match and replace > Type: Request header > Replace: `X-Custom-IP-Authorization: 127.0.0.1`
	1. **Now every request we send will include that custom header**
![421](Content%20Discovery-20251013215310753.png)
![402](Content%20Discovery-20251013215143142.png)

3. Refresh the page then click Delete for carlos
![364](Content%20Discovery-20251013215056680.png)

> [!tip] Lesson Learned
> Using the HTTP match and replace rules (Request header) will help add headers to every request.
> 

- - - 
#### 1.5. Lab: Information disclosure in version control history ⭕️
This lab discloses sensitive information via its version control history. To solve the lab, obtain the password for the `administrator` user then log in and delete the user `carlos`.

1. Go to `/.git` 
![409](Content%20Discovery-20251013220542293.png)

2. Download all the contents of this page `wget -r https://<LAB-ID>.web-security-academy.net/.git`. But opening it in Finder or File Explorer won't show you anything, gotta use any Git software with a GUI like GitKraken to open it.
![417](Content%20Discovery-20251013221319771.png)

3. Set the repository to the downloaded folder and click on the "Remove admin password..." and click on `admin.conf`
![502](Content%20Discovery-20251013221539294.png)

Login with `administrator:getnt59mlk3m91r9f4hx` and delete carlos
![Content Discovery-20251013221605751](Content%20Discovery-20251013221605751.png)

> [!tip] Lesson Learned
> Navigate to `/.git` to see if we can check an app's Git control data.
> 

- - - 



