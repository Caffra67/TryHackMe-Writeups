# Lookup

## Task 6 Challange 

### Hash 1

Hash -> b16f211a8ad7f97778e5006c7cecdf31

Tip -> English male name, MD5, Border mutation, custom rule

```
./wordlistctl.py search malename
--==[ wordlistctl by blackarch.org ]==--

    0 > femalenames-usa-top1000 (6.94 Kb)
    1 > malenames-usa-top1000 (6.68 Kb)
    2 > Malename (29.67 Kb)
    3 > maleNames-password (22.54 Kb)
    4 > top_1000_usa_femalenames_english (6.78 Kb)
    5 > top_1000_usa_malenames_english (6.52 Kb)

sudo ./wordlistctl.py fetch malename -d

./john --format=raw-md5 --wordlist=/../top_1000_usa_malenames_english.txt hash1.txt --rule=ALL
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
