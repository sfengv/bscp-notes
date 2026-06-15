



---
>Note: x. because unsure of what stage the lab falls under
#### x.1. Lab: Limit overrun race conditions ⭕️
This lab's purchasing flow contains a race condition that enables you to purchase items for an unintended price.

To solve the lab, successfully purchase a **Lightweight L33t Leather Jacket**.

You can log in to your account with the following credentials: `wiener:peter`.

For a faster and more convenient way to trigger the race condition, we recommend that you solve this lab using the [Trigger race conditions](https://github.com/PortSwigger/bambdas/blob/main/CustomAction/ProbeForRaceCondition.bambda) custom action. This is only available in Burp Suite Professional.

1. Add the "l33t" jacket to cart > turn on intercept > paste `PROMO20` into the coupon field > Apply > Send the `POST /cart/coupon` request to Repeater > turn off intercept.
2. `ctrl + r` to duplicate the request like maybe 16 times > right click > add tab to group > create a new group > select all tabs > create
3. Send group (parallel) and get 302 Found: "Coupon applied"
	1. ![Race conditions-20260212162339384](Race%20conditions-20260212162339384.png)
 4. Refresh the browser and see that due to a race condition, we were able to apply the coupon so many times and can buy the jacket to solve.
	 1. ![Race conditions-20260212162452005](Race%20conditions-20260212162452005.png)


How to ID:
- auth shop > add to cart > change email > coupon field in cart > PROMO20 code > csrf token > 

---

#### 1/2.2. Lab: Bypassing rate limits via race conditions ⭕️
This lab's login mechanism uses rate limiting to defend against brute-force attacks. However, this can be bypassed due to a race condition.
To solve the lab:
1. Work out how to exploit the race condition to bypass the rate limit.
2. Successfully brute-force the password for the user `carlos`.
3. Log in and access the admin panel.
4. Delete the user `carlos`.
You can log in to your account with the following credentials: `wiener:peter`.
You should use the following list of potential passwords: [here](https://portswigger.net/web-security/race-conditions/lab-race-conditions-bypassing-rate-limits)

-- 
1. Enter arbitrary creds `aaa:aaa` and after 4 invalid attempts, you get "You have made too many incorrect login attempts. Please try again in 58 seconds."
2. Enter another arbitrary creds `bbb:aaa` and got "Invalid username or password." **This indicates that the rate limit is enforced per-username rather than per-session.**
	1. ![Race conditions-20260212164410536](Race%20conditions-20260212164410536.png)
3. Send that `POST /login` request to Repeater > add it to a group > `ctrl + r` 18 times > Send group (parallel). On the 4th request we expect to see "You have made too many incorrect login attempts..." but going through most of them, it only says "Invalid username or password." *You might see one you have made too many incorrect... errors in there*. 
	1. ![Race conditions-20260212170049527](Race%20conditions-20260212170049527.png)
4. Login > `carlos:a` > send this `POST` request to Repeater. Right click > Extensions > Turbo Intruder: send to turbo intruder. 
	1. set password to `%s`, select `examples/race-single-packet-attack.py` > paste in the payload below > go to the lab and copy the password list in your clipboard > Attack.
	2. ![Race conditions-20260212170839088](Race%20conditions-20260212170839088.png)
```
def queueRequests(target, wordlists):

    # as the target supports HTTP/2, use engine=Engine.BURP2 and concurrentConnections=1 for a single-packet attack
    engine = RequestEngine(endpoint=target.endpoint,
                           concurrentConnections=1,
                           engine=Engine.BURP2
                           )
    
    # assign the list of candidate passwords from your clipboard
    passwords = wordlists.clipboard
    
    # queue a login request using each password from the wordlist
    # the 'gate' argument withholds the final part of each request until engine.openGate() is invoked
    for password in passwords:
        engine.queue(target.req, password, gate='1')
    
    # once every request has been queued
    # invoke engine.openGate() to send all requests in the given gate simultaneously
    engine.openGate('1')


def handleResponse(req, interesting):
    table.add(req)

```

5. Look for a 302 Found and that is the correct password for carlos. `1qaz2wsx`. Login as carlos > admin panel > delete carlos to solve.
	1. ![Race conditions-20260212171026697](Race%20conditions-20260212171026697.png)


How to ID:
- auth blog > every field > change email > 


---

#### x.3. Lab: Multi-endpoint race conditions [Skipped]
This lab's purchasing flow contains a race condition that enables you to purchase items for an unintended price.
To solve the lab, successfully purchase a **Lightweight L33t Leather Jacket**.
You can log into your account with the following credentials: `wiener:peter`.


How to ID:
- auth shop > add to cart > change email > redeem gift cards > valid gift cards
- gift card as a product > coupon field in cart > csrf token 

*Not solving this lab due to time and cant think of anyway this lab can be used in the exam; can't get any user's session or admin's or read the secret file with this*


---

#### 2.4. Lab: Single-endpoint race conditions ⭕️
This lab's email change feature contains a race condition that enables you to associate an arbitrary email address with your account.

Someone with the address `carlos@ginandjuice.shop` has a pending invite to be an administrator for the site, but they have not yet created an account. Therefore, any user who successfully claims this address will automatically inherit admin privileges.

To solve the lab:
1. Identify a race condition that lets you claim an arbitrary email address.
2. Change your email address to `carlos@ginandjuice.shop`.
3. Access the admin panel.
4. Delete the user `carlos`

You can log in to your own account with the following credentials: `wiener:peter`.

You also have access to an email client, where you can view all emails sent to `@exploit-<YOUR-EXPLOIT-SERVER-ID>.exploit-server.net` addresses.

1. Login > change email to `anything@exploit-<YOUR-EXPLOIT-SERVER-ID>.exploit-server.net` > got a message: "Please click the link in your email to confirm the change of e-mail to: anything@exploit-0a2900a104c987ba81d115180130008d.exploit-server.net". 
	1. ![453](Race%20conditions-20260215115815632.png)
2. Email client > click link to confirm. Email changed.
	1. ![Race conditions-20260215115934361](Race%20conditions-20260215115934361.png)
3. Change email 2 times and try the first confirmation link but didnt work: ""This link is invalid." **This means the website only stores one pending email address at a time. As submitting a new email address edits this entry in the database rather than appending to it, there is potential for a collision.**
	1. ![Race conditions-20260215120114605](Race%20conditions-20260215120114605.png)
4. Send `POST /my-account/change-email` to Repeater. Do like 19 or 21 of them like `21@exploit-...exploit-server.net`. Send group (separate connections). Email client to see we got a confirmation email for each request.
	1. ![270](Race%20conditions-20260215120528474.png)
	2. ![Race conditions-20260215120621352](Race%20conditions-20260215120621352.png)
5. Now Send group (parallel). Notice the recipient address doesn't always match the pending new address. 
	1. ![Race conditions-20260215120829790](Race%20conditions-20260215120829790.png)
6. Consider that there may be a race window between when the website:
    1. Kicks off a task that eventually sends an email to the provided address.
    2. Retrieves data from the database and uses this to render the email template.
7. **Deduce that when a parallel request changes the pending email address stored in the database during this window, this results in confirmation emails being sent to the wrong address.**
8. Create another group > 2 POST requests > one with `anything@exploit..` and the other `carlos@ginandjuice.shop` > Send group (parallel).
	1. ![Race conditions-20260215121043193](Race%20conditions-20260215121043193.png)
9. Check your inbox:
    - If you received a confirmation email in which the address in the body matches your own address, resend the requests in parallel and try again.
    - If you received a confirmation email in which the address in the body is `carlos@ginandjuice.shop`, click the confirmation link to update your address accordingly.
	1. ![Race conditions-20260215121122651](Race%20conditions-20260215121122651.png)
	2. ![Race conditions-20260215121207366](Race%20conditions-20260215121207366.png)
	3. ![Race conditions-20260215121219324](Race%20conditions-20260215121219324.png)
10. Click Admin panel > delete carlos to solve.

How to ID:
- auth shop > Add to cart > no check stock > change email > Coupon field > "Please click the link in your email..." after changing email > csrf token > 