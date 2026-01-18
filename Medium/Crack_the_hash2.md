<img width="734" height="335" alt="image" src="https://github.com/user-attachments/assets/4cb2c4a3-b429-4f22-bb81-e310d08ad149" /># Lookup

## Task 6 Challange 

### Hash 1

Hash -> b16f211a8ad7f97778e5006c7cecdf31

Tip -> English male name, MD5, Border mutation, custom rule

```
# Look for available wordlist
./wordlistctl.py search malename
--==[ wordlistctl by blackarch.org ]==--

    0 > femalenames-usa-top1000 (6.94 Kb)
    1 > malenames-usa-top1000 (6.68 Kb)
    2 > Malename (29.67 Kb)
    3 > maleNames-password (22.54 Kb)
    4 > top_1000_usa_femalenames_english (6.78 Kb)
    5 > top_1000_usa_malenames_english (6.52 Kb)

# Unpack wordlist 
sudo ./wordlistctl.py fetch malenames -d

# Decode hash
./john --format=raw-md5 --wordlist=/../top_1000_usa_malenames_english.txt hash1.txt --rule=ALL
```
### Hash 2

Hash -> 7463fcb720de92803d179e7f83070f97

Tip -> English female name, MD5, Border mutation, custom rule

```
# Unpack wordlist 
sudo ./wordlistctl.py fetch femalenames -d


./john --format=raw-md5 --wordlist=/../top_1000_usa_femalenames_english.txt hash2.txt --rule=ALL --fork=24
```
### Hash 3

Hash -> f4476669333651be5b37ec6d81ef526f

Tip -> Town name of Mexico, MD5, Freak mutation, mentalist tool

<img width="500" height="250" alt="image" src="https://github.com/user-attachments/assets/5c5abb73-a4ca-4313-b829-a9a639f9e970" />

<img width="500" height="250" alt="image" src="https://github.com/user-attachments/assets/7f17c48e-bbe5-4f51-a2ea-8910bc4ca1dc" />


```
./john --format=raw-md5 --wordlist=/../XDt.txt hash3.txt --rule=ALL
```
### Hash 4

Hash -> a3a321e1c246c773177363200a6c0466a5030afc

Tip -> Own name, SHA1, Case mutation; existing rule

```

```
### Hash 5

Hash -> d5e085772469d544a447bc8250890949

Tip -> Lyrics, MD5, Order mutation, lyricpass

```

```
### Hash 6

Hash -> 377081d69d23759c5946a95d1b757adc

Tip -> Phone number, MD5, No mutation, pnwgen

```

```

### Hash 7

Hash -> ba6e8f9cd4140ac8b8d2bf96c9acd2fb58c0827d556b78e331d1113fcbfe425ca9299fe917f6015978f7e1644382d1ea45fd581aed6298acde2fa01e7d83cdbd

Tip -> Rockyou, SHA3-512, No mutation

```
./john --format=raw-sha3 --wordlist=/../rockyou.txt hash7.txt
```
### Hash 8

Hash -> ba6e8f9cd4140ac8b8d2bf96c9acd2fb58c0827d556b78e331d1113fcbfe425ca9299fe917f6015978f7e1644382d1ea45fd581aed6298acde2fa01e7d83cdbd

Tip -> Web scrapping, blake2, Repetition, CeWL

<img width="500" height="250" alt="image" src="https://github.com/user-attachments/assets/67f89ea0-43af-4fe7-938c-875dcef0aa7f" />

```
# You need to download wordlist from web 
./cewl.rb -d 2 -w $(pwd)/hash8.txt http://10.67.169.247/rtfm.re/en/sponsors/index.html

./john --format=raw-blake2 --wordlist=/../wordlist.txt hash8.txt --rule=ALL
```
### Hash 9

Hash -> $6$kI6VJ0a31.SNRsLR$Wk30X8w8iEC2FpasTo0Z5U7wke0TpfbDtSwayrNebqKjYWC4gjKoNEJxO/DkP.YFTLVFirQ5PEh4glQIHuKfA/

Tip -> Rockyou, SHA512-crypt, No mutation

```
./john --wordlist=/../rockyou.txt hash9.txt
```
