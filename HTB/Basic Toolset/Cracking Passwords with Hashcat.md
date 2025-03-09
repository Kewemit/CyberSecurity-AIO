# Introduction
## Hashing vs. Encryption
### Hashing
* Hashing is the process of converting some text to a string, which is unique to that particular text. 
* Usually, a hash function always returns hashes with the same length irrespective of the type, length, or size of the data.

* Hashing is a one-way process, meaning there is no way of reconstructing the original plaintext from a hash. 
* Hashing can be used for various purposes; for example, 
	* The [MD5](https://en.wikipedia.org/wiki/MD5) and [SHA256](https://en.wikipedia.org/wiki/SHA-2) algorithms are usually used to verify file integrity, while 
	* Algorithms such as [PBKDF2](https://en.wikipedia.org/wiki/PBKDF2) are used to hash passwords before storage. 
	* Some hash functions can be keyed (an additional secret is used to create the hash). One such example is [HMAC](https://en.wikipedia.org/wiki/HMAC), which acts as a [checksum](https://en.wikipedia.org/wiki/Checksum) to verify if a particular message was tampered with during transmission.
* Since hashing is a one-way process, the only way to attack it is to use a list containing possible passwords.
	* Each password from this list is hashed and compared to the original hash.

For example, mainly four different algorithms can be used to protect passwords on **Unix systems**. These are `SHA-512`, `Blowfish`, `BCrypt`, and `Argon2`. These algorithms are available on Unix operating systems such as Linux, BSD, and Solaris.

* `SHA-512` 
	* Converts a long string of characters into a hash value.
	* Fast and efficient, 
	* But there are many **rainbow table attacks** where an attacker uses a pre-computed table to reconstruct the original passwords.
* `Blowfish` 
	* A symmetric block cipher algorithm that encrypts a password with a key. 
	* More secure but slower than ``SHA-512``.
* `BCrypt` 
	* Uses a slow hash function to make it harder for potential attackers to guess passwords or perform rainbow table attacks.
* `Argon2` 
	* A modern and secure algorithm explicitly designed for password hashing systems. 
	* Uses multiple rounds of hash functions and a large amount of memory to make it harder for attackers to guess passwords. 
	* Considered one of the most secure algorithms because it has a high time and resource requirement.

One protection employed against the brute-forcing of hashes is "**salting**". A **salt** is a random piece of data added to the plaintext before hashing it. This increases the computation time but does not prevent brute force altogether.

Let's consider the plaintext password value "``p@ssw0rd``". The **MD5 hash** for this can be calculated as follows:
```bash
$ echo -n "p@ssw0rd" | md5sum

0f359740bd1cda994f8b55330c86d845
```

Now, suppose a random **salt** such as "``123456``" is introduced and appended to the plaintext.
```bash
$ echo -n "p@ssw0rd123456" | md5sum

f64c413ca36f5cfe643ddbec4f7d92d0
```
A completely new hash was generated using this method, which will not be present in any pre-computed list. 

Some hash functions such as MD5 have also been vulnerable to collisions, where two sets of plaintext can produce the same hash.
### Encryption
* Encryption is the process of converting data into a format in which the original content is not accessible. 
* Unlike **hashing**, encryption is reversible, i.e., it's possible to decrypt the ciphertext (encrypted data) and obtain the original content. 
* Some classic examples of encryption ciphers are the [Caesar cipher](https://en.wikipedia.org/wiki/Caesar_cipher), [Bacon's cipher](https://en.wikipedia.org/wiki/Bacon%27s_cipher) and [Substitution cipher](https://en.wikipedia.org/wiki/Substitution_cipher). 
* Encryption algorithms are of two types: **Symmetric** and **Asymmetric**.
#### Symmetric Encryption
Symmetric algorithms use a **key** or **secret** to encrypt the data and use the same key to decrypt it. 

A basic example of symmetric encryption is XOR.
```bash
$ python3

Python 3.8.3 (default, May 14 2020, 11:03:12) 
[GCC 9.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from pwn import xor
>>> xor("p@ssw0rd", "secret")
b'\x03%\x10\x01\x12D\x01\x01'
```
Above, the plaintext is "`p@ssw0rd,`" and the key is "`secret`". 

Anyone who has the key can decrypt the ciphertext and obtain the plaintext:
```bash
$ python3

Python 3.8.3 (default, May 14 2020, 11:03:12) 
[GCC 9.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> from pwn import xor
>>> xor('\x03%\x10\x01\x12D\x01\x01', "secret")
b'p@ssw0rd'
```
The "`b`" in the above outputs denotes a **byte string**. This distinction was not made pre **Python3**.

* Some other examples of **symmetric algorithms** are:
* [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard),
* [DES](https://en.wikipedia.org/wiki/Data_Encryption_Standard), 
* [3DES](https://en.wikipedia.org/wiki/Triple_DES) and 
* [Blowfish](https://en.wikipedia.org/wiki/Blowfish_(cipher)#The_algorithm). 

These algorithms can be vulnerable to attacks such as:
* key bruteforcing,
* [frequency analysis](https://en.wikipedia.org/wiki/Frequency_analysis),
* [padding oracle attack](https://en.wikipedia.org/wiki/Padding_oracle_attack), 
* etc.
#### Asymmetric Encryption
On the other hand, asymmetric algorithms divide the key into two parts (**public** and **private**). 

The **public key** can be given to anyone who wishes to encrypt some information and pass it securely to the owner. 

The owner then uses their **private key** to decrypt the content. Some examples of asymmetric algorithms are:
* [RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)),
* [ECDSA](https://en.wikipedia.org/wiki/Elliptic_Curve_Digital_Signature_Algorithm), and 
* [Diffie-Hellman](https://en.wikipedia.org/wiki/Diffie%E2%80%93Hellman_key_exchange).

One of the prominent uses of **asymmetric encryption** is the **Hypertext Transfer Protocol Secure** (`HTTPS`) protocol in the form of **Secure Sockets Layer** (`SSL`). 
* When a client connects to a server hosting an `HTTPS` website, a **public key exchange** occurs. 
* The client's browser uses this **public key** to encrypt any kind of data sent to the server. 
* The server decrypts the incoming traffic before passing it on to the processing service.
## Identifying Hashes
* Most hashing algorithms produce hashes of a constant length. 
* The length of a particular hash can be used to map it to the algorithm it was hashed with. 
* For example, a hash of 32 characters in length can be an ``MD5`` or ``NTLM`` hash.
* Sometimes, hashes are stored in certain formats. For example,
	* `hash:salt` or
	* `$id$salt$hash`.
* The hash: "`2fc5a684737ce1bf7b3b239df432416e0dd07357:2014`" is a 
	* ``SHA1`` hash with the salt of "`2014`".
* The hash: "`$6$vb1tLY1qiY$M.1ZCqKtJBxBtZm1gRi8Bbkn39KU0YJW1cuMFzTRANcNKFKR4RmAQVk4rqQQCkaJT6wXqjUkFcA/qNxLyqW.U/` contains three fields delimited by `$`, where the first field is the `id`, i.e., `6`. 
	* This is used to identify the type of algorithm used for hashing. The following list contains some ``ids`` and their corresponding algorithms.
```bash
$1$  : MD5
$2a$ : Blowfish
$2y$ : Blowfish, with correct handling of 8 bit characters
$5$  : SHA256
$6$  : SHA512
```
* The next field, "`vb1tLY1qiY`", is the salt used during hashing, 
* And the final field is the actual hash.

* Open and closed source software use many different kinds of hash formats. 
	* `Apache` web server stores its hashes in the format "`$apr1$71850310$gh9m4xcAn3MGxogwX/ztb.`", 
	* `WordPress` stores hashes in the form "`$P$984478476IagS59wHZvyQMArzfx58u.`".
### Hashid
[Hashid](https://github.com/psypanda/hashID) can be used to detect various kinds of hashes. The full list of supported hashes can be found [here](https://github.com/psypanda/hashID/blob/master/doc/HASHINFO.xlsx).

```bash
$ pip install hashid
```

Hashes can be supplied as command-line arguments or using a file:
```bash
$ hashid '$apr1$71850310$gh9m4xcAn3MGxogwX/ztb.'

Analyzing '$apr1$71850310$gh9m4xcAn3MGxogwX/ztb.'
[+] MD5(APR) 
[+] Apache MD5
```
or
```bash
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
--End of file 'hashes.txt'--
```
### Context is Important
It is not always possible to identify the algorithm. Sometimes the plaintext might undergo multiple encryptions and salting transformations.

It is important to note that `hashid` uses regex to make a best-effort determination for the type of hash provided. 
* `hashid` will often provide many possibilities for a given hash, and we will still be left with a certain amount of guesswork. 
* We usually have some context around the type of hash we are looking to identify.
	* Was it obtained via an **Active Directory attack** or from a Windows host? 
	* Was it obtained through a **SQL injection vulnerability**? 
* Knowing where a hash came from will help us narrow down the **hash type** and, therefore, the `Hashcat` hash mode necessary to attempt to crack it.

`Hashcat` provides an excellent [reference](https://hashcat.net/wiki/doku.php?id=example_hashes), which maps hash modes to example hashes. This reference is invaluable during a penetration test to determine the type of hash we are dealing with.

For example, passing the hash "`a2d1f7b7a1862d0d4a52644e72d59df5:500:lp@trash-mail.com`" to `hashid` will give us various possibilities:
```bash
$ hashid 'a2d1f7b7a1862d0d4a52644e72d59df5:500:lp@trash-mail.com'

Analyzing 'a2d1f7b7a1862d0d4a52644e72d59df5:500:lp@trash-mail.com'
[+] MD5 
[+] MD4 
[+] Double MD5 
[+] LM 
[+] RIPEMD-128 
[+] Haval-128 
[+] Tiger-128 
[+] Skein-256(128) 
[+] Skein-512(128) 
[+] Lotus Notes/Domino 5 
[+] Skype 
[+] Lastpass 
```
However, a quick look through the `Hashcat` example hashes reference will help us determine that it is indeed a **Lastpass** hash, which is hash mode `6800`. 

## Hashcat Overview
* [Hashcat](https://hashcat.net/hashcat/) is a popular open-source password cracking tool.
* The full **help menu** can be viewed by typing:
	* `hashcat -h`. 
* The `Hashcat` team does not maintain any packages, aside from the official GitHub [repo](https://github.com/hashcat/hashcat). 
	* Any other repos (like `apt`), are left up to the 3rd party to keep up-to-date. 
	* The latest version can be obtained directly from their GitHub repo.
#### Hashcat Installation
```bash
$ sudo apt install hashcat
$ hashcat -h

hashcat (v6.1.1) starting...

Usage: hashcat [options]... hash|hashfile|hccapxfile [dictionary|mask|directory]...

- [ Options ] -

 Options Short / Long           | Type | Description                                          | Example
================================+======+======================================================+=======================
 -m, --hash-type                | Num  | Hash-type, see references below                      | -m 1000
 -a, --attack-mode              | Num  | Attack-mode, see references below                    | -a 3
 -V, --version                  |      | Print version                                        |
 -h, --help                     |      | Print help                                           |
     --quiet                    |      | Suppress output                                      |
     --hex-charset              |      | Assume charset is given in hex                       |
     --hex-salt                 |      | Assume salt is given in hex                          |
     --hex-wordlist             |      | Assume words in wordlist are given in hex            |
     --force                    |      | Ignore warnings                                      |
     --status                   |      | Enable automatic update of the status screen         |
     --status-json              |      | Enable JSON format for status output                 |
     --status-timer             | Num  | Sets seconds between status screen updates to X      | --status-timer=1
     --stdin-timeout-abort      | Num  | Abort if there is no input from stdin for X seconds  | --stdin-timeout-abort=300
     --machine-readable         |      | Display the status view in a machine-readable format |

<SNIP>
```
The `-a` and `-m` arguments are used to specify the type of attack mode and hash type. 

`Hashcat` supports the following attack modes:

| #   | Mode                   |
| --- | ---------------------- |
| 0   | Straight               |
| 1   | Combination            |
| 3   | Brute-Force            |
| 6   | Hybrid Wordlist + Mask |
| 7   | Hybrid Mask + Wordlist |

* The hash type value is based on the algorithm of the hash to be cracked. 
* A complete list of hash types and their corresponding examples can be found [here](https://hashcat.net/wiki/doku.php?id=example_hashes). 
#### Hashcat - Example Hashes
You can also view the list of example hashes via the command line using the following command:
```bash
$ hashcat --example-hashes | less

hashcat (v6.1.1) starting...

MODE: 0
TYPE: MD5
HASH: 8743b52063cd84097a65d1633f5c74f5
PASS: hashcat

MODE: 10
TYPE: md5($pass.$salt)
HASH: 3d83c8e717ff0e7ecfe187f088d69954:343141
PASS: hashcat

MODE: 11
TYPE: Joomla < 2.5.18
HASH: b78f863f2c67410c41e617f724e22f34:89384528665349271307465505333378
PASS: hashcat

<SNIP>
```
#### Hashcat - Benchmark
The benchmark test for a particular hash type can be performed using the `-b` flag:
```bash
$ hashcat -b -m 0
hashcat (v6.1.1) starting in benchmark mode...

Hashmode: 0 - MD5


Speed.#1.........:   449.4 MH/s (12.84ms) @ Accel:1024 Loops:1024 Thr:1 Vec:8

Started: Fri Aug 28 21:52:35 2020
Stopped: Fri Aug 28 21:53:25 2020
```
For example, the hash rate for MD5 on a given CPU is found to be 450.7 MH/s.

We can also run `hashcat -b` to run benchmarks for all hash modes.
#### Hashcat - Optimizations
Hashcat has two main ways to optimize speed:

| Option            | Description                                                                                                                                                                                                                                                                                                                                                                                    |
| ----------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Optimized Kernels | This is the `-O` flag, which according to the documentation, means `Enable optimized kernels (limits password length)`. The magical password length number is generally 32, with most wordlists won't even hit that number. This can take the estimated time from days to hours, so it is always recommended to run with `-O` first and then rerun after without the `-O` if your GPU is idle. |
| Workload          | This is the `-w` flag, which, according to the documentation, means `Enable a specific workload profile`. The default number is `2`, but if you want to use your computer while Hashcat is running, set this to `1`. If you plan on the computer only running Hashcat, this can be set to `3`.\|                                                                                               |

It is important to note that the use of `--force` should be avoided. 
* While this appears to make `Hashcat` work on certain hosts, it is actually disabling safety checks, muting warnings, and bypasses problems that the tool's developers have deemed to be blockers. 
	* These problems can lead to false positives, false negatives, malfunctions, etc.
* If the tool is not working properly without forcing it to run with `--force` appended to your command, we should troubleshoot the root cause (i.e., a driver issue). 
# Hashcat Attack Types
## Dictionary Attack
Hashcat has **5 different attack modes** that have different applications depending on the type of hash you are trying to crack and the complexity of the password. 
* The most straightforward but extremely effective attack type is the **dictionary attack**. 
* It is not uncommon to encounter organizations with weak password policies whose users select common words and phrases with little to no complexity as their passwords. 
### Password List - Top 5 (2020)
Based on an analysis of millions of leaked passwords, the organization SplashData listed the following as the top 5 most common passwords of 2020:
```bash
123456
123456789
qwerty
password
1234567
```
This happens since users will often choose one out of convenience.

There are many sources for obtaining password lists such as [SecLists](https://github.com/danielmiessler/SecLists/tree/master/Passwords), a large collection of password lists, and the `rockyou.txt` wordlist.

We can also find large wordlists such as [CrackStation's Password Cracking Dictionary](https://crackstation.net/crackstation-wordlist-password-cracking-dictionary.htm), which contains 1,493,677,782 words and is 15GB in size. 
### Straight or Dictionary Attack
As the name suggests, this attack reads from a wordlist and tries to crack the supplied hashes. 

* Dictionary attacks are useful if you know that the target organization uses weak passwords or just wants to run through some cracking attempts rather quickly. 
* This attack is typically faster to complete than the more complex attacks discussed later in this module. It's basic syntax is:
#### Hashcat - Syntax
```bash
$ hashcat -a 0 -m <hash type> <hash file> <wordlist>
```

For example, the following commands will crack a SHA256 hash using the `rockyou.txt` wordlist:
```bash
$ echo -n '!academy' | sha256sum | cut -f1 -d' ' > sha256_hash_example

$ hashcat -a 0 -m 1400 sha256_hash_example 
/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...

<SNIP>

Dictionary cache built:
* Filename..: /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 14344385
* Runtime...: 2 secs

Approaching final keyspace - workload adjusted.  

006fc3a9613f3edd9f97f8e8a8eff3b899a2d89e1aabf33d7cc04fe0728b0fe6:!academy
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: SHA2-256
Hash.Target......: 006fc3a9613f3edd9f97f8e8a8eff3b899a2d89e1aabf33d7cc...8b0fe6
Time.Started.....: Fri Aug 28 21:58:44 2020 (4 secs)
Time.Estimated...: Fri Aug 28 21:58:48 2020 (0 secs)
Guess.Base.......: File (/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  3383.5 kH/s (0.46ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 14344385/14344385 (100.00%)
Rejected.........: 0/14344385 (0.00%)
Restore.Point....: 14340096/14344385 (99.97%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: $HEX[216361726f6c796e] -> $HEX[042a0337c2a156616d6f732103]

Started: Fri Aug 28 21:58:05 2020
Stopped: Fri Aug 28 21:58:49 2020

```
In the above example, the hash cracked in 4 seconds. Cracking speed varies depending on the underlying hardware, hash type, and complexity of the password.

Let's look at a more complex hash such as [Bcrypt](https://en.wikipedia.org/wiki/Bcrypt), which is a type of password hash based on the [Blowfish](https://en.wikipedia.org/wiki/Blowfish_(cipher)) cipher. It utilizes a salt to protect it from rainbow table attacks and can have many rounds of the algorithm applied, making the hash resistant to brute force attacks even with a large password cracking rig.

For example, take the ``bcrypt`` hash of the same password "`!academy`" which would be "`$2a$05$ZdEkj8cup/JycBRn2CX.B.nIceCYR8GbPbCCg6RlD7uvuREexEbVy`" with 5 rounds of the Blowfish algorithm applied. 

This hash run on the same hardware with the same wordlist takes considerably longer to crack.
#### Hashcat - Status
At any time we can press "`s`" to see the status of the cracking process:
```shell-session
[s]tatus [p]ause [b]ypass [c]heckpoint [q]uit => s

Session..........: hashcat
Status...........: Running
Hash.Name........: bcrypt $2*$, Blowfish (Unix)
Hash.Target......: $2a$05$ZdEkj8cup/JycBRn2CX.B.nIceCYR8GbPbCCg6RlD7uv...exEbVy
Time.Started.....: Mon Jun 22 19:43:40 2020 (3 mins, 10 secs)
Time.Estimated...: Mon Jun 22 21:20:28 2020 (1 hour, 33 mins)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:     2470 H/s (8.98ms) @ Accel:8 Loops:16 Thr:1 Vec:8
Recovered........: 0/1 (0.00%) Digests
Progress.........: 468576/14344385 (3.27%)
Rejected.........: 0/468576 (0.00%)
Restore.Point....: 468576/14344385 (3.27%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:16-32
Candidates.#1....: septiembre29 -> sep1101
```

Dictionary attacks can be very effective for weak passwords, but the attack's efficiency also depends on the type of hash targeted. 

Certain types of weaker passwords can be much more difficult to crack just based on the hashing algorithm in use. 
## Combination Attack WIP
The combination attack modes take in two wordlists as input and create combinations from them. 

This attack is useful because it is not uncommon for users to join two or more words together (i.e., `welcomehome` or `hotelcalifornia`).

To demonstrate this attack, consider the following wordlists:
```bash
$ cat wordlist1

super
world
secret

$ cat wordlist2

hello
password
```

If given these two word lists `Hashcat` will produce exactly **3 x 2 = 6** words, such as:
```bash
$ awk '(NR==FNR) { a[NR]=$0 } (NR != FNR) { for (i in a) { print $0 a[i] } }' file2 file1

superhello
superpassword
worldhello
wordpassword
secrethello
secretpassword
```

This can also be done with `Hashcat` using the `--stdout` flag which can be very helpful for debugging purposes and seeing how the tool is handling things.

We can see what `Hashcat` will produce given the same two files in the following example:
```bash
$ hashcat -a 1 --stdout file1 file2
superhello
superpassword
worldhello
worldpassword
secrethello
secretpassword
```
#### Hashcat - Syntax
The syntax for the combination attack is:
```bash
$ hashcat -a 1 -m <hash type> <hash file> <wordlist1> <wordlist2>
```

This attack provides more flexibility and customization when using wordlists.

Let's see this example in practice. First, create the md5 of the password `secretpassword`.
```bash
$ echo -n 'secretpassword' | md5sum | cut -f1 -d' '  > combination_md5

2034f6e32958647fdff75d265b455ebf
```

Next, let's run `Hashcat` against the hash using the two wordlists above with the combination attack mode:
```bash
$ hashcat -a 1 -m 0 combination_md5 wordlist1 wordlist2

hashcat (v6.1.1) starting...
<SNIP>

Dictionary cache hit:
* Filename..: wordlist1
* Passwords.: 3
* Bytes.....: 19
* Keyspace..: 6

The wordlist or mask that you are using is too small.
This means that hashcat cannot use the full parallel power of your device(s).
Unless you supply more work, your cracking speed will drop.
For tips on supplying more work, see: https://hashcat.net/faq/morework

Approaching final keyspace - workload adjusted.  

2034f6e32958647fdff75d265b455ebf:secretpassword  
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: MD5
Hash.Target......: 2034f6e32958647fdff75d265b455ebf
Time.Started.....: Fri Aug 28 22:05:51 2020, (0 secs)
Time.Estimated...: Fri Aug 28 22:05:51 2020, (0 secs)
Guess.Base.......: File (wordlist1), Left Side
Guess.Mod........: File (wordlist2), Right Side
Speed.#1.........:       42 H/s (0.02ms) @ Accel:1024 Loops:2 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 6/6 (100.00%)
Rejected.........: 0/6 (0.00%)
Restore.Point....: 0/3 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-2 Iteration:0-2
Candidates.#1....: superhello -> secretpassword
```

Combination attacks are another powerful tool to keep in our arsenal.
## Mask Attack
Mask attacks are used to generate words matching a specific pattern. 
* This type of attack is particularly useful when the password length or format is known. 

A mask can be created using static characters, ranges of characters (e.g. `[a-z]` or `[A-Z0-9]`), or placeholders. 

The following list shows some important placeholders:

| Placeholder | Meaning                                                       |
| ----------- | ------------------------------------------------------------- |
| ?l          | lower-case ASCII letters (a-z)                                |
| ?u          | upper-case ASCII letters (A-Z)                                |
| ?d          | digits (0-9)                                                  |
| ?h          | 0123456789abcdef                                              |
| ?H          | 0123456789ABCDEF                                              |
| ?s          | special characters ``(«space»!"#$%&'()\*+,-./:;<=>?@[]^_\`{`` |
| ?a          | ?l?u?d?s                                                      |
| ?b          | 0x00 - 0xff                                                   |

The above placeholders can be combined with options "`-1`" to "`-4`" which can be used for custom placeholders. 

See the [Custom charsets](https://hashcat.net/wiki/doku.php?id=mask_attack) for a detailed breakdown of each of these four command-line parameters that can be used to configure four custom charsets.

Consider the company Inlane Freight, which 
* Has passwords with the scheme "`ILFREIGHT<userid><year>`," where userid is **5 characters long**. 
* The mask "`ILFREIGHT?l?l?l?l?l20[0-1]?d`" can be used to crack passwords with the specified pattern, where
	* "`?l`" is a letter and 
	* "`20[0-1]?d`" will include all years from 2000 to 2019.

Let's try creating a hash and cracking it using this mask.
#### Creating MD5 hashes
```bash
$ echo -n 'ILFREIGHTabcxy2015' | md5sum | tr -d " -" > md5_mask_example_hash
```

In the below example, the attack mode is `3`, and the hash type for MD5 is `0`.
#### Hashcat - Mask Attack
```bash
$ hashcat -a 3 -m 0 md5_mask_example_hash -1 01 'ILFREIGHT?l?l?l?l?l20?1?d'

hashcat (v6.1.1) starting...
<SNIP>

d53ec4d0b37bbf565b1e09d64834e1ae:ILFREIGHTabcxy2015
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: MD5
Hash.Target......: d53ec4d0b37bbf565b1e09d64834e1ae
Time.Started.....: Fri Aug 28 22:08:44 2020, (43 secs)
Time.Estimated...: Fri Aug 28 22:09:27 2020, (0 secs)
Guess.Mask.......: ILFREIGHT?l?l?l?l?l20?1?d [18]
Guess.Charset....: -1 01, -2 Undefined, -3 Undefined, -4 Undefined 
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  3756.3 kH/s (0.36ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 155222016/237627520 (65.32%)
Rejected.........: 0/155222016 (0.00%)
Restore.Point....: 155215872/237627520 (65.32%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: ILFREIGHTuisba2015 -> ILFREIGHTkmrff2015
```

The "`-1`" option was used to specify a placeholder with just 0 and 1. 

`Hashcat` could crack the hash in 43 seconds on CPU power. 

The "`--increment`" flag can be used to increment the mask length automatically, with a length limit that can be supplied using the "`--increment-max`" flag.
## Hybrid Mode
Hybrid mode is a variation of the combinator attack, wherein multiple modes can be used together for a fine-tuned wordlist creation. 

* Can be used to perform very targeted attacks by creating very customized wordlists. 
* Particularly useful when you know or have a general idea of the organization's password policy or common password syntax. 
* The attack mode is "`6`".

Let's consider a password such as "`football1$`". The example below shows how a wordlist can be used in combination with a mask.
#### Creating Hybrid Hash
```bash
$ echo -n 'football1$' | md5sum | tr -d " -" > hybrid_hash
```
#### Hashcat - Hybrid Attack using Wordlists
Hashcat reads words from the wordlist and appends a unique string based on the mask supplied. 

In this case, the mask "`?d?s`" tells hashcat to append a digit and a special character at the end of each word in the `rockyou.txt` wordlist.
```bash
$ hashcat -a 6 -m 0 hybrid_hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt '?d?s'

hashcat (v6.1.1) starting...
<SNIP>

f7a4a94ff3a722bf500d60805e16b604:football1$      
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: MD5
Hash.Target......: f7a4a94ff3a722bf500d60805e16b604
Time.Started.....: Fri Aug 28 22:11:15 2020, (0 secs)
Time.Estimated...: Fri Aug 28 22:11:15 2020, (0 secs)
Guess.Base.......: File (/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt), Left Side
Guess.Mod........: Mask (?d?s) [2], Right Side
Guess.Queue.Base.: 1/1 (100.00%)
Guess.Queue.Mod..: 1/1 (100.00%)
Speed.#1.........:  5118.2 kH/s (11.56ms) @ Accel:768 Loops:82 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 755712/4733647050 (0.02%)
Rejected.........: 0/755712 (0.00%)
Restore.Point....: 0/14344385 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:82-164 Iteration:0-82
Candidates.#1....: 1234562= -> class083~
```

Attack mode "`7`" can be used to prepend characters to words using a given mask. 
#### Creating another Hybrid Hash
```bash
$ echo -n '2015football' | md5sum | tr -d " -" > hybrid_hash_prefix 
```
#### Hashcat - Hybrid Attack using Wordlists with Masks
The following example shows a mask using a custom character set to add a prefix to each word in the `rockyou.txt` wordlist. 

The custom character mask "`20?1?d`" with the custom character set "`-1 01`" will prepend various years to each word in the wordlist (i.e., 2010, 2011, 2012..).
```bash
$ hashcat -a 7 -m 0 hybrid_hash_prefix -1 01 '20?1?d' /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...
<SNIP> 

eac4fe196339e1b511278911cb77d453:2015football    
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: MD5
Hash.Target......: eac4fe196339e1b511278911cb77d453
Time.Started.....: Thu Nov 12 01:32:34 2020 (0 secs)
Time.Estimated...: Thu Nov 12 01:32:34 2020 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt), Right Side
Guess.Mod........: Mask (20?1?d) [4], Left Side
Guess.Charset....: -1 01, -2 Undefined, -3 Undefined, -4 Undefined
Speed.#1.........:     8420 H/s (0.22ms) @ Accel:384 Loops:64 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 1280/286887700 (0.00%)
Rejected.........: 0/1280 (0.00%)
Restore.Point....: 0/20 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-64 Iteration:0-64
Candidates.#1....: 2001123456 -> 2017charlie
```
# Working with Wordlists
## Wordlists
During an assessment, we may retrieve one or more password hashes that are crucial to the engagement's success. 
* It may be necessary to create a custom, targeted wordlist to achieve our goal in these instances.
* It is necessary to spend time refining a wordlist because the success rate heavily depends on it. 
## Creating Wordlists
Many open-source tools help in creating customized password wordlists based on our requirements.
### Crunch
Crunch can create wordlists based on parameters such as words of a specific length, a limited character set, or a certain pattern. It can generate both permutations and combinations.
#### Crunch - Syntax
```bash
$ crunch <minimum length> <maximum length> <charset> -t <pattern> -o <output file>
```

* The "`-t`" option is used to specify the pattern for generated passwords. The pattern can contain
	* "`@`," representing lower case characters, 
	* "`,`" (comma) will insert upper case characters, 
	* "`%`" will insert numbers, 
	* "`^`" will insert symbols.
#### Crunch - Generate Word List
```bash
$ crunch 4 8 -o wordlist
```

The command above creates a wordlist consisting of words with a length of 4 to 8 characters, using the default character set.

Let's assume that Inlane Freight user passwords are of the form "`ILFREIGHTYYYYXXXX`," where "`XXXX`" is the employee ID containing letters, and "`YYYY`" is the year. We can use `crunch` to create a list of such passwords.
#### Crunch - Create Word List using Pattern
```bash
$ crunch 17 17 -t ILFREIGHT201%@@@@ -o wordlist
```

The pattern "`ILFREIGHT201%@@@@`" will create words with the years 2010-2019 followed by four letters. The length here is 17, which is constant for all words.

If we know a user's birthdate is 10/03/1998 (through social media, etc.), we can include this in their password, followed by a string of letters. 

Crunch can be used to create a wordlist of such words. The "`-d`" option is used to specify the amount of repetition.

#### Crunch - Specified Repetition
```bash
$ crunch 12 12 -t 10031998@@@@ -d 1 -o wordlist
```
### CUPP
`CUPP` stands for `Common User Password Profiler`, and is used to create highly targeted and customized wordlists based on information gained from social engineering and OSINT.

* People tend to use personal information while creating passwords, such as phone numbers, pet names, birth dates, etc. 
* CUPP takes in this information and creates passwords from them. 
* These wordlists are mostly used to gain access to social media accounts. 

The "`-i`" option is used to run in interactive mode, prompting `CUPP` to ask us for information on the target.
#### CUPP - Usage Example
```bash
$ python3 cupp.py -i

[+] Insert the information about the victim to make a dictionary
[+] If you don't know all the info, just hit enter when asked! ;)

> First Name: roger
> Surname: penrose
> Nickname:      
> Birthdate (DDMMYYYY): 11051972

> Partners) name: beth
> Partners) nickname:
> Partners) birthdate (DDMMYYYY):

> Child's name: john
> Child's nickname: johnny
> Child's birthdate (DDMMYYYY):

> Pet's name: tommy
> Company name: INLANE FREIGHT

> Do you want to add some key words about the victim? Y/[N]: Y
> Please enter the words, separated by comma. [i.e. hacker,juice,black], spaces will be removed: sysadmin,linux,86391512
> Do you want to add special chars at the end of words? Y/[N]:
> Do you want to add some random numbers at the end of words? Y/[N]:
> Leet mode? (i.e. leet = 1337) Y/[N]:

[+] Now making a dictionary...
[+] Sorting list and removing duplicates...
[+] Saving dictionary to roger.txt, counting 2419 words.
[+] Now load your pistolero with roger.txt and shoot! Good luck!
```

The command above shows how the data for the user Roger Penrose, was provided to CUPP. 

The unknown fields can be just left empty. After taking in all data, CUPP creates a wordlist based on it. 

It also supports appending random characters and a "Leet" mode, which uses combinations of letters and numbers in common words.

`CUPP` can also fetch common names from various online databases using the "`-l`" option.
### KWPROCESSOR
`Kwprocessor` is a tool that creates wordlists with keyboard walks. 

Another common password generation technique is to follow patterns on the keyboard. 

These passwords are called keyboard walks, as they look like a walk along the keys. For example, the string "`qwertyasdfg`" is created by using the first five characters from the keyboard's first two rows. 

This seems complex to the normal eye but can be easily predicted. `Kwprocessor` uses various algorithms to guess patterns such as these.
#### Kwprocessor - Installation
The tool can be found [here](https://github.com/hashcat/kwprocessor) and has to be installed manually.
```bash
$ git clone https://github.com/hashcat/kwprocessor
$ cd kwprocessor
$ make
```

The **help** menu shows the various options supported by kwp. The pattern is based on the geographical directions a user could choose on the keyboard.

For example, the "`--keywalk-west`" option is used to specify movement towards the west from the base character. 
* The program takes in base characters as a parameter, which is the character set the pattern will start with. 
* Next, it needs a keymap, which maps the locations of keys on language-specific keyboard layouts. 
* The final option is used to specify the route to be used. 
	* A route is a pattern to be followed by passwords. 
	* It defines how passwords will be formed, starting from the base characters. 
	* For example, the route 222 can denote the path ``2 * EAST + 2 * SOUTH + 2 * WEST`` from the base character. 
	* If the base character is considered to be "`T`" then the password generated by the route would be "`TYUJNBV`" on a US keymap. 
#### Kwprocessor - Example
```bash
$ kwp -s 1 basechars/full.base keymaps/en-us.keymap  routes/2-to-10-max-3-direction-changes.route
```

The command above generates words with characters reachable while holding shift (`-s`), using the full base, the standard ``en-us`` keymap, and 3 direction changes route.
### Princeprocessor
`PRINCE` or `PRobability INfinite Chained Elements` is an efficient password guessing algorithm to improve password cracking rates.

* [Princeprocessor](https://github.com/hashcat/princeprocessor) is a tool that generates passwords using the **PRINCE** algorithm. 
* The program takes in a wordlist and creates chains of words taken from this wordlist. For example, if a wordlist contains the words:
```bash
dog
cat
ball
```

The generated wordlist would be of the form:
```bash
dog
cat
ball
dogdog
catdog
dogcat
catcat
dogball
catball
balldog
ballcat
ballball
dogdogdog
catdogdog
dogcatdog
catcatdog
dogdogcat
<SNIP>
```

The `PRINCE` algorithm considers various permutation and combinations while creating each word. 
#### Installation
```bash
$ wget https://github.com/hashcat/princeprocessor/releases/download/v0.22/princeprocessor-0.22.7z

$ 7z x princeprocessor-0.22.7z
$ cd princeprocessor-0.22
$ ./pp64.bin -h
```
#### Princeprocessor - Find the Number of Combinations
The "`--keyspace`" option can be used to find the number of combinations produced from the input wordlist.
```bash
$ ./pp64.bin --keyspace < words

232
```

According to princeprocessor, 232 unique words can be formed from our wordlist above.
#### Princeprocessor - Forming Wordlist
```bash
$ ./pp64.bin -o wordlist.txt < words
```

The command above writes the output words to a file named `wordlist.txt`. 
#### Princeprocessor - Password Length Limits
By default, princeprocessor only outputs words up to 16 in length. This can be controlled using the "`--pw-min`" and "`--pw-max`" arguments.
```bash
$ ./pp64.bin --pw-min=10 --pw-max=25 -o wordlist.txt < words
```

The command above will output words between 10 and 25 in length. 
#### Princeprocessor - Specifying Elements
The number of elements per word can be controlled using "`--elem-cnt-min`" and "`--elem-cnt-max`". These values ensure that number of elements in an output word is above or below the given value.
```bash
$ ./pp64.bin --elem-cnt-min=3 -o wordlist.txt < words
```

The command above will output words with three elements or more (i.e. "`dogdogdog`").
### CeWL
[CeWL](https://github.com/digininja/CeWL) is another tool that can be used to create custom wordlists.

* CeWL spiders and scrapes a website and creates a list of the words that are present. 
* This kind of wordlist is effective, as people tend to use passwords associated with the content they write or operate on. 
* For example, a blogger who blogs about nature, wildlife, etc. could have a password associated with those topics.
* Organizations often have passwords associated with their branding and industry-specific vocabulary. 
	* For example, users of a networking company may have passwords consisting of words like `router`, `switch`, `server`. Such words can be found on their websites under blogs, testimonials, and product descriptions.
#### CeWL - Syntax
The general syntax of `CeWL` is as follows:
```bash
$ cewl -d <depth to spider> -m <minimum word length> -w <output wordlist> <url of website>
```

CeWL can spider multiple pages present on a given website.

The length of the outputted words can be altered using the "`-m`" parameter, depending on the password requirements (i.e. minimum password length).

CeWL also supports the extraction of emails from websites with the "`-e`" option. 

It's helpful to get this information when phishing, password spraying, or brute-forcing passwords later.
#### CeWL - Example
```bash
$ cewl -d 5 -m 8 -e http://inlanefreight.com/blog -w wordlist.txt
```

The command above scrapes up to a depth of five pages from "`http://inlanefreight.com/blog`", and includes only words greater than 8 in length.
### Previously Cracked Passwords
By default, hashcat stores all cracked passwords in the `hashcat.potfile` file; the format is `hash:password`. 

The main purpose of this file is to remove previously cracked hashes from the work log and display cracked passwords with the `--show` command. 

However, it can be used to create new wordlists of previously cracked passwords, and when combined with rule files, it can prove quite effective at cracking themed passwords:
```shell-session
$ cut -d: -f 2- ~/hashcat.potfile
```
### Hashcat-utils
The Hashcat-utils [repo](https://github.com/hashcat/hashcat-utils) contains many utilities that can be useful for more advanced password cracking. 

The tool [maskprocessor](https://github.com/hashcat/maskprocessor), for example, can be used to create wordlists using a given mask. Detailed usage for this tool can be found [here](https://hashcat.net/wiki/doku.php?id=maskprocessor).

`maskprocessor` can be used to append all special characters to the end of a word:
```bash
$ /mp64.bin Welcome?s

Welcome 
Welcome!
Welcome"
Welcome#
Welcome$
Welcome%
Welcome&
Welcome'
Welcome(
Welcome)
Welcome*
Welcome+

<SNIP>
```
## Working with Rules
The rule-based attack is the most advanced and complex password cracking mode. 

Rules help perform various operations on the input wordlist, such as:
* prefixing, 
* suffixing, 
* toggling case, 
* cutting, 
* reversing, 
* and much more. 

Rules take mask-based attacks to another level and provide increased cracking rates. Additionally, the usage of rules saves disk space and processing time incurred as a result of larger wordlists.

A rule can be created using functions, which take a word as input and output it's modified version. 

The following table describes some functions which are compatible with ``JtR`` as well as ``Hashcat``:

| Function | Description                                                        | Input                                 | Output                                                                                                            |
| -------- | ------------------------------------------------------------------ | ------------------------------------- | ----------------------------------------------------------------------------------------------------------------- |
| l        | Convert all letters to lowercase                                   | InlaneFreight2020                     | inlanefreight2020                                                                                                 |
| u        | Convert all letters to uppercase                                   | InlaneFreight2020                     | INLANEFREIGHT2020                                                                                                 |
| c / C    | Capitalize / lowercase first letter and invert the rest            | inlaneFreight2020 / Inlanefreight2020 | Inlanefreight2020 / iNLANEFREIGHT2020                                                                             |
| t / TN   | Toggle case : whole word / at position N                           | InlaneFreight2020                     | iNLANEfREIGHT2020                                                                                                 |
| d / q    | Duplicate word / all characters / first character / last character | InlaneFreight2020                     | InlaneFreight2020InlaneFreight2020 / IInnllaanneeFFrreeiigghhtt22002200 / IInlaneFreight2020 / InlaneFreight20200 |
| { / }    | Rotate word left / right                                           | InlaneFreight2020                     | nlaneFreight2020I / 0InlaneFreight202                                                                             |
| ^X / $X  | Prepend / Append character X                                       | InlaneFreight2020 (^! / $! )          | !InlaneFreight2020 / InlaneFreight2020!                                                                           |
| r        | Reverse                                                            | InlaneFreight2020                     | 0202thgierFenalnI                                                                                                 |

A complete list of functions can be found [here](https://hashcat.net/wiki/doku.php?id=rule_based_attack#implemented_compatible_functions). 

Sometimes, the input wordlists contain words that don't match our target specifications. 

For example, a company's password policy might not allow users to set passwords less than 7 characters in length. In such cases, rejection rules can be used to prevent the processing of such words.

Words of length less than N can be rejected with `>N`, while words greater than N can be rejected with `<N`. 

A list of rejection rules can be found [here](https://hashcat.net/wiki/doku.php?id=rule_based_attack#rules_used_to_reject_plains).
>[!Note]
Reject rules only work either with `hashcat-legacy`, or when using `-j` or `-k` with `Hashcat`. They will not work as regular rules (in a rule file) with `Hashcat`.
### Example Rule Creation
Let's look at how we can create a rule based on common passwords. 

Usual user behavior suggests that they tend to replace letters with similar numbers, like

* "`o`" can be replaced with "`0`" 
* or "`i`" can be replaced with "`1`". 

This is commonly known as `L33tspeak` and is very efficient. Corporate passwords are often prepended or appended by a year. Let's create a rule to generate such words.
#### Rules
```shell-session
c so0 si1 se3 ss5 sa@ $2 $0 $1 $9
```

* The first letter word is capitalized with the `c` function. 
* Then rule uses the substitute function `s` to replace
	* `o` with `0`,
	* `i` with `1`,
	* `e` with `3` 
	* ``a`` with `@`. 

At the end, the year `2019` is appended to it. Copy the rule to a file so that we can debug it.
#### Create a Rule File
```bash
$ echo 'c so0 si1 se3 ss5 sa@ $2 $0 $1 $9' > rule.txt
```
#### Store the Password in a File
```bash
$ echo 'password_ilfreight' > test.txt
```
#### Hashcat - Debugging Rules
Rules can be debugged using the "`-r`" flag to specify the rule, followed by the wordlist.
```bash
$ hashcat -r rule.txt test.txt --stdout

P@55w0rd_1lfr31ght2019
```

As expected, the first letter was capitalized, and the letters were replaced with numbers. 

Let's consider the password "`St@r5h1p2019`". We can create the `SHA1` hash of this password via the command line:
```bash
$ echo -n 'St@r5h1p2019' | sha1sum | awk '{print $1}' | tee hash
```
#### Hashcat - Cracking Passwords Using Wordlists and Rules
We can then use the custom rule created above and the `rockyou.txt` dictionary file to crack the hash using `Hashcat`.
```bash
$ hashcat -a 0 -m 100 hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt -r rule.txt

hashcat (v6.1.1) starting...
<SNIP>

08004e35561328e357e34d07c53c7e4f41944e28:St@r5h1p2019
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: SHA1
Hash.Target......: 08004e35561328e357e34d07c53c7e4f41944e28
Time.Started.....: Fri Aug 28 22:17:13 2020, (3 secs)
Time.Estimated...: Fri Aug 28 22:17:16 2020, (0 secs)
Guess.Base.......: File (/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt)
Guess.Mod........: Rules (rule.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  3519.2 kH/s (0.39ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 10592256/14344385 (73.84%)
Rejected.........: 0/10592256 (0.00%)
Restore.Point....: 10586112/14344385 (73.80%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: St0p69692019 -> S0r051x53nt2019
```

We were able to crack the hash with our custom rule and rockyou.txt. 
#### Hashcat - Default Rules
Hashcat supports the usage of multi-rules with repeated use of the `-r` flag. 

`Hashcat` installs with a variety of rules by default. They can be found in the rules folder:
```bash
$ ls /usr/share/hashcat/rules/

total 2576

best64.rule
combinator.rule
d3ad0ne.rule
dive.rule
generated2.rule
generated.rule
hybrid
Incisive-leetspeak.rule
InsidePro-HashManager.rule
InsidePro-PasswordsPro.rule
leetspeak.rule
oscommerce.rule
rockyou-30000.rule
specific.rule
T0XlC-insert_00-99_1950-2050_toprules_0_F.rule
T0XlC-insert_space_and_special_0_F.rule
T0XlC-insert_top_100_passwords_1_G.rule
T0XlC.rule
T0XlCv1.rule
toggles1.rule
toggles2.rule
toggles3.rule
toggles4.rule
toggles5.rule
unix-ninja-leetspeak.rule
```

It is always better to try using these rules before going ahead and creating custom rules.

`Hashcat` provides an option to generate random rules on the fly and apply them to the input wordlist. 
#### Hashcat - Generate Random Rules
The following command will generate 1000 random rules and apply them to each word from `rockyou.txt` by specifying the "`-g`" flag. There is no certainty to the success rate of this attack as the generated rules are not constant:
```bash
$ hashcat -a 0 -m 100 -g 1000 hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt
```

There are a variety of publicly available rules as well, such as the [nsa-rules](https://github.com/NSAKEY/nsa-rules), [Hob0Rules](https://github.com/praetorian-code/Hob0Rules), and the [corporate.rule](https://github.com/sparcflow/HackLikeALegend/blob/master/old/chap3/corporate.rule) which is featured in the book [How to Hack Like a Legend](https://www.sparcflow.com/new-release-hack-like-legend/). 

These are curated rulesets generally targeted at common corporate Windows password policies or based on statistics and probably industry password patterns.

On an engagement or password cracking exercise, it is generally best to start with small targeted wordlists and rule sets, especially if the password policy is known or we have a general idea of the policy. 

Extremely large dictionary files combined with large rule sets can be effective as well. Still, we are limited by our computing power (i.e., a laptop with a single CPU). 
# Cracking
## Cracking Common Hashes
### Common Hash Types
The list includes the hash mode, hash name, and a sample hash of the specified type. Some of the most commonly seen hashes are:

| Hashmode | Hash Name                          | Example Hash                                                                     |
| -------- | ---------------------------------- | -------------------------------------------------------------------------------- |
| 0        | ``MD5``                            | ``8743b52063cd84097a65d1633f5c74f5``                                             |
| 100      | ``SHA1``                           | ``b89eaac7e61417341b710b727768294d0e6a277b``                                     |
| 1000     | ``NTLM``                           | ``b4b9b02e6f09a9bd760f388b67351e2b``                                             |
| 1800     | ``sha512crypt $6$, SHA512 (Unix)`` | ``$6$52450745$k5ka2p8bFuSmoVT1t <SNIP>``                                         |
| 3200     | ``bcrypt $2\*$, Blowfish (Unix)``  | ``$2a$05$LhayLxezLhK1LhWvKxCyLOj0j1u.Kj0jZ0pEmm134uzrQlFvQJLF6``                 |
| 5500     | ``NetNTLMv1`` / ``NetNTLMv1+ESS``  | ``u4-netntlm::kNS:338d08f8e26de9330000000000000000000000 <SNIP>``                |
| 5600     | ``NetNTLMv2``                      | ``admin::N46iSNekpT:08ca45b7d7ea58ee:88dcbe4446168966a153a0064958da <SNIP>``     |
| 13100    | ``Kerberos 5 TGS-REP etype 23``    | ``$krb5tgs$23$_user$realm$test/spn_$63386d22d359fe42230300d56852c9eb$ < SNIP >`` |

### Example 1 - Database Dumps
MD5, SHA1, and bcrypt hashes are often seen in database dumps. 
* These hashes may be retrieved following a successful SQL injection attack or found in publicly available password data breach database dumps. 
* MD5 and SHA1 are typically easier to crack than bcrypt, which may have many rounds of the Blowfish algorithm applied.

Let's crack some SHA1 hashes. Take the following list:
```bash
winter!
baseball1
waterslide
summertime
baconandeggs
beach1234
sunshine1
welcome1
password123
```

We can create a SHA1 of each word quickly:
```bash
$ for i in $(cat words); do echo -n $i | sha1sum | tr -d ' -';done

fa3c9ecfc251824df74026b4f40e4b373fd4fc46
e6852777c0260493de41fb43918ab07bbb3a659c
0c3feaa16f73493f998970e22b2a02cb9b546768
b863c49eada14e3a8816220a7ab7054c28693664
b0feedd70a346f7f75086026169825996d7196f9
f47f832cba913ec305b07958b41babe2e0ad0437
08b314f0e1e2c41ec92c3735910658e5a82c6ba7
e35bece6c5e6e0e86ca51d0440e92282a9d6ac8a
cbfdac6008f9cab4083784cbd1874f76618d2a97
```

We can then run these through a `Hashcat` dictionary attack using the `rockyou.txt` wordlist.
```bash
$ hashcat -m 100 SHA1_hashes /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...
<SNIP>

08b314f0e1e2c41ec92c3735910658e5a82c6ba7:sunshine1
e35bece6c5e6e0e86ca51d0440e92282a9d6ac8a:welcome1
e6852777c0260493de41fb43918ab07bbb3a659c:baseball1
b863c49eada14e3a8816220a7ab7054c28693664:summertime
fa3c9ecfc251824df74026b4f40e4b373fd4fc46:winter! 
b0feedd70a346f7f75086026169825996d7196f9:baconandeggs
f47f832cba913ec305b07958b41babe2e0ad0437:beach1234
0c3feaa16f73493f998970e22b2a02cb9b546768:waterslide
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: SHA1
Hash.Target......: SHA1_hashes
Time.Started.....: Fri Aug 28 22:22:56 2020, (1 sec)
Time.Estimated...: Fri Aug 28 22:22:57 2020, (0 secs)
Guess.Base.......: File (/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  2790.2 kH/s (0.33ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 9/9 (100.00%) Digests
Progress.........: 1173504/14344385 (8.18%)
Rejected.........: 0/1173504 (0.00%)
Restore.Point....: 1167360/14344385 (8.14%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: whitenerdy -> warut69
```

The above hashes cracked very quickly as they are common words/phrases with little to no complexity. 

Variations on the above list, such as "`Bas3b@ll1`" or "`Wat3rSl1de`" would likely take longer to crack and may require additional techniques such as mask and hybrid attacks.
### Example 2 - Linux Shadow File
``Sha512crypt`` hashes are commonly found in the `/etc/shadow` file on Linux systems. 

We may exploit a service that is already running in the context of the highest privileged `root` account and perform a successful privesc attack and access the `/etc/shadow` file. 

Password re-use is widespread. A cracked password may give us access to other servers, network devices, or even be used as a foothold into a target's Active Directory environment.

Let's look at a hash from a standard Ubuntu installation. The corresponding plaintext for the following hash is "`password123`":
```bash
root:$6$tOA0cyybhb/Hr7DN$htr2vffCWiPGnyFOicJiXJVMbk1muPORR.eRGYfBYUnNPUjWABGPFiphjIjJC5xPfFUASIbVKDAHS3vTW1qU.1:18285:0:99999:7:::
```

* The hash contains nine fields separated by colons. 
	* The first two fields contain the username and its encrypted hash. 
	* The rest of the fields contain various attributes such as
		* password creation time, 
		* last change time, 
		* and expiry.

Coming to the hash, we already know that it contains three fields delimited by "`$`". The value "`6`" stands for the SHA-512 hashing algorithm; the next 16 characters represent the salt, while the rest of it is the actual hash.

Let's crack this hash using `Hashcat`:
```bash
$ hashcat -m 1800 nix_hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...
<SNIP>

$6$tOA0cyybhb/Hr7DN$htr2vffCWiPGnyFOicJiXJVMbk1muPORR.eRGYfBYUnNPUjWABGPFiphjIjJC5xPfFUASIbVKDAHS3vTW1qU.1:password123
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: sha512crypt $6$, SHA512 (Unix)
Hash.Target......: $6$tOA0cyybhb/Hr7DN$htr2vffCWiPGnyFOicJiXJVMbk1muPO...W1qU.1
Time.Started.....: Fri Aug 28 22:25:26 2020, (1 sec)
Time.Estimated...: Fri Aug 28 22:25:27 2020, (0 secs)
Guess.Base.......: File (/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:      955 H/s (4.62ms) @ Accel:32 Loops:256 Thr:1 Vec:4
Recovered........: 1/1 (100.00%) Digests
Progress.........: 1536/14344385 (0.01%)
Rejected.........: 0/1536 (0.00%)
Restore.Point....: 1344/14344385 (0.01%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:4864-5000
Candidates.#1....: teacher -> mexico1
```
### Example 3 - Common Active Directory Password Hash Types
Credential theft and password re-use are widespread tactics during assessments against organizations using Active Directory to manage their environment. 

It is often possible to obtain credentials in cleartext or re-use password hashes to further access via **Pass-the-Hash** or **SMB Relay attacks**. 

Still, some techniques will result in a password hash that must be cracked offline to further our access. Some examples include:
* a ``NetNTLMv1`` or ``NetNTLMv2`` obtained through a **Man-in-the-middle (MITM)** attack, 
* a ``Kerberos 5 TGS-REP`` hash obtained through a **Kerberoasting** attack, 
* an ``NTLM`` hash obtained either by dumping credentials from memory using the `Mimikatz` tool or obtained from a Windows machine's local SAM database.
#### NTLM
One example is retrieving an NTLM password hash for a user that has Remote Desktop (RDP) access to a server but is not a local administrator, so the NTLM hash cannot be used for a pass-the-hash attack to gain access. 

In this case, the cleartext password is necessary to further our access by connecting to the server via RDP and performing further enumeration within the network or looking for local privilege escalation vectors.
#### Python3 - Hashlib
Let's walk through an example. We can quickly generate an NTLM hash of the password "`Password01`" for our purposes using 3 lines of Python:
```bash
$ python3

Python 3.8.3 (default, May 14 2020, 11:03:12) 
[GCC 9.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.

>>> import hashlib,binascii
>>> hash = hashlib.new('md4', "Password01".encode('utf-16le')).digest()
>>> print (binascii.hexlify(hash))

b'7100a909c7ff05b266af3c42ec058c33'
```
#### Cracking NTLM Hashes
We can then run the resultant NTLM password hash value "`7100a909c7ff05b266af3c42ec058c33`" through `Hashcat` using the standard `rockyou.txt` wordlist to retrieve the cleartext.
```bash
$ hashcat -a 0 -m 1000 ntlm_example /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...
<SNIP>

7100a909c7ff05b266af3c42ec058c33:Password01      
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: NTLM
Hash.Target......: 7100a909c7ff05b266af3c42ec058c33
Time.Started.....: Fri Aug 28 22:27:40 2020, (0 secs)
Time.Estimated...: Fri Aug 28 22:27:40 2020, (0 secs)
Guess.Base.......: File (/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  2110.5 kH/s (0.62ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 61440/14344385 (0.43%)
Rejected.........: 0/61440 (0.00%)
Restore.Point....: 55296/14344385 (0.39%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: gonoles -> sinead1
```

Now, armed with the cleartext password, we can further our access within the network.
#### NetNTLMv2
During a pentest it's common to run tools such as [Responder](https://github.com/lgandx/Responder)to perform MITM attacks to attempt to "steal" credentials.

In busy corporate networks it is common to retrieve many NetNTLMv2 password hashes using this method. 

These can often be cracked and leveraged to establish a foothold in the Active Directory environment or sometimes even gain full admin access to many or all systems depending on the privileges granted to the user account associated with the password hash. 

Consider the password hash below retrieved using `Responder` at the beginning of an assessment:
```bash
sqladmin::INLANEFREIGHT:f54d6f198a7a47d4:7FECABAE13101DAAA20F1B09F7F7A4EA:0101000000000000C0653150DE09D20126F3F71DF13C1FD8000000000200080053004D004200330001001E00570049004E002D00500052004800340039003200520051004100460056000400140053004D00420033002E006C006F00630061006C0003003400570049004E002D00500052004800340039003200520051004100460056002E0053004D00420033002E006C006F00630061006C000500140053004D00420033002E006C006F00630061006C0007000800C0653150DE09D201060004000200000008003000300000000000000000000000003000001A67637962F2B7BF297745E6074934196D5F4371B6BA3E796F2997306FD4C1C00A001000000000000000000000000000000000000900280063006900660073002F003100390032002E003100360038002E003100390035002E00310037003000000000000000000000000000
```

Some tools, such as `Responder`, will inform you what type of hash was received. (We can check the [Example hashes](https://hashcat.net/wiki/doku.php?id=example_hashes) page to confirm that this is indeed a NetNTLMv2 hash)
#### Cracking NTLMv2 Hashes
We can run this hash with `Hashcat` using the `rockyou.txt` wordlist to perform an offline dictionary attack:
```bash
$ hashcat -a 0 -m 5600 inlanefreight_ntlmv2 /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...
<SNIP>

SQLADMIN::INLANEFREIGHT:f54d6f198a7a47d4:7fecabae13101daaa20f1b09f7f7a4ea:0101000000000000c0653150de09d20126f3f71df13c1fd8000000000200080053004d004200330001001e00570049004e002d00500052004800340039003200520051004100460056000400140053004d00420033002e006c006f00630061006c0003003400570049004e002d00500052004800340039003200520051004100460056002e0053004d00420033002e006c006f00630061006c000500140053004d00420033002e006c006f00630061006c0007000800c0653150de09d201060004000200000008003000300000000000000000000000003000001a67637962f2b7bf297745e6074934196d5f4371b6ba3e796f2997306fd4c1c00a001000000000000000000000000000000000000900280063006900660073002f003100390032002e003100360038002e003100390035002e00310037003000000000000000000000000000:Database99
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: NetNTLMv2
Hash.Target......: SQLADMIN::INLANEFREIGHT:f54d6f198a7a47d4:7fecabae13...000000
Time.Started.....: Fri Aug 28 22:29:26 2020, (6 secs)
Time.Estimated...: Fri Aug 28 22:29:32 2020, (0 secs)
Guess.Base.......: File (/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  1754.7 kH/s (2.32ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 11237376/14344385 (78.34%)
Rejected.........: 0/11237376 (0.00%)
Restore.Point....: 11231232/14344385 (78.30%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: Devanique -> Darrylw
```

Armed with these credentials, we're off to the races and can begin enumerating and attacking Active Directory.
## Cracking Miscellaneous Files & Hashes
It's very common to encounter password-protected documents such as:
* Microsoft Word,
* Excel documents,
* OneNote notebooks,
* KeePass database files,
* SSH private key passphrases,
* PDF files,
* zip (and other archive formats) files,
* and more. 
The majority of these hashes can be run through `Hashcat` to attempt to crack the hashes.
### Tools
Various tools exist to help us extract the password hashes from these files in a format that `Hashcat` can understand. 

`JohnTheRipper` comes with many of these tools written in C.
```bash
$ sudo git clone https://github.com/magnumripper/JohnTheRipper.git
$ cd JohnTheRipper/src
$ sudo ./configure && sudo make
```

There are also Python ports of most of these tools available that are very easy to work with (`JohnTheRipper` jumbo repo [here](https://github.com/magnumripper/JohnTheRipper/tree/bleeding-jumbo/run)).

One additional tool ported to Python by @Harmj0y is the [keepass2john.py](https://gist.github.com/HarmJ0y/116fa1b559372804877e604d7d367bbc#file-keepass2john-py) tool for extracting a crackable hash from KeePass 1.x/2.x databases that can be run through `Hashcat`
### Example 1 - Cracking Password Protected Microsoft Office Documents

`Hashcat` can be used to attempt to crack password hashes extracted from some Microsoft Office documents using the [office2john.py](https://raw.githubusercontent.com/magnumripper/JohnTheRipper/bleeding-jumbo/run/office2john.py) tool.

`Hashcat` supports the following hash modes for Microsoft Office documents:
| **Mode** | **Target**     |
| -------- | -------------- |
| `9400`   | MS Office 2007 |
| `9500`   | MS Office 2010 |
| `9600`   | MS Office 2013 |

| Mode | Target         |
| ---- | -------------- |
| 9400 | MS Office 2007 |
| 9500 | MS Office 2010 |
| 9600 | MS Office 2013 |

There are also several "`$oldoffice$`" hash modes for MS Office documents older than 2003. 
#### Extract Hash
Let's take a Word document protected with the password "`pa55word`". We can first extract the hash from the document using `office2john.py`:
```bash
$ python office2john.py hashcat_Word_example.docx 

hashcat_Word_example.docx:$office$*2013*100000*256*16*6e059661c3ed733f5730eaabb41da13a*aa38e007ee01c07e4fe95495934cf68f*2f1e2e9bf1f0b320172cd667e02ad6be1718585b6594691907b58191a6
```

#### Hashcat - Cracking MS Office Passwords
We can then run the hash through `Hashcat` using mode `9600` and make short work of it with the `rockyou.txt` wordlist:
```bash
$ hashcat -m 9600 office_hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...
<SNIP>

$office$*2013*100000*256*16*6e059661c3ed733f5730eaabb41da13a*aa38e007ee01c07e4fe95495934cf68f*2f1e2e9bf1f0b320172cd667e02ad6be1718585b6594691907b58191a6489940:pa55word
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: MS Office 2013
Hash.Target......: $office$*2013*100000*256*16*6e059661c3ed733f5730eaa...489940
Time.Started.....: Fri Aug 28 22:32:08 2020, (18 secs)
Time.Estimated...: Fri Aug 28 22:32:26 2020, (0 secs)
Guess.Base.......: File (/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:      327 H/s (5.58ms) @ Accel:1024 Loops:32 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 6144/14344385 (0.04%)
Rejected.........: 0/6144 (0.00%)
Restore.Point....: 0/14344385 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: 123456 -> iheartyou
```
### Example 2 - Cracking Password Protected Zip Files
We can extract hashes from zip files using the compiled version of the [zip2john](https://github.com/magnumripper/JohnTheRipper/blob/bleeding-jumbo/src/zip2john.c) tool. 

`Hashcat` supports a variety of compressed file formats such as:

| Mode  | Target                                      |
| ----- | ------------------------------------------- |
| 11600 | 7-Zip                                       |
| 13600 | WinZip                                      |
| 17200 | PKZIP (Compressed)                          |
| 17210 | PKZIP (Uncompressed)                        |
| 17220 | PKZIP (Compressed Multi-File)               |
| 17225 | PKZIP (Mixed Multi-File)                    |
| 17230 | PKZIP (Compressed Multi-File Checksum-Only) |
| 23001 | SecureZIP AES-128                           |
| 23002 | SecureZIP AES-192                           |
| 23003 | SecureZIP AES-256                           |


For our example, we can take any document and add it to a password protected zip file using the following command:
```bash
$ zip --password zippyzippy blueprints.zip dummy.pdf 

adding: dummy.pdf (deflated 7%)
```

We can then use the compiled version of `zip2john` to extract the hash in a format that can be run through `Hashcat`.
```bash
$ zip2john ~/Desktop/HTB/Academy/Cracking\ with\ Hashcat/blueprints.zip 

ver 2.0 efh 5455 efh 7875 blueprints.zip/dummy.pdf PKZIP Encr: 2b chk, TS_chk, cmplen=12324, decmplen=13264, crc=7EB29321
blueprints.zip/dummy.pdf:$pkzip2$1*2*2*0*3024*33d0*7eb29321*0*43*8*3024*7eb2*69f2*d796c9cde7b7ed8d7b76c1efd12d222d2bfcc7a2e5a94b21a55c965c36c5875ea17ba1ca63d8164d...SNIP...
*$/pkzip2$:dummy.pdf:blueprints.zip::/blueprints.zip
```
We can see from that hash that this is mode `17200 - PKZIP (Compressed)`. 

To run this through `Hashcat`, we need the entire hash starting from `$pkzip2$1` and ending with `/pkzip2$`. 

Armed with the hash, let's run it with `Hashcat` using a straight dictionary attack:
```bash
$ hashcat -a 0 -m 17200 pdf_hash_to_crack /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...
<SNIP>

$pkzip2$1*2 <FULL HASH SNIPPED> k*$/pkzip2$:zippyzippy
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: PKZIP (Compressed)
Hash.Target......: $pkzip2$1*2*2*0*3024*33d0*7eb29321*0*43*8*3024*7eb2...kzip2$
Time.Started.....: Fri Aug 28 22:34:46 2020, (1 sec)
Time.Estimated...: Fri Aug 28 22:34:47 2020, (0 secs)
Guess.Base.......: File (/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  3665.1 kH/s (0.32ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 2500608/14344385 (17.43%)
Rejected.........: 0/2500608 (0.00%)
Restore.Point....: 2494464/14344385 (17.39%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: zj4usm0z -> zietz5632

Started: Fri Aug 28 22:34:24 2020
Stopped: Fri Aug 28 22:34:48 2020
```
We can now use this password to extract the contents from the zip file.
### Example 3 - Cracking Password Protected KeePass Files
It is not uncommon to find KeePass files on a sysadmin's workstation or on an file share. 

These are often a treasure trove of credentials because systems administrators, network administrators, help desk, etc. may store various passwords in a shared KeePass database. 

Gaining access may provide local admin passwords to Windows machines, passwords to infrastructure such as ESXi and vCenter, access to network devices, and more.

We can extract these hashes using the compiled version of the [keepass2john](https://github.com/magnumripper/JohnTheRipper/blob/bleeding-jumbo/src/keepass2john.c) tool or using the Python port done by [HarmJ0y](https://gist.github.com/HarmJ0y), [keepass2john.py](https://gist.githubusercontent.com/HarmJ0y/116fa1b559372804877e604d7d367bbc/raw/c0c6f45ad89310e61ec0363a69913e966fe17633/keepass2john.py).

`Hashcat` supports a variety of compressed file formats such as:

`Hashcat` supports the following hash names for KeePass databases, all designated by the same hash mode:

| Mode  | Target                           |
| ----- | -------------------------------- |
| 13400 | KeePass 1 AES / without keyfile  |
| 13400 | KeePass 2 AES / without keyfile  |
| 13400 | KeePass 1 Twofish / with keyfile |
| 13400 | Keepass 2 AES / with keyfile     |

We can use `keepass2john.py` to extract the hash:
```bash
$ python keepass2john.py Master.kdbx 

Master:$keepass$*2*60000*222*d14132325949a3b4efacdb2e729ec54403308c85654fe4ababccfb8ddc185d09*5c09bed9c98f8ee08aa7a71fe735b30849ec87e6cb7f1caa96d606ce9f077f7e*bd372d79d8aceea9689ad49428b8efde*28d21caedf25617db0833bd721a42c963e874e0b9fbe7fe1187a4a8ecb3b1d19*a539abd3cfd7ee5982fa28c44dd226ce05a1102d04a5f590eabf5138cd2a6403
```

From the output, we can see that this is indeed a KeePass hash, and the type is `KeePass 2 AES / without keyfile`. 

With the hash in hand, we can attack it with a `Hashcat` dictionary attack. 

This hash uses the [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard_process) cipher, which is more difficult to crack and runs slower through `Hashcat` than other hashes such as `MD5` or `SHA1`. Therefore a very complex password may be difficult to crack.

```bash
$ hashcat -a 0 -m 13400 keepass_hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...
<SNIP>

$keepass$*2*60000*222*d14132325949a3b4efacdb2e729ec54403308c85654fe4ababccfb8ddc185d09*5c09bed9c98f8ee08aa7a71fe735b30849ec87e6cb7f1caa96d606ce9f077f7e*bd372d79d8aceea9689ad49428b8efde*28d21caedf25617db0833bd721a42c963e874e0b9fbe7fe1187a4a8ecb3b1d19*a539abd3cfd7ee5982fa28c44dd226ce05a1102d04a5f590eabf5138cd2a6403:1qazzaq1
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: KeePass 1 (AES/Twofish) and KeePass 2 (AES)
Hash.Target......: $keepass$*2*60000*222*d14132325949a3b4efacdb2e729ec...2a6403
Time.Started.....: Fri Aug 28 22:37:08 2020, (2 mins, 12 secs)
Time.Estimated...: Fri Aug 28 22:39:20 2020, (0 secs)
Guess.Base.......: File (/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:      430 H/s (3.75ms) @ Accel:256 Loops:64 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 56832/14344385 (0.40%)
Rejected.........: 0/56832 (0.00%)
Restore.Point....: 55296/14344385 (0.39%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:59968-60000
Candidates.#1....: gonoles -> jacoblee
```

The resulting password `1qazzaq1` is an example of a [keyboard walk](https://github.com/hashcat/kwprocessor) password.
### Example 4 - Cracking Protected PDF Files
The last example focuses on password-protected PDF documents. 

As with other file types, we often encounter password-protected PDFs on workstations, file shares, or even inside a user's email inbox.

We can extract the hash of the passphrase using [pdf2john.py](https://raw.githubusercontent.com/truongkma/ctf-tools/master/John/run/pdf2john.py). 

The following command will extract the hash into a format that `Hashcat` can use:
```bash
$ python pdf2john.py inventory.pdf | awk -F":" '{ print $2}'

$pdf$4*4*128*-1028*1*16*f7d77b3d22b9f92829d49ff5d78b8f28*32*d33f35f776215527d65155f79d9ed79800000000000000000000000000000000*32*6cfb859c107acaae8c0ca9ceec56fd91ff75fe7b1cddb03f629ca3583f59e52f
```

`Hashcat` supports a variety of compressed file formats such as:

| Mode  | Target                                     |
| ----- | ------------------------------------------ |
| 10400 | PDF 1.1 - 1.3 (Acrobat 2 - 4)              |
| 10410 | PDF 1.1 - 1.3 (Acrobat 2 - 4), collider #1 |
| 10420 | PDF 1.1 - 1.3 (Acrobat 2 - 4), collider #2 |
| 10500 | PDF 1.4 - 1.6 (Acrobat 5 - 8)              |
| 10600 | PDF 1.7 Level 3 (Acrobat 9)                |
| 10700 | PDF 1.7 Level 8 (Acrobat 10 - 11)          |

We can crack the hash with mode `10500`:
```bash
$ hashcat -a 0 -m 10500 pdf_hash /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...
<SNIP>

$pdf$4*4*128*-1028*1*16*f7d77b3d22b9f92829d49ff5d78b8f28*32*d33f35f776215527d65155f79d9ed79800000000000000000000000000000000*32*6cfb859c107acaae8c0ca9ceec56fd91ff75fe7b1cddb03f629ca3583f59e52f:puppydog1
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: PDF 1.4 - 1.6 (Acrobat 5 - 8)
Hash.Target......: $pdf$4*4*128*-1028*1*16*f7d77b3d22b9f92829d49ff5d78...59e52f
Time.Started.....: Fri Aug 28 22:41:07 2020, (0 secs)
Time.Estimated...: Fri Aug 28 22:41:07 2020, (0 secs)
Guess.Base.......: File (/opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:   244.2 kH/s (20.86ms) @ Accel:128 Loops:8 Thr:64 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 49153/14344385 (0.34%)
Rejected.........: 1/49153 (0.00%)
Restore.Point....: 0/14344385 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:64-70
Candidates.#1....: 123456 -> truckin
```
## Cracking Wireless (WPA/WPA2) Handshakes with Hashcat
Clients often ask for wireless assessments as part of an Internal Pentest engagement. 

While wireless is not always the most exciting, it can get interesting if you can capture a WPA/WPA2 handshake.

Wireless networks are often not properly segmented from a company's corporate network, and successful authentication to the wireless network may grant full access to the internal corporate network.

`Hashcat` can be used to successfully crack both the **MIC** (4-way handshake) and **PMKID** (1st packet/handshake).
### Cracking MIC
When a client connecting to the wireless network and the wireless access point (AP) communicate, they must ensure that they both have/know the wireless network key but are not transmitting the key across the network. The key is encrypted and verified by the AP.

To perform this type of offline cracking attack, we need to capture a valid 4-way handshake by sending de-authentication frames to force a client (user) to disconnect from an AP. 

When the client re-authenticates (usually automatically), the attacker can attempt to sniff out the WPA **4-way handshake** without their knowledge. 

This handshake is a collection of keys exchanged during the authentication process between the client and the associated AP. 

These keys are used to generate a common key called the **Message Integrity Check** (**MIC**) used by an AP to verify that each packet has not been compromised and received in its original state.

The 4-way handshake is illustrated in the following diagram:
![[NEW_4-way-handshake.png]]
Once we have successfully captured a 4-way handshake with a tool such as [airodump-ng](https://www.aircrack-ng.org/doku.php?id=airodump-ng), we need to convert it to a format that can be supplied to `Hashcat` for cracking. 

The format required is `hccapx`, and `Hashcat` hosts an online service to convert to this format: [cap2hashcat online](https://hashcat.net/cap2hashcat).

To perform the conversion offline, we need the `hashcat-utils` repo from GitHub.

We can clone the repo and compile the tool as follows:
```bash
$ git clone https://github.com/hashcat/hashcat-utils.git

$ cd hashcat-utils/src
$ make
```

Once the tool is compiled, we can run it and see the usage options:
```bash
$ ./cap2hccapx.bin 

usage: ./cap2hccapx.bin input.cap output.hccapx [filter by essid] [additional network essid:bssid]
```

#### Cap2hccapx - Convert To Crackable File
Next, we need to supply a packet capture (``.cap``) file to the tool to convert to the ``.hccapx`` format to supply to `Hashcat`:
```bash
$ ./cap2hccapx.bin corp_capture1-01.cap mic_to_crack.hccapx

Networks detected: 1

[*] BSSID=cc:40:d0:a4:d0:96 ESSID=CORP-WIFI (Length: 9)
 --> STA=48:e2:44:a7:c4:fb, Message Pair=0, Replay Counter=1
 --> STA=48:e2:44:a7:c4:fb, Message Pair=2, Replay Counter=1
 --> STA=48:e2:44:a7:c4:fb, Message Pair=0, Replay Counter=1
 --> STA=48:e2:44:a7:c4:fb, Message Pair=2, Replay Counter=1
 --> STA=48:e2:44:a7:c4:fb, Message Pair=0, Replay Counter=1
 --> STA=48:e2:44:a7:c4:fb, Message Pair=2, Replay Counter=1
 --> STA=48:e2:44:a7:c4:fb, Message Pair=0, Replay Counter=1
 --> STA=48:e2:44:a7:c4:fb, Message Pair=2, Replay Counter=1

Written 8 WPA Handshakes to: /home/mrb3n/Desktop/mic_to_crack.hccapx
```
With this file, we can then move on to cracking using the techniques discussed earlier. 
#### Hashcat - Cracking WPA Handshakes
For this example, we will perform a straight dictionary attack to crack the WPA handshake. 

To attempt to crack this hash, we will use mode `22000`, as the previous mode `2500` has been deprecated. 

Our command for cracking this hash will look like this:
"`hashcat -a 0 -m 22000 mic_to_crack.hccapx /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt`":
```bash
$ hashcat -a 0 -m 22000 mic_to_crack.hccapx /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt

hashcat (v6.1.1) starting...

<SNIP>

18cbc1c03cd674c75bb81aee4a75a086:cc40d0a4d096:48e244a7c4fb:CORP-WIFI:rockyou1
62b1bb7345e110abaaf8304c096239b0:cc40d0a4d096:48e244a7c4fb:CORP-WIFI:rockyou1
be2430ce7a4ed2ddb36fc94373197add:cc40d0a4d096:48e244a7c4fb:CORP-WIFI:rockyou1
15c472b7641042af642fc9ec0b65b500:cc40d0a4d096:48e244a7c4fb:CORP-WIFI:rockyou1
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: WPA-PBKDF2-PMKID+EAPOL
Hash.Target......: mic_to_crack.hccapx
Time.Started.....: Wed Mar  9 11:20:36 2022 (0 secs)
Time.Estimated...: Wed Mar  9 11:20:36 2022 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:    10052 H/s (8.25ms) @ Accel:128 Loops:512 Thr:1 Vec:8
Recovered........: 4/4 (100.00%) Digests
Progress.........: 2888/14344385 (0.02%)
Rejected.........: 2120/2888 (73.41%)
Restore.Point....: 0/14344385 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:3-7
Candidates.#1....: 123456789 -> celtic07

Started: Wed Mar  9 11:20:26 2022
Stopped: Wed Mar  9 11:20:38 2022
```
### Cracking PMKID
This attack can be performed against wireless networks that use **WPA/WPA2-PSK (pre-shared key)** and allows us to obtain the PSK being used by the targeted wireless network by attacking the AP directly. 
* The attack does **not** require **deauthentication** (deauth) of any users from the target AP. 
* The PMK is the same as in the **MIC** (4-way handshake) attack but can generally be obtained faster and without interrupting any users.
* The Pairwise Master Key Identifier (PMKID) is the AP's unique identifier to keep track of the Pairwise Master Key (PMK) used by the client. 
* The PMKID is located in the 1st packet of the 4-way handshake and can be easier to obtain since it does not require capturing the entire 4-way handshake. 
* PMKID is calculated with HMAC-SHA1 with the PMK (Wireless network password) used as a key, the string "PMK Name," MAC address of the access point, and the MAC address of the station. 

Below is a visual representation of the PMKID calculation:
![[Pasted image 20250309131807.png]]
To perform PMKID cracking, we need to obtain the pmkid hash. 

The first step is extracting it from the capture (``.cap``) file using a tool such as `hcxpcapngtool` from `hcxtools`. 
>[!Note]
In the past, this technique was able to be performed with `hcxpcaptool`, which has since been [replaced by](https://github.com/ZerBea/hcxtools/issues/166) `hcxpcapngtool` which we can compile and install directly from the [hcxtools GitHub repo](https://github.com/ZerBea/hcxtools), or via apt (`sudo apt install hcxtools`).

This can also be performed with the deprecated tool ``hcxpcaptool``.

As mentioned above, we can install `hxctools`, which includes `hcxpcapngtool`, via `apt`. Alternatively, we can clone the `hcxtools` repository to our own VM, compile and install it.
#### Hcxtools - Installation
```bash
$ git clone https://github.com/ZerBea/hcxtools.git

$ cd hcxtools
$ make && make install
```

Once we've successfully installed `hcxtools`, we can issue the command "`hcxpcapngtool -h`" to check out the options available with the tool.
#### Hcxpcapngtool - Help
```bash
$ hcxpcapngtool -h

hcxpcapngtool 6.3.5-44-g6be8d76 (C) 2025 ZeroBeat
convert pcapng, pcap and cap files to hash formats that hashcat and JtR use
usage:
hcxpcapngtool <options>
hcxpcapngtool <options> input.pcapng
hcxpcapngtool <options> *.pcapng
hcxpcapngtool <options> *.pcap
hcxpcapngtool <options> *.cap
hcxpcapngtool <options> *.*

short options:
-o <file> : output WPA-PBKDF2-PMKID+EAPOL hash file (hashcat -m 22000)
            get full advantage of reuse of PBKDF2 on PMKID and EAPOL
-E <file> : output wordlist (autohex enabled on non ASCII characters) to use as input wordlist for cracker
            retrieved from every frame that contain an ESSID
-R <file> : output wordlist (autohex enabled on non ASCII characters) to use as input wordlist for cracker
            retrieved from PROBEREQUEST frames only
-I <file> : output unsorted identity list to use as input wordlist for cracker
-U <file> : output unsorted username list to use as input wordlist for cracker
-D <file> : output device information list
            format MAC MANUFACTURER MODELNAME SERIALNUMBER DEVICENAME UUID
-h        : show this help
-v        : show version

<SNIP>
```

Though the tool can be used for various tasks, we can use `hcxpcapngtool` to extract the hash as follows:
```bash
$ hcxpcapngtool cracking_pmkid.cap -o pmkidhash_corp

reading from cracking_pmkid.cap...

summary capture file
--------------------
file name................................: cracking_pmkid.cap
version (pcapng).........................: 1.0
operating system.........................: Linux 5.7.0-kali1-amd64
application..............................: hcxdumptool 6.0.7-22-g2f82e84
interface name...........................: wlan0
interface vendor.........................: 00c0ca
weak candidate...........................: 12345678
MAC ACCESS POINT.........................: 0c8112953006 (incremented on every new client)
MAC CLIENT...............................: fcc23374f354
REPLAYCOUNT..............................: 63795
ANONCE...................................: 4e0fee9e1a8961ca63b74023d90ac081d8677ae748b7050a559cf481cf50d31f
SNONCE...................................: 90d86a9fc2a314df52b3b36b9080c88e90488594f0aa83e84196bfce8b90d1ac
timestamp minimum (GMT)..................: 17.07.2020 10:07:19
timestamp maximum (GMT)..................: 17.07.2020 10:14:21
used capture interfaces..................: 1
link layer header type...................: DLT_IEEE802_11_RADIO (127)
endianess (capture system)...............: little endian
packets inside...........................: 75
frames with correct FCS..................: 75
BEACON (total)...........................: 1
PROBEREQUEST.............................: 3
PROBERESPONSE............................: 1
EAPOL messages (total)...................: 69
EAPOL RSN messages.......................: 69
ESSID (total unique).....................: 3
EAPOLTIME gap (measured maximum usec)....: 172313401
EAPOL ANONCE error corrections (NC)......: working
REPLAYCOUNT gap (suggested NC)...........: 5
EAPOL M1 messages (total)................: 47
EAPOL M2 messages (total)................: 18
EAPOL M3 messages (total)................: 4
EAPOL pairs (total)......................: 41
EAPOL pairs (best).......................: 1
EAPOL pairs written to combi hash file...: 1 (RC checked)
EAPOL M12E2 (challenge)..................: 1
PMKID (total)............................: 45
PMKID (best).............................: 1
PMKID written to combi hash file.........: 1
```
#### PMKID-Hash
We can check the contents of the file to ensure that we captured a valid hash:
```bash
$ cat pmkidhash_corp 

7943ba84a475e3bf1fbb1b34fdf6d102*10da43bef746*80822381a9c8*434f52502d57494649
```
Once again, we will perform a straightforward dictionary attack in an attempt to crack the WPA handshake.
#### Hashcat - Cracking PMKID
To attempt to crack this hash, we will use mode `22000`, as the previous mode `16800` has been deprecated. Here, our command will be in the format:
```bash
$ hashcat -a 0 -m 22000 pmkidhash_corp /opt/useful/seclists/Passwords/Leaked-Databases/rockyou.txt.tar.gz

hashcat (v6.2.6) starting...

<SNIP>

7943ba84a475e3bf1fbb1b34fdf6d102:10da43bef746:80822381a9c8:CORP-WIFI:cleopatra
                                                 
Session..........: hashcat
Status...........: Cracked
Hash.Name........: WPA-PBKDF2-PMKID+EAPOL
Hash.Target......: pmkidhash_corp
Time.Started.....: Wed Mar  9 11:27:21 2022 (1 sec)
Time.Estimated...: Wed Mar  9 11:27:22 2022 (0 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:    12563 H/s (3.49ms) @ Accel:1024 Loops:32 Thr:1 Vec:8
Recovered........: 1/1 (100.00%) Digests
Progress.........: 18130/14344385 (0.13%)
Rejected.........: 11986/18130 (66.11%)
Restore.Point....: 0/14344385 (0.00%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidates.#1....: 123456789 -> celtic07

Started: Wed Mar  9 11:27:20 2022
Stopped: Wed Mar  9 11:27:23 2022
```
### Closing Thoughts
We have successfully cracked the PMKID hash and could now attempt to authenticate to the wireless network.

This section covered some valuable techniques that can be used during penetration tests, wireless assessments, and red team assessments. 

They can also be used by defenders to test the security of their wireless networks. 