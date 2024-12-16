# Introduction
Keys and passwords, the modern equivalent of locks and combinations, secure the digital world. But what if someone tries every possible combination until they find the one that opens the door? That, in essence, is `brute forcing`.
## What exactly is Brute Forcing?
In cybersecurity, brute forcing is a **trial-and-error** method used to **crack passwords, login credentials, or encryption keys**. It involves systematically trying every possible combination of characters until the correct one is found. The process can be likened to a thief trying every key on a giant keyring until they find the one that unlocks the treasure chest.

**The success of a brute force attack depends on several factors, including:**
- The `complexity` of the password or key. Longer passwords with a mix of uppercase and lowercase letters, numbers, and symbols are **exponentially** more complex to crack.

- The `computational power` available to the attacker. Modern computers and specialized hardware can try **billions** of combinations per second, significantly reducing the time needed for a successful attack.

- The `security measures` in place. **Account lockouts**, **CAPTCHAs**, and **other defences** can slow down or even thwart brute-force attempts.
## How Brute Forcing Works
The brute force process can be visualized as follows:
![[Pasted image 20241216205315.png]]
1. `Start`: The attacker initiates the brute force process.
2. `Generate Possible Combination`: The software generates a potential password or key combination based on predefined parameters, **such as character sets and length**.
3. `Apply Combination`: The generated combination is attempted against the target system, such as a **login form** or **encrypted file**.
4. `Check if Successful`: The system evaluates the attempted combination. If it matches the stored password or key, access is granted. Otherwise, the process continues.
5. `Access Granted`: The attacker gains unauthorized access to the system or data.
6. `End`: The process repeats, generating and testing new combinations until either the correct one is found or the attacker gives up.
## Types of Brute Forcing

Brute forcing is not a monolithic entity but a **collection of diverse techniques**, each with its strengths, weaknesses, and ideal use cases. Understanding these variations is crucial for both attackers and defenders, as it enables the former to choose the most effective approach and the latter to implement targeted countermeasures.
The following table provides a comparative overview of various brute-forcing methods:
t

| Method                  | Desc.                                                                                                                                   | Example                                                                                                                                                  | Best Used When..                                                                                                                                        |
| ----------------------- | --------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Simple Brute Force      | Systematically tries all possible combinations of characters within a defined character set and range.                                  | Trying all combinations of lowercase letters from `a` to `z` for passwords of length `4` to `6`.                                                         | **No prior information** about the password is available, and computational resources are abundant.                                                     |
| Dictionary Attack       | Uses a pre-compiled list of common words, phrases, and passwords.                                                                       | Trying passwords from a list like `rockyou.txt` against a login form.                                                                                    | The target will likely use a **weak** or **easily guessable** password based on common patterns.                                                        |
| Hybrid Attack           | Combines elements of simple brute force and dictionary attacks, often with appending and prepending characters to the dictionary words. | **Adding** **numbers** or **special characters** to the **end** of words **from** a **dictionary** list.                                                 | The target might use a slightly modified version of a common password.                                                                                  |
| Credential Stuffing     | Utilizes leaked credentials from one service to attempt access to other services, assuming users reuse passwords.                       | Using a list of usernames and passwords **leaked** from a data breach to try logging into various online accounts.                                       | **A large set of leaked credentials is available**, and the target is suspected of **reusing passwords** across multiple services.                      |
| Password Spraying       | Attempts a small set of commonly used passwords against a large number of usernames.                                                    | Trying passwords like `password123` or `qwerty` **against all usernames in an organization**.                                                            | **Account lockout policies** are in place, and the attacker aims to **avoid detection** by spreading attempts across multiple accounts.                 |
| Rainbow Table Attack    | Uses pre-computed tables of password hashes to reverse hashes and recover plaintext passwords quickly.                                  | Pre-computing hashes for all possible passwords of a certain length and character set, then comparing captured hashes against the table to find matches. | A large number of password hashes need to be cracked, and storage space for the rainbow tables is available.                                            |
| Reverse Brute Force     | Targets a single password against multiple usernames, often used in conjunction with credential stuffing attacks.                       | Using a **leaked password** from one service to try logging into **multiple accounts** with different **usernames**.                                     | A strong suspicion exists that a particular password is being **reused** across multiple accounts.                                                      |
| Distributed Brute Force | Distributes the brute forcing workload across multiple devices to accelerate the process.                                               | Using a cluster of computers to perform a **brute-force** attack to significantly increase the number of combinations that can be tried per second.      | The target password or key is **highly complex**, and a single machine **lacks** the **computational power** to crack it within a reasonable timeframe. |
### The Role of Brute Forcing in Penetration Testing
While penetration tests encompass a range of techniques, brute forcing is often strategically employed when:
- **Other avenues are exhausted**: Initial attempts to gain access, such as exploiting known vulnerabilities or utilizing social engineering tactics, may prove unsuccessful. In such scenarios, brute forcing is a viable alternative to overcome password barriers.
- **Password policies are weak**: If the target system employs lax password policies, it increases the likelihood of users having weak or easily guessable passwords. Brute forcing can effectively expose these vulnerabilities.
- **Specific accounts are targeted**: In some instances, penetration testers may focus on compromising specific user accounts, such as those with elevated privileges. Brute forcing can be tailored to target these accounts directly.
## Password Security Fundamentals
Passwords are the first line of defence in protecting sensitive information and systems. A strong password is a formidable barrier, making it significantly harder for attackers to gain unauthorized access.
### Common Password Weaknesses
- **Short Passwords:** Passwords with fewer than **eight** characters are particularly vulnerable to brute-force attacks, as the number of possible combinations is relatively small.
- **Common Words and Phrases:** Using dictionary words, names, or common phrases as passwords makes them susceptible to dictionary attacks.
- **Personal Information:** Incorporating personal information like **birthdates**, **pet names**, or **addresses** into passwords makes them easier to guess, especially if this information is publicly available on social media or other online platforms.
- **Reusing Passwords:** Using the same password across multiple accounts is risky. If one account is compromised, all other accounts using the same password are also at risk.
- **Predictable Patterns:** Using patterns like "**qwerty**" or "**123456**" or simple substitutions like "**p@ssw0rd**" makes passwords easy to guess.
### Password Policies
Organizations often implement password policies to enforce the use of strong passwords. These policies typically include requirements for:
- **Minimum Length**: The minimum number of characters a password must have.
- **Complexity:** The types of characters that must be included in a password (e.g., **uppercase, lowercase, numbers, symbols**).
- **Password Expiration:** The frequency where you have to change passwords periodically.
- **Password History:** Previous passwords cannot be reused.
### The Perils of Default Credentials
Often overlooked is the danger posed by **default passwords**. They are often simple and easily guessable, making them a prime target for attackers.

The prevalence of default passwords makes them **low-hanging fruit** for attackers. They provide an easy entry point into systems and networks, potentially leading to data breaches, unauthorized access, and other malicious activities. **Below are examples of certain default passwords:**
![[Pasted image 20241216232427.png]]
**Or in a table format**

| <font color=" #8a5cf5">**Device/Manufacturer**</font> | <font color="#29dd01">Default Username</font> | <font color="#29dd01">Default Password </font> | <font color="#8a5cf5">Device Type </font> |
| ----------------------------------------------------- | --------------------------------------------- | ---------------------------------------------- | ----------------------------------------- |
| **Linksys Router**                                    | **admin**                                     | **admin**                                      | **Router**                                |
| **D-Link Router**                                     | **admin**                                     | **admin**                                      | **Router**                                |
| **Netgear Router**                                    | **admin**                                     | **password**                                   | **Router**                                |
| **TP-Link Router**                                    | **admin**                                     | **admin**                                      | **Router**                                |
| **Cisco Router**                                      | **admin**                                     | **cisco**                                      | **Router**                                |
| **Asus Router**                                       | **admin**                                     | **admin**                                      | **Router**                                |
| **Belkin Router**                                     | **admin**                                     | **password**                                   | **Router**                                |
| **Zyxel Router**                                      | **admin**                                     | **1234**                                       | **Router**                                |
| **Samsung SmartCam**                                  | **admin**                                     | **4321**                                       | **IP Camera**                             |
| **Hikvision DVR**                                     | **admin**                                     | **12345**                                      | **Digital Video Recorder (DVR)**          |
| **Axis IP Camera**                                    | **root**                                      | **pass**                                       | **IP Camera**                             |
| **Ubiquiti UniFi AP**                                 | **ubnt**                                      | **ubnt**                                       | **Wireless Access Point (WAP)**           |
| **Canon Printer**                                     | **admin**                                     | **admin**                                      | **Network Printer**                       |
| **Honeywell Thermostat**                              | **admin**                                     | **1234**                                       | **Smart Thermostat**                      |
| **Panasonic DVR**                                     | **admin**                                     | **12345**                                      | **Digital Video Recorder (DVR)**          |
To truly grasp the challenge of brute forcing, it's essential **to understand the underlying mathematics**. The following formula determines the **total number of possible combinations for a password:**
```mathML
Possible Combinations = Character Set Size^Password Length
```
For example, a **6-character password** using only lowercase letters (character set size of 26) has 26^6 (approximately 300 million) possible combinations. In contrast, an 8-character password with the same character set has 26^8 (approximately 200 billion) combinations. Adding uppercase letters, numbers, and symbols to the character set further expands the search space exponentially.

This exponential growth in the number of combinations highlights the importance of password length and complexity. Even a small increase in length or the inclusion of additional character types can dramatically increase the time and resources required for a successful brute-force attack.

Let's consider a few scenarios to illustrate the impact of password length and character set on the search space:

|                             | Password Length | Character Set                                         | Possible Combinations               |
| --------------------------- | --------------- | ----------------------------------------------------- | ----------------------------------- |
| **Short and Simple**        | 6               | Lowercase letters (a-z)                               | 26^6 = 308,915,776                  |
| **Longer but Still Simple** | 8               | Lowercase letters (a-z)                               | 26^8 = 208,827,064,576              |
| **Adding Complexity**       | 8               | Lowercase and uppercase letters (a-z, A-Z)            | 52^8 = 53,459,728,531,456           |
| **Maximum Complexity**      | 12              | Lowercase and uppercase letters, numbers, and symbols | 94^12 = 475,920,493,781,698,549,504 |
While a complex password can take **years** to brute-force with a single machine, a sophisticated attacker using a distributed network of high-performance computing resources could reduce that time drastically:
![[Pasted image 20241216233948.png]]
# Bute Force Attacks
##  Cracking the PIN