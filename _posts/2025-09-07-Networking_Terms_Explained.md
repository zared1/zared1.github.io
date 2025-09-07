---
title: DSL, Fiber, and Internet Providers - Networking Terms Explained
date: 2025-09-07 15:47:58 +01:00
categories: [Networking, Networking Basics]
tags: [Networking, Networking Basics] # TAG names should always be lowercase
---

When it comes to the Internet, there’s a lot of jargon flying around DSL, FTTH, ISP… it can feel like a secret code only network engineers understand. Let’s break these down in a way that actually makes sense.

## DSL (Digital Subscriber Line)

DSL is a technology that transmits data over **traditional telephone lines**. Unlike the old dial-up, DSL lets you stay online while using the phone. It’s the classic way ISPs bring Internet to homes without laying new fiber everywhere.

## ADSL (Asymmetric Digital Subscriber Line)

ADSL is a **type of DSL** where download speeds are higher than upload speeds. Perfect for home users because most of us download way more than we upload (Netflix, YouTube, gaming, etc.).

## SDSL (Symmetric Digital Subscriber Line)

SDSL is the opposite of ADSL: **download and upload speeds are the same**. This is ideal for businesses or anyone running servers where uploading data is as important as downloading.

## DSLAM (Digital Subscriber Line Access Multiplexer)

A DSLAM is like the **traffic director at the ISP**. It aggregates multiple DSL connections from users and sends them onto a **high-speed backbone network**. Think of it as the hub where all the neighborhood DSL lines meet before heading out to the Internet.

## Fiber Optic Internet

Fiber is the modern upgrade over DSL, and it comes in different flavors:

* **FTTH (Fiber to the Home)**: Fiber goes straight into your home. Best speeds, lowest latency.
* **FTTB (Fiber to the Building)**: Fiber reaches the building, then internal cables distribute it to each apartment or office.
* **FTTN (Fiber to the Node)**: Fiber reaches a neighborhood node, then **copper cables** complete the connection to homes. Slightly slower than FTTH.

## ISP / FAI

* **ISP (Internet Service Provider)**: The company that gives you access to the Internet. Examples: Comcast, Orange, AT\&T.
* **FAI (Fournisseur d’Accès Internet)**: Same as ISP, but it’s the French term.

## AS (Autonomous System)

An **Autonomous System (AS)** is a network, or a group of networks, **under one administrative control**, using a common routing protocol. Each AS has a unique number (ASN) and communicates with other ASes via **BGP (Border Gateway Protocol)**. Basically, it’s like a city in the global Internet traffic map.
