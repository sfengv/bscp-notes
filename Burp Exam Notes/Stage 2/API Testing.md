
### Identify Vulnerabilities in Exam
- `/api` or see `PATCH`
	- [[API Testing#2.1. Lab Exploiting an API endpoint using documentation ⭕️]]
- `forgot-password?reset_token=${resetToken}` or in Forgot Password try `username=administrator%23` == "Field not specified"
	- [[API Testing#2.2. Lab Exploiting server-side parameter pollution in a query string ⭕️]]
- `PATCH` in a my-account url path to get "Only application/json Content-Type is supported" > **Goal is to add something like "Admin":true** 
	- [[API Testing#2.3. Lab Finding and exploiting an unused API endpoint ⭕️]]
- Check for hidden parameters fields in `POST` + `GET` requests
	- [[API Testing#2.4. Lab Exploiting a mass assignment vulnerability ⭕️]]


---
#### 2.1. Lab: Exploiting an API endpoint using documentation ⭕️
To solve the lab, find the exposed API documentation and delete `carlos`. You can log in to your own account using the following credentials: `wiener:peter`.
1. Login > Update email > observe a `PATCH` request > send to Repeater. Modify the endpoint to `/api/user` and see an error.
	1. ![[API Testing-20260112093112799.png]]
2. Modify the endpoint to `/api` and got a 302. Right click > Open response in browser and see that API info is shown.
	1. ![[API Testing-20260112093238420.png|345]]
3. Click on the `DELETE` one > type `carlos` > click Send Request to solve the lab OR send this request `DELETE /api/user/carlos`
	1. ![[API Testing-20260112093435195.png|401]]
>[!tip] How to Identify this Vulnerability?
>1. `PATCH` request after performing a `POST` action e.g., changing email
>2. API info disclosed when manipulating the endpoint e.g., `/api`

---

#### 2.2. Lab: Exploiting server-side parameter pollution in a query string ⭕️
To solve the lab, log in as the `administrator` and delete `carlos`.
1. Click Forgot password? and enter `administrator`. Send `POST /forgot-password` to Repeater and Send.
	1. ![[API Testing-20260112093839517.png]]
2. Make the username invalid like append `X` to`username` like `administratorX`. Notice an "Invalid username" error.
	1. ![[API Testing-20260112093951817.png]]
3. Append a second parameter-value pair *need to URL encode the `&` character*. So `&x=y` becomes `%26x=y` and got a "Parameter is not supported" error. **This suggests that the  API may have interpreted `&x=y` as a separate parameter, instead of part of the username**.
	1. ![[API Testing-20260112094100701.png]]
4. Enter URL encoded of `#` so `#23`. "Field not specified" **suggests that the server-side query may include an additional parameter called `field`, which has been removed by the `#` character**.
	1. ![[API Testing-20260112094310955.png]]
>[!tip] If `%23` doesn't work
>Use Intruder with the delimiters.txt file to get the "Field not specified" error.
5. Enter `%26field=x%23` (`&field=x#`) and got "Invalid field" error. **This suggests that the server-side application may recognize the injected field parameter**.
	1. ![[API Testing-20260112094551585.png]]
6. Brute force the `field` parameter:
	1. Send to Intruder > add position to `x` > Add from list: **Server-side variable names**
		1. ![[API Testing-20260112094700594.png]]
		2. ![[API Testing-20260112094758056.png]]
	2. Notice `username` and `email` got 200 OK
		1. ![[API Testing-20260112094920466.png|268]]
7. Repeater > replace `x#` to `email` like `email%23` and notice the response is normal now. **This suggests that `email` is a valid field type**.
	1. ![[API Testing-20260112095113153.png]]
8. Look in `forgotPassword.js` and look for `reset_token`. **This is the part we're looking for `/forgot-password?reset_token=${resetToken}`.**
```
forgotPwdReady(() => {
    const queryString = window.location.search;
    const urlParams = new URLSearchParams(queryString);
    const resetToken = urlParams.get('reset-token');
    if (resetToken)
    {
        window.location.href = `/forgot-password?reset_token=${resetToken}`;
    }
    else
    {
        const forgotPasswordBtn = document.getElementById("forgot-password-btn");
        forgotPasswordBtn.addEventListener("click", displayMsg);
    }
}
```
9. Modify the request to `username=administrator%26field=reset_token%23` and got a reset token back.
	1. ![[API Testing-20260112095420379.png]]
10. In the browser, enter this `/forgot-password?reset_token=123` but enter the reset token you got instead of 123. Reset the password to something like `aaa` > login as `administrastor:aaa` > Admin panel > delete carlos and solved.
	1. ![[API Testing-20260112095759093.png|224]]
>[!tip] How to Identify this Vulnerability?
>1. `Forgot password?` feature
>2. `forgotPassword.js` `GET` request in HTTP History
>3. "Parameter is not supported" error
>4. "Field not specified" error

---

#### 2.3. Lab: Finding and exploiting an unused API endpoint ⭕️
To solve the lab, exploit a hidden API endpoint to buy a **Lightweight l33t Leather Jacket**. You can log in to your own account using the following credentials: `wiener:peter`.
1. Login > click on view details on the leather jacket > send `GET /api/products/1/price` to Repeater > put `OPTIONS` and notice that `PATCH` is allowed.
	1. ![[API Testing-20260112104209554.png]]
2. Enter `PATCH` and the error says only `application/json` Content-Type is allowed.
	1. ![[API Testing-20260112104424546.png]]
3. #Useful  Add the `Content-Type: application/json` header and `{}` in the body. **Now we know the `price` parameter exists and we could update it**.
	1. ![[API Testing-20260112104529198.png]]
4. Enter this `{ "price": 0 }` and Send. Got a 200 OK. Add to cart > Place order and solved.
	1. ![[API Testing-20260112104748011.png]]
	2. ![[API Testing-20260112104804886.png]]
>[!tip] How to Identify this Vulnerability?
>1. `GET /api/products/{productId}/price`
>2. `PATCH` in the `Allow` response header

---

#### 2.4. Lab: Exploiting a mass assignment vulnerability ⭕️
To solve the lab, find and exploit a mass assignment vulnerability to buy a **Lightweight l33t Leather Jacket**. You can log in to your own account using the following credentials: `wiener:peter`.

>[!info] Mass Assignment
>Bind HTTP request parameters (typically sent via forms) to object properties and attackers could modify those fields. E.g., setting `"isAdmin":true` or `"password":"aaa"`.

1. Login > Home > View details on leather jacket > Add to cart > Cart > Place order. No money :(. Notice both `GET` and `POST` requests for `/api/checkout`. Send both to Repeater.
2. Send the `GET` request and notice a difference between the two. `GET` response has `chosen_discount` that isn't found in the `POST` request body.
	1. ![[API Testing-20260112113947354.png]] 
	2. ![[API Testing-20260112114009060.png|273]]
3. In the `POST` request add the following payload and Send. Set `percentage` to 100. Lab solved.
```
{
    "chosen_discount":{
        "percentage":100
    },
    "chosen_products":[
        {
            "product_id":"1",
            "quantity":1
        }
    ]
}
```
![[API Testing-20260112114206630.png]]
>[!tip] How to Identify this Vulnerability?
>1. Both `GET` + `POST` requests for `/api/checkout`
>2. `GET` request response revealed an extra parameter that `POST` can modify

**For exam**
The idea for this lab is if hidden params were found, try something like this:
```
{
    "username": "carlos",
    "email": "carlos@exploit.com",
    "isAdminLevel": true
}
```

---







