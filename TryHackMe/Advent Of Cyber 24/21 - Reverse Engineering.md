- Understanding the structure of a binary file ✅
- The difference between Disassembly vs Decompiling ✅
- Familiarity with multi-stage binaries ✅
- Practically reversing a multi-stage binary ✅
# Introduction to Reverse Engineering
Reverse Engineering (RE) is the process of breaking something down to understand its function. In cyber security, reverse engineering is used to analyse how applications (binaries) function. This can be used to determine whether or not the application is **malicious** or if there are any **security bugs** present.

![[Pasted image 20241228140410.png]]
**For example**: Cyber security analysts reverse engineer malicious applications distributed by attackers to understand if there are any attributable indicators to associate the binary with an attacker and any potential ways to defend against the malicious binary.

A famous example of this is the [WannaCry](https://en.wikipedia.org/wiki/WannaCry_ransomware_attack) ransomware from 2017. Security researcher [Marcus Hutchins](https://en.wikipedia.org/wiki/Marcus_Hutchins) reverse-engineered the ransomware application and discovered a specific function within the application where the malware **wouldn't run if a particular domain were registered and available**. 

Marcus then registered this domain, **stopping the global WannaCry attack**. This is just one of many famous cases of reverse engineering being used in cyber security defence.
# Binaries
﻿In computing, **binaries are files compiled from source code**. 
﻿
﻿**For example**: You run a **binary** when launching an **executable file** (`.exe`) on your computer. At one point in time, this application would've been programmed in a programming language such as **C#**. It is then **compiled**, and the **compiler translates the code into machine instructions**.

**Binaries** have a specific structure depending on the OS they are designed to run. 
**For example**:
* Windows binaries follow the **Portable Executable** (**PE**) structure,
* Whereas on Linux, binaries follow the **Executable** and **Linkable Format** (**ELF**). 
This is why, for example, you cannot run a **.exe** file on **MacOS**. 

With that said, all binaries will contain at least:
- **A code section:** This section contains the instructions that the **CPU** will execute
- **A data section:** This section contains information such as **variables**, **resources** (images, other data), **etc**
- **Import/Export tables:** These tables reference additional libraries used (imported) or exported by the binary. Binaries often rely on libraries to perform functions. For example, interacting with the **Windows API** to manipulate files

The binaries in today's task follow the **PE** structure. This structure will be explained throughout the task.
# Disassembly Vs. Decompiling
When reverse engineering binaries, you will employ **two primary techniques**. 
* Disassembly 
* Decompiling
The task here today will be explaining the key differences and their pros and cons.

**Disassembling** a binary shows the **low-level machine instructions the binary will perform** (you may know this as assembly). Because the output is translated machine instructions, you can see a detailed view of how the binary will interact with the system at what stage. Tools such as ``IDA``, ``Ghidra``, and ``GDB`` can do this:
![[Pasted image 20241228141224.png]]

**Decompiling**, however, converts the binary into its high-level code, such as C++, C#, etc., making it easier to read. However, this translation can often lose information such as variable names. This method of reverse engineering a binary is useful if you want to get a high-level understanding of the application's flow:
![[Pasted image 20241228141402.png]]
There are specific circumstances where you would choose one method over the other. For example, decompiling is sometimes a "best guess" based on the tooling you've used and does **not** provide the actual full source code.

A table outlining the key differences between the two has been provided below:

| Comparison      | Disassembly                                                                                       | Decompiling                                                                                                                                                               |
| --------------- | ------------------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Readability     | Requires knowing assembly and low-level knowledge of computing concepts.                          | Requires familiarity with programming and logic.                                                                                                                          |
| Level of Output | The translated output is the exact instructions that the machine will perform.                    | The translated output is often a "best guess". The output may not be accurate, and useful information, such as variables, function names, etc, will likely be lost.       |
| Difficulty      | The difficulty can be considered higher as the machine instructions are translated into assembly. | The machine instructions are translated into a high-level language, which makes them easier to understand if you are familiar with the language the binary is written in. |
| Usefulness      | The entire behaviour of the binary can be studied given enough time                               | Decompiling is a quick way to understand some of the logic of the binary.                                                                                                 |
# Multi-Stage Binaries
Recent trends in cyber security have seen the rise of attackers using what's known as "Multi-stage binaries" in their campaigns - especially malware. These attacks involve using multiple binaries responsible for different actions rather than one performing the entire attack. Usually, an attack involving various binaries will look like the following:
1. **Stage 1 - Dropper:** This binary is usually a lightweight, basic binary responsible for actions such as **enumerating the operating system** to see if the payload will work. Once certain conditions are verified, the binary will download the second - much more malicious - binary from the attacker's infrastructure.
2. **Stage 2 - Payload:** This binary is the "**meat and bones**" of the attack. For example, in the event of ransomware, this payload will encrypt and exfiltrate the data.
Sophisticated attackers may further split actions of the attack chain (e.g., lateral movement) into additional binaries. Using multiple stages helps evade detection and makes the analysis process more difficult.

For example, a small, more "**harmless**" initial binary is likelier to **evade detection** via email filtering than a fully-fledged binary that performs malicious actions such as encryption. 

Additionally, splitting these functions into multiple stages gives the attacker much more control (i.e. **only downloading specific stages once conditions such as time have been met**).

The diagram below shows what an attack involving multiple staged binaries may look like.
![[Pasted image 20241228142155.png]]

# Jingle .NET all the way
For today's task, we will be **reverse engineering two .NET binaries** using the **decompiler** ``ILSpy``. 

Before analysing our target, we need to learn and find a way to identify the original binary file, modify it, or use it as evidence. Also, it is good practice to have a big picture of the file we are dealing with so that we can choose the proper tools we will need.

Let's start by navigating to the file location in the **demo** folder on the machine's Desktop by right-clicking on the file named **demo** and clicking on `Properties`:
![[Pasted image 20241228142340.png]]
We can observe that the file's extension is ``.exe``, indicating that it is a **Windows executable**.

Since it's a Windows file, we'll use [PEStudio](https://www.winitor.com/download), a software designed to investigate potentially malicious files and extract information from them without execution. This will help us focus on the static analysis side of the investigation. 

Let's open ``PEstudio`` from the taskbar and then click on  **File** > **Open** and select the file `demo.exe`.

As shown below, ``PEStudio`` will display information about the file, so let's start enumerating some of the most important aspects we can get from it. 
Using the **left panel**, we can navigate through different sections that will share different types of information about the file. In the **general information** output displayed when opening the file, we can see the **hash** of the file in the form of **SHA-256**, The **architecture type**, in this case, **x64**, the **file type**, and the **signature of the language used** to compile the executable, in this case, .**NET framework** that uses the **C# language**:
![[Pasted image 20241228142620.png]]

Let's focus on some **critical data** we can obtain. First, if we want to **identify the file** and provide evidence of its **alteration**, we need to take note of the file’s **SHA-256 hash**, as we mentioned above, as well as the hash of each section on the ``PE`` file. ``PE`` stands for ``Portable Executable``, and it's the format in which Windows executables are arranged; you can learn more about it [here](https://learn.microsoft.com/en-us/windows/win32/debug/pe-format).

[The sections](https://learn.microsoft.com/en-us/windows/win32/debug/pe-format#section-table-section-headers) represent a **memory space** with different content of the Windows executable within the ``PE`` format. We can calculate the hash of these sections in order to identify the executable properly. 

We'll focus this time on two hashes: the one from the [.text](https://learn.microsoft.com/en-us/windows/win32/debug/pe-format#:~:text=in%20that%20module.-,.text,Executable%20code%20(free%20format),-IMAGE_SCN_CNT_CODE%20%7C%20IMAGE_SCN_MEM_EXECUTE%20%7C%20IIMAGE_SCN_MEM_READ) section, which is the section on the file containing the executable code; this section is often marked as **Read and executable**, so it should not have any alterations when the file is being copied. We can observe them in the screenshot below:
![[Pasted image 20241228142905.png]]

Another essential section we can use to obtain information about the binary is the "**indicators**" section. This section tells us about the potential indicators like **URLs** or **other suspicious attributes** of a binary:
![[Pasted image 20241228142950.png]]
The screenshot above shows several strings on the file, like **file names**, **URLs**, and **function names**. This can be very important depending on the file's execution flow. 
**Additionally**, looking for artefacts such as **IP addresses**, **URLs**, and **crypto wallets** can be a "quick win" for gathering some identifying intelligence. We'll learn about that in the next section.

Now that we have information about the file we are investigating, let's try to understand what the executable is doing. We need to understand its flow. If we try to read the file by opening it, we cannot do it since it's in binary format. In the previous section, we learned that the file is **compiled** using the `.NET` framework used by the `C#` language; we can **decompile** the binary code into **C#** using a decompilation tool like [ILSpy](https://github.com/icsharpcode/ILSpy).

`ILSpy` will decompile the code, providing us with readable information we can use to determine the flow of execution:
![[Pasted image 20241228143232.png]]
As we can observe from above, the **left panel** contains the libraries used by the framework, and the actual **decompiled code** is in the section with the file name **demo**. Let's click on it to expand and see what it contains:
![[Pasted image 20241228143302.png]]

As the screenshot above shows, `ILSpy` can provide much information, like **metadata** and **references**. However, the actual show is displayed on the brackets symbols ``{}``, in this case, under `DemoBinary > Program > Main`, which is actually the **Main function of the executable**. Now that we have access to the code running on the binary, let's analyse it:
```C#
private static void Main(string[] args)
{
	Console.WriteLine("Hello THM DEMO Binary");
	Thread.Sleep(5000);
	string address = "http://10.10.10.10/img/tryhackme_connect.png";
	string text = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.Desktop), "thm-demo.png");
	using (WebClient webClient = new WebClient())
	{
		try
		{
			Console.WriteLine("Downloading file...");
			webClient.DownloadFile(address, text);
			Console.WriteLine("File downloaded to: " + text);
			Process.Start(new ProcessStartInfo(text)
			{
				UseShellExecute = true
			});
			Console.WriteLine("Image opened successfully.");
		}
		catch (Exception ex)
		{
			Console.WriteLine("An error occurred: " + ex.Message);
		}
	}
	Console.WriteLine("Bye Bye leaving Demo Binary");
	Thread.Sleep(5000);
}
```
The code above displays the **main function** and its code. We can observe that first, it prints to the screen the message "Hello THM DEMO Binary" using the [Console.Writeline method](https://learn.microsoft.com/en-us/dotnet/api/system.console.writeline?view=net-8.0):
```C#
Console.WriteLine("Hello THM DEMO Binary");
```

It then uses the [Sleep](https://learn.microsoft.com/en-us/dotnet/api/system.threading.thread.sleep?view=net-8.0) method to wait for 5 seconds:
```C#
Thread.sleep(5000);
```

Then, it assigns a value to two string variables: **address** and **text**, the first one with a **URL accessing a PNG file**, and the second one with a file name on the user's Desktop named **thm-demo.png**:
```C#
string address = "http://10.10.10.10/img/tryhackme_connect.png";
string text = Path.Combine(Environment.GetFolderPath(Environment.SpecialFolder.Desktop), "thm-demo.png");
```

Then, it will try to connect to the **URL** on the **address** variable and save the content to a file on the Desktop using the [WebClient class](https://learn.microsoft.com/en-us/dotnet/api/system.net.webclient?view=net-8.0), and it will then execute the downloaded file path assigned to the **text** variable using the [Process class](https://learn.microsoft.com/en-us/dotnet/api/system.diagnostics.process?view=net-8.0) and the [Start method](https://learn.microsoft.com/en-us/dotnet/api/system.diagnostics.process.start?view=net-8.0#system-diagnostics-process-start(system-string)), as displayed below:
```C#
using (WebClient webClient = new WebClient())
	{
		try
		{
			Console.WriteLine("Downloading file...");
			webClient.DownloadFile(address, text);
			Console.WriteLine("File downloaded to: " + text);
			Process.Start(new ProcessStartInfo(text)
			{
				UseShellExecute = true
			});
			Console.WriteLine("Image opened successfully.");
		}
		catch (Exception ex)
		{
			Console.WriteLine("An error occurred: " + ex.Message);
		}
	}
```

Finally, it prints the message "**Bye Bye, leaving THM DEMO Binary**" again to the console and waits for 5 seconds using the **Sleep** method before closing:
```C#
Console.WriteLine("Bye Bye, leaving THM DEMO Binary");
Thread.Sleep(5000);
```

Great! We now understand what the binary is doing. It will download a **PNG** file to the user's Desktop from the URL: `http://10.10.10.10/img/tryhackme_connect.png`.

Let's execute the file and see if this is true. Once the binary starts, wait for the text "`Hello THM DEMO Binary`" to appear, then press `Enter` to download the file:
![[Pasted image 20241228143939.png]]
After executing the file, we can observe that it was downloaded to the **Desktop**, and the messages print to the screen as expected. Also, the downloaded file is executed and opened using the default app for PNG **Paint**. Excellent, we successfully Reverse-Engineered the flow of the code.

