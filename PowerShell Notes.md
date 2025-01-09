#PowerShell #Scripting #Cmdlets

# Command Prompt Basics
## CMD VS PowerShell

Here, we will look at Windows' successor to CMD, **PowerShell**. This note will cover what PowerShell is, the differences between PowerShell and CMD, how to get help within the CLI, and basic navigation within the CLI.
### Differences
PowerShell and CMD are included natively on **any** Windows host, so we may ask ourselves, "Why would I use one over the other?" Let's address this quickly. Below is a table with some differences between **PowerShell** and **CMD**:

| Feature             | CMD                                                                 | PowerShell                                                       |
| ------------------- | ------------------------------------------------------------------- | ---------------------------------------------------------------- |
| Language            | Batch and basic CMD commands only.                                  | PowerShell can interpret Batch, CMD, PS cmdlets, and aliases.    |
| Command Utilization | The output from one command cannot be passed into another directly. | The output from one command can be passed into another directly. |
| Command Output      | Text only.                                                          | PowerShell outputs in object formatting.                         |
| Parallel Execution  | CMD must finish one command before running another.                 | PowerShell can multi-thread commands to run in parallel.         |
Most people think PowerShell is just another CLI, but it's a lot more. PowerShell itself is also a scripting language!

While CMD has been default for Windows only, PowerShell has been released as an open-source project and has extensive offering of capabilities that support it's use with Linux based systems as well. Using the `.NET` framework has made PowerShell capable of utilizing an **object base model of interaction and output** instead of **text only** like CMD.
### Why Choose PowerShell over CMD?
PowerShell has become increasingly prominent among IT and InfoSec professionals. It has utility for SysAdmins, PenTesters, SOC Analyst and many other disciplines where Windows is administered. 

Consider IT admins and Windows SysAdmins administering environments made up of Windows servers, desktops (Win10/11), Azure and M365 cloud-based applications. Many of them are using PowerShell to automate their daily tasks. Some of these tasks include:
- Provisioning servers and installing server roles
- Creating Active Directory user accounts for new employees
- Managing Active Directory group permissions
- Disabling and deleting Active Directory user accounts
- Managing file share permissions
- Interacting with [Azure](https://azure.microsoft.com/en-us/) AD and Azure VMs
- Creating, deleting, and monitoring directories & files
- Gathering information about workstations and servers
- Setting up Microsoft Exchange email inboxes for users (in the cloud &/or on-premises)

PowerShell can provide us way more capability like automation, scripting and expandability. As a PenTester, many well known capabilities are built into PowerShell. PowerShell's module import capability makes it easy for us to bring our tools to the environment. However from a stealth perspective, PowerShell's logging and history capability is powerful and will log more of our interactions with the host. **So if we want to be stealthy, we should stick to CMD**.

## Taking a Look at the Shell
![[Pasted image 20241230230320.png]]
As we can see, the prompt is almost identical to CMD. Also similar to CMD, PowerShell gives us many commands and cmdlets to utilize. Almost all commands that work in the CMD will work in PowerShell.

We will only cover some commands in this note. It's essential to understand that there is very little utility in memorizing commands. Focus more on understanding context, concepts and what is possible. Memorization will naturally happen with repetition.

## Commands
### Get-Help
* Using the Help function. If we want to see options and functionality available to us with a specific cmdlet, we can use the `Get-Help` cmdlet.
**Using Get-Help**:
```powershell
$ Get-Help Test-Wsman

NAME
    Test-WSMan

SYNTAX
    Test-WSMan [[-ComputerName] <string>] [-Authentication {None | Default | Digest | Negotiate | Basic | Kerberos |
    ClientCertificate | Credssp}] [-Port <int>] [-UseSSL] [-ApplicationName <string>] [-Credential <pscredential>]
    [-CertificateThumbprint <string>]  [<CommonParameters>]


ALIASES
    None


REMARKS
    Get-Help cannot find the Help files for this cmdlet on this computer. It is displaying only partial help.
        -- To download and install Help files for the module that includes this cmdlet, use Update-Help.
        -- To view the Help topic for this cmdlet online, type: "Get-Help Test-WSMan -Online" or
           go to https://go.microsoft.com/fwlink/?LinkId=141464.
```

``Get-Help`` can give helpful information about a cmdlet.
* Notice that the **Syntax** output shows us several available options and additional keywords. 
* **Aliases** are also mentioned, essentially shorter names for our commands.
* The **Remarks** output provides us with more info about the cmdlet and additional options to learn more about the cmdlet. One of these options is `-online`, which will open a Microsoft Docs page.
### Update-Help
We can use a helpful cmdlet called [Update-Help](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.core/update-help?view=powershell-7.2) to ensure we have the most **up-to-date** information for each cmdlet on the Windows system.
_I suggest running this as an administrator_
```powershell
$ Update-Help
```
Notice how much information was populated regarding `Test-Wsman` after running `Update-Help`:
```powershell
$ Get-Help Test-Wsman

NAME
    Test-WSMan

SYNOPSIS
    Tests whether the WinRM service is running on a local or remote computer.


SYNTAX
    Test-WSMan [[-ComputerName] <System.String>] [-ApplicationName <System.String>]
    [-Authentication {None | Default | Digest | Negotiate | Basic | Kerberos |
    ClientCertificate | Credssp}] [-CertificateThumbprint <System.String>]
    [-Credential <System.Management.Automation.PSCredential>] [-Port <System.Int32>]
    [-UseSSL] [<CommonParameters>]


DESCRIPTION
    The `Test-WSMan` cmdlet submits an identification request that determines
    whether the WinRM service is running on a local or remote computer. If the
    tested computer is running the service, the cmdlet displays the WS-Management
    identity schema, the protocol version, the product vendor, and the product
    version of the tested service.


RELATED LINKS
    Online Version: https://docs.microsoft.com/powershell/module/microsoft.wsman.mana
    gement/test-wsman?view=powershell-5.1&WT.mc_id=ps-gethelp
    
    Connect-WSMan
    Disable-WSManCredSSP
    Disconnect-WSMan
    Enable-WSManCredSSP
    Get-WSManCredSSP
    Get-WSManInstance
    Invoke-WSManAction
    New-WSManInstance
    New-WSManSessionOption
    Remove-WSManInstance
    Set-WSManInstance
    Set-WSManQuickConfig

REMARKS
    To see the examples, type: "get-help Test-WSMan -examples".
    For more information, type: "get-help Test-WSMan -detailed".
    For technical information, type: "get-help Test-WSMan -full".
    For online help, type: "get-help Test-WSMan -online"
```
### Getting Around in PowerShell
We can determine our current **working directory** (in relation to the host system) by utilizing the `Get-Location` cmdlet:
#### Get-Location / Print Working Directory
```powershell
$ Get-Location

Path
----
C:\Users\MyUser
```
We can see it printed the full path of the directory we are currently working from; in this case, that would be `C:\Users\MyUser`. 

Now that we have our bearings let us look at what objects and files exist within this directory.
#### Get-ChildItem / List Directory
The `Get-ChildItem` cmdlet can display the contents of our current directory or the one we specify:
```powershell
$ Get-ChildItem

Directory: C:\Users\MyUser

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
d-----        10/26/2021  10:26 PM                .ssh
d-----         1/28/2021   7:05 PM                .vscode
d-r---         1/27/2021   2:44 PM                3D Objects
d-r---         1/27/2021   2:44 PM                Contacts
d-r---         9/18/2022  12:35 PM                Desktop
d-r---         9/18/2022   1:01 PM                Documents
d-r---         9/26/2022  12:27 PM                Downloads
d-r---         1/27/2021   2:44 PM                Favorites
d-r---         1/27/2021   2:44 PM                Music
dar--l         9/26/2022  12:03 PM                OneDrive
d-r---         5/22/2022   2:00 PM                Pictures
```
We can see several other directories within our current working directory. Let's explore one.
### Set-Location / Moving to New Directory
```powershell
$ Set-Location .\Documents\

$ Get-Location

Path
----
C:\Users\MyUser\Documents
```
We fed the parameters `.\Documents\` to the ``Set-Location`` cmdlet, telling PowerShell that we want to move into the Documents directory. 

We could have also given it the full file path like this:
```powershell
$ Set-Location C:\Users\MyUser\Documents
```

### Get-Content / Display Contents Of File
Now, if we wish to see the contents of a file, we can use `Get-Content`. Looking in the Documents directory, we notice a file called `Readme.md`. Let us check it out.
```powershell
$ Get-Content Readme.md

Welcome to the PowerShell GitHub Community!
PowerShell Core is a cross-platform (Windows, Linux, and macOS) automation and configuration tool/framework that works well with your existing tools and is optimized
for dealing with structured data (e.g., JSON, CSV, XML, etc.), REST APIs, and object models.
It includes a command-line shell, an associated scripting language and a framework for processing cmdlets. 

.. SNIP ..
```
Utilizing the `Get-Content` cmdlet is as simple as that.

## Tips & Tricks for PowerShell Usage
### Get-Command / Find Command
`Get-Command` is a great way to find a command. 

With PowerShell using the `verb-noun` convention for cmdlets, we can search on either.
```powershell
$ Get-Command

CommandType     Name                                               Version   Source
-----------     ----                                               -------    ----
Alias           Add-AppPackage                                     2.0.1.0    Appx
Alias           Add-AppPackageVolume                               2.0.1.0    Appx
Alias           Add-AppProvisionedPackage                          3.0        Dism
Alias           Add-ProvisionedAppPackage                          3.0        Dism
Alias           Add-ProvisionedAppxPackage                         3.0        Dism
Alias           Add-ProvisioningPackage                            3.0        Provisioning
Alias           Add-TrustedProvisioningCertificate                 3.0        Provisioning
```
#### Get-Command (verb)
```powershell
$ Get-Command -verb get

.. SNIP ..  
Cmdlet          Get-Acl                                            3.0.0.0    Microsoft.Pow...
Cmdlet          Get-Alias                                          3.1.0.0    Microsoft.Pow...
Cmdlet          Get-AppLockerFileInformation                       2.0.0.0    AppLocker
Cmdlet          Get-AppLockerPolicy                                2.0.0.0    AppLocker
Cmdlet          Get-AppvClientApplication                          1.0.0.0    AppvClient  
.. SNIP ..  
```
Using the `-verb` modifier and looking for any cmdlet, alias, or function with the term ``get`` in the name, we are provided with a detailed list of everything PowerShell is currently aware of. 

We can also perform the exact search using the filter `get*` instead of the `-verb` `get`. The ``Get-Command`` cmdlet recognizes the `*` as a **wildcard** and shows each variant of `get`(anything).

#### Get-Command (noun)
```powershell
$ Get-Command -noun windows*  

CommandType     Name                                               Version   Source
-----------     ----                                               -------   ------
Alias           Apply-WindowsUnattend                              3.0        Dism
Function        Get-WindowsUpdateLog                               1.0.0.0    WindowsUpdate
Cmdlet          Add-WindowsCapability                              3.0        Dism
Cmdlet          Add-WindowsDriver                                  3.0        Dism
Cmdlet          Add-WindowsImage                                   3.0        Dism
Cmdlet          Add-WindowsPackage                                 3.0        Dism
Cmdlet          Clear-WindowsCorruptMountPoint                     3.0        Dism
Cmdlet          Disable-WindowsErrorReporting                      1.0        WindowsErrorR...
Cmdlet          Disable-WindowsOptionalFeature                     3.0        Dism
Cmdlet          Dismount-WindowsImage                              3.0        Dism
Cmdlet          Enable-WindowsErrorReporting                       1.0        WindowsErrorR...
Cmdlet          Enable-WindowsOptionalFeature                      3.0        Dism
```
In the above output, we utilized the `-noun` modifier, took the filter a step further, and looked for any portion of the noun that contained `windows*`, so our results came up pretty specific. 

**Anything** that begins with ``windows`` in the noun portion and is followed by anything else would **match** this filter. 

---
These were just a few demonstrations of how powerful the `Get-Command` cmdlet can be. Paired with the `Get-Help` cmdlet, these can be powerful help functions provided to us directly by PowerShell. Our next tip dives into our PowerShell session History.

### History
PowerShell keeps a history of the commands run in two ways. The **first** is the **built-in session history** which is implemented and deleted at the start and end of each console session. 

The other is through the `PSReadLine` module. The `PSReadLine` module tracks the history of any PowerShell commands used in all sessions across the host, among many other features. By default, PowerShell keeps the last ``4096`` commands entered, but this setting can be modified by changing the `$MaximumHistoryCount` variable.
#### Get-History
```powershell
$ Get-History

Id CommandLine
-- -----------
   1 Get-Command
   2 clear
   3 get-command -verb set
   4 get-command set*
   5 clear
   6 get-command -verb get
   7 get-command -noun windows
   8 get-command -noun windows*
   9 get-module
  10 clear
  11 get-history
  12 clear
  13 ipconfig /all
  14 arp -a
  15 get-help
  16 get-help get-module
```
By default, `Get-History` will only show the commands that have been run during this active session. 

Notice how the commands are numbered. We can recall those commands by using the alias `r` followed by the number to run that command again. 

**For example**: If we wanted to rerun the `arp -a` command, we could issue `r 14`, and PowerShell will action it. Keep in mind that if we close the shell window, or in the instance of a remote shell through command and control, once we kill that session or process that we are running, our PowerShell history will **disappear**. 

With `PSReadLine`, however, that is not the case. `PSReadLine` stores everything in a file called `$($host.Name)_history.txt` located at `$env:APPDATA\Microsoft\Windows\PowerShell\PSReadLine`.
#### PSReadLine History
```powershell
$ Get-Content C:\Users\MyUser\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt

get-module
Get-ChildItem Env: | ft Key,Value
Get-ExecutionPolicy
clear
ssh administrator@10.172.16.110.55
powershell -nop -c "iex(New-Object Net.WebClient).DownloadString('https://download.sysinternals.com/files/PSTools.zip')"
Get-ExecutionPolicy

.. SNIP ..
```
The output above was snipped to save time and screen space. One great feature of `PSReadline` from an admin perspective is that it will automatically attempt to filter any entries that include the strings:
* password
* asplaintext
* token
* apikey
* secret
This behaviour is excellent for us as admins since it will clear any entries from the PSReadLine history file that contains keys, credentials, or other sensitive information. The built-in session history does **not** do this.
### Clear Screen
We can remove text from our console with the command `Clear-Host`. It will only affect our current display and will not get rid of any variables or other objects we may have set or made during the session. We can also use `clear` or `cls` if we prefer using short commands or aliases.
### Hotkeys
Unless we are working in the **CLI** from a **GUI** environment, our mouse will **not** often work. 

**For example**: Let's say we landed a **shell** on a host during a PenTest. We will have access to **CMD** or **PowerShell** from this **shell**, but we will not be able to utilize the `GUI`. 

So we need to be comfortable using just a keyboard. `Hotkeys` can enable us to perform more complex actions that typically require a mouse with just our keys. Below is a quick list of some of the more useful hotkeys:

| Hotkey              | Description                                                                                      |
| ------------------- | ------------------------------------------------------------------------------------------------ |
| ``Ctrl+R``          | Makes for searchable history. We can start typing after, and it will show us results that match. |
| ``Ctrl+L``          | Quick screen clear.                                                                              |
| ``Ctrl+Alt+Shft+?`` | This will print the entire list of keyboard shortcuts.                                           |
| ``↑``               | Scroll previous history up.                                                                      |
| `↓`                 | Scroll previous history down.                                                                    |
| ``F7``              | Brings up a TUI with scrollable interactive history from our session.                            |
### Tab Completion
We can use `TAB` and `SHIFT+TAB` to move through options that can complete the command we are typing:
![[PowerShellTab.gif]]
### Aliases
A PowerShell **alias** is another name for a **cmdlet**, **command**, or **executable file**. We can see a list of default aliases using the [Get-Alias](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/get-alias?view=powershell-7.2) cmdlet. Most built-in aliases are **shortened versions** of the cmdlet, making it easier to remember and quick to use.
#### Get-Alias
```powershell
$ Get-Alias

CommandType     Name                                               Version   Source
                                                                              
-----------     ----                                               -------    -----
Alias           % -> ForEach-Object
Alias           ? -> Where-Object
Alias           ac -> Add-Content
Alias           asnp -> Add-PSSnapin
Alias           cat -> Get-Content
Alias           cd -> Set-Location
Alias           CFS -> ConvertFrom-String                          3.1.0.0    Mi...
Alias           chdir -> Set-Location
Alias           clc -> Clear-Content
Alias           clear -> Clear-Host
Alias           clhy -> Clear-History
Alias           cli -> Clear-Item
Alias           clp -> Clear-ItemProperty
Alias           cls -> Clear-Host
Alias           clv -> Clear-Variable
Alias           cnsn -> Connect-PSSession
Alias           compare -> Compare-Object
Alias           copy -> Copy-Item
Alias           cp -> Copy-Item
Alias           cpi -> Copy-Item
Alias           cpp -> Copy-ItemProperty
Alias           curl -> Invoke-WebRequest
Alias           cvpa -> Convert-Path
Alias           dbp -> Disable-PSBreakpoint
Alias           del -> Remove-Item
Alias           diff -> Compare-Object
Alias           dir -> Get-ChildItem
.. SNIP ..
```
It is an excellent practice to make aliases shorter than the name of the actual cmdlet, command, or executable. Even the `Get-Alias` cmdlet has a default alias of `gal`.
#### Set-Alias 
We can also set an alias for a specific cmdlet using [Set-Alias](https://docs.microsoft.com/en-us/powershell/module/microsoft.powershell.utility/set-alias?view=powershell-7.2). Let us practice with this by making an alias for the `Get-Help` cmdlet.
```powershell
$ Set-Alias -Name gh -Value Get-Help
```
When using ``Set-Alias``, we need to specify the name of the alias (`-Name gh`) and the corresponding cmdlet (`-Value Get-Help`).

Below is a list of some common, helpful aliases:

| Alias             | Description                                                                                             |
| ----------------- | ------------------------------------------------------------------------------------------------------- |
| ``pwd``           | Alias for `Get-Location`. You can also use `gl`.                                                        |
| ``ls``            | Alias for `Get-ChildItem`. You can also use `dir` and `gci`.                                            |
| ``cd``            | Alias for `Set-Location`. You can also use `sl` and `chdir`.                                            |
| ``cat``           | Alias for `Get-Content`. You can also use `type` and `gc`.                                              |
| ``clear``         | Can be used in place of `Clear-Host`.                                                                   |
| ``curl``          | Curl is an alias for `Invoke-WebRequest`, which can be used to download files. You can also use `wget`. |
| ``fl`` and ``ft`` | These aliases can be used to format output into **list** and **table** outputs.                         |
| ``man``           | Can be used instead of Help.                                                                            |
You may have noticed that many of the aliases match up to commands widely used within **Linux** distributions. This knowledge can be helpful and help ease the learning curve.

# All About Cmdlets and Modules