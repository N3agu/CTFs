# CTF-Writeups
Solutions for CyberEDU and Unbreakable CTFs.

## CTFs
- [include-this (WEB)](https://github.com/N3agu/CTF-Writeups#include-this-web)
- [this-file-hides-something (FORENSICS)](https://github.com/N3agu/CTF-Writeups#this-file-hides-something-forensics)
- [spy-agency (FORENSICS)](https://github.com/N3agu/CTF-Writeups#spy-agency-forensics)

## include-this (WEB)
After visiting the webpage, I encounter a button that redirects me to a specific URL (ip:port/file=test.txt) and displays the content of the file upon clicking. This behavior suggests a potential vulnerability such as Local File Inclusion (LFI).
![image1](https://raw.githubusercontent.com/N3agu/CTF-Writeups/main/images/include-this1.png)

Testing the "file" parameter for LFI, I attempted to modify the parameter value to "flag.txt," which resulted in an error message. The error indicates that the current directory is /var/www/html/test.
![image2](https://raw.githubusercontent.com/N3agu/CTF-Writeups/main/images/include-this2.png)

By adjusting the parameter value to traverse four directories up and access "flag.txt," I successfully retrieved the flag.
![image3](https://raw.githubusercontent.com/N3agu/CTF-Writeups/main/images/include-this3.png)

## this-file-hides-something (Forensics)
After extracting the zip file, I obtained 'crashdump.elf.' From the description, I understood that I should search for a password within the crash dump, and that the flag format is non-standard.

I decided to run volatility to extract the lsa secrets using: `vol.py -f crashdump.elf windows.lsadump.Lsadump` and got the flag: "DefaultPassword: Str0ngAsAR0ck!"

## spy-agency
After extracting the zip file, I obtained 'crashdump.elf.' From the description, I understood that I should search for an application within the crash dump, and that the flag format is ctf{sha256(location name from coordinates in lowercase)}.

I decided to run a filescan through volatility using `vol.py -f spyagency3.bin windows.filescan.FileScan` and found an interesting file `0x3fefb8c0 \Users\volf\Desktop\app-release.apk.zip      216`. I used `vol.py -f spyagency3.bin windows.dumpfiles.DumpFiles --physaddr 0x3fefb8c0` to extract the zip file from the dump.

After that, I've unzipped the archive and managed to find `app-release.apk/app-release/res/drawable/coordinates_can_be_found_here.jpg`. Used  `exiftool coordinates_can_be_found_here.jpg` and got the coordinates: "-coordinates=44.44672703736637, 26.098652847616506"

Pasting the coordinates on [google maps](https://www.google.com/maps/place/44%C2%B026'48.2%22N+26%C2%B005'55.2%22E/@44.4467332,26.0983283,20z/data=!4m4!3m3!8m2!3d44.446727!4d26.0986528?entry=ttu) we get the location, which is "Pizza Hut". The flag is ctf{sha256(pizzahut)} | ctf{a939311a5c5be93e7a93d907ac4c22adb23ce45c39b8bfe2a26fb0d493521c4f}
