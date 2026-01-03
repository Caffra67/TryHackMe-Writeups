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
