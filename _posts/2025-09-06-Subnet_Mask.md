---
title: Subnet Mask
date: 2025-09-06 11:47:58 +01:00
categories: [Networking,Networking Basics]
tags: [Networking,Networking Basics] # TAG names should always be lowercase
---

## What Is a Subnet Mask?

A **subnet mask** is a 32-bit number that works hand in hand with an IP address. It tells the computer which part of the IP is the **network portion** and which part is the **host portion**.

* **Network portion**: Identifies the specific network.
* **Host portion**: Identifies a device within that network.

Think of it like a postal system: the **network portion** is the city, and the **host portion** is the street address.

## How Does It Work?

Every bit in the subnet mask is either:

* `1` → means *network bit*.
* `0` → means *host bit*.

Example:

```
IP Address:   192.168.1.10
Subnet Mask:  255.255.255.0
```

* `255.255.255.0` in binary is: `11111111.11111111.11111111.00000000`
* First 24 bits are **network** → `192.168.1`
* Last 8 bits are **host** → `.10`

So all devices with IPs like `192.168.1.x` are on the same network.

## CIDR Notation

Instead of writing `255.255.255.0`, we often write `/24`. The number after the slash just tells us how many bits are set to 1.

* `/24` → 24 bits network, 8 bits host.
* `/16` → 16 bits network, 16 bits host.

So `/24` is the same as `255.255.255.0`.

## Full Subnet Mask Table

| Prefix | Subnet Mask     | Binary Mask                         | Wildcard        | Total Addresses | Usable Hosts\*               | Block Size      |
| -----: | --------------- | ----------------------------------- | --------------- | --------------- | ---------------------------- | --------------- |
|     /0 | 0.0.0.0         | 00000000.00000000.00000000.00000000 | 255.255.255.255 | 4,294,967,296   | 4,294,967,294                | 256 (1st octet) |
|     /1 | 128.0.0.0       | 10000000.00000000.00000000.00000000 | 127.255.255.255 | 2,147,483,648   | 2,147,483,646                | 128 (1st octet) |
|     /2 | 192.0.0.0       | 11000000.00000000.00000000.00000000 | 63.255.255.255  | 1,073,741,824   | 1,073,741,822                | 64 (1st octet)  |
|     /3 | 224.0.0.0       | 11100000.00000000.00000000.00000000 | 31.255.255.255  | 536,870,912     | 536,870,910                  | 32 (1st octet)  |
|     /4 | 240.0.0.0       | 11110000.00000000.00000000.00000000 | 15.255.255.255  | 268,435,456     | 268,435,454                  | 16 (1st octet)  |
|     /5 | 248.0.0.0       | 11111000.00000000.00000000.00000000 | 7.255.255.255   | 134,217,728     | 134,217,726                  | 8 (1st octet)   |
|     /6 | 252.0.0.0       | 11111100.00000000.00000000.00000000 | 3.255.255.255   | 67,108,864      | 67,108,862                   | 4 (1st octet)   |
|     /7 | 254.0.0.0       | 11111110.00000000.00000000.00000000 | 1.255.255.255   | 33,554,432      | 33,554,430                   | 2 (1st octet)   |
|     /8 | 255.0.0.0       | 11111111.00000000.00000000.00000000 | 0.255.255.255   | 16,777,216      | 16,777,214                   | 1 (1st octet)   |
|     /9 | 255.128.0.0     | 11111111.10000000.00000000.00000000 | 0.127.255.255   | 8,388,608       | 8,388,606                    | 128 (2nd octet) |
|    /10 | 255.192.0.0     | 11111111.11000000.00000000.00000000 | 0.63.255.255    | 4,194,304       | 4,194,302                    | 64 (2nd octet)  |
|    /11 | 255.224.0.0     | 11111111.11100000.00000000.00000000 | 0.31.255.255    | 2,097,152       | 2,097,150                    | 32 (2nd octet)  |
|    /12 | 255.240.0.0     | 11111111.11110000.00000000.00000000 | 0.15.255.255    | 1,048,576       | 1,048,574                    | 16 (2nd octet)  |
|    /13 | 255.248.0.0     | 11111111.11111000.00000000.00000000 | 0.7.255.255     | 524,288         | 524,286                      | 8 (2nd octet)   |
|    /14 | 255.252.0.0     | 11111111.11111100.00000000.00000000 | 0.3.255.255     | 262,144         | 262,142                      | 4 (2nd octet)   |
|    /15 | 255.254.0.0     | 11111111.11111110.00000000.00000000 | 0.1.255.255     | 131,072         | 131,070                      | 2 (2nd octet)   |
|    /16 | 255.255.0.0     | 11111111.11111111.00000000.00000000 | 0.0.255.255     | 65,536          | 65,534                       | 1 (2nd octet)   |
|    /17 | 255.255.128.0   | 11111111.11111111.10000000.00000000 | 0.0.127.255     | 32,768          | 32,766                       | 128 (3rd octet) |
|    /18 | 255.255.192.0   | 11111111.11111111.11000000.00000000 | 0.0.63.255      | 16,384          | 16,382                       | 64 (3rd octet)  |
|    /19 | 255.255.224.0   | 11111111.11111111.11100000.00000000 | 0.0.31.255      | 8,192           | 8,190                        | 32 (3rd octet)  |
|    /20 | 255.255.240.0   | 11111111.11111111.11110000.00000000 | 0.0.15.255      | 4,096           | 4,094                        | 16 (3rd octet)  |
|    /21 | 255.255.248.0   | 11111111.11111111.11111000.00000000 | 0.0.7.255       | 2,048           | 2,046                        | 8 (3rd octet)   |
|    /22 | 255.255.252.0   | 11111111.11111111.11111100.00000000 | 0.0.3.255       | 1,024           | 1,022                        | 4 (3rd octet)   |
|    /23 | 255.255.254.0   | 11111111.11111111.11111110.00000000 | 0.0.1.255       | 512             | 510                          | 2 (3rd octet)   |
|    /24 | 255.255.255.0   | 11111111.11111111.11111111.00000000 | 0.0.0.255       | 256             | 254                          | 1 (3rd octet)   |
|    /25 | 255.255.255.128 | 11111111.11111111.11111111.10000000 | 0.0.0.127       | 128             | 126                          | 128 (4th octet) |
|    /26 | 255.255.255.192 | 11111111.11111111.11111111.11000000 | 0.0.0.63        | 64              | 62                           | 64 (4th octet)  |
|    /27 | 255.255.255.224 | 11111111.11111111.11111111.11100000 | 0.0.0.31        | 32              | 30                           | 32 (4th octet)  |
|    /28 | 255.255.255.240 | 11111111.11111111.11111111.11110000 | 0.0.0.15        | 16              | 14                           | 16 (4th octet)  |
|    /29 | 255.255.255.248 | 11111111.11111111.11111111.11111000 | 0.0.0.7         | 8               | 6                            | 8 (4th octet)   |
|    /30 | 255.255.255.252 | 11111111.11111111.11111111.11111100 | 0.0.0.3         | 4               | 2                            | 4 (4th octet)   |
|    /31 | 255.255.255.254 | 11111111.11111111.11111111.11111110 | 0.0.0.1         | 2               | RFC3021: 2 / Traditionally 0 | 2 (4th octet)   |
|    /32 | 255.255.255.255 | 11111111.11111111.11111111.11111111 | 0.0.0.0         | 1               | 1 (host only)                | 1 (4th octet)   |

## Why Do We Need Subnet Masks?

1. **Efficient IP usage** → Without subnetting, you’d waste tons of IP addresses.
2. **Better security** → Networks can be segmented, keeping traffic contained.
3. **Performance** → Smaller subnets = less broadcast traffic = faster networks.

Example: Imagine a university network. You don’t want *every* device on one giant subnet. Instead, you can subnet into:

* `/24` for students’ Wi-Fi
* `/24` for staff
* `/24` for servers

That way, traffic is isolated, and troubleshooting is easier.

Would you like me to **expand this further with a worked-out subnetting example** (like dividing `192.168.1.0/24` into smaller subnets step by step), so it feels even more like a real IT guy’s tutorial?

