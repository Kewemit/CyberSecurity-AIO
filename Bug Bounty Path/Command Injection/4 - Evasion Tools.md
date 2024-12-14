# Evasion Tools
If we are dealing with **advanced security tools**, we may **not** be able to use basic, manual obfuscation techniques. In such cases, it may be best to resort to **automated** obfuscation tools. This section will discuss a couple of examples of these types of tools, one for Linux and another for Windows

## Linux (Bashfuscator)
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

## Windows (DOSfuscation)
There is also a very similar tool that we can use for **Windows** called **DOSfuscation**. Unlike **Bashfuscator**, this is an **interactive** tool, as we run it once and interact with it to get the desired obfuscated command. We can once again clone the tool from GitHub and then invoke it through PowerShell, as follows:
![[SJfA-HPNkx.png]]

We can even use **tutorial** to see an example of how the tool works. Once we are set, we can start using the tool, as follows:
![[HkP1frPEkx.png]]

Finally, we can try running the obfuscated command on CMD, and we see that it indeed works as expected:
![[HJiWGHDEyg.png]]
![[ryVGGHw4kl.png]]

