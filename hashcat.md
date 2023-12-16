# Hashid

Indentify hash type.

### Installation

```shell-session
pip install hashid
```

Hashid can receive hashes, hash formats and files.

```shell-session
hashid '$apr1$71850310$gh9m4xcAn3MGxogwX/ztb.'

Analyzing '$apr1$71850310$gh9m4xcAn3MGxogwX/ztb.'
[+] MD5(APR) 
[+] Apache MD5
```

```shell-session
$ hashid hashes.txt 

--File 'hashes.txt'--
Analyzing '2fc5a684737ce1bf7b3b239df432416e0dd07357:2014'
[+] SHA-1 
[+] Double SHA-1 
[+] RIPEMD-160 
[+] Haval-160 
[+] Tiger-160 
[+] HAS-160 
[+] LinkedIn 
[+] Skein-256(160) 
[+] Skein-512(160) 
[+] Redmine Project Management Web App 
[+] SMF ≥ v1.1 
Analyzing '$P$984478476IagS59wHZvyQMArzfx58u.'
[+] Wordpress ≥ v2.6.2 
[+] Joomla ≥ v2.5.18 
[+] PHPass' Portable Hash 
```

```shell-session
$ hashid '$DCC2$10240#tom#e4e938d12fe5974dc42a90120bd9c90f' -m
Analyzing '$DCC2$10240#tom#e4e938d12fe5974dc42a90120bd9c90f'
[+] Domain Cached Credentials 2 [Hashcat Mode: 2100
```

-m option displays hashcat hash mode

Hashcat hash reference: https://hashcat.net/wiki/doku.php?id=example_hashes

# Hashcat

Open-source password cracking tool.

### Installation

The Hashcat team maintain only the Github [repository](https://github.com/hashcat/hashcat). All other repositories are maintained by the 3rd party.

``sudo apt install hashcat``

### Important options

#### -m

Hash type
Example: -m 1000

#### -O

Enable optimized kernels (limits password length)
Makes things run faster

#### -w

Enable a specific workload profile

| # | Performance | Runtime | Power Consumption | Desktop Impact |
| - | ----------- | ------- | ----------------- | -------------- |
| 1 | Low         | 2 ms    | Low               | Minimal        |
| 2 | Default     | 12 ms   | Economic          | Noticeable     |
| 3 | High        | 96 ms   | High              | Unresponsive   |
| 4 | Nightmare   | 480 ms  | Insane            | Headless       |

#### -a

Attack mode

| # | Mode                   |
| - | ---------------------- |
| 0 | Straight               |
| 1 | Combination            |
| 3 | Brute-force            |
| 6 | Hybrid Wordlist + Mask |
| 7 | Hybrid Mask + Wordlist |
| 0 | Rules                  |

##### Straight (Dictionary) Attack Mode

hashcat -a 0 -m `<hash type>` `<hash file>` `<wordlist>`

##### Combination Attack Mode

hashcat -a 1 -m `<hash type>` `<hash file>` `<wordlist1>` `<wordlist2>`

##### Mask Attack Mode

Mask attacks are used to generate words matching a specific pattern. This type of attack is particularly useful when the password length or format is known. A mask can be created using static characters, ranges of characters (e.g. [a-z] or [A-Z0-9]), or placeholders.

| Placeholder | Meaning                                                   |
| ----------- | --------------------------------------------------------- |
| ?l          | lower-case ASCII letters (a-z)                            |
| ?u          | upper-case ASCII letters (A-Z)                            |
| ?d          | digits (0-9)                                              |
| ?h          | 0123456789abcdef                                          |
| ?H          | 0123456789ABCDEF                                          |
| ?s          | special characters («space»!"#$%&'()*+,-./:;<=>?@[]^_`{ |
| ?a          | ?l?u?d?s                                                  |
| ?b          | 0x00 - 0xff                                               |

The above placeholders can be combined with options "-1" to "-4" which can be used for custom placeholders.
Consider the company Inlane Freight, which this time has passwords with the scheme "ILFREIGHT`<userid><year>`," where userid is 5 characters long. The mask "ILFREIGHT?l?l?l?l?l20[0-1]?d" can be used to crack passwords with the specified pattern, where "?l" is a letter and "20[0-1]?d" will include all years from 2000 to 2019.

hashcat -a 3 -m `<hash type>` `<hash file>` `<option>` `<option value>` `<mask>`

##### Hybrid Wordlist + Mask

hashcat -a 6 -m `<hash type>` `<hash file>` `<wordlist>` `<mask>`

##### Mask + Hybrid Wordlist

hashcat -a 7 -m `<hash type>` `<hash file>` `<mask>` `<wordlist>`

##### Rules

Rules help perform various operations on the input wordlist, such as prefixing, suffixing, toggling case, cutting, reversing, and much more. Rules take mask-based attacks to another level and provide increased cracking rates. Additionally, the usage of rules saves disk space and processing time incurred as a result of larger wordlists.

A rule can be created using functions, which take a word as input and output it's modified version. The following table describes some functions which are compatible with JtR as well as Hashcat.



| **Function** | **Description**                                              | **Input**                       | **Output**                                                                                                  |
| ------------------ | ------------------------------------------------------------------ | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| l                  | Convert all letters to lowercase                                   | InlaneFreight2020                     | inlanefreight2020                                                                                                 |
| u                  | Convert all letters to uppercase                                   | InlaneFreight2020                     | INLANEFREIGHT2020                                                                                                 |
| c / C              | capitalize / lowercase first letter and invert the rest            | inlaneFreight2020 / Inlanefreight2020 | Inlanefreight2020 / iNLANEFREIGHT2020                                                                             |
| t / TN             | Toggle case : whole word / at position N                           | InlaneFreight2020                     | iNLANEfREIGHT2020                                                                                                 |
| d / q / zN / ZN    | Duplicate word / all characters / first character / last character | InlaneFreight2020                     | InlaneFreight2020InlaneFreight2020 / IInnllaanneeFFrreeiigghhtt22002200 / IInlaneFreight2020 / InlaneFreight20200 |
| { / }              | Rotate word left / right                                           | InlaneFreight2020                     | nlaneFreight2020I / 0InlaneFreight202                                                                             |
| ^X / $X            | Prepend / Append character X                                       | InlaneFreight(^! /\\$!)               | !InlaneFreight2020 / InlaneFreight2020!                                                                           |
| r                  | Reverse                                                            | InlaneFreight2020                     | 0202thgierFenalnI                                                                                                 |


hashcat -a 0 -m `<hash type>` `<wordlist> `-r `<rule file>`
