# Command Injection
Allows us to execute **system commands** directly on the **back-end** hosting server, which could lead to **compromising** the entire **network**. If a web application uses user-controlled input to execute a system command on the **back-end** server to retrieve and return specific output, we may be able to inject a **malicious** payload to **subvert** the intended command and execute our commands.

Most common types of injections:
![[Pasted image 20241214160707.png]]
There are many other types of injections other than the above, like **LDAP injection, NoSQL Injection, HTTP Header Injection, XPath Injection, IMAP Injection, ORM Injection, and others**. Whenever user input is used within a query without being properly sanitized, it may be possible to escape the boundaries of the user input string to the parent query and manipulate it

## OS Command Injection
When it comes to **OS Command Injections**, the user input we control must directly or indirectly go into (or somehow affect) a web query that executes system commands. **All** web programming languages have different functions that enable the **developer** to execute operating system commands directly on the **back-end** server whenever they need to. This may be used for various purposes, like installing plugins or executing certain applications.
![[Pasted image 20241214160829.png]]

### PHP Example
A web application written in **PHP** may use the **exec**, **system**, **shell_exec**, **passthru**, or **popen** functions to execute commands directly on the back-end server. The following code is an example of PHP code that is vulnerable to command injections:
![[rkuL6V8Nyl.png]]

If a particular web application has a functionality that allows users to create a new **.pdf** document that gets created in the **/tmp** directory with a file name supplied by the user and may then be used by the web application for document processing purposes. However, as the user input from the **filename** parameter in the **GET** request is used directly with the **touch** command (without being sanitized or escaped first), the web application becomes **vulnerable** to OS command injection. This flaw can be exploited to execute arbitrary system commands on the **back-end** server

**Command Injection** vulnerabilities are **not** unique to web applications but can also affect other binaries and thick clients if they pass **unsanitized** user input to a function that executes **system commands**, which can also be **exploited** with the same command injection methods.

## Detection 
The process of **detecting** basic **OS Command Injection** vulnerabilities is the same process for exploiting such vulnerabilities. We attempt to append our command through various **injection** methods. If the command output changes from the intended usual result, we have **successfully** exploited the vulnerability. This may **not** be true for more advanced command injection vulnerabilities because we may utilize various **fuzzing** methods or code reviews to identify potential command injection vulnerabilities. We may then gradually build our payload until we achieve command injection. This chapter will focus on **basic** command injections, where we control user input that is being **directly** used in a system command execution a function without **any** sanitization.

When we visit the web application below, we see a **Host Checker** utility that appears to ask us for an IP to check whether it is alive or not: 
![[Bkm3xH84Jg.png]]

We can enter the localhost IP **127.0.0.1** to check the functionality, and it returns the output of the **ping** command:
![[rke1-HUVyl.png]]

Even without access to the source code of the web application, we can confidently guess that the IP we entered is going into a **ping** command, since the output we receive suggests that. As the result shows a single packet transmitted in the **ping** command, the command used may be as follows:
```ping -c 1 OUR INPUT```

If our input is **not** sanitized and escaped before it is used with the ping command, we may be able to inject another arbitrary command.

To inject an additional command to the intended one, we may use any of the following operators:
![[B1KS-S841e.png]]

We can use any of these operators to inject another command so **both** or **either** of the commands get executed. We would write our expected input (e.g., an IP), then use any of the above operators, and then write our new command.
## Injecting Commands
So far, we have found the **Host Checker** web application to be potentially vulnerable to command injections so let's start our command injection attempts with the semi-colon operator (;).

We can add a (;) after our input IP **127.0.0.1**, and then append our command (e.g. **whoami**), such that the final payload we will use is (**127.0.0.1; whoami**), and the final command to be executed would be:
```ping -c 1 127.0.0.1; whoami```

First, let's try running the above command on our Linux VM to ensure it does run:
![[ByqyQS8V1g.png]]

It works so now we can try using our previous payload in the Host Checker web application: 
![[BJHZ7B8N1x.png]]

As we can see, the web application **refused** our input, as it seems only to accept input in an IP format. However, from the look of the error message, it appears to be originating from the front-end rather than the back-end. We can double-check this with the Firefox Developer Tools by clicking **[CTRL + SHIFT + E]** to show the **Network** tab and then clicking on the Check button **again**:
![[Syp7QrU4kg.png]]

As we can see, **no** new network requests were made when we clicked on the **Check** button, yet we got an error message. This indicates that the user input validation is happening on the front-end.

It is very **common** for developers only to perform input validation on the **front-end** while **not** validating or sanitizing the input on the **back-end**. This occurs for **various** reasons, like having two **different** teams working on the **front-end/back-end**.

However, as we will see, **front-end** validations are usually **not** enough to **prevent** injections, as they can be very easily **bypassed** by sending custom **HTTP** requests directly to the **back-end**.