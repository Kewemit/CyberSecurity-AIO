- Learn about the structures of Active Directory. ✅
- Learn about common Active Directory attacks. ✅
- Investigate a breach against an Active Directory. ✅

AD = **Active Directory**, which is a directory service developed by Microsoft for **Windows domain networks**. It stores information about network objects such as computers, users, and groups. It provides authentication and authorisation services, and allows administrators to manage network resources centrally.

DC = **Domain controller**. which is a server that **manages** security authentication requests in a Windows Server network. It stores user account information and controls access to resources on the network.

OU = Organizational Unit, which refers to containers that hold users, groups and computers to which similar policies should apply. In most cases, **OUs** will match **departments** in an enterprise.
# Introducing Active Directory
Before diving into ``Active Directory``, let us understand how network infrastructures can be mapped out and ensure that access to resources is well managed. 

This is typically done through **Directory Services,** which map and provide access to network resources within an organisation. The **Lightweight Directory Access Protocol (LDAP)** forms the **core** of ``Directory Services``. It provides a mechanism for accessing and managing directory data to ensure that searching for and retrieving information about subjects and objects such as users, computers, and groups is quick.

**Active Directory** (AD) is, therefore, a ``Directory Service`` at the heart of most enterprise networks that stores information about objects in a network. **The associated objects can include**:

* ``Users``: Individual accounts representing people or services.
* ``Groups``: Collections of users or other objects, often with specific permissions.
* ``Computers``: Machines, that belong to the domain governed by AD policies.
* ``Printers and other resources``: Network-accessible devices or services.
The building blocks of an AD architecture include:
* ``Domains``: Logical groupings of network resources such as **users, computers and services**. Domains serve as the main boundary for AD administration and can be identified by their **Domain Component and Domain Controller** name. Everything inside a domain is subject to the same security policies and permissions'.
* ``Organisational Units (OUs)``: Containers within a domain that help group objects based on **departments, locations or functions** for easier management. Administrators can apply **Group Policy** settings to specific **OUs**, allowing more granular control over permissions and security settings.
* ``Forest``: Collection of one or more domains that share a standard schema, config, and global catalogue. The forest is the **top-level container** in AD.
* ``Trust Relationships``: Domains within a forest (and across forests) can establish **trust relationships** that allow users in one domain to access resources in another, subject to permission.
Combining all these components allows us to establish the **Distinguished Name (DN)** that an object belongs to within the **AD**. The structure of the name would be as follows:
``DN=CN=Mayor, OU=Management, DC=Wareville, DC=thm
## Core Active Directory Components
Active Directory contains several key components that allow it to provide a wide range of services. Understanding these components will give one a clear picture of how AD supports administrative and security operations:
* ``Domain Controllers (DCs)``:
	* **The servers that host Active Directory services. 
	* They store the AD database and handle authentication and authorisation requests, such as logging in users or verifying access to resources.
	* Multiple DCs can exist within a domain.
	* When changes are made to **AD** (i.e. adding users, updating passwords), these changes are replicated across all DCs, ensuring that the directory remains consistent.
*  ``Global Catalog (GC)``: 
	* Searchable database within **AD** that contains a **subset of information from all objects in the directory**.
	* Allows users and services to locate objects in any domain in the **forest**, even if they reside in different domains.
*  ``LDAP (Lightweight Directory Access Protocol)``:
	* AD uses this protocol to query and modify the directory.
	* This protocol allows for fast searching and retrieving of info about objects such as **users, computers, and groups**
*  ``Kerberos Authentication``: 
	* The default authentication protocol used by **AD**.
	* Provides secure authentication by using tickets, rather than passwords.
## Group Policy
One of **Active Directory**'s most powerful features is **Group Policy**, which allows administrators to enforce policies across the domain. 

Group Policies can be applied to users and computers to **enforce password policies, software deployment, firewall settings, and more**.

**Group Policy Objects (GPOs)** are the containers that hold these policies. A **GPO** can be linked to the **entire domain**, an **OU**, or a **site**, giving the flexibility in applying policies.

Let us say that McSkidy wants to ensure that all users within **Wareville's SOC** follow a strict password policy, enforcing minimum password lengths and complexity rules. 
Here is how it would be done:
1. Open **Group Policy Management** from your server by typing `gpmc.msc`.
2. Right-click your domain and select **"Create a GPO in this domain, and Link it here"**. Name the new GPO **"Password Policy"**.
3. Edit the GPO by navigating to **Computer Configuration -> Policies -> Windows Settings -> Security Settings -> Account Policies -> Password Policy**.
4. Configure the following settings:
    - Minimum password length: 12 characters
    - Enforce password history: 10 passwords
    - Maximum password age: 90 days
    - Password must meet complexity requirements: Enabled
This policy will now be applied across the domain, ensuring all users meet these password requirements:
![[Pasted image 20241225145744.png]]
# Common Active Directory Attacks
Adversaries are always looking for ways to breach and exploit Active Directory environments to destabilise and cause havoc to organisations.
## Golden Ticket Attack
A **Golden Ticket** attack allows attackers to exploit the **Kerberos** protocol and impersonate any account on the AD by forging a ``Ticket Granting Ticket`` (**TGT**).

By compromising the **krbtgt** account and using its password hash, the attackers gain complete control over the domain for as long as the forged ticket remains valid. 

The attack requires four critical pieces of information to be successful:
- **Fully Qualified Domain Name** (**FQDN**) of the domain
- **SID** of the domain
- **Username** of an account to impersonate
- **KRBTGT account password hash**
### Detection
Detection for this type of attack involves monitoring for unusual activity involving the **krbtgt**:
* ``Event ID 4768``: Look for **TGT** requests for high-privilege accounts.
* ``Event ID 4672``: Logs when special privileges (i.e ``SeTcbPrivilege``) are assigned to a user.

## Pass-the-Hash
This type of attack steals the **hash of a password** and can be used to authenticate to services **without needing the actual password**. This is possible because the **NTLM** protocol allows authentication based on password hashes.
### Mitigation
* Enforce strong password policies
* Conduct regular audits on account privileges 
* Implement multi-factor authentication across the domain.

## Kerberoasting
This is an attack targeting the **Kerberos**, in which the attacker **requests service tickets** for accounts with ``Service Principal Names`` (**SPNs**), then **extracts the tickets** and **password hashes**, and finally attempts to **crack them offline** to retrieve the plaintext password
### Mitigation
* Ensure that service accounts have strong passwords.
* Implement secure policies across the **AD**.
## Pass-The-Ticket
Attackers steal **Kerberos tickets** from a compromised machine and **use them** to authenticate as the **user** or **service** whose ticket was stolen.
### Detection
* Monitor for suspicious logins using ``Event ID 4768`` (**TGT** request), especially if user is logging in from unusual location or device.
* ``Event ID 4624`` (Successful login) will reveal tickets used for authentication.

## Malicious GPOs
Adversaries are known to abuse **Group Policy** to create persistent, privileged access accounts and distribute and execute malware by setting up policies that mimic software deployment across entire domains. 

With escalated privileges across the domain, attackers can create **GPOs** to disable core security software and features such as firewalls, antivirus, security updates, and logging. 

Additionally, scheduled tasks can be created to execute malicious scripts or exfiltration data from affected devices across the domain.
### Mitigation
* Regularly audit **GPOs** for unauthorised changes.
* Strict permissions and procedures for **GPO** modification should be enforced
## Skeleton Key Attack
Attackers install a **malware backdoor** to log into any account using the **master password**. The legitimate password for each account would remain unchanged, but attackers can bypass it via **skeleton key password**.
# Investigating an AD Breach
## Group Policy
**Group Policy** is a means to distribute configurations and policies to enrolled devices in the domain. For attackers, **Group Policy** is a lucrative means of spreading malicious scripts to multiple devices.

Reviewing **Group Policy Objects** (**GPOs**) is a great investigation step. In this section, we will use **PowerShell** to audit our **GPOs**. First, we can use the `Get-GPO` cmdlet to list all **GPOs** installed on the domain controller:
```powershell
PS C:\Users\Administrator> Get-GPO -All

DisplayName      : Default Domain Policy
DomainName       : wareville.thm
Owner            : WAREVILLE\Domain Admins
Id               : 31b2f340-016d-11d2-945f-00c04fb984f9
GpoStatus        : AllSettingsEnabled
Description      :
CreationTime     : 10/14/2024 12:17:31 PM
ModificationTime : 10/14/2024 12:19:28 PM
UserVersion      : AD Version: 0, SysVol Version: 0
ComputerVersion  : AD Version: 3, SysVol Version: 3
WmiFilter        :

DisplayName      : Default Domain Controllers Policy
DomainName       : wareville.thm
Owner            : WAREVILLE\Domain Admins
Id               : 6ac1786c-016f-11d2-945f-00c04fb984f9
GpoStatus        : AllSettingsEnabled
Description      :
CreationTime     : 10/14/2024 12:17:31 PM
ModificationTime : 10/14/2024 12:17:30 PM
UserVersion      : AD Version: 0, SysVol Version: 0
ComputerVersion  : AD Version: 1, SysVol Version: 1
WmiFilter        :

DisplayName      : SetWallpaper GPO
DomainName       : wareville.thm
Owner            : WAREVILLE\Domain Admins
Id               : d634d7c1-db7a-4c7a-bf32-efca23d93a56
GpoStatus        : AllSettingsEnabled
Description      : Set the wallpaper of every domain joined machine
CreationTime     : 10/30/2024 9:01:36 AM
ModificationTime : 10/30/2024 9:01:36 AM
UserVersion      : AD Version: 0, SysVol Version: 0
ComputerVersion  : AD Version: 0, SysVol Version: 0
WmiFilter        :
```
This would allow us to look for **out-of-place GPO**s. We can export a **GPO** to an **HTML** file for further investigation to make it easier to see what configurations the policy enforces.

For this example, we will export the "``SetWallpaper``" **GPO**:
```powershell
PS C:\Users\Administrator> Get-GPOReport -Name "SetWallpaper" -ReportType HTML -Path ".\SetWallpaper.html"
```
Then, when opening the **HTML** file in the browser, we are presented with an overview of things:
- When the policy was created and modified.
- What devices or users the GPO applies to.
- The permissions over the GPO.
- The user or computer configurations that it enforces.
![[Pasted image 20241225151519.png]]
From the screenshot above, we can see that the policy sets the Desktop Wallpaper of devices using the image located in ``C:\THM.jpg`` on the domain controller.

Domains are naturally likely to have many **GPOs**. We can use the same ``Get-GPO`` cmdlet, but this time only list those **GPOs** that were recently modified:
```powershell
PS C:\Users\Administrator\Desktop> Get-GPO -All | Where-Object { $_.ModificationTime } | Select-Object DisplayName, ModificationTime

DisplayName                                ModificationTime
-----------                                ----------------
Default Domain Policy                      10/14/2024 12:19:28 PM
Default Domain Controllers Policy          10/14/2024 12:17:30 PM
SetWallpaper                               10/31/2024 1:01:04 PM
```
## Event Viewer
Event Viewer stores a record of system activity, including **security events, service behaviours, and so forth**.

For example: Within the "``Security``" tab of Event Viewer, we can see the history of **user logins, attempts and logoffs**. The screenshot below shows a record of the user "``cmnatic``" attempting to log into the device:
![[Pasted image 20241225152409.png]]
All categories of events are given an **event ID**. The table below provides **notable** event IDs for today's task:

| Event ID | Description                                                              |
| -------- | ------------------------------------------------------------------------ |
| 4624     | A user has logged on                                                     |
| 4625     | A user account failed to log on                                          |
| 4672     | Special privileges (i.e. ``SeTcbPrivilege``) have ben assigned to a user |
| 4768     | A **TGT** (Kerberos) ticket was requested for a high-privileged account  |
## User Auditing
User accounts are a valuable and often successful method of attack. You can use **Event Viewer IDs** to review user events and **PowerShell** to audit their status. Attack methods such as password spraying will eventually result in user accounts being locked out, depending on the domain controller's lockout policy.

To view all locked accounts, you can use the ``Search-ADAccount`` cmdlet, applying some filters to show information such as the **last successful log-on**.
```powershell
Search-ADAccount -LockedOut | Select-Object Name, SamAccountName, LockedOut, LastLogonDate, DistinguishedName`
```
Additionally, a great way to quickly review the **user accounts present** on a domain, as well as their **group membership**, is by using the `Get-ADUser` cmdlet, demonstrated below:
```powershell
PS C:\Users\Administrator\Desktop> Get-ADUser -Filter * -Properties MemberOf | Select-Object Name, SamAccountName, @{Name="Groups";Expression={$_.MemberOf}} 
```
Which would output something like:

| Name          | SamAccountName | Groups                                                                                                        |
| ------------- | -------------- | ------------------------------------------------------------------------------------------------------------- |
| Administrator | Administrator  | {CN=Group Policy Creator Owners, CN=Users ,DC=wareville, DC=thm ....                                          |
| Guest         | Guest          | CN=Guests, CN=Builtin, DC=wareville, DC=thm                                                                   |
| krbtgt        | krbtgt         | CN=Denied RODC Password replication Group, CN=Users, DC=Wareville, DC=thm                                     |
| tryhackme     | tryhackme      | CN=Domain Admins, CN=Users, DC=wareville, DC=thm                                                              |
| DAVID         | DAVID          |                                                                                                               |
| james         | james          |                                                                                                               |
| NewAccount    | NewAccount     |                                                                                                               |
| cmnatic       | cmnatic        | {CN=Domain Admins, CN=Users, DC=wareville, DC=thm, CN=Remote Desktop Users, CN=Builtin, DC=wareville, DC=thm} |
## Reviewing PowerShell History and Logs
**PowerShell**, like **Bash** on Linux, keeps a history of the commands inputted into the session. Reviewing these can be a fantastic way to see recent actions taken by the user account on the machine.

On a Windows Server, this history file  is located at `%APPDATA%\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt`.
In my case it's:
```ConsoleHost_history.txt
whoami
ifconfig
ipconfig
ping 1.2.3.4
ping 1.1.1.1
Get-ADUser -Filter * -Properties MemberOf | Select-Object Name
Get-GPO -All
Get-GPO -ALl | Where-Object  { $_.ModificationTime } | Select-Object DisplayName, ModificationTime
```
Additionally, logs are recorded for every PowerShell process executed on a system. These logs are located within the Event Viewer under:
`Application and Services Logs -> Microsoft -> Windows -> PowerShell -> Operational` or 
`Application and Service Logs -> Windows PowerShell`. 
The logs have a wealth of information useful for incident response.
![[Pasted image 20241225154023.png]]