# Crossroads CTF Writeup

## Overview

Crossroads is a CTF machine that focuses on web enumeration, steganography, SMB enumeration and credential attacks.

Target IP:

```bash
192.168.1.152
```

---

## Reconnaissance

First I performed a service scan:

```bash
nmap -sC -sV 192.168.1.152
```

Open services:

* HTTP (Apache 2.4.38)
* SMB

![nmap scan](screenshot/image1.png)

---

## Web Enumeration

I started directory brute forcing:

```bash
dirb http://192.168.1.152
```

![dirb](screenshot/image2.png)

Discovered:

```
/robots.txt
```

Inside robots.txt:

```
Disallow: /crossroads.png
```

---

## Image Analysis

I downloaded the image and checked metadata:

```bash
exiftool crossroads.png
```

![exiftool](screenshot/image3.png)

Nothing useful in metadata.

Next I checked for steganography:

```bash
zsteg crossroads.png
```

This revealed an **OpenPGP secret key**.

![zsteg](screenshot/image4.png)

---

## SMB Enumeration

To find valid users:

```bash
enum4linux -A 192.168.1.152
```

Discovered user:

```
albert
```

![enum4linux](screenshot/image5.png)

---

## Password Brute Force

I performed an SMB brute force attack using Metasploit and found valid credentials:

```
albert : bradley1
```

![bruteforce](screenshot/image6.png)

---

## SMB Access

Connected to the SMB share:

```bash
smbclient //192.168.1.152/albert -U albert
```

Listed files:

```bash
ls
```

Downloaded the user flag:

```bash
get user.txt
```

![smb access](screenshot/image7.png)

---

## Flags

User flag successfully captured.

---

## Conclusion

This machine demonstrates the importance of:

* checking robots.txt
* analyzing files for hidden data
* enumerating SMB users
* password attacks against network services

A small information leak led to full access to the share.
