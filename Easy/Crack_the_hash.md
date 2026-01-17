# Lookup

## Level 1 

### Hash 1

Hash -> 48bb6e862e54f2a795ffc4e541caed4d

Hash type -> MD5

```
./john --format=raw-md5 --wordlist=/../rockyou.txt hash1.txt
```
### Hash 2

Hash -> CBFDAC6008F9CAB4083784CBD1874F76618D2A97 

Hash type -> SHA-1

```
./john --format=raw-sha1 --wordlist=/../rockyou.txt hash2.txt
```
### Hash 3

Hash -> 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032

Hash type -> SHA-256

```
./john --format=raw-sha256 --wordlist=/../rockyou.txt hash3.txt
```
### Hash 4

Hash -> $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom

Hash type ->  bcrypt (https://hashcat.net/wiki/doku.php?id=example_hashes)

```
hashcat -m 3200 hash4.txt /../rockyou.txt
```
### Hash 5

Hash -> 279412f945939ba78ce0758d3fd83daa

Hash type -> MD4

i used **https://crackstation.net/**

---

## Level 2 

### Hash 1

Hash -> F09EDCB1FCEFC6DFB23DC3505A882655FF77375ED8AA2D1C13F640FCCC2D0C85

Hash type -> SHA-256

```
./john --format=raw-sha256 --wordlist=/../rockyou.txt hash1.txt
```

### Hash 2

Hash -> 1DFECA0C002AE40B8619ECF94819CC1B

Hash type -> NTML

```
./john --format=raw-md5 --wordlist=/../rockyou.txt hash1.txt
```

### Hash 3

Hash -> $6$aReallyHardSalt$6WKUTqzq.UQQmrm0p/T7MPpMbGNnzXPMAXi4bJMl9be.cfi3/qxIf.hsGpS41BqMhSrHVXgMpdjS6xeKZAs02.

Hash type -> SHA-512

```
hashcat -m 1800 hash3.txt /../rockyou.txt
```

### Hash 4

Hash -> e5d8870e5bdd26602cab8dbe07a942c8669e56d6

Hash type -> SHA-1

With salt remeber to add salt at the end hash in .txt file
```
# like that
e5d8870e5bdd26602cab8dbe07a942c8669e56d6:tryhackme
```

```
hashcat -m 110 hash4.txt /../rockyou.txt
```


