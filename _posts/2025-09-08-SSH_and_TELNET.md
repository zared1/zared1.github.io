---
title: SSH and Telnet - Remote Access Protocols
date: 2025-09-08 11:30:58 +01:00
categories: [Networking,Networking Protocols]
tags: [Networking,Networking Protocols] # TAG names should always be lowercase
---

Managing network devices often requires **remote access**. Instead of physically plugging into the console port of a router or switch, administrators can connect remotely using **Telnet** or **SSH (Secure Shell)**. Both provide a **command-line interface (CLI)** over a network, but their security and usage differ greatly.

## Telnet: The Old-School Remote Access Protocol

### Definition

**Telnet (TELecommunication NETwork)** is one of the earliest remote login protocols, created in the late 1960s. It allows users to open a **terminal session** with another device over TCP/IP.

### Characteristics

* **Port number**: TCP **23**
* **Transport**: Sends data, including usernames and passwords, in **plain text** (unencrypted).
* **Function**: Provides command-line access to network devices.
* **Supported devices**: Routers, switches, servers, firewalls, and even legacy UNIX systems.

### Weaknesses of Telnet

The biggest drawback is **no encryption**. Anyone with a packet sniffer (like Wireshark) can capture Telnet traffic and immediately read sensitive data, including credentials.

Example: if you log in with `admin / cisco123`, that information is visible on the wire.

### Why Telnet Is Still Studied

Even though it’s insecure, Telnet is:

* Still used in labs for **educational purposes**.
* Useful for quick connectivity tests (e.g., `telnet ip port`).
* Found in some **legacy systems** that cannot be upgraded.

## SSH: The Secure Alternative

### Definition

**SSH (Secure Shell)** was developed in 1995 to replace Telnet. It provides the same functionality (remote CLI access) but adds **encryption and authentication**.

### Characteristics

* **Port number**: TCP **22**
* **Transport**: Encrypts all traffic (usernames, passwords, commands, output).
* **Function**: Secure remote administration of devices and servers.
* **Supported devices**: Cisco routers/switches, Linux/UNIX systems, Windows servers (with OpenSSH).

### Security Features of SSH

1. **Encryption**: Protects against eavesdropping.
2. **Integrity**: Detects if data is altered in transit.
3. **Authentication**: Verifies both client and server.
4. **Key Management**: Uses RSA or newer algorithms for secure sessions.

### Why SSH Is the Standard Today

* Protects sensitive data.
* Meets compliance requirements (HIPAA, PCI DSS, ISO 27001).
* Universally supported.

## Telnet vs SSH: Comparison

| Feature        | Telnet                   | SSH                           |
| -------------- | ------------------------ | ----------------------------- |
| Port           | 23                       | 22                            |
| Encryption     | None (clear text)        | Full (encrypted)              |
| Security       | Vulnerable to sniffing   | Strong, industry standard     |
| Usage Today    | Rare, legacy, labs only  | Default for remote management |
| Authentication | Username + password only | Password, keys, certificates  |

In short: **Telnet = old, insecure. SSH = modern, secure.**

## Cisco Router Configuration: Telnet

Even though Telnet is insecure, here’s how to configure it on Cisco IOS (for practice only).

```bash
Router> enable
Router# configure terminal

! Create a local user
Router(config)# username ISTA password cisco1234

! Enable VTY lines (virtual terminals)
Router(config)# line vty 0 4
Router(config-line)# password cisco1234
Router(config-line)# login
Router(config-line)# transport input telnet
```

Now you can connect with:

```
telnet <router-ip>
```

Keep in mind: this connection is **not encrypted**.

## Cisco Router Configuration: SSH

SSH configuration takes a bit more work but is much safer.

```bash
Router> enable
Router# configure terminal

! Step 1: Create a local user
Router(config)# username ISTA password cisco1234

! Step 2: Define a domain name (required for key generation)
Router(config)# ip domain-name ista.local

! Step 3: Generate RSA keys (used for encryption)
Router(config)# crypto key generate rsa
How many bits in the modulus [512]: 1024

! Step 4: Enable SSH version 2
Router(config)# ip ssh version 2

! Step 5: Configure VTY lines for SSH
Router(config)# line vty 0 4
Router(config-line)# login local
Router(config-line)# transport input ssh
```

Now you can connect with:

```
ssh ISTA@<router-ip>
```

## Useful SSH Commands

* Show SSH information:

  ```bash
  Router# show ip ssh
  ```

  Displays version, authentication retries, and status.

* Debug SSH connections:

  ```bash
  Router# debug ip ssh
  ```

* Clear SSH sessions:

  ```bash
  Router# clear line vty 0
  ```

## Security Best Practices

When configuring SSH on production networks, follow these guidelines:

* Use **SSH version 2** (never v1).
* Use **RSA keys with 2048 bits or higher**.
* Prefer **key-based authentication** over passwords.
* Disable Telnet completely:

  ```bash
  Router(config)# line vty 0 4
  Router(config-line)# transport input ssh
  ```
* Restrict access with **ACLs** so only admins can connect.

## Real-World Use Cases

* **Network Engineers**: Remotely manage routers and switches securely.
* **System Administrators**: Connect to Linux/UNIX servers for updates and monitoring.
* **Developers**: Use SSH tunnels for secure communication.
* **Cloud Providers**: Default access method for AWS EC2, Azure VMs, etc.

