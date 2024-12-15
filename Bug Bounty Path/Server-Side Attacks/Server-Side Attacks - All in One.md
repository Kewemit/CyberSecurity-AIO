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
### Enumerating the System
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
