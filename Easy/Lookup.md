### Lookup

## Add host to /etc/hosts


## Nmap

```
[ Oguri ~ ]$ nmap -sC -sV 10.80.183.237
Starting Nmap 7.98 ( https://nmap.org ) at 2025-11-25 14:50 +0000
Nmap scan report for lookup.thm (10.80.183.237)
Host is up (0.044s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.9 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 9d:f8:d7:4b:6c:fd:13:dc:83:ff:24:04:39:9a:1e:bf (RSA)
|   256 12:15:39:68:25:5a:65:5b:4c:c4:b6:7d:af:26:73:b6 (ECDSA)
|_  256 f1:16:f7:cd:1d:64:69:07:28:5e:98:4e:f2:d1:10:9f (ED25519)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: Login Page
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 10.29 seconds
```
