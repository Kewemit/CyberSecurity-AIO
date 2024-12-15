- Grasp the fundamentals of writing shellcode ✅
- Generate shellcode for reverse shells ✅
- Executing shellcode with PowerShell ✅

# TLDR:
# Exploit:

# Deepened:
## Essential Terminologies
- **Shellcode**: A piece of code usually used by malicious actors during exploits like **buffer overflow** attacks to inject commands into a vulnerable system, often leading to executing arbitrary commands or giving attackers control over a compromised machine. Shellcode is typically written in assembly language and delivered through various techniques, depending on the exploited vulnerability.

- **PowerShell**: A powerful scripting language and command-line shell built into **Windows** for **task automation** and **configuration management**. It allows users to interact with system components and is widely used by administrators for legitimate purposes. However, attackers often use PowerShell as **a post-exploitation** tool because of its deep access to system resources and ability to run scripts directly in memory, **avoiding disk-based detection mechanisms.**

- **Windows Defender**: A built-in security feature that detects and prevents malicious scripts, including PowerShell-based attacks, by scanning code at runtime. Common bypass methods for evading Defender include **obfuscating scripts** to disguise malicious content, making it harder for the software to recognise known patterns. Another technique is a **reflective injection**, where malicious code is loaded directly into memory, avoiding detection by signature-based defences.  

- **Windows API**: The Windows Application Programming Interface (**API**) allows programs to interact with the underlying operating system, giving them access to essential system-level functions such as memory management, file operations, and networking. It serves **as a bridge between the application and the operating system**, enabling efficient resource handling. The Windows **API** is crucial because many exploitation techniques and malware rely on it to manipulate processes, allocate memory, and execute **shellcodes**. Common Windows **API** functions frequently used by malicious actors include `VirtualAlloc`, `CreateThread`, `WaitForSingleObject`, which we will also use in this task for exploitation.

- **Accessing Windows API through PowerShell Reflection**: Windows API via PowerShell Reflection is an advanced technique that enables dynamic interaction with the Windows **API** from PowerShell. Instead of relying on precompiled binaries, PowerShell Reflection allows attackers to call Windows API functions directly at runtime. This will enable them to manipulate low-level system processes, making it a primary tool for bypassing security mechanisms, interacting with the operating system, and executing code stealthily.

- **Reverse shell**: A type of connection in which the target (the machine you're trying to hack) initiates a connection back to your attacking machine (in this case, your machine will be the AttackBox).