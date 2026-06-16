
### What is it?
GraphQL is an API query language that is designed to facilitate efficient communication between clients and servers. It enables the user to specify exactly what data they want in the response, helping to avoid the large response objects and multiple calls that can sometimes be seen with REST APIs.
Commonly used in authentication and data retrieval mechanisms. 
Could execute higher-severity exploits such as cross-site request forgery (CSRF).

### Useful resources
- https://nathanrandal.com/graphql-visualizer/
- InQL Scanner extension
- GraphQL Voyager == visualizer

### Look for
- Variables like `id` and any missing values like blogs 1-5 but 3 is missing
- A mutation in the `POST` request
- Send `/api` to get `Query not present` response

### Identify Vulnerabilities in Exam
Use InQL - GraphQL scanner

- GraphQL Visualizer
	- hidden parameter 
		- [[GraphQL Endpoints#2.1. Lab Accessing private GraphQL posts ⭕️]]
- `/api`
	- 400 Bad Request: "Query not present"
		- [[GraphQL Endpoints#2.3. Lab Finding a hidden GraphQL endpoint ⭕️]]
- "mutation"
	- [[GraphQL Endpoints#2.2 Lab Accidental exposure of private GraphQL fields ⭕️]]
	- "You have made too many incorrect login attempts..."
		- [[GraphQL Endpoints#2.4. Lab Bypassing GraphQL brute force protections ⭕️]]
	- [[GraphQL Endpoints#2.5. Lab Performing CSRF exploits over GraphQL ⭕️



---
#### 2.1. Lab: Accessing private GraphQL posts ⭕️
The blog page for this lab contains a hidden blog post that has a secret password. To solve the lab, find the hidden blog post and enter the password.
Learn more about [Working with GraphQL in Burp Suite](https://portswigger.net/burp/documentation/desktop/testing-workflow/working-with-graphql).
1. Click on each blog post, observe the `id` parameter on each `POST /graphql/v1` request and notice the `id`s are sequential integers from 1-5 but *3 is missing*. Send to Repeater.
2. Right click > GraphQL > Set introspection query > Send > copy the response and paste it in the [GraphQL Visualizer](https://nathanrandal.com/graphql-visualizer/) (remove the headers) and see that the BlogPost type has a `postPassword` field
	1. ![[GraphQL Endpoints-20260112161751361.png|371]]
3. Repeater > GraphQL tab > add `postPassword` in Query and change `id` to 3 in Variables > Send and got the password. Submit `7fxo342mevjkqkmv7w5b16xtq1fubzs3` to solve.
	1. ![[GraphQL Endpoints-20260112162053871.png]]

>[!tip] How to Identify this Vulnerability?
>1. `POST /graphql/v1` requests
>2. `id` are integers but one `id` is missing

---

#### 2.2 Lab: Accidental exposure of private GraphQL fields ⭕️
The user management functions for this lab are powered by a GraphQL endpoint. The lab contains an access control vulnerability whereby you can induce the API to reveal user credential fields.
To solve the lab, sign in as the administrator and delete the username `carlos`.
1. Try to login with arbitrary credentials like `aaa:aaa` and notice the `POST /graphql/v1` request with a *mutation* containing username + password. Send to Repeater.
	1. ![[GraphQL Endpoints-20260112220901396.png]]
>[!info] GraphQL Mutation
>A type of operation used to **modify data** on the server. Unlike queries, which are for fetching data, mutations allow clients to create, update, or delete records in the database, similar to using `POST`, `PUT`, `PATCH`, or `DELETE` methods in a REST API.
##### Manual Method
1. Right click > GraphQL > set introspection query > Send. 
2. Right click on the response > GraphQL > Save GraphQL queries to site map > click on your lab id > inspect the `POST /graphql/v1` requests and find `getUser` query. It takes in an `id` and returns the user's `username` + `password`. 
	1. ![[GraphQL Endpoints-20260112221344578.png|303]]
##### Automated Method
1. Right click > Extensions > InQL - GraphQL Scanner > Generate 
	1. ![[GraphQL Endpoints-20260112222237529.png]]
 2. InQL tab > Scanner > Queries > getUser and notice the `getUser` query.
	 1. ![[GraphQL Endpoints-20260112222331795.png]]
 3. Send to Repeater > Send. `"id":0` doesn't return any user credentials. Enter `"id":1` will get us the administrator's credentials. Login > delete carlos to solve.
	1. ![[GraphQL Endpoints-20260112221726410.png]]
>[!tip] How to Identify this Vulnerability?
>1. A GraphQL mutation in the `POST` request
>2. Query that return user credentials in Site Map or use InQL scanner

---

#### 2.3. Lab: Finding a hidden GraphQL endpoint ⭕️
The user management functions for this lab are powered by a hidden GraphQL endpoint. You won't be able to find this endpoint by simply clicking pages in the site. The endpoint also has some defenses against introspection.
To solve the lab, find the hidden endpoint and delete `carlos`.
1. Click around on the app and login as `wiener:peter` and use the update email feature but *no graphQL requests*.
2. Send `/` to Repeater > Send `/api` and notice the response says "Query not present". Where normally it would give a 404 Not Found.
	1. ![[GraphQL Endpoints-20260112222655356.png]]
3. Send a *universal query* (`/api?query=query{__typename}`) in the URL due to this being a `GET` request. This **confirms that this is a GraphQL endpoint**.
	1. ![[GraphQL Endpoints-20260112222922676.png]]
4. Right click > GraphQL > Set introspection query > Send. Note the `__schema` in the message.
	1. ![[GraphQL Endpoints-20260112223124611.png]]
5. To bypass introspection protection: add a newline `%0a` after `___schema`. Got a 200 OK!
	1. ![[GraphQL Endpoints-20260112223314630.png]]
##### Manual Method
1. Right click on the response > GraphQL > Save GraphQL queries to site map > click on your lab id > inspect the `POST /graphql/v1` requests and find `getUser` query. Send to Repeater.
	1. ![[GraphQL Endpoints-20260112223549539.png|259]]
2. Send and `"id":0` doesn't have any user credentials. GraphQL tab in Request > change `id` to 3 > Send. Now we confirmed that carlos's user id is 3.
	1. ![[GraphQL Endpoints-20260112223828346.png]]
3. Back to Target > Site Map and send the "DeleteOrganizationUserInput" query to Repeater. GraphQL tab > change id to 3 and send to solve.
	1. ![[GraphQL Endpoints-20260112223938163.png]]
##### Automated Method
Follow this: https://github.com/botesjuan/Burp-Suite-Certified-Practitioner-Exam-Study?tab=readme-ov-file#identify-graphql-api
>[!tip] How to Identify this Vulnerability?
>1. `GET` request
>2. Add `/api` and get `Query not present`
>3. Provide universal query and got a corresponding response
>4. Introspection not allowed

---

#### 2.4. Lab: Bypassing GraphQL brute force protections ⭕️
The user login mechanism for this lab is powered by a GraphQL API. The API endpoint has a rate limiter that returns an error if it receives too many requests from the same origin in a short space of time.
To solve the lab, brute force the login mechanism to sign in as `carlos`. Use the list of [authentication lab passwords](https://portswigger.net/web-security/authentication/auth-lab-passwords) as your password source.
1. Login with arbitrary credentials `aaa:aaa` until you get a response length longer than usual (e.g., 592 vs 228). *The app has rate limiting for too many invalid login attempts*. Send to Repeater.
	1. ![[GraphQL Endpoints-20260113103501975.png]]
2. Generate a password list 
	1. Paste the following code into Console (might have to type `allow pasting` first) and the payload we need will be in our clipboard.
	2. This takes in a list of potential passwords and pass it in `$password` to guess carlos's password.
```
copy(`123456,password,12345678,qwerty,123456789,12345,1234,111111,1234567,dragon,123123,baseball,abc123,football,monkey,letmein,shadow,master,666666,qwertyuiop,123321,mustang,1234567890,michael,654321,superman,1qaz2wsx,7777777,121212,000000,qazwsx,123qwe,killer,trustno1,jordan,jennifer,zxcvbnm,asdfgh,hunter,buster,soccer,harley,batman,andrew,tigger,sunshine,iloveyou,2000,charlie,robert,thomas,hockey,ranger,daniel,starwars,klaster,112233,george,computer,michelle,jessica,pepper,1111,zxcvbn,555555,11111111,131313,freedom,777777,pass,maggie,159753,aaaaaa,ginger,princess,joshua,cheese,amanda,summer,love,ashley,nicole,chelsea,biteme,matthew,access,yankees,987654321,dallas,austin,thunder,taylor,matrix,mobilemail,mom,monitor,monitoring,montana,moon,moscow`.split(',').map((element,index)=>`
bruteforce$index:login(input:{password: "$password", username: "carlos"}) {
        token
        success
    }
`.replaceAll('$index',index).replaceAll('$password',element)).join('\n'));console.log("The query has been copied to your clipboard.");
```
3. Repeater > GraphQL > replace the existing code inside `mutation login {...}`
	1. ![[GraphQL Endpoints-20260113105054171.png]]
4. Scroll til we find `bruteforce14` and the password is `monkey`. Login as carlos and solve.
	1. ![[GraphQL Endpoints-20260113105147313.png]]
>[!tip] How to Identify this Vulnerability?
>1. `POST` GraphQL mutation request
>2. Rate limiting 

---

#### 2.5. Lab: Performing CSRF exploits over GraphQL ⭕️
The user management functions for this lab are powered by a GraphQL endpoint. The endpoint accepts requests with a content-type of `x-www-form-urlencoded` and is therefore vulnerable to cross-site request forgery (CSRF) attacks.
To solve the lab, craft some HTML that uses a CSRF attack to change the viewer's email address, then upload it to your exploit server.

You can log in to your own account using the following credentials: `wiener:peter`.
1. Login as wiener, change email, and notice a `POST /graphql/v1` request with a *mutation.* Send to Repeater. 
2. Convert the `POST` request with a content type of `x-www-form-urlencoded`:
	1. Right click > Change request method *twice*
	2. Request body is empty now, need to add it back in (see below)
3. Duplicate the tab > GraphQL tab > copy the contents under both boxes and paste it in the previous tab in this format: `query=PASTE-HERE&variable=PASTE-HERE`
	1. ![[GraphQL Endpoints-20260113111648688.png]]
4. Change the email. It should look like this
	1. ![[GraphQL Endpoints-20260113111901936.png]]
5. URL encode key characters EXCEPT `query=` and `&variables=`
	1. ![[GraphQL Endpoints-20260113111959643.png]]
6. Craft a CSRF payload:
	1. Change the email. 
	2. Right click > Engagement Tools > Generate CSRF PoC > Test in browser *didn't work for me so had to URL encode all characters then it worked*
	3. Before
		1. ![[GraphQL Endpoints-20260113115246350.png]]
	4. After
		1. ![[GraphQL Endpoints-20260113115324873.png]]
		2. ![[GraphQL Endpoints-20260113115334363.png]]
7. Change the email > Copy HTML > paste in exploit server > store > deliver exploit to victim and solve.

>`POST` requests with `application/json` as the content type are secure against CSRF (as long as the content type is validated) but `GET` requests with `x-www-form-urlencoded` are vulnerable.

>[!tip] How to Identify this Vulnerability?
>1. `POST` GraphQL mutation request after performing an action e.g., change email




