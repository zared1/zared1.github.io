---
title: Usefull Commands
date: 2025-09-06 15:47:58 +01:00
categories: [System Administration, Linux]
tags: [System Administration, Linux] # TAG names should always be lowercase
---

## System

```bash
uname -a     # Display Linux System Information
uname -r     # Display kernel release information
uptime       # Show how long the system has been running + load
hostname     # Show system host name
hostname -i  # Display the IP address of the host
last reboot  # Show system reboot history
date         # Show the current date and time
cal          # Show this month calendar
w            # Display who is online
whoami       # Who you are logged in as
finger user  # Display information about user
```

## File Permission Related

```bash
chmod octal file-name    # Change the permissions of the file to octal
# Example
chmod 777 /data/test.c   # Set rwx permission for owner, group, world
chmod 775 /data/test.c   # Set rwx permission for owner, rx for group and world
chown owner-user file    # Change owner of the file
chown owner-user:owner-group file-name  # Change owner and group owner of the file
chown owner-user:owner-group directory/ # Change owner and group owner of the directory
```

## Hardware

```bash
dmesg                 # Detected hardware and boot messages
cat /proc/cpuinfo     # CPU Model
cat /proc/meminfo     # Hardware memory
cat /proc/interrupts  # Lists the number of interrupts per CPU per I/O device
lshw                  # Displays information on hardware configuration of the system
lsblk                 # Displays block device related information in Linux
free -m               # Used and free memory (-m for MB)
lspci -tv             # Show PCI Devices
lsusb -tv             # Show USB Devices
dmidecode             # Show hardware info from the BIOS
hdparm -i /dev/sda    # Show info about disk sda
hdparm -tT /dev/sda   # Do a read speed test on disk sda
badblocks -s /dev/sda # Test for unreadable blocks on disk sda
```

## Network

```bash
ip address add 192.168.0.1 dev eth0 # Set an IP Address
ip addr show     # Display all network interfaces and ip address
ethtool eth0     # Linux tool to show ethernet status
mii-tool eth0    # Linux tool to show ethernet status
ping host        # Send echo request to test connection
whois domain     # Get Whois information for domain
dig domain       # Get DNS information for domain
dig -x host      # Reverse lookup host
host google.com  # Lookup DNS IP address for the name
hostname -i      # Lookup local IP address
wget file        # Download file
netstat -tupl    # List all active listening ports
```

## Users

```bash
id                   # Show the active user id with login and group
last                 # Show last logins on the system
who                  # Show who is logged on the system
groupadd admin       # Add group "admin"
useradd \            # Create user "sam"
  -c "Sam Tomshi" \
  -g admin -m sam
userdel sam          # Delete user sam
adduser sam          # Add user "sam"
usermod              # Modify user information
```

## Compression / Archives

```bash
tar cf home.tar home        # Create tar named home.tar containing home/
tar xf file.tar             # Extract the files from file.tar
tar czf file.tar.gz files   # Create a tar with gzip compression
gzip file                   # Compress file and renames it to file.gz
```

## Install Package

```bash
rpm -i pkgname.rpm  # Install RPM based package
rpm -e pkgname      # Remove package
```

## Install from Source

```bash
./configure
make
make install
```

## File Commands

```bash
ls -al                # Display all information about files/directories
pwd                   # Show the path of the current directory
mkdir directory-name  # Create a directory
rm file-name          # Delete a file
rm -r directory-name  # Delete a directory recursively
rm -f file-name       # Forcefully remove a file
rm -rf directory-name # Forecfully remove the directory structure
cp file1 file2        # Copy file1 to file2
cp -r dir1 dir2       # Copy dir1 to dir2, create dir2 if it doesn't exist
mv file1 file2        # Rename source to dest / move the source to directory
ln -s /path/to/file-name link-name # Create a symbolic link to file-name
touch file            # Create or update a file
cat > file            # Place standard input into a file
more file             # Output contents of a file
head file             # Output first 10 lines of a file
tail file             # Output last 10 lines of a file
tail -f file          # Output the contents of file as it grows starting with the last 10 lines
gpg -c file           # Encrypt file
gpg file.gpg          # Decrypt file
wc                    # Print the number of bytes, words, and lines of a file
xargs                 # Execute command lines from standard input
```

## Search

```bash
grep pattern file              # Search for patterns in a file
grep -r pattern dir/           # Search recursively for a pattern in a dir
locate file                    # Find all instances of a file
find /home/tom -name 'index*'  # Find file names that start with index
find /home -size +10000k       # Find files larger than 10000k in /home
```

## Login (SSH and Telnet)

```bash
ssh user@host          # Connect to a host as a user
ssh -p port user@host  # Connect to a host using a specific port
telnet host            # Connect to the system using telnet port
```

## File Transfer

```bash
# scp
scp file.txt server2:/tmp    # Secure copy file.txt to remote host /tmp folder

# rsync
rsync -a /home/apps /backup/ # Sync source to destination
```

## Process Related

```bash
ps                      # Display your currently active process
ps aux | grep 'telnet'  # Find all process id's related to telnet process
pmap                    # Memory map of process
top                     # Display all running processes
kill pid                # Kill process with mentioned pid id
killall proc            # Kill all processes named proc
pkill process-name      # Send signal to a process with its name
bg                      # Resumes suspended jobs without bringing them to the foreground
fg                      # Brings the most recent job to the foreground
fg n                    # Brings job n to the foreground
```

## Disk Usage

```bash
df -h      # Show free space on mounted filesystems
df -i      # Show free inodes on mounted filesystems
fdish -l   # Show disk partition sizes and types
du -ah     # Display disk usage in human readable form
du -sh     # Display total disk usage on the current directory
findmnt    # Displays target mount point for all filesystems
mount device-path mount-point # Mount a device
```

## Directory Traversal

```bash
cd ..    # Go up one level of the directory tree
cd       # Go to the $HOME directory
cd /test # Change to the /test directory
```
