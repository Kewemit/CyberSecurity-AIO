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

### Linux
There are many techniques we can utilize to have **slashes** in our payload. While ```${IFS}``` is directly replaced with a space, there's no such environment variable for **slashes** or **semi-colons**. However, these characters may be used in an **environment variable**, and we can specify start and length of our string to exactly match this character.

For example, if we look at the ```$PATH``` environment variable in Linux, it may look something like the following:
![[ByXOH0UV1x.png]]

So, if we start at the **0** character, and only take a string of length **1**, we will end up with only the **/** character, which we can use in our payload:
![[SkbcSRLEyg.png]]

We can do the same with the ```$HOME``` or ```$PWD``` environment variables as well. We can also use the same concept to get a **semi-colon** character, to be used as an injection operator. For example, the following command gives us a **semi-colon**:
![[HJnnrR84ye.png]]

So, let's try to use environment variables to add a **semi-colon** and a **space** to our payload (```127.0.0.1${LS_COLORS:10:1}${IFS}```) as our payload, and see if we can bypass the filter: 
![[SkhxUC8V1l 1.png]]
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

## Bypassing Front-End Validation
The easiest method to customize the **HTTP** requests being sent to the **back-end** server is to use a web proxy that can intercept the **HTTP** requests being sent by the application. To do so, we can start **Burp Suite** or **ZAP** and configure Firefox to proxy the traffic through them. Then, we can enable the proxy intercept feature, send a standard request and send the intercepted HTTP request to **repeater** by clicking **[CTRL + R]**, and we should have the **HTTP** request for customization:
![[HkGENr8E1e.png]]

We can now **customize** our HTTP request and send it to see how the web application handles it. We will start by using the same previous payload (**127.0.0.1; whoami**). We should also **URL-encode** our payload to ensure it gets sent as we intend. We can do so by selecting the **payload** and then clicking **[CTRL + U]**. Finally, we can click Send to send our **HTTP** request:
![[HyVw4BLE1e.png]]
As we can see, the response we got this time contains the **output** of the **ping** command **and** the result of the **whoami** command.
