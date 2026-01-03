# Pickle Rick

Begginer friendly CTF to do it you need to exploit web with RCE

### Recon

At first i did Nmap to check if there is something more to it, spoiler ssh is no use for this ctf
```
[ Oguri ~ ]$ nmap 10.81.161.175
Starting Nmap 7.98 ( https://nmap.org ) at 2026-01-03 16:28 +0000
Nmap scan report for 10.81.161.175
Host is up (0.045s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 2.44 seconds
```

Secound steep i did is fuzzing web to check hidden endpoint because on front page you cant find anythink special

<img width="2072" height="1149" alt="2026-01-03_16-44" src="https://github.com/user-attachments/assets/f5d8bc4f-93d8-4801-a541-f5a6173a11a9" />

i used FeroxBuster
```
[ Oguri ~ ]$ feroxbuster -u http://10.81.162.185/ -w /home/caffra/Wordlists/dirbuster-wordlist/directory-list-2.3-medium.txt -x php,txt,jpg 
                                                                                                                               
 ___  ___  __   __     __      __         __   ___
|__  |__  |__) |__) | /  `    /  \ \_/ | |  \ |__
|    |___ |  \ |  \ | \__,    \__/ / \ | |__/ |___
by Ben "epi" Risher 🤓                 ver: 2.13.1
───────────────────────────┬──────────────────────
 🎯  Target Url            │ http://10.81.162.185/
 🚩  In-Scope Url          │ 10.81.162.185
 🚀  Threads               │ 50
 📖  Wordlist              │ /home/caffra/Wordlists/dirbuster-wordlist/directory-list-2.3-medium.txt
 👌  Status Codes          │ All Status Codes!
 💥  Timeout (secs)        │ 7
 🦡  User-Agent            │ feroxbuster/2.13.1
 🔎  Extract Links         │ true
 💲  Extensions            │ [php, txt, jpg]
 🏁  HTTP methods          │ [GET]
 🔃  Recursion Depth       │ 4
───────────────────────────┴──────────────────────
 🏁  Press [ENTER] to use the Scan Management Menu™
──────────────────────────────────────────────────
403      GET        9l       28w      278c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
404      GET        9l       31w      275c Auto-filtering found 404-like response and created new filter; toggle off with --dont-filter
200      GET        6l      430w    37609c http://10.81.162.185/assets/bootstrap.min.js
200      GET        6l     1460w   121458c http://10.81.162.185/assets/bootstrap.min.css
200      GET     1493l     8917w   917403c http://10.81.162.185/assets/rickandmorty.jpeg
301      GET        9l       28w      315c http://10.81.162.185/assets => http://10.81.162.185/assets/
200      GET      163l     1557w    87738c http://10.81.162.185/assets/fail.gif
200      GET      201l     1054w    91974c http://10.81.162.185/assets/portal.jpg
200      GET       25l       61w      882c http://10.81.162.185/login.php
200      GET     1655l     9030w   389362c http://10.81.162.185/assets/picklerick.gif
302      GET        0l        0w        0c http://10.81.162.185/portal.php => http://10.81.162.185/login.php
200      GET        2l     1283w    86927c http://10.81.162.185/assets/jquery.min.js
200      GET       37l      110w     1062c http://10.81.162.185/
200      GET        1l        1w       17c http://10.81.162.185/robots.txt
[#######>------------] - 7m    333531/882240  37m     found:12      errors:29     
🚨 Caught ctrl+c 🚨 saving scan state to ferox-http_10_81_162_185_-1767454053.state ...
[#######>------------] - 7m    333580/882240  37m     found:12      errors:29     
[#######>------------] - 7m    333500/882184  789/s   http://10.81.162.185/ 
[####################] - 2s    882184/882184  475059/s http://10.81.162.185/assets/ => Directory listing (add --scan-dir-listings to scan)
[--------------------] - 0s         0/882184  -       http://10.81.162.185/assets/bootstrap.min.js
```
There is 3 interesting places to check
1. http://10.81.162.185/assets
<img width="919" height="858" alt="image" src="https://github.com/user-attachments/assets/02a3f5c8-ecfc-4ab6-996e-46c585674bc2" />

   
http://10.81.162.185/robots.txt
the is only 1 word -> **Wubbalubbadubdub**

http://10.81.162.185/login.php
Just a login page


