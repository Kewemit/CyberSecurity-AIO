![[Skg15-841g.png]]
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

## Bypassing Front-End Validation
The easiest method to customize the **HTTP** requests being sent to the **back-end** server is to use a web proxy that can intercept the **HTTP** requests being sent by the application. To do so, we can start **Burp Suite** or **ZAP** and configure Firefox to proxy the traffic through them. Then, we can enable the proxy intercept feature, send a standard request and send the intercepted HTTP request to **repeater** by clicking **[CTRL + R]**, and we should have the **HTTP** request for customization:
![[HkGENr8E1e.png]]

We can now **customize** our HTTP request and send it to see how the web application handles it. We will start by using the same previous payload (**127.0.0.1; whoami**). We should also **URL-encode** our payload to ensure it gets sent as we intend. We can do so by selecting the **payload** and then clicking **[CTRL + U]**. Finally, we can click Send to send our **HTTP** request:
![[HyVw4BLE1e.png]]

As we can see, the response we got this time contains the **output** of the **ping** command **and** the result of the **whoami** command.

## Filter Evasion
### Detecting filters 
Even if developers **attempt** to secure the web application against injections, it may still be exploitable if it was not **securely** coded. Another type of injection **mitigation** is utilizing **blacklisted** characters and words on the **back-end** to detect injection attempts and deny the request if any request contained them. Yet another layer on top of this is utilizing Web Application Firewalls (**WAFs**), which may have a broader scope and **various** methods of injection detection and prevent various other attacks like **SQL injections** or **XSS** attacks.

#### Filter/WAF Detection
Let us start by visiting the web app again. We see the same **Host Checker** web app we have been exploiting, but now it has a few **mitigations** up its sleeve. We can see that if we try the previous operators we tested, like (**;**, **&&**, **||**), we get the error message invalid input:
![[HkZLIrUVJl.png]]

This indicates that something we sent triggered a **security mechanism** in place that denied our request. This error message can be displayed in various ways. In this case, we see it in the field where the output is displayed, meaning that it was detected and prevented by the **PHP** web app itself. If the error message displayed a different page, with information like our IP and our request, this may indicate that it was denied by a **WAF**.

Let us check the payload we sent:
```127.0.0.1; whoami```
Other than the **IP** (which we know is not blacklisted), we sent:
![[HkiqIHIVke-1.png]]
So, the web application either detected a **blacklisted character** or detected a **blacklisted command**, or **both**. So, let us see how to bypass each.

**Blacklisted Characters**
A web application may have a list of **blacklisted** characters, and if the command contains them, it would deny the request. The **PHP** code may look something like the following:
![[SyNRLBLNJg.png]]

If any character in the string we sent **matches** a character in the **blacklist**, our request is **denied**. Before we start our attempts at bypassing the filter, we should try to identify which character caused the denied request.

**Identifying Blacklisted Character**
Let us reduce our request to one character at a time and see when it gets blocked. We know that the (**127.0.0.1**) payload does work, so let us start by adding the semi-colon **(127.0.0.1;**): 
![[ryq7PHL4ye.png]]

We still get an **invalid input** error meaning that a (**;**) is blacklisted.

### Bypass Blacklisted Operators
We will see that most of the injection operators are indeed blacklisted. However, the **new-line** character is usually **not** blacklisted, as it may be needed in the payload itself. The new-line character works in appending our commands **both** in Linux and on Windows, so let's try using it as our injection operator: 
![[rkhVHp8Eyg.png]]

Even though our payload did include a **new-line** character, our request was **not** denied, which means that this character is **not** blacklisted. Let us start by discussing how to bypass a commonly blacklisted character - a **space** character.

### Bypass Blacklisted Spaces
If we modify our original payload and send it again as (```127.0.0.1%0a whoami```): 
![[S1N8DpUE1e.png]]

As we can see, we still get an **invalid input** error message, meaning that we still have **other filters** to bypass. So, as we did before, let us only add the next character (which is a space) and see if it caused the denied request:
![[rJSFPpINye.png]]

The **space** character is indeed **blacklisted** as well. A space is a **commonly** blacklisted character, especially if the input should not contain any spaces, like an IP, for example. Still, there are many ways to add a space character without actually using the space character!

#### Using Tabs
Using tabs (**%09**) instead of **spaces** is a technique that may work. So, let us try to use a tab instead of the space character (```127.0.0.1%0a%09```) and see if our request is accepted: 
![[Bkhg_aL4yx.png]]

As we can see, we successfully bypassed the space character filter by using a **tab** instead. Let us see another method of replacing **space** characters.

#### Using IFS
Using the (```$IFS```) Linux Environment Variable may also work since its default value is a **space** and a **tab**, which would work between command arguments. So, if we use ```${IFS}``` where the spaces should be, our command should work:
![[S1kjdTINkg.png]]
We see that our request was **not denied** this time, and we bypassed the space filter again.

#### Using Brace Expansion
There are **many** other methods we can utilize to bypass **space filters**. For example, we can use the **Bash Brace Expansion** feature, which automatically adds spaces between arguments wrapped between braces, as follows:
![[rktmYTUE1l.png]]

As we can see, the command was **successfully** executed without having spaces in it. We can utilize the same method in **command injection filter** bypasses, by using **brace expansion** on our command arguments, like (**127.0.0.1%0a{ls,-la}**). To discover more space filter bypasses, check out the [PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings/tree/master/Command%20Injection#bypass-without-space) page on writing commands without spaces.

### Bypassing Other Blacklisted Characters
Besides injection operators and space characters, a very commonly blacklisted character is the slash (**/**) or backslash (**\**) character, as it is necessary to specify directories. We can utilize several techniques to produce any character we want while avoiding the use of blacklisted characters.
#### Linux
There are many techniques we can utilize to have **slashes** in our payload. While ```${IFS}``` is directly replaced with a space, there's no such environment variable for **slashes** or **semi-colons**. However, these characters may be used in an **environment variable**, and we can specify start and length of our string to exactly match this character.

For example, if we look at the ```$PATH``` environment variable in Linux, it may look something like the following:
![[ByXOH0UV1x.png]]

So, if we start at the **0** character, and only take a string of length **1**, we will end up with only the **/** character, which we can use in our payload:
![[SkbcSRLEyg.png]]

We can do the same with the ```$HOME``` or ```$PWD``` environment variables as well. We can also use the same concept to get a **semi-colon** character, to be used as an injection operator. For example, the following command gives us a **semi-colon**:
![[HJnnrR84ye.png]]

So, let's try to use environment variables to add a **semi-colon** and a **space** to our payload (```127.0.0.1${LS_COLORS:10:1}${IFS}```) as our payload, and see if we can bypass the filter: 
![kuva](https://hackmd.io/_uploads/SkhxUC8V1l.png)
As we can see, we successfully **bypassed** the character filter this time as well.
#### Character Shifting
There are other techniques to produce the required characters without using them, like **shifting characters**. For example, the following Linux command **shifts** the character we pass by **1**. So, all we have to do is find the character in the **ASCII** table that is just **before** our needed character, then add it instead of `[` in the below example. This way, the last printed character would be the one we need:
![[SkhxUC8V1l.png]]

### Bypassing Blacklisted Commands
A **command** blacklist usually consists of a set of **words**, and if we can **obfuscate** our commands and make them look **different**, we **may** be able to bypass the filters.

Let us go back to our very first payload and re-add the **whoami** command to see if it gets executed: 
![[rJbxcbDNye.png]]

We see that even though we used characters that are **not** blocked by the web app, the request gets blocked again once we added our command. This is likely due to another type of filter, which is a command blacklist filter.

A basic command blacklist filter in **PHP** would look like the following:
![[S1DmcbP4kg.png]]

As we can see, it is checking each word of the user **input** to see if it **matches** any of the **blacklisted** words. However, this code is looking for an **exact** match of the provided command, so if we send a slightly different command, it may **not** get blocked.

#### Linux & Windows
One very common and easy obfuscation technique is inserting certain characters within our command that are usually ignored by command shells like **Bash** or **PowerShell** and will execute the same command as if they were not there. Some of these characters are a single-quote **'** and a double-quote **"**, in addition to a few others.

The easiest to use are **quotes**, and they work on both **Linux** and **Windows** servers. For example, if we want to obfuscate the **whoami** command:
![[H1Akj-DV1e.png]]

The same works with double-quotes as well:
![[HkwJibD4Jx.png]]

The important things to remember are that **we cannot mix types of quotes** and **the number of quotes must be even**. We can try one of the above in our payload (**127.0.0.1%0aw'h'o'am'i**) and see if it works
![[rJOGsWwE1l 1.png]]
As we can see, this method indeed **works**.

### Advanced Command Obfuscation
In some instances, we may be dealing with **advanced filtering solutions**, like Web Application Firewalls (**WAF**s). We can utilize more advanced techniques, which make detecting the **injected** commands much less likely.
#### Case Manipulation
One **command obfuscation** technique we can use is inverting the character cases of a command (e.g. **WHOAMI**) or alternating between cases (e.g. **WhOaMi**). This usually works because a command blacklist may **not** check for different case variations of a single word, as **Linux systems are case-sensitive**.

In **Windows**, commands for **PowerShell** and **CMD** are **case-insensitive**, meaning they **will** execute the command regardless of what case it is written in:
![[S141zzPNJl 1.png]]

**However**, when it comes to **Linux** and a **bash** shell, which are **case-sensitive**, as mentioned earlier, we have to get a bit creative and find a command that turns the command into an all-lowercase word. One working command we can use is the following:
![[HksgzGDVJe.png]]

This command uses **tr** to replace all upper-case characters with lower-case characters, which results in an **all lower-case command**. However, if we try to use the above command with the** Host Checker** web application, we will see that it still gets blocked:
![[SkJXfMvEkx.png]]

It is because the command above contains **spaces**, which is a **filtered character** in our web appl. So, with such techniques, we must always be sure **not** to use any filtered characters, otherwise our requests will fail, and we may think the techniques failed to work.

Once we replace the spaces with tabs (**%09**), we see that the command works perfectly:
![[SJ_5GGDVyg.png]]

There are **many** other commands we may use for the same purpose, like the following:
```$(a="WhOaMi";printf %s "${a,,}")```

#### Reversed Commands
Another technique is reversing commands and switching them back. In this case, we will be writing **imaohw** instead of **whoami** to avoid triggering the blacklisted command.
```
echo 'whoami' | rev

which results in -> imaohw
```

Then, we can execute the original command by **reversing** it back in a sub-shell ```($())```, as follows:
![[BJcf4MP4yx-1.png]]

Even though the command does **not** contain the actual **whoami** word, it does work the same:
![[SJvSVfDVkx.png]]

#### Encoded Commands
The final technique is helpful for **commands** containing filtered characters or characters that may be **URL-decoded** by the server. Instead of copying an existing command online, we will try to create our own unique obfuscation command this time. This way, it is much less likely to be denied by a **filter** or a **WAF**.

We can utilize various **encoding tools**, like **base64** (for b64 encoding) or **xxd** (for hex encoding). Let's take **base64** as an example. First, we'll encode the payload we want to execute (which includes filtered characters):
![[B1zlSMPN1g.png]]

Now we can **create** a command that will **decode** the encoded string in a **sub-shell** ```($())```, and then pass it to **bash** to be executed (i.e. **bash<<<**), as follows:
![[Sy7_BMPV1l.png]]

As we can see, the above command executes the command **perfectly**. We did **not** include any filtered characters and avoided encoded characters that may lead the command to fail to execute.
![[HJY9rMD4kg.png]]

Now we can use this command (once we replace the **spaces**) to execute the same command through **command injection**:
![[BJnRHfwN1l.png]]

Even if some commands **were** filtered, like **bash** or **base64**, we **could** bypass that filter with the techniques from earlier (e.g., **character insertion**), or use other alternatives like **sh** for command execution and **openSSL** for b64 decoding, or **xxd** for hex decoding.

### Evasion Tools
If we are dealing with **advanced security tools**, we may **not** be able to use basic, manual obfuscation techniques. In such cases, it may be best to resort to **automated** obfuscation tools. This section will discuss a couple of examples of these types of tools, one for Linux and another for Windows

#### Linux (Bashfuscator)
A handy tool we can utilize for obfuscating **bash** commands is **Bashfuscator**. We can clone the repository from GitHub and then install its requirements, as follows:
![[S1ok-rP4yg.png]]

Once we have the tool set up, we can start using it from the **./bashfuscator/bin/** directory. There are many flags we can use with the tool to fine-tune our final obfuscated command, as we can see in the **-h** help menu:
![[ByI--HPN1l.png]]

We can start by simply providing the command we want to obfuscate with the **-c** flag:
![[SyFz-rwEyx.png]]

However, running the tool this way will **randomly** pick an obfuscation technique, which can output a command length ranging from a **few hundred characters to over a million characters**! So, we can use some of the flags from the help menu to produce a **shorter** and **simpler** obfuscated command, as follows:
![[ByVNbSvEJe.png]]

We can now test the outputted command with bash -c '', to see whether it does execute the intended command:
![[SJJ8WrwE1e.png]]

We can see that the obfuscated command works, all while looking completely obfuscated, and does not resemble our original command. We may also notice that the tool utilizes many obfuscation techniques, including the ones we previously discussed and many others.
![[B1Fj-SDN1l.png]]

#### Windows (DOSfuscation)
There is also a very similar tool that we can use for **Windows** called **DOSfuscation**. Unlike **Bashfuscator**, this is an **interactive** tool, as we run it once and interact with it to get the desired obfuscated command. We can once again clone the tool from GitHub and then invoke it through PowerShell, as follows:
![[SJfA-HPNkx.png]]

We can even use **tutorial** to see an example of how the tool works. Once we are set, we can start using the tool, as follows:
![[HkP1frPEkx.png]]

Finally, we can try running the obfuscated command on CMD, and we see that it indeed works as expected:
![[HJiWGHDEyg.png]]
![[ryVGGHw4kl.png]]

