# CTF-Writeups
Solutions for CyberEDU and Unbreakable CTFs.

## CTFs
- [Include-This (WEB)](https://github.com/N3agu/CTF-Writeups#include-this-web)

## Include-This (WEB)
After visiting the webpage, I encounter a button that redirects me to a specific URL (ip:port/file=test.txt) and displays the content of the file upon clicking. This behavior suggests a potential vulnerability such as Local File Inclusion (LFI).
![image1](https://raw.githubusercontent.com/N3agu/CTF-Writeups/main/images/include-this1.png)

Testing the "file" parameter for LFI, I attempted to modify the parameter value to "flag.txt," which resulted in an error message. The error indicates that the current directory is /var/www/html/test.
![image2](https://raw.githubusercontent.com/N3agu/CTF-Writeups/main/images/include-this2.png)

By adjusting the parameter value to traverse four directories up and access "flag.txt," I successfully retrieved the flag.
![image3](https://raw.githubusercontent.com/N3agu/CTF-Writeups/main/images/include-this3.png)
