- Hash functions and hash values
- Saving hashed passwords
- Cracking hashes
- Finding the password of a password-protected document

# Hashed Passwords
Before we dive further, it is helpful to learn how passwords are saved in authentication systems. In the past passwords were stored in cleartext along with the associated username.
* When logging in, the system compares the provided password for this account with the saved one. 
* If a user forgot their password, someone with enough access privileges could look at the table and respond with something like, “The password for `joebloggs` is `ASDF1234`.”  

To protect passwords, even in the case of a data breach, companies started to save a **hashed version** of the password. For that, we need to use a **hash function**. 

A **hash function** takes an input of any size and returns a fixed size value. For example, **SHA256 (Secure Hash Algorithm 256)** creates a 256-bit hash value. In other words, `sha256sum FILE_NAME` will return a 256-bit hash value regardless of whether the input file is a few bytes or several gigabytes. In the terminal below, we demonstrate this with one file being ``2.3 gigabytes`` and another being ``13 bytes``:
```bash
$ ls -lh
total 2.3G
-rw-rw-r-- 1 user user 2.3G Oct 24 15:05 Fedora-Workstation-Live-x86_64-41-1.4.iso
-rw-rw-r-- 1 user user   13 Nov 14 14:49 hello.txt

$ sha256sum *
a2dd3caf3224b8f3a640d9e31b1016d2a4e98a6d7cb435a1e2030235976d6da2  Fedora-Workstation-Live-x86_64-41-1.4.iso
03ba204e50d126e4674c005e04d82e84c21366780af1f43bd54a37816b6ab340  hello.txt
```
Therefore, instead of saving the password `ASDF1234` verbatim, its hash is saved. For instance, if **MD5** (**Message Digest 5**) is being used, then `ce1bccda287f1d9e6d80dbd4cb6beb60` would be saved. 

Problem solved?

Not really. Firstly, **MD5** is now considered insecure. Secondly, in addition to choosing a secure hash function, we should add a **salt**, i.e., _**a random string of characters**_, to the password before hashing it. In other words, instead of saving `hash(password)` in the table, we save `hash(password + salt)`. 

So, when the user tries to log in, the authentication system takes their password and the saved salt, calculates the hash and compares it with the saved hash value; if identical, they are granted access. This makes the saved passwords more immune to a variety of attacks.

Although it is recommended to use a modern secure hashing algorithm to calculate the hash value of the password concatenated with a random salt before saving it, reality is not that shiny. In many cases, there are issues in the implementation, be it due to **negligence** or **ignorance**. In a recent story, a social media platform was discovered to have saved 600 million passwords in plaintext for seven years, despite all the security guidelines warning against that. In other words, password cracking is not over yet.
# Password-Protected Files
On [[14 - Certificate Mismanagement |Day 14]], we saw how Mayor Malware intercepted network data to eavesdrop on the village. Technically speaking, he was attacking the confidentiality and integrity of **data in transit**. Today, we will explore how to view his password-protected document. So technically speaking, we will be attacking the confidentiality of the **data at rest**.

One aspect of our security requires us to protect data while it is stored on any storage device; examples include a flash memory drive, smartphone storage, laptop storage, and external drives. If an adversary gains access to any such device, we don’t want them to be able to access our files. **Protecting data at rest is usually achieved by encrypting the whole disk or specific files on the disk**.

On the other hand, encrypted storage and files can pose an **obstacle** for the **good guys** who are investigating a criminal case. Digital forensic investigators need to find a way to access the plaintext files to prove or disprove any wrongdoing. 

In this case, we must figure out how to access the **encrypted PDF** file. 
# Passwords
**Opening a password-protected document is impossible unless we know or can find the password.** The problem is that many users prefer to pick relatively easy passwords that they can remember easily and then use the same password across multiple places. Have you ever wondered which passwords are most commonly used? Below are the top 15 most commonly used passwords according to **one source**:

| Rank | Password  |
| ---- | --------- |
| 1    | 123456    |
| 2    | password  |
| 3    | 12345678  |
| 4    | qwerty    |
| 5    | 123456789 |
| 6    | 12345     |
| 7    | 1234      |
| 8    | 111111    |
| 9    | 1234567   |
| 10   | dragon    |
| 11   | 123123    |
| 12   | baseball  |
| 13   | abc123    |
| 14   | football  |
| 15   | monkey    |

Of course, users might get a little bit creative. They might append the **current year, a memorable date**, or **a few random characters or numbers** to the original word. 

Knowing that Mayor Malware has a cat called **Fluffy**, some passwords we expect him to come up with are `f1uffyc4t` and `fluffy2024` unless he uses his name or title and creates a password such as `m4y0r2024`.
# Demonstration
We will cover the following:
- Cracking a password found in a breached database
- Finding the password of an encrypted PDF
## Data Breach and Hash Values
Mayor Malware had an online account in a now-defunct forum that was breached. After checking online, we were able to retrieve the **Mayor’s password in hashed format**. It is listed below:

| Username        | PasswordHash                                                     |
| --------------- | ---------------------------------------------------------------- |
| mayor@email.thm | d956a72c83a895cb767bb5be8dba791395021dcece002b689cf3b5bf5aaa20ac |
If we want to discover the original password, the first step is to figure out the **type of the hash function**. Then, we will try to hash different passwords from a password list until we find a match.

Finding out the **hash type** is shown below:
```bash
$ cd AOC2024/

$ cat hash1.txt  
d956a72c83a895cb767bb5be8dba791395021dcece002b689cf3b5bf5aaa20ac 

$ python hash-id.py    #########################################################################      ....SNIP....
Hash ID by Zion3r
....SNiP....
######################################################################### 
--------------------------------------------------
HASH: d956a72c83a895cb767bb5be8dba791395021dcece002b689cf3b5bf5aaa20ac

Possible Hashs:
[+] SHA-256
[+] Haval-256

Least Possible Hashs:
[+] GOST R 34.11-94
[+] RipeMD-256
[+] SNEFRU-256
[+] SHA-256(HMAC)
[+] Haval-256(HMAC)
[+] RipeMD-256(HMAC)
[+] SNEFRU-256(HMAC)
[+] SHA-256(md5($pass))
[+] SHA-256(sha1($pass))
--------------------------------------------------
 HASH: ^C
```

Now knowing the hash type, we will try passwords from `rockyou.txt`, a popular password wordlist from a real data breach. The command is as follows:
```bash
john --format=raw-sha256 --wordlist=/usr/share/wordlists/rockyou.txt hash1.txt
```
- `john`: Starts John the Ripper (_hash cracker_).
- `--format=raw-sha256`: Specifies the hash format, which we got earlier that it is most likely a **SHA-256**
- `--wordlist=/usr/share/wordlists/rockyou.txt`: Sets the wordlist that we will use.
- `hash1.txt`: Is the text file containing the hash value we are trying to crack.
In our first attempt, `john` calculated the **SHA-256** hash value for every password in `rockyou.txt` and compared it with the hash value in `hash1.txt`. Unfortunately, no password was found, as shown in the terminal output below:
```bash
$ john --format=raw-sha256 --wordlist=/usr/share/wordlists/rockyou.txt hash1.txt 

Using default input encoding: UTF-8
Loaded 1 password hash (Raw-SHA256 [SHA256 256/256 AVX2 8x])
Warning: poor OpenMP scalability for this hash type, consider --fork=2
Will run 2 OpenMP threads
Note: Passwords longer than 18 [worst case UTF-8] to 55 [ASCII] rejected
Press 'q' or Ctrl-C to abort, 'h' for help, almost any other key for status
0g 0:00:00:03 DONE (2024-11-03 09:49) 0g/s 4765Kp/s 4765Kc/s 4765KC/s (4510458faruk)..*7¡Vamos!
Session completed.
```
There is a high chance that Mayor Malware has made some transformation to his password. For example, he might have replaced `a` with `4` or added a couple of digits to his password. 

``John`` can start from a long password list and **attempt various common derivations** from each of the passwords to increase its chances of success. This behaviour can be triggered through the use of **rules**. Various rules come bundled with ``John the Ripper’s`` configuration files; one is suited for lengthy wordlists, `--rules=wordlist`.

Adding the `--rules=wordlist` option to the `john` command generates multiple passwords from each one. For instance, it **appends and prepends** single digits. It does various common substitutions; for example, `a` can be replaced with `@`, `i` can be replaced with `!`, and `s` can be replaced with `$`. Many more mutations and transformations are part of these rules. You can check all the underlying rules by checking the `[List.Rules:Wordlist]` section in `/etc/john/john.conf`.

Unlike the first attempt, using ``John`` with this option should crack the hash for you:
```bash
$ john --format=raw-sha256 --rules=wordlist --wordlist=/usr/share/wordlists/rockyou.txt hash1.txt
```

We should note that `john` will not spend computing resources to crack an already-cracked password hash. Consequently, if you repeat a command that has successfully found a password earlier, you will get a message like “``No password hashes left to crack (see FAQ)``”. 

Let’s say that you executed the command listed above and you recovered the password; then, the next time you want to see that password, you would use `john` with the `--show` option, for example, `john --format=raw-sha256 --show hash1.txt`.
