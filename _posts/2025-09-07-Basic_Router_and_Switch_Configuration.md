---
title: Basic Router and Switch Configuration - Step by Step
date: 2025-09-07 18:47:58 +01:00
categories: [Networking, Networking Basics]
tags: [Networking, Networking Basics] # TAG names should always be lowercase
---

Getting a router or a switch up and running might seem intimidating at first, but once you know the commands and their purpose, it’s pretty straightforward. Let’s go through a **detailed basic configuration**, explaining each step clearly.

## 1. Accessing the Router

### Privileged Mode

To start configuring a router, you need to enter **privileged mode**:

```
Router> enable
```

This gives you access to commands that can change the device configuration.

### Global Configuration Mode

Once in privileged mode, enter **global configuration mode** to make changes to the router:

```
Router# configure terminal
```

Now you can configure interfaces, passwords, hostnames, and more.

### Saving the Configuration

After making changes, save the running configuration to avoid losing it after a reboot:

```
Router# copy running-config startup-config
```

* `running-config` = current settings in RAM
* `startup-config` = saved settings in NVRAM

Check the saved configuration with:

```
Router# show startup-config
```

## 2. Setting the Hostname

Set a meaningful name for your router to identify it on the network:

```
Router(config)# hostname Router1
```

## 3. Configuring Passwords

### Enable Password (Unencrypted)

```
Router(config)# enable password cisco1234
```

### Enable Secret (Encrypted)

```
Router(config)# enable secret cisco1234
```

### Encrypt All Passwords

```
Router(config)# service password-encryption
```

## 4. Banner Message

Set a **login banner** to warn unauthorized users:

```
Router(config)# banner motd # Authorized Access Only #
```

## 5. Disabling DNS Lookup

To prevent the router from trying to resolve mistyped commands as domain names:

```
Router(config)# no ip domain-lookup
```

## 6. Configuring Console and VTY Access

### Console Access

```
Router(config)# line console 0
Router(config-line)# password cisco1234
Router(config-line)# login
Router(config-line)# logging synchronous
```

* `logging synchronous` prevents console messages from messing up your typing.

### VTY (Telnet/SSH) Access

```
Router(config)# line vty 0 4
Router(config-line)# password cisco1234
Router(config-line)# login
```

## 7. Configuring Interfaces

### Basic IP Configuration

```
Router(config)# interface [type] [port]
Router(config-if)# ip address [IP address] [subnet mask]
Router(config-if)# no shutdown
Router(config-if)# exit
```

* `show ip interface` to verify IP assignment and interface status.

Example:

```
Router(config)# interface gigabitEthernet 0/0
Router(config-if)# ip address 192.168.1.1 255.255.255.0
Router(config-if)# no shutdown
Router(config-if)# exit
```

## 8. Switch MAC Address Configuration

### Display MAC Table

```
Switch# show mac address-table
```

### Static MAC Assignment

```
Switch(config)# mac address-table static [MAC] vlan [VLAN] interface [port]
```

Example:

```
Switch(config)# mac address-table static 00:1A:2B:3C:4D:5E vlan 1 interface f2/1
```

## 9. Switch Port Security

### Enable Port Security

```
Switch(config-if)# switchport port-security
```

### Limit Maximum MAC Addresses

```
Switch(config-if)# switchport port-security maximum [n]
```

### Sticky MAC Address

```
Switch(config-if)# switchport port-security mac-address sticky [MAC]
```

### Security Violation Action

```
Switch(config-if)# switchport port-security violation shutdown
```

This ensures that unauthorized devices cannot connect to the switch port.

## 10. Verifying Configuration

Use these commands to check your work:

* **Check interfaces and IPs**:

```
Router# show ip interface brief
```

* **Check running configuration**:

```
Router# show running-config
```

* **Check switch MAC addresses**:

```
Switch# show mac address-table
```

This covers **all the essential commands for basic router and switch setup**, with explanations so you understand not just what to type, but why it matters.
