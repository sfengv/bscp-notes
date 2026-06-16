### What is it?
Web cache deception is a vulnerability that enables an attacker to trick a web cache into storing sensitive, dynamic content. It's caused by discrepancies between how the cache server and origin server handle requests.

In a web cache deception attack, an attacker persuades a victim to visit a malicious URL, inducing the victim's browser to make an ambiguous request for sensitive content. The cache misinterprets this as a request for a static resource and stores the response. The attacker can then request the same URL to access the cached response, gaining unauthorized access to private information.

### Deception vs Poisoning
- Web cache poisoning manipulates cache keys to inject malicious content into a cached response, which is then served to other users.
- Web cache deception exploits cache rules to trick the cache into storing sensitive or private content, which the attacker can then access.




---
>For Stages 1 + 2
#### 1/2.1. Lab: Exploiting path mapping for web cache deception ⭕️
To solve the lab, find the API key for the user `carlos`. You can log in to your own account using the following credentials: `wiener:peter`.

1. Login and notice there is no `X-Cache` header in `/` or `/my-account`.  Enter `/my-account/aaa` and still got a 200 OK.
	1. ![[Web cache deception-20260213105527149.png]]
2. Adding `.js` to `aaa` now reveal cache headers. **Putting `GET /aaa.js` will also reveal the headers**.
	1. ![[Web cache deception-20260213105617843.png]]
3. Send a couple times and now see `X-Cache: hit`. **This confirms that `/aaa.js` has a cache rule based on the `.js` static extension**.
	1. ![[Web cache deception-20260213105755335.png]]
4. Exploit server > replace `YOUR-LAB-ID` and `aaa.js` to something different like `bbb.js`. Deliver exploit to victim. *When the victim views the exploit, the response they receive is stored in the cache*.
	1. ![[Web cache deception-20260213110036464.png]]
```
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account/bbb.js"</script>
```

5. Go to the URL in the payload which is `https://0adb002603592c3c82e1e783006b00b3.web-security-academy.net/my-account/bbb.js` and we got carlos's info. Submit his API key and solve.
	1. ![[Web cache deception-20260213110118653.png]]



How to ID:
- auth blog > every field > change email > Your API Key is: ... > no X-Cache header in `/` or `/my-account` > 


---

#### 1/2.2. Lab: Exploiting path delimiters for web cache deception ⭕️
To solve the lab, find the API key for the user `carlos`. You can log in to your own account using the following credentials: `wiener:peter`.
We have provided a list of possible delimiter characters to help you solve the lab: [Web cache deception lab delimiter list](https://portswigger.net/web-security/web-cache-deception/wcd-lab-delimiter-list).

1. Login > notice the following: 
	1. no cache headers
	2. server is `Apache-Coyote/1.1`
	3. API key in `/my-account`
2. Add `aaa` and got a 404 Not Found. But `/aaa.js` does reveal cache headers. 
	1. ![[Web cache deception-20260213123737632.png]]
	2. ![[Web cache deception-20260213123819975.png]]
3. Send `GET /my-account/aaa` to Intruder > Sniper attack > add position to `/` > Simple list > go to the portswigger delimiter list and paste it in > **Deselect: URL-encode these characters**. Start attack. Notice only `;` and `?` return 200 OKs. **This indicates that the origin server uses `;` and `?` as path delimiters.**
	1. ![[Web cache deception-20260213124124037.png|277]]
	2. ![[Web cache deception-20260213124249341.png|434]]
4. Repeater > replace `/` with either `;` or `?` and got 200 OKs. BUT only `;` returned cache headers. **This indicates that the cache doesn't use `;` as a path delimiter and has a cache rule based on the `.js` static extension.**
	1. ![[Web cache deception-20260213124512076.png]]
5. Exploit server > replace `YOUR-LAB-ID` and to `bbb.js` > deliver exploit to victim > go to the URL > submit carlos's API key to solve.
	1. URL: `https://0a6500da04b4d3ad81dceda200fa00f7.web-security-academy.net/my-account;bbb.js`
	2. ![[Web cache deception-20260213124708128.png]]
	3. ![[Web cache deception-20260213124729410.png]]
```
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account;bbb.js"</script>
```


How to ID:
- auth blog > every field > change email > Your API Key is: ... > no X-Cache header in `/` or `/my-account` > Server: Apache-Coyote/1.1


---

#### 1/2.3. Lab: Exploiting origin server normalization for web cache deception ⭕️
To solve the lab, find the API key for the user `carlos`. You can log in to your own account using the following credentials: `wiener:peter`.
We have provided a list of possible delimiter characters to help you solve the lab: [Web cache deception lab delimiter list](https://portswigger.net/web-security/web-cache-deception/wcd-lab-delimiter-list).

1. Login > notice the following: 
	1. no cache headers
	2. API key in `/my-account`
2. `GET /my-account` to Repeater > add `aaa` and got a 404 Not Found. Even `aaa.js` doesn't reveal cache headers.
	1. ![[Web cache deception-20260213125342437.png]]
3. Follow steps in the previous lab for Intruder set up. Only `?` got a 200 OK. **This indicates that the origin server only uses `?` as a path delimiter.**
	1. ![[Web cache deception-20260213125523673.png|408]]
4. Investigate normalization discrepancies:
	1. Repeater > `GET /aaa/..%2fmy-account` > 200 OK. **This indicates that the origin server decodes and resolves the dot-segment, interpreting the URL path as `/my-account`.**
		1. ![[Web cache deception-20260213125827138.png]]
	2. Target > site map > notice only `/resources` have cache headers. Send one to Repeater.
		1. ![[Web cache deception-20260213125954796.png|283]]
	3. Add `..%2f` after `resources/` and got a 404. Send it a couple times and notice `X-Cache: hit`.
		1. `GET /resources/..%2fimages/avatarDefault.svg`
		2. ![[Web cache deception-20260213130158388.png]]
	4. Change to `GET /resources/aaa` and send a couple times to also get a `X-Cache: hit`. **This confirms that there is a static directory cache rule based on the `/resources` prefix.**
		1. ![[Web cache deception-20260213130307526.png]]
5. Go to `GET /resources/..%2fmy-account` and got 200 OK. *If get 302 Found > grab a fresh `/my-account` request.* Send a couple times til `X-Cache: hit`. 
	1. ![[Web cache deception-20260213130739046.png]]
6. Exploit server > replace lab id and change to bbb > deliver exploit to victim. Go to the URL then submit carlos's api key to solve.
	1. ![[Web cache deception-20260213130942023.png]]
```
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/resources/..%2fmy-account?bbb"</script>
```


How to ID:
- auth blog > every field > change email > Your API Key is: ... > no X-Cache header in `/` or `/my-account` > `.js` doesn't reveal cache headers
- Only `/resources` have cache headers


---

#### 1/2.4. Lab: Exploiting cache server normalization for web cache deception ⭕️
To solve the lab, find the API key for the user `carlos`. You can log in to your own account using the following credentials: `wiener:peter`.
We have provided a list of possible delimiter characters to help you solve the lab: [Web cache deception lab delimiter list](https://portswigger.net/web-security/web-cache-deception/wcd-lab-delimiter-list).

1. Login > notice the following: 
	1. no cache headers
	2. API key in `/my-account`
2. `GET /my-account` to Repeater > add `aaa` and got a 404 Not Found. Even `aaa.js` doesn't reveal cache headers.
	1. ![[Web cache deception-20260213125342437.png]]
3. Follow steps in the previous lab for Intruder set up. Notice `# ? %23 %3F` got 200 OKs. Ignore `#`. 
	1. ![[Web cache deception-20260213131240838.png]]
4. Investigate path delimiter discrepancies:
	1. Send `/my-account?aaa.js` notice we don't see any cache headers. `%23` `%3f` doesn't either. **This either indicates that the cache also uses `?` as a path delimiter, or that the cache doesn't have a rule based on the `.js` extension.**
		1. ![[Web cache deception-20260213131445709.png]]
5. Investigate normalization discrepancies:
	1. `GET /aaa/..%2fmy-account` got 404. **This indicates that the origin server doesn't decode or resolve the dot-segment to normalize the path to `/my-account`.**
		1. ![[Web cache deception-20260213131614235.png]]
	2. Target > site map > notice only `/resources` have cache headers. Send one to Repeater.
		1. ![[Web cache deception-20260213125954796.png|283]]
	3. `GET /resources/..%2fimages/avatarDefault.svg` DOES NOT show cache headers. But `GET /aaa/..%2fresources/avatarDefault.svg` does. Send a couple times til `X-Cache: hit`. **This may indicate that the cache decodes and resolves the dot-segment and has a cache rule based on the `/resources` prefix.**
		1. ![[Web cache deception-20260213131940419.png]]
6. Get a refresh `GET /my-account` request to Repeater. Enter `/my-account?%2f%2e%2e%2fresources` and got 200 OK but no cache headers. Replace `?` with `%3f`: still no cache headers. But `%23` do reveal cache headers. Send a couple times to get a `X-Cache: hit`.
	1. ![[Web cache deception-20260213132556837.png]]
7. Exploit server > replace lab id > deliver exploit to victim > go to URL > submit carlos's api key to solve.
	1. URL: `https://0ae7004d049e0ab9813a2ffe00a00079.web-security-academy.net/my-account%23%2f%2e%2e%2fresources?bbb`
	2. ![[Web cache deception-20260213132806355.png]]

```
<script>document.location="https://YOUR-LAB-ID.web-security-academy.net/my-account%23%2f%2e%2e%2fresources?bbb"</script>
```

How to ID:
- auth blog > every field > change email > Your API Key is: ... > no X-Cache header in `/` or `/my-account` > `.js` doesn't reveal cache headers
- Only `/resources` have cache headers



