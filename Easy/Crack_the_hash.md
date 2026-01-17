# Lookup

### Level 1 

## Hash 1

Hash -> 48bb6e862e54f2a795ffc4e541caed4d

Hash type -> MD5

```
./john --format=raw-md5 --wordlist=/../rockyou.txt hash1.txt
```
## Hash 2

Hash -> CBFDAC6008F9CAB4083784CBD1874F76618D2A97 

Hash type -> SHA-1

```
./john --format=raw-sha1 --wordlist=/../rockyou.txt hash1.txt
```
## Hash 3

Hash -> 1C8BFE8F801D79745C4631D09FFF36C82AA37FC4CCE4FC946683D7B336B63032

Hash type -> SHA-256

```
./john --format=raw-sha256 --wordlist=/../rockyou.txt hash1.txt
```
## Hash 4

Hash -> $2y$12$Dwt1BZj6pcyc3Dy1FWZ5ieeUznr71EeNkJkUlypTsgbX1H68wsRom

Hash type ->  bcrypt (https://hashcat.net/wiki/doku.php?id=example_hashes)

```
hashcat -m 3200 hash4.txt /mnt/c/Users/Goldship/Desktop/Hash/rockyou.txt
```
## Hash 5

Hash -> 279412f945939ba78ce0758d3fd83daa

Hash type -> MD4

i used **https://crackstation.net/**


