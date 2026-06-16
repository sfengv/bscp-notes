### Stage 1 + 2
### Look for
- `/robots.txt`
- View page source for hidden directories or hidden fields
- Modifiable cookies like `Admin=false`
- Modifiable params in `POST` response like `roleid`
- IDOR via URL param like `/my-account?id=carlos` or `2.txt`
- Look for blog posts/comments/etc > Click on the user > Get info like GUID
- Observe for redirects and info in the response
- Try `X-Original-URL` in requests on simple responses like `"Access denied"`
- Change the request method when encounter any unauthorized responses
- Observe HTTP history for additional requests that might not have protections
- Try adding `Referer` header 

### Identify Vulnerabilities in Exam
- Comment in Page Source
	- `/admin-*******`
		- [[Access Control#2.2. Lab Unprotected admin functionality with unpredictable URL ⭕️]]
	- `/robots.txt`
		- `Disallow: /administrator-panel`
- "Admin interface only available if logged in as an administrator"
	- `Admin=false` cookie > **change to `true`**
		- [[Access Control#2.3. Lab User role controlled by request parameter ⭕️]]
	- `roleid` in `POST /my-account/change-email` > **if `0-10` doesn't work, Intruder it from `0-100`**
		- [[Access Control#2.4. Lab User role can be modified in user profile ⭕️]]
- Password field: `*****` > **view page source**
	- [[Access Control#2.8. Lab User ID controlled by request parameter with password disclosure ⭕️]]
- `/my-account?id={GUID}` > **Go to blog post and click on a user's name for their GUID**
	- [[Access Control#2.6. Lab User ID controlled by request parameter, with unpredictable user IDs ⭕️]]
- `/my-account?id={USERNAME}` 
	- [[Access Control#2.5. Lab User ID controlled by request parameter ⭕️]]
	- `?id=carlos` gets redirected to login > **navigate straight to `GET /my-account?id=carlos`**
		- [[Access Control#2.7. Lab User ID controlled by request parameter with data leakage in redirect ⭕️]]
- Live Chat + View transcript (`GET /download-transcript/2.txt`)
	- [[Access Control#2.9. Lab Insecure direct object references ⭕️]]
- Add `X-Original-URL: /invalid` to `GET /` == "Not Found" > **Set `X-Original-URL: /admin`**
	- [[Access Control#2.10. Lab URL-based access control can be circumvented ⭕️]]
- Open private window > login as wiener then run: `GET /admin-roles?username=wiener&action=upgrade`
	- 302 Found
		- [[Access Control#2.11. Lab Method-based access control can be circumvented ⭕️]]
	- 401 Unauthorized > **Set `Referer` to the `/admin` url path**
		- [[Access Control#2.13. Lab Referer-based access control ⭕️]]
- `confirmed=true` in `POST /admin-roles`
	- [[Access Control#2.12. Lab Multi-step process with no access control on one step ⭕️

---
#### 2.1. Lab: Unprotected admin functionality ⭕️
This lab has an unprotected admin panel.
Solve the lab by deleting the user `carlos`.
1. Go to `/robots.txt` > `/administrator-panel` > delete carlos to solve
---

#### 2.2. Lab: Unprotected admin functionality with unpredictable URL ⭕️
This lab has an unprotected admin panel. It's located at an unpredictable location, but the location is disclosed somewhere in the application.
Solve the lab by accessing the admin panel, and using it to delete the user `carlos`.
1. View page source and search for `admin` and notice an unusual endpoint `/admin-qaovcc`. Delete carlos and solve
![[Access Control-20260112141628853.png]]
---

#### 2.3. Lab: User role controlled by request parameter ⭕️
This lab has an admin panel at `/admin`, which identifies administrators using a forgeable cookie.
Solve the lab by accessing the admin panel and using it to delete the user `carlos`.
You can log in to your own account using the following credentials: `wiener:peter`
1. Login > go to `/admin` > notice that `GET /admin` has a cookie `Admin=false` > send to Repeater. 
	1. ![[Access Control-20260112142306611.png]]
2. Change that to `true` > right click > Request in browser: in original session > click delete carlos. Won't solve the lab. Grab the URL `/admin/delete?username=carlos`, paste it in the request in Repeater, Send and solve.
3. OR edit the `Admin` cookie in Cookie Editor.
	1. ![[Access Control-20260112142513663.png]]
---

#### 2.4. Lab: User role can be modified in user profile ⭕️
This lab has an admin panel at `/admin`. It's only accessible to logged-in users with a `roleid` of 2.
Solve the lab by accessing the admin panel and using it to delete the user `carlos`.
You can log in to your own account using the following credentials: `wiener:peter`
1. Login > change email > notice the `POST /my-account/change-email` request has a `roleId` param in the response. Send to Repeater.
	1. ![[Access Control-20260112142906228.png]]
2. Enter this in the request: `{"email":"aaa@aaa.com","roleid": 2}` > Send > now that our `roleId` is `2`. Refresh the page > Admin panel > delete carlos to solve.
	1. ![[Access Control-20260112142726300.png]]
---

#### 2.5. Lab: User ID controlled by request parameter ⭕️
This lab has a horizontal privilege escalation vulnerability on the user account page.
To solve the lab, obtain the API key for the user `carlos` and submit it as the solution.
You can log in to your own account using the following credentials: `wiener:peter`
1. Login as `wiener:peter` > make the change in the URL `/my-account?id=carlos` > and we have his API Key: `FzmOTugpUC6xqNQsJqmdqpB0Ty240Ont`. Submit that and solve.
	1. ![[Access Control-20260112143353567.png]]
---

#### 2.6. Lab: User ID controlled by request parameter, with unpredictable user IDs ⭕️
This lab has a horizontal privilege escalation vulnerability on the user account page, but identifies users with GUIDs.
To solve the lab, find the GUID for `carlos`, then submit his API key as the solution.
You can log in to your own account using the following credentials: `wiener:peter`
1. Login and notice the URL has an `id` param.
2. Click Home and find a blog post by carlos > click on his name > get his GUID
	1. ![[Access Control-20260112144301940.png|133]]
3. My account > replace the `id` value with carlos's: `1cee218d-8b19-4ee4-9e61-ad2608dd4523`. Submit his API Key and solve.
	1. ![[Access Control-20260112144421983.png]]
---

#### 2.7. Lab: User ID controlled by request parameter with data leakage in redirect ⭕️
This lab contains an access control vulnerability where sensitive information is leaked in the body of a redirect response.
To solve the lab, obtain the API key for the user `carlos` and submit it as the solution.
You can log in to your own account using the following credentials: `wiener:peter`
1. Login > change the `id` URL param to `carlos`: `/my-account?id=carlos`. Gets redirected to the Login page.
2. HTTP History > `GET /my-account?id=carlos`, find and submit the API Key (`HNgjeMQyCd3e6ioOIsPEgDVs6RsyrT7q`) to solve.
	1. ![[Access Control-20260112144736967.png]]
---

#### 2.8. Lab: User ID controlled by request parameter with password disclosure ⭕️
This lab has user account page that contains the current user's existing password, prefilled in a masked input.
To solve the lab, retrieve the administrator's password, then use it to delete the user `carlos`.
You can log in to your own account using the following credentials: `wiener:peter`
1. Login > right click on Password > Inspect and see the value of the password
	1. ![[Access Control-20260112145140083.png|453]]
2. In the URL > change `id` to `administrator` and repeat Step 1. Login as `administrator:8olvxquy41cdlu86b4op` > delete carlos and solve.
	1. ![[Access Control-20260112145215246.png|519]]
---

#### 2.9. Lab: Insecure direct object references ⭕️
This lab stores user chat logs directly on the server's file system, and retrieves them using static URLs.
Solve the lab by finding the password for the user `carlos`, and logging into their account.
1. Click Live Chat > Send some messages > Click View transcript. Notice `GET /download-transcript/2.txt` which indicates they are storing chat logs using integers.
	1. ![[Access Control-20260112145657473.png|172]]
	2. ![[Access Control-20260112145715333.png]]
2. Throw that request in Intruder and enumerate the integers. Payload type: Numbers > From: 0, To: 20, Step: 1. Only `1.txt` was valid and got the password. Login with `carlos:ccqb7g3we44icup13zxg` to solve.
	1. ![[Access Control-20260112145920380.png]]
---

#### 2.10. Lab: URL-based access control can be circumvented ⭕️
This website has an unauthenticated admin panel at `/admin`, but a front-end system has been configured to block external access to that path. However, the back-end application is built on a framework that supports the `X-Original-URL` header.
To solve the lab, access the admin panel and delete the user `carlos`.
1. Click Admin panel and notice the simple response `Access denied` suggesting it may originate from a *front-end system*.
	1. ![[Access Control-20260112150211729.png]]
2. Send the `/` to Repeater, add `X-Original-URL: /invalid` and Send. Got a 404 Not Found. This means the app is **processing the endpoint in `X-Original-URL`**.
	1. ![[Access Control-20260112150438621.png]]
3. Enter `X-Original-URL: /admin` and Send and got a 200 OK.
	1. ![[Access Control-20260112150647534.png]]
4. Configure the request like the screenshot to solve.
	1. ![[Access Control-20260112150908510.png]]
>[!tip] How to Identify this Vulnerability?
>1. Simple response messages like "Access Denied"
>2. App interprets `X-Original-URL` header

---

#### 2.11. Lab: Method-based access control can be circumvented ⭕️
This lab implements access controls based partly on the HTTP method of requests. You can familiarize yourself with the admin panel by logging in using the credentials `administrator:admin`.

To solve the lab, log in using the credentials `wiener:peter` and exploit the flawed access controls to promote yourself to become an administrator.
1. Login as administrator > Admin panel > upgrade carlos
	1. ![[Access Control-20260112151158250.png]]
2. Open a private window, login as `wiener:peter`, grab wiener's session cookie and use Cookie Editor to paste it in the main window, save, try to upgrade carlos to an admin. Got a 401 unauthorized.
	1. ![[Access Control-20260112152302276.png]]
3. #ExamTip  Right click > Change request method > change username to wiener > Send and solved. *We've successfully escalated our privileged from a normal user to an admin*!
	1. ![[Access Control-20260112152249202.png]]

>[!tip] How to Identify this Vulnerability?
>1. Any unauthorized message

---

#### 2.12. Lab: Multi-step process with no access control on one step ⭕️
This lab has an admin panel with a flawed multi-step process for changing a user's role. You can familiarize yourself with the admin panel by logging in using the credentials `administrator:admin`.

To solve the lab, log in using the credentials `wiener:peter` and exploit the flawed access controls to promote yourself to become an administrator.
1. Login as admin > Admin panel > upgrade carlos > Yes, I'm sure. Observe HTTP History on the second `POST /admin-roles` request to see an additional body param `confirmed=true`. Send to Repeater.
	1. ![[Access Control-20260112152719087.png]]
2. Open a private window > login as `wiener:peter` > grab the session cookie and paste it in the Repeater request. Send and solve.
	1. ![[Access Control-20260112152913558.png]]
>[!tip] How to Identify this Vulnerability?
>1. Additional requests in HTTP history with additional parameter(s)

---

#### 2.13. Lab: Referer-based access control ⭕️
This lab controls access to certain admin functionality based on the Referer header. You can familiarize yourself with the admin panel by logging in using the credentials `administrator:admin`.

To solve the lab, log in using the credentials `wiener:peter` and exploit the flawed access controls to promote yourself to become an administrator.
1. Login as admin > upgrade carlos and notice it's a `GET` request this time. Also notice the `Referer` header. Send to Repeater.
	1. ![[Access Control-20260112153213719.png]]
	2. ![[Access Control-20260112153248871.png]]
2. Open a private window > login as wiener > navigate to `/admin-roles?username=wiener&action=upgrade` directly and get a 401 error. This doesn't work due to the lack of the `Referer` header.
	1. ![[Access Control-20260112153549424.png]]
	2. ![[Access Control-20260112153659118.png]]
3. Grab session cookie and replace in the request, Send, and solve. I think as long as the *`Referer` points to `/admin` we can exploit this*.
>[!tip] How to Identify this Vulnerability?
>1. `GET` request where the action is an URL param?
>2. `Referer` header needed to exploit

**Request to solve the lab looks like this. Session cookie belongs to wiener. As long as the Referer comes from `/admin` then solved.**
![[Access Control-20260219112201177.png]]

















	