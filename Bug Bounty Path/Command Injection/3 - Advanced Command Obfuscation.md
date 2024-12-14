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