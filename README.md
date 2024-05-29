# CTF-Writeups
Solutions for CyberEDU, Unbreakable and ROCSC CTFs.

## CTFs
- [include-this (WEB)](https://github.com/N3agu/CTF-Writeups#include-this-web)
- [rubies (WEB)](https://github.com/N3agu/CTF-Writeups#rubies-web)
- [this-file-hides-something (FORENSICS)](https://github.com/N3agu/CTF-Writeups#this-file-hides-something-forensics)
- [spy-agency (FORENSICS)](https://github.com/N3agu/CTF-Writeups#spy-agency-forensics)
- [linux-recovery (MISC)](https://github.com/N3agu/CTF-Writeups#linux-recovery-misc)
- [access-vip-only (FORENSICS)](https://github.com/N3agu/CTF-Writeups#access-vip-only-forensics)
- [unconditional (REVERSE)](https://github.com/N3agu/CTF-Writeups#unconditional-reverse)

## include-this (Web)
After visiting the webpage, I encounter a button that redirects me to a specific URL (ip:port/file=test.txt) and displays the content of the file upon clicking. This behavior suggests a potential vulnerability such as Local File Inclusion (LFI).

![image1](https://raw.githubusercontent.com/N3agu/CTF-Writeups/main/images/include-this1.png)

Testing the "file" parameter for LFI, I attempted to modify the parameter value to "flag.txt," which resulted in an error message. The error indicates that the current directory is /var/www/html/test.

![image2](https://raw.githubusercontent.com/N3agu/CTF-Writeups/main/images/include-this2.png)

By adjusting the parameter value to traverse four directories up and access "flag.txt," I successfully retrieved the flag.

![image3](https://raw.githubusercontent.com/N3agu/CTF-Writeups/main/images/include-this3.png)

## rubies (Web)
After starting the service, I decoded the page name from base64, revealing "rails." I saw a large Ruby logo in the middle of the page. After looking up the terms on Google, I found that Ruby on Rails is a server-side web application framework written in Ruby. The image had a link (href attribute) that redirected to /vuln. I found information about a possible vulnerability [CVE-2019-5418](https://nvd.nist.gov/vuln/detail/CVE-2019-5418) ([POC](https://github.com/mpgn/CVE-2019-5418)) online and started testing.

![image4](https://raw.githubusercontent.com/N3agu/CTFs/main/images/rubies.png)

The flag was located in /home/gem/flag.txt: CTF{c5547baa6ce135850b3a728d442925f1ae63f2bf22301676282958a0ce5fae59}.

## this-file-hides-something (Forensics)
After extracting the zip file, I obtained 'crashdump.elf.' From the description, I understood that I should search for a password within the crash dump, and that the flag format is non-standard.

I decided to run volatility to extract the lsa secrets using: `vol -f crashdump.elf windows.lsadump.Lsadump` and got the flag: "DefaultPassword: Str0ngAsAR0ck!"

## spy-agency (Forensics)
After extracting the zip file, I obtained 'crashdump.elf.' From the description, I understood that I should search for an application within the crash dump, and that the flag format is ctf{sha256(location name from coordinates in lowercase)}.

I decided to run a filescan through volatility using `vol -f spyagency3.bin windows.filescan.FileScan` and found an interesting file called "app-release.apk.zip" at offset `0x3fefb8c0`.

I used `vol -f spyagency3.bin windows.dumpfiles.DumpFiles --physaddr 0x3fefb8c0` to extract the zip file from the dump.

After that, I've unzipped the archive and managed to find `app-release.apk/app-release/res/drawable/coordinates_can_be_found_here.jpg`. Used  `exiftool coordinates_can_be_found_here.jpg` and got the coordinates: "-coordinates=44.44672703736637, 26.098652847616506"

Pasting the coordinates on [google maps](https://www.google.com/maps/place/44%C2%B026'48.2%22N+26%C2%B005'55.2%22E/@44.4467332,26.0983283,20z/data=!4m4!3m3!8m2!3d44.446727!4d26.0986528?entry=ttu) we get the location, which is "Pizza Hut". The flag is ctf{sha256(pizzahut)} | ctf{a939311a5c5be93e7a93d907ac4c22adb23ce45c39b8bfe2a26fb0d493521c4f}

## linux-recovery (Misc)
The challenge contains two files: a UPX-packed executable called "chess" and a password-protected .rar archive. Running the "chess" executable launches a tic-tac-toe game in our terminal. We can win the game by playing in the following positions: 1, 8, 3, 5, 2. Upon winning, the application responds with `Congrats, the secret message is 347774197377. Please read this note: VIC, you should like straddles and checkerboards: KCSLQMYOPHTZUBVAFJXGERIWDNSS 3 7`. Based on the name, I thought about the [VIC cipher](https://www.dcode.fr/vic-cipher). Using the cipher "347774197377," the alphabet "KCSLQMYOPHTZUBVAFJXGERIWDNSS," and 3 & 7 for the spare positions, I obtained the password "unicorn".

Entering "unicorn" as the secret message returns "$sdfg3e4", which is the password for the .rar archive. After using this password to extract the .rar archive, we get "logs.txt". Running strings `logs.txt | grep -i "ctf"` gives us: CTF{socskc-343fs-fefewvsw}.

## access-vip-only (Forensics)
After extracting the archive, we get "access-only-vip.bin", which is a memory dump. I used `vol -f access-only-vip.bin windows.filescan.FileScan` to scan for files. The description mentioned that the "malicious employee" looked at something on the internet, so I decided to dump the Google history with `vol -f access-only-vip.bin windows.dumpfiles.DumpFiles --physaddr 0x7dc4f570`. Searching through the history, I found two interesting links: https://pastebin.pl/view/29088365 and https://pastebin.pl/view/9c63cf9c, which contained the password: "poiuytrewq".

Looking again through the file scan, I saw "flag.rar" and dumped it with the command `vol -f access-only-vip.bin windows.dumpfiles.DumpFiles --physaddr 0x7ee72200`. I renamed it to flag.rar with `mv file.0x7ee72200.0xfa8001c6ac90.DataSectionObject.flag.ra.dat flag.rar` and used the password "poiuytrewq" to extract it. This gave me "win.txt" with the contents "B8FA9EFBC8C8F043AFCA1B60F8F4C5245C54B5FF5BFB0603A71071F66C1EF295". The flag was: CTF{B8FA9EFBC8C8F043AFCA1B60F8F4C5245C54B5FF5BFB0603A71071F66C1EF295}.

## unconditional (Reverse)
I decompiled the binary (ELF 32-bit) in IDA and obtained the following snippet of code:
```cpp
v9 = *argv;
if ((*argv)[28] == 114 && v9[10] + v9[20] == 156 && v9[36] + v9[7] == 142 && v9[23] + v9[42] == 146 && v9[10] >> 1 == 55 && v9[22] == 101 && *v9 == 67 && (v9[31] ^ 0xA) == v9[22] && v9[40] == 45 && v9[3] - v9[2] == 53 && v9[4] << 7 == 11392 && 2 * v9[3] == 246 && 2 * v9[33] == 90 && v9[34] << (v9[9] % 8) == 7360 && v9[30] == 121 && v9[5] == 111 && v9[33] << (v9[23] % 8) == 1440 && v9[43] == 125 && v9[16] >> (v9[41] % 8) == 1 && v9[22] == 101 && v9[33] >> (v9[37] % 8) == 11 && v9[9] >> (v9[29] % 8) == 3 && v9[34] >> 1 == 57 && v9[18] == 111 && v9[38] + v9[25] == 155 && v9[39] >> 2 == 13 && (v9[25] ^ 0x13) == v9[6] && v9[28] == 114 && 4 * v9[3] == 492 && (v9[34] ^ 0x16) == v9[22] && v9[24] + v9[31] == 208 && v9[18] << (v9[36] % 8) == 222 && v9[29] << (v9[31] % 8) == 5760 && (v9[11] ^ 0xA) == v9[43] && (v9[24] ^ 0x4C) == v9[29] && v9[34] == 115 && v9[7] == 45 && v9[23] == v9[12] && v9[26] - v9[39] == 62 && v9[6] >> (v9[30] % 8) == 58 && v9[36] + v9[21] == 206 && v9[28] == 114 && *v9 + v9[9] == 177 && v9[8] >> (v9[38] % 8) == 3 && *v9 >> 1 == 33 && v9[22] + v9[15] == 218 && v9[18] << (v9[42] % 8) == 3552 && 32 * v9[12] == 1440 && v9[35] == 104 && v9[4] == 89 && v9[5] >> (v9[16] % 8) == 3 && v9[21] == 109 && v9[14] == 111 && v9[34] == 115 && v9[36] == 97 && v9[13] == 121 && v9[29] == 45 && v9[30] == 121 && v9[22] >> (v9[25] % 8) == 1 && v9[23] == 45 && v9[6] == 117 && v9[24] == 97 && v9[11] << 6 == 7616 && v9[40] << (v9[37] % 8) == 180 && (v9[38] ^ 0x48) == v9[43] && v9[8] << (v9[40] % 8) == 3424 && 8 * v9[26] == 928 && v9[17] == 103 && !(v9[29] >> (v9[14] % 8)) && v9[27] == 101 && v9[39] - v9[7] == 9 && v9[26] == 116 && v9[5] + v9[28] == 225 && v9[15] + v9[35] == 221 && v9[33] + v9[26] == 161 && (v9[39] ^ 0x4F) == v9[13] && 2 * v9[39] == 108 && 8 * v9[1] == 672 && v9[24] >> (v9[35] % 8) == 97 && v9[1] >> (v9[16] % 8) == 2 && v9[4] + v9[15] == 206 && 2 * v9[39] == 108 && v9[5] == 111 && v9[19] >> 4 == 7 && v9[34] >> (v9[28] % 8) == 28 && v9[31] == 111 && v9[16] == 45 && v9[37] + v9[9] == 160 && v9[30] >> (v9[2] % 8) == 1 && v9[26] >> (v9[41] % 8) == 3 && *v9 - v9[5] == -44 && v9[5] == 111 && v9[9] << (v9[23] % 8) == 3520 && v9[34] == 115 && v9[37] + v9[32] == 167 && v9[26] - v9[28] == 2 && v9[13] >> (v9[20] % 8) == 3 && 8 * v9[23] == 360 && v9[35] == 104 && v9[5] + v9[31] == 222 && !(v9[40] >> (v9[14] % 8)) && (v9[11] ^ 0x1A) == v9[41] && v9[17] >> 3 == 12 && v9[28] << (v9[15] % 8) == 3648 && v9[27] - v9[37] == 51 && v9[9] == 110 && 4 * v9[1] == 336 && v9[15] >> 6 == 1 && v9[6] << (v9[28] % 8) == 468 && v9[33] == 45 && v9[3] == 123 && v9[35] - v9[31] == -7 && v9[39] + v9[24] == 151 && v9[12] - v9[26] == -71 && v9[11] - v9[22] == 18 && v9[33] + v9[25] == 147 && v9[2] == 70 && v9[29] - v9[37] == -5 && 32 * v9[36] == 3104 && v9[7] << (v9[21] % 8) == 1440 && (v9[8] ^ 0x1F) == v9[26] && v9[22] == 101 && v9[38] == 53 && v9[29] == 45 && v9[23] - v9[4] == -44 && v9[35] - v9[12] == 59 && v9[17] - v9[39] == 49 && *v9 == 67 && v9[5] >> (v9[15] % 8) == 3 && v9[27] == 101 && v9[32] == 117 && v9[18] << (v9[40] % 8) == 3552 && v9[20] << (v9[1] % 8) == 720 && v9[18] << (v9[26] % 8) == 1776 && v9[10] >> (v9[25] % 8) == 1 && v9[8] == 107 && v9[14] >> (v9[4] % 8) == 55 && v9[31] - v9[17] == 8 && v9[24] - v9[11] == -22 && v9[31] + v9[23] == 156 && v9[23] - v9[24] == -52 && v9[12] >> (v9[7] % 8) == 1 && v9[35] >> (v9[35] % 8) == 104 && v9[4] >> (v9[1] % 8) == 720 && v9[18] << (v9[26] % 8) == 1776 && v9[10] >> (v9[25] % 8) == 1 && v9[8] == 107 && v9[14] >> (v9[4] % 8) == 55 && v9[31] - v9[17] == 8 && v9[24] - v9[11] == -22 && v9[31] + v9[23] == 156 && v9[23] - v9[24] == -52 && v9[12] >> (v9[7] % 8) == 1 && v9[35] >> (v9[35] % 8) == 104 && v9[4] >> (v9[1] % 8) == 5 && v9[18] >= 0 && v9[22] >> (v9[41] % 8) == 3 && v9[12] >> (v9[34] % 8) == 5 && v9[40] == 45 && v9[18] == 111 && v9[8] == 107 && v9[39] - v9[41] == -55 && v9[34] << 6 == 7360 && v9[36] >> (v9[8] % 8) == 12 && 32 * v9[6] == 3744 && !(v9[20] >> (v9[18] % 8)) && v9[5] >> 4 == 6 && (v9[23] ^ 0x48) == v9[22] && 4 * v9[6] == 468 && v9[25] - v9[40] == 57 && v9[39] == 54 && v9[29] == 45 && v9[26] == 116 && 32 * v9[11] == 3808 && v9[30] == 121 && v9[22] == v9[27] && v9[14] << (v9[23] % 8) == 3552 && v9[25] >> 4 == 6 && v9[7] + v9[18] == 156 && v9[13] >> 1 == 60 && v9[36] == 97 && v9[18] + v9[21] == 220 && v9[42] - v9[7] == 56 && 4 * v9[15] == 468 && v9[25] == 102 && v9[38] >> (v9[8] % 8) == 6 && v9[41] >> (v9[35] % 8) == 109 && v9[11] - v9[2] == 49 && (v9[24] ^ 0xC) == v9[41] && 8 * v9[34] == 920 && v9[28] == 114 && v9[38] >> (v9[20] % 8) == 1 && v9[35] >> (v9[39] % 8) == 1 && (v9[40] ^ 0x6E) == *v9 && v9[14] << (v9[26] % 8) == 1776 && v9[29] >> (v9[21] % 8) == 1 && v9[19] + v9[18] == 227) {
    printf("Happy Hacking!", v4, v5, v6, v7, v8, argv, argc);
  }
```
Reversing the operations gives you: CTF{You-know-you-got-me-after-you-sha256-me}, and the flag is CTF{e60100e9b047ca672947fdae0f114b3b052d33955c81b6df767843a4ffde439e}.
