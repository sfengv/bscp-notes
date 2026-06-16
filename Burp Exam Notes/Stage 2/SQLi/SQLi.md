


- - -
#### 2.1. Lab: SQL injection vulnerability in WHERE clause allowing retrieval of hidden data ⭕️
This lab contains a SQL injection vulnerability in the product category filter. When the user selects a category, the application carries out a SQL query like the following:

`SELECT * FROM products WHERE category = 'Gifts' AND released = 1`

To solve the lab, perform a SQL injection attack that causes the application to display one or more unreleased products.

1. Clicked on the Clothing filter and the SQL query looks like the one above.
	1. ![[SQLi-20260108114433526.png|398]]
2. Enter the following payload `'+OR+1=1--` in the URL or Repeater and Send. Now that it showed more items. So the SQL query now looks like `category = ...+accessories'+OR+1=1--`. Single quote ends that part of the query, `OR+1=1` makes the statement true. Lab solved!
	1. ![[SQLi-20260108114522354.png]]
	2. ![[SQLi-20260108114501557.png]]
- - -
#### 2.2. Lab: SQL injection vulnerability allowing login bypass ⭕️
This lab contains a SQL injection vulnerability in the login function.
To solve the lab, perform a SQL injection attack that logs in to the application as the `administrator` user.

1. Enter arbitrary values for username + password
2. Intercept ON > click Login
3. Change the `username` param to `administrator'--` where `--` comments out the rest of the query that contains the `password` param. Click My account and solved.
	1. ![[SQLi-20260108115135778.png]]
- - -
#### 2.3. Lab: SQL injection attack, querying the database type and version on Oracle ⭕️
This lab contains a SQL injection vulnerability in the product category filter. You can use a UNION attack to retrieve the results from an injected query.
To solve the lab, display the database version string.
1. Click on a filter category like `Pets`. Send the `GET /filter` request to Repeater. Add a single quote `'` after `Pets` and notice we get a 500 Internal Server Error. *This means this might be vulnerable to SQLi*.
	1. ![[SQLi-20260121141404173.png]]
2. Determine the number of columns:
	1. Enter this payload: `'+order+by+1--` and we got a 200 OK meaning *there are at least 1 column*. Change that to `2` and we get 200 OK -- there are 2 columns.
		1. ![[SQLi-20260121141640779.png]]
	2. Enter `3` and we get a 500 meaning there are only 2 columns.
		1. ![[SQLi-20260121141731496.png]]
3. Determine data types of the columns:
	1. Enter this payload: `'+UNION+SELECT+'abc','def'+FROM+dual--` and notice we get a 200 OK meaning the 2 columns are of *type string* due to `'abc' and 'def'`.
		1. ![[SQLi-20260121142245600.png]]
	2. To further confirm this, search `abc` or `def` in the response and see the text being reflected.
		1. ![[SQLi-20260121142420223.png]]
>Oracle DBs require a `FROM` clause and `dual` is just a required dummy table.
4. Use the SQLi cheat sheet to find the payload to output an Oracle database's version number: `'+UNION+SELECT+BANNER,+NULL+FROM+v$version--` and the lab is solved.
	1. ![[SQLi-20260121142711564.png]]
>[!tip] How to Identify this Vulnerability?
>1. Filter feature
>2. `'+UNION+SELECT+'abc','def'+FROM+dual--` get you a 200 OK and string reflected in the response
>	1. Oracle databases require `--`
>3. `FROM+dual` get a 200 OK meaning this is an **Oracle database**

---
#### 2.4. Lab: SQL injection attack, querying the database type and version on MySQL and Microsoft ⭕️
This lab contains a SQL injection vulnerability in the product category filter. You can use a UNION attack to retrieve the results from an injected query.
To solve the lab, display the database version string.
1. Click on a filter category like `Pets`. Send the `GET /filter` request to Repeater. Add a single quote `'` after `Pets` and notice we get a 500 Internal Server Error. *This means this might be vulnerable to SQLi*.
	1. ![[SQLi-20260121141404173.png]]
2. Determine the number of columns:
	1. Enter this payload: `'+order+by+1#` (*notice the `#`*) and notice a 200 OK meaning there is at least 1 column. Changing it to `2` will get 200 OK (at least 2 columns). But `3` get a 500. Meaning there are only 2 columns.
		1. ![[SQLi-20260121151232919.png]]
3. Determine the data types of the columns:
	1. Enter this payload: `'+UNION+SELECT+'abc','def'#` and notice a *200 OK* and the strings in the payload are *reflected in the response*.
		1. ![[SQLi-20260121150413983.png]]
4. Use the SQLi cheat sheet to find the payload to output an Oracle database's version number: `''+UNION+SELECT+@@version,+NULL#` and the lab is solved.
	1. ![[SQLi-20260121151507655.png]]
>[!tip] How to Identify this Vulnerability?
>1. Filter feature
>2. `'+UNION+SELECT+'abc','def'#` get you a 200 OK and string reflected in the response
>	1. MySQL and Microsoft require `#`
>3. `FROM+dual` get a 200 OK meaning this is an **Oracle database

---
#### 2.5. Lab: SQL injection attack, listing the database contents on non-Oracle databases ⭕️
This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response so you can use a UNION attack to retrieve data from other tables.

The application has a login function, and the database contains a table that holds usernames and passwords. You need to determine the name of this table and the columns it contains, then retrieve the contents of the table to obtain the username and password of all users.

To solve the lab, log in as the `administrator` user.

1. Click on a filter like `Accessories`, send that `GET /filter` request to Repeater, add a single quote `'`, Send, and notice a 500 Internal Server Error. **This is an indication of a potential SQLi vulnerability**.
	1. ![[SQLi-20260109101127959.png]]
2. Very similar to the Lab 5, determine the number of columns in the table: `'+order+by+1--`. Increase the number like `2`. Keep increasing til you get a 500. `'+order+by+3--` get us a 500 meaning there cannot be 3 columns. *So only 2 columns*.
	1. ![[SQLi-20260109101412200.png]]
	2. ![[SQLi-20260109101522429.png]]
3. Determine the datatype of each column: `'+UNION+SELECT+'abc','def'--`. Difference Lab 5 is that non-Oracle databases do not require `FROM+dual--`. Reflection of those 2 texts meaning both columns are varchars or contains text. If you try integers or anything you should get a 500.
	1. ![[SQLi-20260109101819407.png]]
4. List table names: `'+UNION+SELECT+table_name,+NULL+FROM+information_schema.tables--`.
	1. ![[SQLi-20260109102140750.png]]
5. List column name(s) of a table: `'+UNION+SELECT+column_name,+NULL+FROM+information_schema.columns+WHERE+table_name='users_abcdef'--`. Replace *users_abcdef* to the actual table name like `users_jvnkjd`.
	1. ![[SQLi-20260109104852223.png]]
	2. ![[SQLi-20260109104913328.png]]
6. List the contents of columns in a table:  `'+UNION+SELECT+username_abcdef,+password_abcdef+FROM+users_abcdef--`. *Replace the username_abcdef, etc. to the actual column and table name*.
	1. ![[SQLi-20260109105048900.png]]
7. Login using `administrator:4mdl4h3psg1ehta362b1` and solve.

>[!tip] How to Identify this Vulnerability
>1. Filter feature or another parameter that gives some sort of error or change of http response when you add a single quote `'`
>2. Different HTTP response (e.g., 500) when a single quote `'` was appended in the affected parameter

- - - 
#### 2.6. Lab: SQL injection attack, listing the database contents on Oracle ⭕️
This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response so you can use a UNION attack to retrieve data from other tables.

The application has a login function, and the database contains a table that holds usernames and passwords. You need to determine the name of this table and the columns it contains, then retrieve the contents of the table to obtain the username and password of all users.

To solve the lab, log in as the `administrator` user.

1. Click on a category in the filter like `Gifts` and send the request to Repeater. Add a single quote like `Gifts'`, Send, notice we get a 500 Internal Server Error. **We just confirmed a SQLi vulnerability**.
	1. ![[SQLi-20260108200906420.png]]
2. Determine the number of columns: `'+order+by+1--`. If we get a 200 OK that means there is 1 column in the table. But `'+order+by+3--` got us a 500 Internal Server Error so there are only *2 columns*.
	1. ![[SQLi-20260108201958701.png]]

>[!tip] Where to get Payloads
>Could use the Burp SQLi cheat sheet.

3. Determine the datatype of each column: `'+UNION+SELECT+'abc','def'+FROM+dual--`. Testing if both columns are letters/varchar datatypes with `'abc' and 'def'`, if true, we get 200 OK and it will reflect in the response. If false, like `1` for the 2nd column, get 500.
	1. ![[SQLi-20260108202109936.png]]
	2. ![[SQLi-20260108202131849.png]]
4. List the table names: `'+UNION+SELECT+table_name,NULL+FROM+all_tables--`
	1. ![[SQLi-20260108202636871.png]]
5. List the column name(s) in a table: `'+UNION+SELECT+column_name,NULL+FROM+all_tab_columns+WHERE+table_name='USERS_RQAIEI'--`
	1. ![[SQLi-20260108203122718.png]]
	2. ![[SQLi-20260108203619313.png]]
#ExamTip  **IMPORTANT:** Examine the entire HTTP response for the columns. Couldn't see the password column and had to scroll up to see it.
6. List the contents of the columns in a table: `'+UNION+SELECT+USERNAME_CIUBEA,+PASSWORD_FQUGYU+FROM+USERS_RQAIEI--`
	1. ![[SQLi-20260108203847400.png]]
7. Login with `administrator:ydoampif1cm14dujd9af` to solve the lab.

>[!tip] FROM dual #ExamTip 
>The `FROM dual` part in Step 3 in unique to Oracle databases and it's required to have `FROM` when using `SELECT` statements. `dual` is a dummy table.

>[!tip] How to Identify this Vulnerability
>1. Filter feature or another parameter that gives some sort of error or change of http response when you add a single quote `'`
>2. Different HTTP response (e.g., 500) when a single quote `'` was appended in the affected parameter

- - -
#### 2.7. Lab: SQL injection UNION attack, determining the number of columns returned by the query ⭕️
This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. The first step of such an attack is to determine the number of columns that are being returned by the query. You will then use this technique in subsequent labs to construct the full attack.

To solve the lab, determine the number of columns returned by the query by performing a SQL injection UNION attack that returns an additional row containing null values.

1. Click on a filter > Send that `GET /filter` request to Repeater > add `'` single quote at the end of the URI > notice a *500 status code meaning this could be vulnerable to SQLi*.
	1. ![[SQLi-20260121152037917.png]]
2. Determine the SQL database:
	1. Enter `'+order+by+1--` and notice a *200 OK meaning this is an Oracle database*.
		1. ![[SQLi-20260121152207694.png]]
	2. Replace `--` with `#` and get a 500. MySQL and Microsoft require `#` so it is not any of those. 
		1. ![[SQLi-20260121152318446.png]]
3. Determine the number of columns:
	1. Enter `'+order+by+1--` and get a 200 OK. Meaning there is at least 1 column. Keep increasing the integer til you get a 500 code. The integer before that is the number of columns. So `4` get us 500 but `3` get us 200. *So there are 3 columns*. 
		1. ![[SQLi-20260121152638547.png]]
4. If that doesn't solve the lab, use the `NULL` method to determine number of columns:
	1. Enter `'+UNION+SELECT+NULL--` and notice a 500 code. 1 `NULL` means 1 column. Keep adding `,NULL` til we get a 200 OK.
		1. ![[SQLi-20260121152758388.png]]
	2. Enter `'+UNION+SELECT+NULL,NULL,NULL--` to solve. **3 NULLs means 3 columns**.
		1. ![[SQLi-20260121152821277.png]]
>[!tip] How to Identify this Vulnerability?
>1. Filter feature
>2. `'+order+by+1--` get you a 200 OK
>3.  Or `'+UNION+SELECT+NULL--` get you a 500

---
#### 2.8. Lab: SQL injection UNION attack, finding a column containing text ⭕️
This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. To construct such an attack, you first need to determine the number of columns returned by the query. You can do this using a technique you learned in a [previous lab](https://portswigger.net/web-security/sql-injection/union-attacks/lab-determine-number-of-columns). The next step is to identify a column that is compatible with string data.

The lab will provide a random value that you need to make appear within the query results. To solve the lab, perform a SQL injection UNION attack that returns an additional row containing the value provided. This technique helps you determine which columns are compatible with string data.
1. Click on a filter > Send that `GET /filter` request to Repeater > add `'` single quote at the end of the URI > notice a *500 status code meaning this could be vulnerable to SQLi*.
	1. ![[SQLi-20260121152037917.png]]
2. Determine the number of columns using ORDER BY (METHOD 1):
	1. Enter `'+order+by+1--` and notice a 200 OK and because we used `--` AND a 200 OK means that this is an **Oracle database**.
	2. Keep increasing the integer til you get a 500 status code. The integer before that, the one that gave you a 200 OK, will be the number of columns in the table. **So it's `3`**.
		1. ![[SQLi-20260121192839740.png]]
		2. ![[SQLi-20260121192829085.png]]
3. Determine the number of columns using NULL (METHOD 2):
	1. Enter `'+UNION+SELECT+NULL--` and notice a 500 code. 1 `NULL` means 1 column. Keep adding `,NULL` til we get a 200 OK. **3 `NULL`s = 3 columns**.
		1. ![[SQLi-20260121152821277.png]]
4. Determine the columns' datatype (`NULL` Method):
	1. Replace each `NULL` with a datatype like `'abc'`, Send, if get 500, turn it back to NULL then move to the next one. Lab is solved when we guess the correct type `string` and make it reflect `mA4yTv`.
		1. ![[SQLi-20260121193450816.png]]
>[!tip] How to Identify this Vulnerability?
>1. Filter feature
>2. `'+order+by+1--` get you a 200 OK
>3.  Or `'+UNION+SELECT+NULL--` get you a 500
>4. Input during the determine datatype step is reflected in the response

---
#### 2.9. Lab: SQL injection UNION attack, retrieving data from other tables ⭕️
This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables. To construct such an attack, you need to combine some of the techniques you learned in previous labs.
The database contains a different table called `users`, with columns called `username` and `password`.
To solve the lab, perform a SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the `administrator` user.
1. Click on a filter > Send that `GET /filter` request to Repeater > add `'` single quote at the end of the URI > notice a *500 status code meaning this could be vulnerable to SQLi*.
	1. ![[SQLi-20260121194032119.png]]
2. Determine the number of columns (ORDER BY METHOD):
	1. `'+order+by+3--` gave us 500 but `2` gave us 200 OK **so there are 2 columns**.
		1. ![[SQLi-20260121194348089.png]]
3. Determine data types for each column:
	1. `'+UNION+SELECT+'abc','def'--` got us **200 OK AND the input is reflected in the response so both columns are data type `string`**.
		1. ![[SQLi-20260121194512474.png]]
	2. If you try a different datatype like integers, you'll get a 500 
		1. ![[SQLi-20260121194643061.png]]
4. Find admin's credentials:
	1. Enter `'+UNION+SELECT+username,+password+FROM+users--` and check the response for the admin's creds: `administrator:uj4nhnog996l8we3z0jw`.
	2. Login with those creds and solve.
>[!tip] How to Identify this Vulnerability?
>1. Filter feature
>2. `'+order+by+1--` get you a 200 OK
>3.  Or `'+UNION+SELECT+NULL--` get you a 500
>4. Input during the determine datatype step is reflected in the response

---
#### 2.10. Lab: SQL injection UNION attack, retrieving multiple values in a single column ⭕️
This lab contains a SQL injection vulnerability in the product category filter. The results from the query are returned in the application's response so you can use a UNION attack to retrieve data from other tables.
The database contains a different table called `users`, with columns called `username` and `password`.
To solve the lab, perform a SQL injection UNION attack that retrieves all usernames and passwords, and use the information to log in as the `administrator` user.
1. Follow Steps 1-3 from Lab 9: 2 columns and only the 2nd column contain text.
	1. ![[SQLi-20260121195126393.png]]
	2. ![[SQLi-20260121195248316.png]]
2. Retrieve data from each column:
	1. Enter `'+UNION+SELECT+NULL,username+FROM+users--` to display usernames in the table `users`. Got a 200 OK and check the response.
		1. ![[SQLi-20260121195815016.png]]
	2. Replace `username` with `password` to display the passwords. Unless we want to try each combination manually, there is a better way: **output both columns together**.
		1. ![[SQLi-20260121195906101.png]]
3. Retrieve data from *both* columns:
	1. Check the SQLi cheat sheet but we need to determine the SQL database first.	
		1. If we see 200 OK then we have the SQL database. Used `'+UNION+SELECT+NULL,version()--`, got a **200 OK and it's PostgreSQL**.
		2. ![[SQLi-20260121200808152.png]]
>Put `NULL,version()` need to put `NULL` first for this lab because we don't know the first column's data type and `string` and `integer` didn't work.
4. So, use the || to concatenate.
	1. ![[SQLi-20260121201033779.png]]
	2. Payload: `'+UNION+SELECT+NULL,username||'~'||password+FROM+users--`. *The `~` is the create a separation between the username and password.*
	3. ![[SQLi-20260121201222468.png]]
5. Login with `administrator:o2ht0xhsg2y73dmpoj6f` to solve.
>[!tip] How to Identify this Vulnerability?
>1. Filter feature
>2. `'+order+by+1--` get you a 200 OK
>3.  `'+UNION+SELECT+NULL,'def'--` get you a **200 OK**
>4. Input during the determine datatype step is reflected in the response

---


#### 2.18. Lab: SQL injection with filter bypass via XML encoding ⭕️
This lab contains a SQL injection vulnerability in its stock check feature. The results from the query are returned in the application's response, so you can use a UNION attack to retrieve data from other tables.

The database contains a `users` table, which contains the usernames and passwords of registered users. To solve the lab, perform a SQL injection attack to retrieve the admin user's credentials, then log in to their account.
1. `POST /product/stock` request > add `UNION SELECT NULL` for `<storeId>` and got a "Attack detected" confirming a WAF.
2. Try to obfuscate our payload using *Hackvertor*. In Repeater > highlight `1 UNION SELECT NULL` > Extensions > Hackvertor > Encode > hex entities. Send. Notice we get a 200 OK and `null` in the response.
	1. ![[SQLi-20260121204543039.png|317]]
3. Check Lab 10 to determine the SQL database (use SQLi cheat sheet)
	1. `UNION SELECT version()` got us an output: **PostgreSQL**.
	2. ![[SQLi-20260121205026854.png]]
4. Use the SQLi Cheat Sheet for concatenating strings: |PostgreSQL|`'foo'\|'bar'`|. Use this payload: `UNION SELECT username||'~'||password FROM users`. 
	1. ![[SQLi-20260121205316584.png]]
5. Login as `administrator:bl6du8iriqwzbdec5934` to solve.
>[!tip] How to Identify this Vulnerability?
>1. Check stock feature
>2. XML in `POST` request
>3. Some sort of WAF to detect SQLi and give an error message like "Attack detected"

---

#### 2.13. Lab: Visible error-based SQL injection ⭕️
This lab contains a SQL injection vulnerability. The application uses a tracking cookie for analytics, and performs a SQL query containing the value of the submitted cookie. The results of the SQL query are not returned.

The database contains a different table called `users`, with columns called `username` and `password`. To solve the lab, find a way to leak the password for the `administrator` user, then log in to their account.

1. Refresh the page to see the `TrackingId` cookie. At first launch, the cookie isn't in the request yet. Send `GET /` to Repeater and Send. Got a 500 AND **visually inspect the response to see if there's any indicator of a SQLi vulnerability**. This one we do.
	1. ![[SQLi-20260109105928998.png]]
	2. ![[SQLi-20260109105952901.png]]
		1. See that it says `Unterminated string literal`. This means we have an **unclosed string literal**. **This is the verbose error**.
2. Add `--` to the cookie and now we get a 200 OK. This means the query is syntactically correct. 
	1. ![[SQLi-20260109110248525.png]]
>[!tip] CASK()
>The `CASK()` converts datatypes(??). Basically, if you specify `int` but the datatype isn't `int`, you'll get an error. 
3. Enter this: `'+AND+CAST((SELECT+1)+AS+int)--`. Got a 500 error and inspect the response to see that the `AND` should be a boolean so modify the payload.
	1. ![[SQLi-20260109111931709.png]]
4. Add `1=` before `CASK`: `'+AND+1=CAST((SELECT+1)+AS+int)--`. Now that makes it a boolean because `1=1` which is true so we get a 200 OK.
	1. ![[SQLi-20260109112057545.png]]
5. Get the username: `'+AND+1=CAST((SELECT+username+FROM+users)+AS+int)--`. *Used `username` column and `users` table because the lab told us*. Now we're trying to run a SQL query to get what we want with `SELECT username FROM users`. Got an error. We should've gotten an error that has nothing to do with unterminated string literal. If anything it should be saying an integer `1` is not equal to a string (which is the result of `SELECT username FROM users`). But got a message about Unterminated string literal. So this part was truncated and not processed `int)--`. **Solution: remove the tracking cookie value**.
	1. ![[SQLi-20260109112501877.png]]
6. New payload: `TrackingId='+AND+1=CAST((SELECT+username+FROM+users)+AS+int)--`. This error says there are more than one row of data meaning there is more than 1 username in the database.
	1. ![[SQLi-20260109112944666.png]]
7. Solution: `'+AND+1=CAST((SELECT+username+FROM+users+LIMIT+1)+AS+int)--`. Only show 1 result back. Got the username: `administrator`.
	1. ![[SQLi-20260109113155317.png]]
8. Change `username` to `password` and send.
	1. ![[SQLi-20260109113707121.png]]
9. Login as `administrator:pt1ioru33my7zb5ess1l` and solved.

>[!tip] How to Identify this Vulnerability
>1. `TrackingId` cookie or another parameter that gives some sort of error or change of http response when you add a single quote `'`
>2. Different HTTP response (e.g., 500) when a single quote `'` was appended in the affected parameter
>3. **Visually inspect the HTTP response** for indicators of SQLi (e.g., SQL queries)

- - -












