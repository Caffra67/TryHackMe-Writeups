# Lookup

### Host Information Gathering

### Add host to /etc/hosts
```
echo "10.10.80.211 lookup.thm" | sudo tee -a /etc/hosts
```

<img width="646" height="313" alt="image" src="https://github.com/user-attachments/assets/4ad31f30-1fbc-443c-90aa-468e55ddf4eb" />

### Nmap

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

## Web Information Gathering

### Fuzzing
```
[ Oguri ~ ]$ feroxbuster --url http://lookup.thm/ -w /home/caffra/Wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt
                                                                                                                                                                                  
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.13.0
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://lookup.thm/
 🚩  In-Scope Url          │ lookup.thm
 🚀  Threads               │ 50
 📖  Wordlist              │ /home/caffra/Wordlists/SecLists/Discovery/Web-Content/raft-medium-directories.txt
 👌  Status Codes          │ All Status Codes!
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.13.0
 🔎  Extract Links         │ true
 🏁  HTTP methods          │ [GET]
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
403      GET        9l       28w      275c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
404      GET        9l       31w      272c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
200      GET        1l        0w        1c http://lookup.thm/login.php
200      GET       50l       84w      687c http://lookup.thm/styles.css
200      GET       26l       50w      719c http://lookup.thm/
```

### Whatweb
```
[ Oguri ~ ]$ whatweb 10.80.183.237
ERROR Opening: https://10.80.183.237 - Connection refused - connect(2) for "10.80.183.237" port 443
http://10.80.183.237 [302 Found] Apache[2.4.41], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.80.183.237], RedirectLocation[http://lookup.thm]
http://lookup.thm [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.80.183.237], PasswordField[password], Title[Login Page]
```
