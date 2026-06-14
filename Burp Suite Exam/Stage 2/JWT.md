## Stage 1 + 2
### Useful Resources
- [JWT attacks cheat sheet](https://portswigger.net/web-security/jwt#brute-forcing-secret-keys-using-hashcat)
- [Wordlist of JWT secrets](https://github.com/wallarm/jwt-secrets/blob/master/jwt.secrets.list)

### Identify Vulnerabilities in Exam
- Target scan will reveal vulnerability
- JWT is smaller? `hashcat -a 0 -m 16500 <your-jwt> <wordlist>` and get a hit
	- [[JWT#2.3. Lab JWT authentication bypass via weak signing key ⭕️
- JWT Web Token extension
	- Set `"sub": "administrator"`
		- [[JWT#2.1. Lab JWT authentication bypass via unverified signature ⭕️]]
	- `alg: none`
		- [[JWT#2.2. Lab JWT authentication bypass via flawed signature verification ⭕️]]
	- [[JWT#2.4. Lab JWT authentication bypass via jwk header injection ⭕️]]
	- [[JWT#2.5. Lab JWT authentication bypass via jku header injection ⭕️]]
	- [[JWT#2.6. Lab JWT authentication bypass via kid header path traversal ⭕️]]

- - - 
#### 2.1. Lab: JWT authentication bypass via unverified signature ⭕️
This lab uses a JWT-based mechanism for handling sessions. Due to implementation flaws, the server doesn't verify the signature of any JWTs that it receives.
To solve the lab, modify your session token to gain access to the admin panel at `/admin`, then delete the user `carlos`.
You can log in to your own account using the following credentials: `wiener:peter`

1. Login as `wiener:peter` and notice a JWT in the session cookie. 
2. Go to `/admin` and it says only administrators can access it.
3. Send `GET /admin` to Repeater, use JWT Editor (JSON Web Token tab), change `wiener` to `administrator`, Send, get a 200 OK.
	1. ![[JWT-20260109150414763.png|596]]
4. The endpoint to delete `carlos` is: `/admin/delete?username=carlos`. Replace the current endpoint with this in Repeater. Send and solve.
	1. ![[JWT-20260109150640967.png|459]]
---

#### 2.2. Lab: JWT authentication bypass via flawed signature verification ⭕️
This lab uses a JWT-based mechanism for handling sessions. The server is insecurely configured to accept unsigned JWTs.

To solve the lab, modify your session token to gain access to the admin panel at `/admin`, then delete the user `carlos`.

You can log in to your own account using the following credentials: `wiener:peter`

1. Tried `GET /admin` as `wiener` will get you a 401 Unauthorized error.
	1. ![[JWT-20260109151005320.png|468]]
2. Go to JSON Web Token > change `"sub"` to `administrator` doesn't work like it did in Lab 0.1.
	1. ![[JWT-20260109151116496.png|364]]
3. In "Header" change that to `none` and remove the signature part of the JWT but leave the dot `.` 
	1. ![[JWT-20260109151351731.png]]
	2. ![[JWT-20260109151415318.png|360]]
	3. ![[JWT-20260109151441014.png|363]]
4. Repeat Step 4 in the previous lab and solved.
	1. ![[JWT-20260109151540732.png]]

- -- 

#### 2.3. Lab: JWT authentication bypass via weak signing key ⭕️
#Useful 
This lab uses a JWT-based mechanism for handling sessions. It uses an extremely weak secret key to both sign and verify tokens. This can be easily brute-forced using a [wordlist of common secrets](https://github.com/wallarm/jwt-secrets/blob/master/jwt.secrets.list).
To solve the lab, first brute-force the website's secret key. Once you've obtained this, use it to sign a modified session token that gives you access to the admin panel at `/admin`, then delete the user `carlos`.
You can log in to your own account using the following credentials: `wiener:peter`

1. Login, go straight to `/admin/delete?username=carlos` but got 401.
2. Brute force the `secret key`
	1. Download the wordlist [from here](https://github.com/wallarm/jwt-secrets/blob/master/jwt.secrets.list)
	2. `hashcat -a 0 -m 16500 <your-jwt> <wordlist>`
	3. the key is `secret1`
		1. ![[JWT-20260109154025756.png]]
		2. *Note that if you run the command more than once, you need to include the `--show` flag to output the results to the console again.*
3. Generate a forged signing key
	1. Decoder > encode `secret1` as Base64: `c2VjcmV0MQ==`
	2. JWT Editor > New Symmetric Key > Generate > replace `k` with Base64 encoded key > OK
		1. ![[JWT-20260109154308039.png]]
4. Modify and sign the JWT
	1. Repeater > JSON Web Token > change `sub` to `administrator`
	2. Click Sign > select the signing key we made > *select Don't modify header* > OK
		1. ![[JWT-20260109154435702.png]]
5. Send the request and got a 302, solved.

> `/Users/shixian/Documents/Hax/bscp` for `jwt.secrets.list`

>[!tip] How to Identify this Vulnerability?
>Notice the size of the JWT. It looks smaller than normal.
>![[JWT-20260109153302742.png]]

- - -

#### 2.4. Lab: JWT authentication bypass via jwk header injection ⭕️
This lab uses a JWT-based mechanism for handling sessions. The server supports the `jwk` parameter in the JWT header. This is sometimes used to embed the correct verification key directly in the token. However, it fails to check whether the provided key came from a trusted source.
To solve the lab, modify and sign a JWT that gives you access to the admin panel at `/admin`, then delete the user `carlos`.

You can log in to your own account using the following credentials: `wiener:peter`

1. Login, go to `/admin`, got 401 unauthorized. This is expected because `wiener` is not the `administrator` account.
	1. ![[JWT-20260109151941087.png]]
2. #Useful  JWT Editor > New RSA Key > Generate > OK. Repeater > JSON Web Token > change `"sub"` to `administrator`. Click Attack > Embedded JWK > select the RSA key > OK. Notice we have a new `jwk` param in the Header. Send and got 200 OK. **This confirms that the app failed to validate if the provided key came from a trusted source**. 
	1. ![[JWT-20260109152020065.png]]
	2. ![[JWT-20260109152538161.png]]
3. Navigate to `/admin/delete?username=carlos` and solved.
- - -


#### 2.5. Lab: JWT authentication bypass via jku header injection ⭕️
This lab uses a JWT-based mechanism for handling sessions. The server supports the `jku` parameter in the JWT header. However, it fails to check whether the provided URL belongs to a trusted domain before fetching the key.

To solve the lab, forge a JWT that gives you access to the admin panel at `/admin`, then delete the user `carlos`.

You can log in to your own account using the following credentials: `wiener:peter`

1. Login, go to `/admin/delete?username=carlos` again, 401, again.
2. Upload malicious JWT set
	1. JWT Editor > New RSA Key > Generate > OK > select key > right click > Copy Public Key as JWK 
		1. ![[JWT-20260109161005823.png]]
	2. Exploit server > paste `{ "keys": [] }` in the Body and then paste the Public Key in between the `[]`. Click **Store**.
		1. ![[JWT-20260109161209631.png]]
3. Modify and sign the JWT
	1. Replace `kid` with the `kid` that was pasted in the exploit server
		1. ![[JWT-20260109161413470.png]]
		2. ![[JWT-20260109161423470.png]]
	2. Create a `jku` parameter and set the value to the exploit server URL
		1. ![[JWT-20260109161546965.png]]
		2. ![[JWT-20260109161554839.png]]
	3. Change `sub` to `administrator`
	4. Click Sign > select the RSA key > *Don't modify header* > OK.
4. Send the request and solved.
>[!info] `jku` **(JSON Web Key Set URL)**
>Provides a URL from which servers can fetch keys containing the correct key.

>[!tip] How to Identify this Vulnerability?
>Burp scan manual insertion point JWT

- -- 

#### 2.6. Lab: JWT authentication bypass via kid header path traversal ⭕️
This lab uses a JWT-based mechanism for handling sessions. In order to verify the signature, the server uses the `kid` parameter in JWT header to fetch the relevant key from its filesystem.

To solve the lab, forge a JWT that gives you access to the admin panel at `/admin`, then delete the user `carlos`.

You can log in to your own account using the following credentials: `wiener:peter`

1. Login, set `/admin/delete?username=carlos`, Send, 401.
2. Generate a signing key
	1. JWT Editor > New Symmetric Key > Generate > replace `k` with `AA==` > OK.
		1. *`AA==` is a Base64 null byte because JWT Editor won't let you sign tokens with an empty string*.
		2. ![[JWT-20260109155932738.png]]
3. Modify and sign the JWT
	1. Repeater > JSON Web Token > in Header change `kid` to `../../../../../../../dev/null`
		1. ![[JWT-20260109160032780.png]]
	2. In Payload change `sub` to `administrator`
	3. Click Sign > select the key > *Don't modify header* > OK
4. Send request and solved.
>In this solution, we'll point the `kid` parameter to the standard file `/dev/null`. In practice, you can point the `kid` parameter to any file with predictable contents.

>[!info] `kid` (Key ID)
>Servers uses the `kid` parameter in JWT header to fetch the relevant key from its file system. It provides an ID that servers can use to identify the correct key in cases where there are multiple keys to choose from.

---
