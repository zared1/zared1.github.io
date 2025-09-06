---
title: Ports and Services
date: 2025-09-06 13:00:58 +01:00
categories: [Networking,Networking Basics]
tags: [Networking,Networking Basics] # TAG names should always be lowercase
---

## What Is a Port?

* A **port** is just a number ranging from **0 to 65535**.
* It works with the **transport layer protocols** (TCP and UDP) to identify services.
* Think of it like:

  * **IP address** = the house address.
  * **Port number** = the specific room or person inside.

## Types of Ports

* **Well-Known Ports (0–1023)**: Assigned to core services (HTTP, SSH, DNS, etc.).
* **Registered Ports (1024–49151)**: Used by software/apps (like databases, game servers).
* **Dynamic/Ephemeral Ports (49152–65535)**: Temporary ports picked by your OS for client connections.

## The Big Services and Their Ports

Here’s the reference list of the most important (and most asked about) services and their default ports:

| Port    | Protocol | Service                                  |
| ------- | -------- | ---------------------------------------- |
| 20      | TCP      | FTP Data Transfer                        |
| 21      | TCP      | FTP Control                              |
| 22      | TCP      | SSH (Secure Shell)                       |
| 23      | TCP      | Telnet                                   |
| 25      | TCP      | SMTP (Simple Mail Transfer Protocol)     |
| 53      | TCP/UDP  | DNS (Domain Name System)                 |
| 67      | UDP      | DHCP Server                              |
| 68      | UDP      | DHCP Client                              |
| 69      | UDP      | TFTP (Trivial File Transfer Protocol)    |
| 80      | TCP      | HTTP (Hypertext Transfer Protocol)       |
| 110     | TCP      | POP3 (Post Office Protocol v3)           |
| 119     | TCP      | NNTP (Network News Transfer Protocol)    |
| 123     | UDP      | NTP (Network Time Protocol)              |
| 135     | TCP      | RPC (Microsoft RPC)                      |
| 137-139 | TCP/UDP  | NetBIOS                                  |
| 143     | TCP      | IMAP                                     |
| 161     | UDP      | SNMP                                     |
| 162     | UDP      | SNMP Trap                                |
| 179     | TCP      | BGP (Border Gateway Protocol)            |
| 194     | TCP      | IRC (Internet Relay Chat)                |
| 389     | TCP/UDP  | LDAP                                     |
| 443     | TCP      | HTTPS (Secure HTTP)                      |
| 445     | TCP      | Microsoft SMB/CIFS                       |
| 465     | TCP      | SMTPS (SMTP over SSL)                    |
| 514     | UDP      | Syslog                                   |
| 515     | TCP      | LPD (Line Printer Daemon)                |
| 520     | UDP      | RIP (Routing Information Protocol)       |
| 546     | UDP      | DHCPv6 Client                            |
| 547     | UDP      | DHCPv6 Server                            |
| 587     | TCP      | SMTP (Mail Submission)                   |
| 636     | TCP      | LDAPS (LDAP over SSL)                    |
| 873     | TCP      | Rsync                                    |
| 993     | TCP      | IMAPS (IMAP over SSL)                    |
| 995     | TCP      | POP3S (POP3 over SSL)                    |
| 1080    | TCP      | SOCKS Proxy                              |
| 1433    | TCP      | Microsoft SQL Server                     |
| 1521    | TCP      | Oracle Database                          |
| 1701    | UDP      | L2TP (Layer 2 Tunneling Protocol)        |
| 1723    | TCP      | PPTP (Point-to-Point Tunneling Protocol) |
| 1812    | UDP      | RADIUS Authentication                    |
| 1813    | UDP      | RADIUS Accounting                        |
| 2049    | TCP/UDP  | NFS (Network File System)                |
| 2082    | TCP      | cPanel                                   |
| 2083    | TCP      | cPanel (SSL)                             |
| 2181    | TCP      | Apache Zookeeper                         |
| 2222    | TCP      | DirectAdmin                              |
| 2375    | TCP      | Docker REST API (Unencrypted)            |
| 2376    | TCP      | Docker REST API (SSL)                    |
| 2483    | TCP      | Oracle DB (unsecure)                     |
| 2484    | TCP      | Oracle DB (SSL)                          |
| 3306    | TCP      | MySQL                                    |
| 3389    | TCP      | RDP (Remote Desktop Protocol)            |
| 3690    | TCP      | Subversion (SVN)                         |
| 4000    | TCP/UDP  | ICQ                                      |
| 4369    | TCP      | Erlang Port Mapper                       |
| 5000    | TCP      | UPnP / Flask Dev Server                  |
| 5060    | TCP/UDP  | SIP (VoIP Signaling)                     |
| 5432    | TCP      | PostgreSQL                               |
| 5631    | TCP      | pcAnywhere (data)                        |
| 5632    | UDP      | pcAnywhere (status)                      |
| 5900    | TCP      | VNC (Virtual Network Computing)          |
| 5984    | TCP      | CouchDB                                  |
| 6379    | TCP      | Redis                                    |
| 6667    | TCP      | IRC                                      |
| 8000    | TCP      | Common Web Servers / Proxy               |
| 8080    | TCP      | HTTP Proxy / Alternate Web               |
| 8443    | TCP      | HTTPS Alternate                          |
| 8888    | TCP      | Alternate Web Services                   |
| 9200    | TCP      | Elasticsearch                            |
| 11211   | TCP/UDP  | Memcached                                |

*(This list can keep going, but these are the ones you’ll run into the most in networking, pentesting, and sysadmin work.)*

## Why Does This Matter?

* **Troubleshooting** → If a service isn’t working, check if the right port is open.
* **Security** → Attackers scan ports to find weak spots (hello, Nmap).
* **Configuration** → Firewalls, NAT, and routers need correct port rules.
