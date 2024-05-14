# CTF-Writeups
Solutions for CyberEDU and Unbreakable CTFs.

## CTFs
- [include-this (WEB)](https://github.com/N3agu/CTF-Writeups#include-this-web)
- [this-file-hides-something (Forensics)](https://github.com/N3agu/CTF-Writeups#) 

## include-this (WEB)
After visiting the webpage, I encounter a button that redirects me to a specific URL (ip:port/file=test.txt) and displays the content of the file upon clicking. This behavior suggests a potential vulnerability such as Local File Inclusion (LFI).
![image1](https://raw.githubusercontent.com/N3agu/CTF-Writeups/main/images/include-this1.png)

Testing the "file" parameter for LFI, I attempted to modify the parameter value to "flag.txt," which resulted in an error message. The error indicates that the current directory is /var/www/html/test.
![image2](https://raw.githubusercontent.com/N3agu/CTF-Writeups/main/images/include-this2.png)

By adjusting the parameter value to traverse four directories up and access "flag.txt," I successfully retrieved the flag.
![image3](https://raw.githubusercontent.com/N3agu/CTF-Writeups/main/images/include-this3.png)

## this-file-hides-something (Forensics)
After extracting the zip file, I obtained "crashdump.elf". From the description we know that we are looking to extract a password from the crash dump and that the flag format is not standard.

I decided to run volatility to extract the lsa secrets using: `./vol.py -f crashdump.elf windows.lsadump.Lsadump` and got the flag: "DefaultPassword: Str0ngAsAR0ck!"
