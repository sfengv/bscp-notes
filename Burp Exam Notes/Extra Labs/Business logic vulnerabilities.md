




---

#### 1/2.1.Lab: Excessive trust in client-side controls ⭕️
This lab doesn't adequately validate user input. You can exploit a logic flaw in its purchasing workflow to buy items for an unintended price. To solve the lab, buy a "Lightweight l33t leather jacket".
You can log in to your own account using the following credentials: `wiener:peter`

1. Add the l33t jacket to cart > notice the `price` param in `POST /cart` > change it to `1` > see a 302 Found meaning it worked. Refresh the browser and it worked.
	1. ![Business logic vulnerabilities-20260214094933063](Business%20logic%20vulnerabilities-20260214094933063.png)
	2. ![267](Business%20logic%20vulnerabilities-20260214094646482.png)
2. Place the order to solve.

How to ID:
- auth shop > add to cart > change email > csrf token > coupon field in cart 

---

#### x.2. Lab: High-level logic vulnerability ⭕️
This lab doesn't adequately validate user input. You can exploit a logic flaw in its purchasing workflow to buy items for an unintended price. To solve the lab, buy a "Lightweight l33t leather jacket".
You can log in to your own account using the following credentials: `wiener:peter`

1. Add the l33t jacket to cart > **add** the `price` param in `POST /cart` > change it to `1` got a 302 Found BUT it didn't change the price when you refresh the page. 
	1. ![Business logic vulnerabilities-20260214094933063](Business%20logic%20vulnerabilities-20260214094933063.png)
	2. ![Business logic vulnerabilities-20260214095026709](Business%20logic%20vulnerabilities-20260214095026709.png)
2. Remove items from cart. Change `product` to `-1` and notice it changed the price to `-$1337.00`. Click Place order and notice this error: "Cart total price cannot be less than zero".
	1. ![Business logic vulnerabilities-20260214095216613](Business%20logic%20vulnerabilities-20260214095216613.png)
	2. ![Business logic vulnerabilities-20260214095224588](Business%20logic%20vulnerabilities-20260214095224588.png)
	3. ![Business logic vulnerabilities-20260214095326971](Business%20logic%20vulnerabilities-20260214095326971.png)
	4. Remove the l33t jacket from the cart. Add it back in again so the price total is normal: $1337.00. Add another item that is valued less like the wall-e one which is $99.97 aka `productId=2`. Remove the item from cart. In `POST /cart` set the `quantity` to `-13`.
		1. ![Business logic vulnerabilities-20260214095651772](Business%20logic%20vulnerabilities-20260214095651772.png)
		2. ![Business logic vulnerabilities-20260214095659616](Business%20logic%20vulnerabilities-20260214095659616.png)
3. Now the total price is less than the store credit: $100.00. Place order and solve.
	1. ![Business logic vulnerabilities-20260214095727623](Business%20logic%20vulnerabilities-20260214095727623.png)


How to ID:
- auth shop > add to cart > change email > csrf token > coupon field in cart 

---

#### 2.3.Lab: Inconsistent security controls ⭕️
This lab's flawed logic allows arbitrary users to access administrative functionality that should only be available to company employees. To solve the lab, access the admin panel and delete the user `carlos`.

1. Notice the Register button. Login with `wiener:peter` got "Invalid username or password." 
2. Go to `/admin` and got this error: "Admin interface only available if logged in as a DontWannaCry user"
	1. ![Business logic vulnerabilities-20260214101022430](Business%20logic%20vulnerabilities-20260214101022430.png)
3. Click on Register and got this message: "If you work for DontWannaCry, please use your @dontwannacry.com email address". Go to Email client and grab the email address and create an account. Check the Email client again to see a link to complete the registration.
	1.![447](Business%20logic%20vulnerabilities-20260214101339706.png)
	![453](Business%20logic%20vulnerabilities-20260214101357625.png) 
4. Click on the link > login as `attacker` > change the email to `attacker@dontwannacry.com` > notice the Admin panel > delete carlos to solve.
	1. ![270](Business%20logic%20vulnerabilities-20260214101524508.png)
	2. ![328](Business%20logic%20vulnerabilities-20260214101551846.png)
	3. ![152](Business%20logic%20vulnerabilities-20260214101539562.png)


How to ID:
- auth shop > no check stock > change email > csrf token > register > `wiener:peter` got "Invalid username or password." > `/admin` got "Admin interface only available if logged in as a DontWannaCry user" > 

---

#### x.4.Lab: Flawed enforcement of business rules ⭕️
This lab has a logic flaw in its purchasing workflow. To solve the lab, exploit this flaw to buy a "Lightweight l33t leather jacket".
You can log in to your own account using the following credentials: `wiener:peter`

1. Notice promo code `NEWCUST5` and scroll down to see "Sign up to our newsletter". Enter an arbitrary email and got another code `SIGNUP30`.
	1. ![224](Business%20logic%20vulnerabilities-20260214102119814.png)
	2. ![Business logic vulnerabilities-20260214102200441](Business%20logic%20vulnerabilities-20260214102200441.png)
2. Try to add the SIGNUP30 code again but got an error "Coupon already applied". But if you use NEWCUST5, it lets you add it. So we have to alternate each promo code to keep getting discounts.
	1. ![273](Business%20logic%20vulnerabilities-20260214102302964.png)
	2. ![192](Business%20logic%20vulnerabilities-20260214102356261.png)
3. Keep alternating the promo codes til it's under $100.00 (store credit) then Place order to solve.


How to ID:
- auth shop > add to cart > change email > csrf token > coupon field in cart > promo code: NEWCUST5 > "Sign up to our newsletter" at the bottom > 


---

#### x.5. Lab: Low-level logic flaw [Skipped]
This lab doesn't adequately validate user input. You can exploit a logic flaw in its purchasing workflow to buy items for an unintended price. To solve the lab, buy a "Lightweight l33t leather jacket".
You can log in to your own account using the following credentials: `wiener:peter`

1. Add l33t jacket to cart > try setting `quantity=100` and got a 400 saying "Invalid parameter: quantity". So you can only put in quantities of 2-digits. Add 99.
	1. ![Business logic vulnerabilities-20260214102800282](Business%20logic%20vulnerabilities-20260214102800282.png)
2. Send to Intruder > Null payloads > Continue indefinitely > refresh the browser a few times and notice it sometimes show a negative number. We'll need to add enough units so that the price loops back around and settles between $0 and the $100 of your remaining store credit.
	1. ![Business logic vulnerabilities-20260214105143491](Business%20logic%20vulnerabilities-20260214105143491.png)
3. Since each of our request has quantity=99 and jacket costs 1337, each request will add 132,363 to the total. Take that number and divide by 132363 = 121. Back to Intruder > Generate 121 payloads AND set resource pool maximum concurrent requests: 1. 
	1. Now we have -$1496174.56. 

*too much work to solve this one. not even needed for the exam*

How to ID:
- auth shop > add to cart > change email > csrf token > coupon field in cart > `quantity=100` == 400 > 

---

#### x.8. Lab: Insufficient workflow validation ⭕️
This lab makes flawed assumptions about the sequence of events in the purchasing workflow. To solve the lab, exploit this flaw to buy a "Lightweight l33t leather jacket".
You can log in to your own account using the following credentials: `wiener:peter`

1. Login > have $100 store credit > buy an item > notice the `POST /cart/checkout` with a weird `303 See Other` status code and has `Location: /cart/order-confirmation?order-confirmed=true` (redirect).
	1. ![Extra Labs-20260128205143338](Extra%20Labs-20260128205143338.png)
2. Send `GET /cart/order-confirmation?order-confirmed=true` to Repeater > add leather jacket to cart > Send request > lab solved. 
	1. ![Extra Labs-20260128205247002](Extra%20Labs-20260128205247002.png)
>[!tip] How to Identify this Vulnerability
>1. Shop feature + $100 store credit 
>2. `POST /cart/checkout` + `303 See Other` status code + `Location` header

---

#### 2.9. Lab: Authentication bypass via flawed state machine ⭕️
This lab makes flawed assumptions about the sequence of events in the login process. To solve the lab, exploit this flaw to bypass the lab's authentication, access the admin interface, and delete the user `carlos`.
You can log in to your own account using the following credentials: `wiener:peter`

1. `/admin` says "Admin interface only available if logged in as an administrator" and logging in will show "Please select a role".
	1. ![327](Business%20logic%20vulnerabilities-20260214110159651.png)
2. Click My account to log out. Intercept ON > enter creds > Login > forward the `/login` request BUT drop the `/role-selector` request. You will see a burp error.
	1. ![244](Business%20logic%20vulnerabilities-20260214110636441.png)
3. In the URL, remove `role-selector` to just get `/` and see we have defaulted to the admin role > click admin panel > delete carlos to solve.
	1. ![286](Business%20logic%20vulnerabilities-20260214110646543.png)

How to ID:
- auth shop > Please select a role > `/role-selector` > `/admin`: Admin interface only available if logged in as an administrator > `/login`: Forward > DROP `/role-selector` 

---

#### 2.10. Lab: Authentication bypass via encryption oracle ⭕️
This lab contains a logic flaw that exposes an encryption oracle to users. To solve the lab, exploit this flaw to gain access to the admin panel and delete the user `carlos`.
You can log in to your own account using the following credentials: `wiener:peter`

1. Login and notice a stay logged in box. Post a comment with an invalid email address and got an error: "Invalid email address: " and your invalid email reflected. Also notice a `notication` cookie too.
	1. ![Business logic vulnerabilities-20260214111228348](Business%20logic%20vulnerabilities-20260214111228348.png)
	2. ![Business logic vulnerabilities-20260214111342955](Business%20logic%20vulnerabilities-20260214111342955.png)
2. Send `POST /post/comment` and `GET /post?postID={INTEGER}` to Repeater > copy `stay-logged-in` value and paste it in `notification` cookie. Search for `wiener` and see a timestamp. Copy it.
	1. ![Business logic vulnerabilities-20260214112359698](Business%20logic%20vulnerabilities-20260214112359698.png)
	2. ![Business logic vulnerabilities-20260214112418511](Business%20logic%20vulnerabilities-20260214112418511.png)
3. Back in `POST /post/comment` > set `email` to `administrator:1771085388635`. Send and grab the `notification` value. 
	1. ![Business logic vulnerabilities-20260214112638017](Business%20logic%20vulnerabilities-20260214112638017.png)
	2. ![Business logic vulnerabilities-20260214112650651](Business%20logic%20vulnerabilities-20260214112650651.png)
4. `GET /post?postId={INTEGER}` paste the `notification` value to the `notification` cookie and see the error: "Invalid email address: administrator:1771085388635". **This confirms that `POST /post/comment` was used to encrypt and `GET /post/postId` was used to decrypt**.
	1. ![Business logic vulnerabilities-20260214112851703](Business%20logic%20vulnerabilities-20260214112851703.png)
5. "Invalid email address: " (including the space) is 23 bytes.
	1. ![Business logic vulnerabilities-20260214113127453](Business%20logic%20vulnerabilities-20260214113127453.png)
6. Send `notification` cookie to decoder > decode as url > decode as base64 > delete the first 23 bytes > encode as base64 > encode as url.
	1. ![Business logic vulnerabilities-20260214113810377](Business%20logic%20vulnerabilities-20260214113810377.png)
7. Copy the final value > paste it in `GET /post/postId` notification cookie and got 500 error "Input length must be multiple of 16 when decrypting with padded cipher"
	1. ![Business logic vulnerabilities-20260214113941733](Business%20logic%20vulnerabilities-20260214113941733.png)
8. `POST` add 9 characters in `email` field. Because 9+16=32 (why 9????)
	1. ![Business logic vulnerabilities-20260214114137820](Business%20logic%20vulnerabilities-20260214114137820.png)
9. Grab the notification cookie > decoder > follow steps 6 + 7.
	1. ![Business logic vulnerabilities-20260214114406656](Business%20logic%20vulnerabilities-20260214114406656.png)
10. Paste in notification cookie in `GET` request and got this:
	1. ![Business logic vulnerabilities-20260214114357573](Business%20logic%20vulnerabilities-20260214114357573.png)
11. In browser go to Home > Paste the notification cookie value in Cookie Editor's stay logged in cookie and remove session cookie > save > refresh > see admin panel. Delete carlos and solve.
	1. ![Business logic vulnerabilities-20260214114603513](Business%20logic%20vulnerabilities-20260214114603513.png)
	2. ![Business logic vulnerabilities-20260214114610678](Business%20logic%20vulnerabilities-20260214114610678.png)


How to ID:
- auth blog > every field > stay logged in box + cookie > csrf token > author name is a hyperlink > no client side validation in Email field > `notification` cookie in `POST /post/comment` with invalid email > 

