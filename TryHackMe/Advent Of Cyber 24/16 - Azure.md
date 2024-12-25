- Learn about Azure, what it is and why it is used. ✅
- Learn about Azure services like Azure Key Vault and Microsoft Entra ID. ✅
- Learn how to interact with an Azure tenant using Azure Cloud Shell. ✅
# Intro to Azure
Azure is a **CSP** (**Cloud Service Provider**), and **CSPs** (_i.e. **Google Cloud** and **AWS**_) provide computing resources such as computing power on demand in a highly scalable fashion. In other words, McSkidy could instead have Azure manage her underlying infrastructure, **scaling it in times of increased demand and decreasing it once traffic resumed to normal** levels. 

The best bit? McSkidy **only has to pay for what she uses**; gone were the days of buying physical infrastructure to handle increased loads, only for that infrastructure to go unused the majority of the time.

Azure boasts many benefits beyond cost optimisation. Azure also gave McSkidy access to lots of cloud services ranging from identity management to data ingestion (at the time of writing, there are over 200 services), these services can be used to **build**, **deploy**, and **manage** McSkidy's current infrastructure as well as give her the options to upgrade or build new applications in the future given the range of services available.

A couple of Azure services will come up during the Glitch's attack path. Let's take a look at them now: [[#Azure Key Vault]] & [[#Microsoft Entra ID]].
## Azure Key Vault
This is an Azure service that allows users to securely store and access secrets. 

These secrets can be anything from **API Keys, certificates, passwords, cryptographic keys**, and **more**. Essentially, anything you want to keep safe, away from the eyes of others, and easily configure and restrict access to is what you want to store in an **Azure Key Vault**.

The secrets are stored in vaults, which are created by **vault owners**. Vault owners have full access and control over the vault, including the ability to enable auditing so a record is kept of who accessed what secrets and grant permissions for other users to access the vault (known as **vault consumers**).
## Microsoft Entra ID
McSkidy also needed a way to grant users access to her system and be able to secure and organise their access easily. So, a Wareville town member could easily access or update their secret. **Microsoft Entra ID** (formerly **Azure Active Directory**) is Azure's solution. 

**Entra ID** is an ``identity and access management`` (**IAM**) service. In short, it has the information needed to assess whether a user/application can access **X** resource. In the case of the Wareville town members, they made an **Entra ID** account, and McSkidy assigned the appropriate permissions to this account.

With that covered, let's see what the Glitch has come up with.
# Assumed Breach Scenario
Knowing that a potential breach had happened, McSkidy decided to conduct an Assumed Breach testing within their Azure tenant. The **Assumed Breach scenario** is a type of penetration testing setup in which an initial access or foothold is provided, mimicking the scenario in which an attacker has already established its access inside the internal network.

In this setup, the mindset is to assess how far an attacker can go once they get inside your network, including all possible attack paths that could branch out from the defined starting point of intrusion.

I get my Azure credentials:
![[Pasted image 20241225174723.png]]
## Azure Cloud Shell
**Azure Cloud Shell** is a browser-based **command-line interface** that provides a convenient and powerful way to **manage** Azure resources. It integrates **both Bash and PowerShell** environments, allowing users to **execute scripts, manage Azure services,** and **run commands** directly from their web browser without needing local installation. 

Cloud Shell has built-in tools and pre-configured environments, including **Azure CLI, Azure PowerShell,** and **popular development tools**, making it an efficient solution for cloud management and automation tasks.

## Azure CLI
**Azure CLI**, is a command-line tool for managing and configuring Azure resources. The Glitch relied heavily on this tool while reviewing the Wareville tenant, so let's use the same one while walking through the Azure attack path.

As mentioned above, **Azure CLI** is part of the built-in tools inside the **Cloud Shell**, so we can launch **Azure Cloud Shell** by clicking on the terminal icon shown below:
![[Pasted image 20241225175035.png]]

We select **Bash**, since we will be executing **Azure CLI** commands:
![[Pasted image 20241225175118.png]]

To get started, we select `No storage account required` and choose `Az-Subs-AoC` for the subscription:
![[Pasted image 20241225175153.png]]
And we successfully get a **CLI**:
![[Pasted image 20241225175300.png]]

Now we are ready to execute **Azure CLI** commands in the **Azure Cloud Shell**:
```bash
$ az help

Group
    az
Subgroups:
    account               : Manage Azure subscription information.
    acr                   : Manage private registries with Azure Container Registries.
    ad                    : Manage Microsoft Entra ID (formerly known as Azure Active Directory,
                            Azure AD, AAD) entities needed for Azure role-based access control
                            (Azure RBAC) through Microsoft Graph API.
    advisor               : Manage Azure Advisor.
    afd                   : Manage Azure Front Door Standard/Premium.
    ai-examples [Preview] : Add AI powered examples to help content.
    aks                   : Manage Azure Kubernetes Services.
    ams                   : Manage Azure Media Services resources.
    apim                  : Manage Azure API Management services.
    appconfig             : Manage App Configurations.
    appservice            : Manage App Service plans.
    aro                   : Manage Azure Red Hat OpenShift clusters.
    backup                : Manage Azure Backups.
    batch                 : Manage Azure Batch.
    bicep                 : Bicep CLI command group.
    billing               : Manage Azure Billing.
    bot                   : Manage Microsoft Azure Bot Service.
    cache                 : Commands to manage CLI objects cached using the `--defer` argument.
    capacity              : Manage capacity.
    cdn                   : Manage Azure Content Delivery Networks (CDNs).
    cloud                 : Manage registered Azure clouds.
    cognitiveservices     : Manage Azure Cognitive Services accounts.
    compute-recommender   : Manage sku/zone/region recommender info for compute resources.
    config [Experimental] : Manage Azure CLI configuration.
    connection            : Commands to manage Service Connector local connections which allow local
                            environment to connect Azure Resource. If you want to manage connection
                            for compute service, please run 'az webapp/containerapp/spring
                            connection'.
    consumption [Preview] : Manage consumption of Azure resources.
    container             : Manage Azure Container Instances.
    containerapp          : Manage Azure Container Apps.
    cosmosdb              : Manage Azure Cosmos DB database accounts.
    databoxedge [Preview] : Manage device with databoxedge.
    deployment            : Manage Azure Resource Manager template deployment at subscription scope.
    deployment-scripts    : Manage deployment scripts at subscription or resource group scope.
    disk                  : Manage Azure Managed Disks.
    disk-access           : Manage disk access resources.
    disk-encryption-set   : Disk Encryption Set resource.
    dla      [Deprecated] : Manage Data Lake Analytics accounts, jobs, and catalogs.
    dls         [Preview] : Manage Data Lake Store accounts and filesystems.
    dms                   : Manage Azure Data Migration Service (classic) instances.
    eventgrid             : Manage Azure Event Grid topics, domains, domain topics, system topics
                            partner topics, event subscriptions, system topic event subscriptions
                            and partner topic event subscriptions.
    eventhubs             : Eventhubs.
    extension             : Manage and update CLI extensions.
    feature               : Manage resource provider features.
    functionapp           : Manage function apps. To install the Azure Functions Core tools see
                            https://github.com/Azure/azure-functions-core-tools.
    group                 : Manage resource groups and template deployments.
    hdinsight             : Manage HDInsight resources.
    identity              : Managed Identities.
    image                 : Manage custom virtual machine images.
    iot                   : Manage Internet of Things (IoT) assets.
    keyvault              : Manage KeyVault keys, secrets, and certificates.
    kusto                 : Manage Azure Kusto resources.
    lab         [Preview] : Manage azure devtest labs.
    lock                  : Manage Azure locks.
    logicapp              : Manage logic apps.
    managed-cassandra     : Azure Managed Cassandra.
    managedapp            : Manage template solutions provided and maintained by Independent
                            Software Vendors (ISVs).
    managedservices       : Manage the registration assignments and definitions in Azure.
    maps                  : Manage Azure Maps.
    mariadb               : Manage Azure Database for MariaDB servers.
    ml                    : Manage Azure Machine Learning resources with the Azure CLI ML extension
                            v2.
    monitor               : Manage the Azure Monitor Service.
    mysql                 : Manage Azure Database for MySQL servers.
    netappfiles           : Manage Azure NetApp Files (ANF) Resources.
    network               : Manage Azure Network resources.
    policy                : Manage resource policies.
    postgres              : Manage Azure Database for PostgreSQL servers.
    ppg                   : Manage Proximity Placement Groups.
    private-link          : Private-link association CLI command group.
    provider              : Manage resource providers.
    redis                 : Manage dedicated Redis caches for your Azure applications.
    relay                 : Manage Azure Relay Service namespaces, WCF relays, hybrid connections,
                            and rules.
    resource              : Manage Azure resources.
    resourcemanagement    : Resourcemanagement CLI command group.
    restore-point         : Manage restore point with res.
    role                  : Manage Azure role-based access control (Azure RBAC).
    search                : Manage Azure Search services, admin keys and query keys.
    security              : Manage your security posture with Microsoft Defender for Cloud.
    servicebus            : Servicebus.
    sf                    : Manage and administer Azure Service Fabric clusters.
    sig                   : Manage shared image gallery.
    signalr               : Manage Azure SignalR Service.
    snapshot              : Manage point-in-time copies of managed disks, native blobs, or other
                            snapshots.
    sql                   : Manage Azure SQL Databases and Data Warehouses.
    ssh                   : SSH into resources (Azure VMs, Arc servers, etc) using AAD issued
                            openssh certificates.
    sshkey                : Manage ssh public key with vm.
    stack                 : A deployment stack is a native Azure resource type that enables you to
                            perform operations on a resource collection as an atomic unit.
    staticwebapp          : Manage static apps.
    storage               : Manage Azure Cloud Storage resources.
    synapse               : Manage and operate Synapse Workspace, Spark Pool, SQL Pool.
    tag                   : Tag Management on a resource.
    term   [Experimental] : Manage marketplace agreement with marketplaceordering.
    ts                    : Manage template specs at subscription or resource group scope.
    vm                    : Manage Linux or Windows virtual machines.
    vmss                  : Manage groupings of virtual machines in an Azure Virtual Machine Scale
                            Set (VMSS).
    webapp                : Manage web apps.
Commands:
    configure             : Manage Azure CLI configuration. This command is interactive.
    feedback              : Send feedback to the Azure CLI Team.
    find                  : Im an AI robot, my advice is based on our Azure documentation as well
                            as the usage patterns of Azure CLI and Azure ARM users. Using me
                            improves Azure products and documentation.
    interactive [Preview] : Start interactive mode. Installs the Interactive extension if
                            not installed already.
    login                 : Log in to Azure.
    logout                : Log out to remove access to Azure subscriptions.
    rest                  : Invoke a custom request.
    survey                : Take Azure CLI survey.
    upgrade     [Preview] : Upgrade Azure CLI and extensions.
    version               : Show the versions of Azure CLI modules and extensions in JSON format by
                            default or format configured by --output.

To search AI knowledge base for examples, use: az find "az "
```
Now we run this command:
```bash
$ az ad signed-in-user show
```
**Breakdown**:
* `az`: We can run **Azure CLI** commands.
* `ad` (**Active Directory**): Narrows the scope to **Azure AD** operations.
* `signed-in-user`: Specifies that the operation is related to the user currently authenticated in Azure CLI.
* `show`: Retrieves and displays details about this **signed-in** user.
The output is something like this:
```bash
{
  "@odata.context": "https://graph.microsoft.com/v1.0/$metadata#users/$entity",
  "businessPhones": [],
  "displayName": "usr-12257688",
  "givenName": null,
  "id": "14512bc0-0410-49f4-b75e-5b41602860f8",
  "jobTitle": null,
  "mail": null,
  "mobilePhone": null,
  "officeLocation": null,
  "preferredLanguage": null,
  "surname": null,
  "userPrincipalName": "usr-12257688@aoc2024.onmicrosoft.com"
}
```
## Going Down the Azure Rabbit Hole
When the Glitch got hold of an initial account in Wareville's Azure tenant, he had no idea what was inside it. So, he decided to enumerate first the existing users and groups within the tenant.
### Entra ID Enumeration
First, let's run ``$ az ad -h`` to see available commands for the AD:
```bash
$ az ad -h

Group
    az ad : Manage Microsoft Entra ID (formerly Active Directory) entities needed      for Azure role-based access control (Azure RBAC) through Microsoft Graph API.
    
Subgroups:
    app            : Manage Microsoft Entra applications.
    group          : Manage Microsoft Entra groups.
    signed-in-user : Show graph information about current signed-in user in CLI.
    sp             : Manage Microsoft Entra service principals.
    user           : Manage Microsoft Entra users.
```

Now we want to use the `user` command. Let's do one more help listing:
```bash
$ az ad user -h

Group
    az ad user : Manage Microsoft Entra users.
Commands:
    create            : Create a user.
    delete            : Delete a user.
    get-member-groups : Get groups of which the user is a member.
    list              : List users.
    show              : Get the details of a user.
    update            : Update a user.
```

Using the current account, let's start by **listing all the users** in the tenant.
```bash
$ az ad user list
```
The **Azure CLI** typically uses the following command syntax: `az GROUP SUBGROUP ACTION OPTIONAL_PARAMETERS`. Given this, the command above can be broken down into:
- Target group or service: `ad` **(Azure AD** or **Entra ID**)
- Target subgroup: `user` (**Azure AD** users)
- Action: `list`
**Example output:**
>[!info]- Collapsed
  {
"businessPhones": [],
"displayName": "breakglass",
"givenName": null,
"id": "d6c5bb7b-36a2-4706-ad5f-dd5a73e5dfd8",
"jobTitle": null,
"mail": null,
"mobilePhone": null,
"officeLocation": null,
"preferredLanguage": null,
"surname": null,
"userPrincipalName": "breakglass@aoc2024.onmicrosoft.com"
  },
  {
"businessPhones": [],
"displayName": "wiz",
...SNIP....

After executing the command, you might have been overwhelmed with the number of accounts listed. **For a better view**, let's only look for the accounts prepended with `wvusr-`. According to McSkidy, these accounts are more interesting than the other ones. 

To do this, we will use the `--filter` parameter and filter all accounts that start with `wvusr-`:
```bash
$ az ad user list --filter "startsWith('wvusr-', displayName)" 
```
**Example Output:**
```bash
... Snip ...
{
    "businessPhones": [],
    "displayName": "wvusr-backupware",
    "givenName": null,
    "id": "1db95432-0c46-45b8-b126-b633ae67e06c",
    "jobTitle": null,
    "mail": null,
    "mobilePhone": null,
    "officeLocation": "R3c0v3r_s3cr3ts!",
    "preferredLanguage": null,
    "surname": null,
    "userPrincipalName": "wvusr-backupware@aoc2024.onmicrosoft.com"
  },
  {
    "businessPhones": [],
    "displayName": "wvusr-firmware",
    "givenName": null,
    "id": "1f80a74b-4abc-4f93-b065-965ea5c826c8",
    "jobTitle": null,
    "mail": null,
    "mobilePhone": null,
    "officeLocation": null,
    "preferredLanguage": null,
    "surname": null,
    "userPrincipalName": "wvusr-firmware@aoc2024.onmicrosoft.com"
  },
  ... Snip ...
```

You may observe that an unusual parameter was set to a specific account in the output. One of the users, **wvusr-backupware**, has its password stored in one of the fields:
```bash
... Snip ...
  {
    "businessPhones": [],
    "displayName": "wvusr-backupware",
    "givenName": null,
    "id": "1db95432-0c46-45b8-b126-b633ae67e06c",
    "jobTitle": null,
    "mail": null,
    "mobilePhone": null,
    "officeLocation": "REDACTED",
    "preferredLanguage": null,
    "surname": null,
    "userPrincipalName": "wvusr-backupware@aoc2024.onmicrosoft.com"
  },
... Snip ...
```

When the Glitch saw this one, he immediately thought it could be the first step taken by the intruder to gain further access inside the tenant. However, he decided to continue the initial reconnaissance of users and groups. Now, let's continue by listing the groups:
```bash
$ az ad group list
```
**Example Output**:
```bash
[
  {
    "classification": null,
    "createdDateTime": "2024-10-13T23:10:55Z",
    "creationOptions": [],
    "deletedDateTime": null,
    "description": "Group for recovering Wareville's secrets",
    "displayName": "Secret Recovery Group",
    "expirationDateTime": null,
    "groupTypes": [],
    "id": "7d96660a-02e1-4112-9515-1762d0cb66b7",
    "isAssignableToRole": null,
    "mail": null,
    "mailEnabled": false,
    "mailNickname": "f315e3ef-c",
    "membershipRule": null,
    "membershipRuleProcessingState": null,
    "onPremisesDomainName": null,
    "onPremisesLastSyncDateTime": null,
    "onPremisesNetBiosName": null,
    "onPremisesProvisioningErrors": [],
    "onPremisesSamAccountName": null,
    "onPremisesSecurityIdentifier": null,
    "onPremisesSyncEnabled": null,
    "preferredDataLocation": null,
    "preferredLanguage": null,
    "proxyAddresses": [],
    "renewedDateTime": "2024-10-13T23:10:55Z",
    "resourceBehaviorOptions": [],
    "resourceProvisioningOptions": [],
    "securityEnabled": true,
    "securityIdentifier": "S-1-12-1-2107008522-1091699425-1645680021-3076967376",
    "serviceProvisioningErrors": [],
    "theme": null,
    "uniqueName": null,
    "visibility": "Private"
  }
]
```
>[!important] You may observe that we just changed the previous command from `az ad user list` to `az ad group list`.

Based on this output, a group named `Secret Recovery Group` exists. This is an interesting group because of the description, so let's list the members of this group:
```bash
$ az ad group member list --group "Secret Recovery Group"
```
**Example Output**:
```bash
[
  {
    "@odata.type": "#microsoft.graph.user",
    "businessPhones": [],
    "displayName": "wvusr-backupware",
    "givenName": null,
    "id": "1db95432-0c46-45b8-b126-b633ae67e06c",
    "jobTitle": null,
    "mail": null,
    "mobilePhone": null,
    "officeLocation": "R3c0v3r_s3cr3ts!",
    "preferredLanguage": null,
    "surname": null,
    "userPrincipalName": "wvusr-backupware@aoc2024.onmicrosoft.com"
  }
]
```
Given the previous output, it looks like everything makes a little sense now. All of the previous commands seem to point to the `wvusr-backupware` account. 

Since we have seen a potential set of credentials, let's jump to another user by clearing the current Azure CLI account session and logging in with the new account:
```bash
$ az account clear

$ az login -u EMAIL -p PASSWORD

#Replace email with the email from "userPrincipalName"
#Replace the password with the one from "officeLocation"
```
### Azure Role Assignments
Since the `wvusr-backupware` account belongs to an interesting group, the first hunch is to see whether **sensitive or privileged roles are assigned to the group**.

But before checking the assigned roles, let's have a quick run-through of **Azure Role Assignments**. 
* **Azure Role Assignments** define the resources that **each user or group** can access. 
* When a new user is created via **Entra ID**, it cannot access any resource by default due to a lack of role.
* To grant access, an administrator must assign a **role** to let users view or manage a specific resource.
* The privilege level configured in a role ranges from **read-only to full-control**. Additionally, **group members can inherit a role** when assigned to a group.

Returning to the **Azure enumeration**, let's see if a role is assigned to the ``Secret Recovery Group``. We will be using the `--all` option to **list all roles** within the Azure subscription, and we will be using the `--assignee` option with the **group's ID** to render only the ones related to our target group:
```bash
$ az role assignment list --all --assignee 7d96660a-02e1-4112-9515-1762d0cb66b7 
```
>[!important] You may retrieve the group ID from the command executed previously: `az ad group list`.

**Output**:
```bash
[
  {
    "condition": null,
    "conditionVersion": null,
    "createdBy": "b470c1dc-9d37-4ce9-b528-4aeaf819781a",
    "createdOn": "2024-10-14T20:25:32.172518+00:00",
    "delegatedManagedIdentityResourceId": null,
    "description": null,
    "id": "/subscriptions/ddd3338d-bc5a-416d-8247-1db1f5b5ff43/resourceGroups/rg-aoc-akv/providers/Microsoft.KeyVault/vaults/warevillesecrets/providers/Microsoft.Authorization/roleAssignments/3038142a-80c7-4bf1-b7c2-0939b906316d",
    "name": "3038142a-80c7-4bf1-b7c2-0939b906316d",
    "principalId": "7d96660a-02e1-4112-9515-1762d0cb66b7",
    "principalName": "Secret Recovery Group",
    "principalType": "Group",
    "resourceGroup": "rg-aoc-akv",
    "roleDefinitionId": "/subscriptions/ddd3338d-bc5a-416d-8247-1db1f5b5ff43/providers/Microsoft.Authorization/roleDefinitions/21090545-7ca7-4776-b22c-e363652d74d2",
    "roleDefinitionName": "Key Vault Reader",
    "scope": "/subscriptions/ddd3338d-bc5a-416d-8247-1db1f5b5ff43/resourceGroups/rg-aoc-akv/providers/Microsoft.KeyVault/vaults/warevillesecrets",
    "type": "Microsoft.Authorization/roleAssignments",
    "updatedBy": "b470c1dc-9d37-4ce9-b528-4aeaf819781a",
    "updatedOn": "2024-10-14T20:25:32.172518+00:00"
  },
  {
    "condition": null,
    "conditionVersion": null,
    "createdBy": "b470c1dc-9d37-4ce9-b528-4aeaf819781a",
    "createdOn": "2024-10-14T20:26:53.771014+00:00",
    "delegatedManagedIdentityResourceId": null,
    "description": null,
    "id": "/subscriptions/ddd3338d-bc5a-416d-8247-1db1f5b5ff43/resourceGroups/rg-aoc-akv/providers/Microsoft.KeyVault/vaults/warevillesecrets/providers/Microsoft.Authorization/roleAssignments/d2edb9d3-620b-45a0-af60-128b5153a00a",
    "name": "d2edb9d3-620b-45a0-af60-128b5153a00a",
    "principalId": "7d96660a-02e1-4112-9515-1762d0cb66b7",
    "principalName": "Secret Recovery Group",
    "principalType": "Group",
    "resourceGroup": "rg-aoc-akv",
    "roleDefinitionId": "/subscriptions/ddd3338d-bc5a-416d-8247-1db1f5b5ff43/providers/Microsoft.Authorization/roleDefinitions/4633458b-17de-408a-b874-0445c86b69e6",
    "roleDefinitionName": "Key Vault Secrets User",
    "scope": "/subscriptions/ddd3338d-bc5a-416d-8247-1db1f5b5ff43/resourceGroups/rg-aoc-akv/providers/Microsoft.KeyVault/vaults/warevillesecrets",
    "type": "Microsoft.Authorization/roleAssignments",
    "updatedBy": "b470c1dc-9d37-4ce9-b528-4aeaf819781a",
    "updatedOn": "2024-10-14T20:26:53.771014+00:00"
  }
]
```
The output seems slightly overwhelming, so let's break it down:
- First, it can be seen that there are **two entries** in the output, which means two roles are assigned to the group.
- Based on the `roleDefinitionName` field, the two roles are `Key Vault Reader` and `Key Vault Secrets User`.
- Both entries have the same scope value, pointing to a **Microsoft Key Vault** resource, specifically on the `warevillesecrets` vault.
Here's the definition of the roles based on the [Microsoft documentation](https://learn.microsoft.com/en-us/azure/role-based-access-control/built-in-roles):

| Role                   | Microsoft Definition                                                                                         | Explanation                                                                                                                                                            |
| ---------------------- | ------------------------------------------------------------------------------------------------------------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Key Vault Reader       | Read metadata of key vaults and it's certificates, keys, and secrets.                                        | - This role allows you to read metadata of key vaults and it's certs, keys, and secrets. <br>- **Can't read sensitive values such as secret contents or key material** |
| Key Vault Secrets User | Read secret contents. Only works for key vaults that use 'Azure role-based access control' permission model. | - This role allows you to read the contents of a **Key Vault Secret**                                                                                                  |
This configuration allowed the attacker to access the sensitive data they were protecting. Now that she knew this, she asked the Glitch to confirm her assumption.
### Azure Key Vault
Glitch is now tasked to verify if the current account, **wvusr-backupware**, can access the sensitive data. 

Let's list the accessible key vaults by executing this command:
```bash
$ az keyvault list
```
**Output**:
```bash
[
  {
    "id": "/subscriptions/ddd3338d-bc5a-416d-8247-1db1f5b5ff43/resourceGroups/rg-aoc-akv/providers/Microsoft.KeyVault/vaults/warevillesecrets",
    "location": "eastus",
    "name": "warevillesecrets",
    "resourceGroup": "rg-aoc-akv",
    "tags": {},
    "type": "Microsoft.KeyVault/vaults"
  }
]
```

The output above confirms **the key vault** discovered from the role assignments named `warevillesecrets`. Now, let's see if **secrets** are stored in this key vault:
```bash
$az keyvault secret list --vault-name warevillesecrets
```
**Output**:
```bash
[
  {
    "attributes": {
      "created": "2024-10-14T20:22:20+00:00",
      "enabled": true,
      "expires": null,
      "notBefore": null,
      "recoverableDays": 90,
      "recoveryLevel": "Recoverable+Purgeable",
      "updated": "2024-10-14T20:22:20+00:00"
    },
    "contentType": null,
    "id": "https://warevillesecrets.vault.azure.net/secrets/aoc2024",
    "managed": null,
    "name": "aoc2024",
    "tags": {}
  }
]
```

After executing the two previous commands, we confirmed that the **Reader** role allows us to view the key vault metadata, specifically the list of key vaults and secrets. 

Now, the only thing left to confirm is whether the current user can access the contents of the discovered secret with the **Key Vault Secrets User** role. This can be done via the following command:
```bash
az keyvault secret show --vault-name warevillesecrets --name aoc2024
```
**Output**:
```bash
{
  "attributes": {
    "created": "2024-10-14T20:22:20+00:00",
    "enabled": true,
    "expires": null,
    "notBefore": null,
    "recoverableDays": 90,
    "recoveryLevel": "Recoverable+Purgeable",
    "updated": "2024-10-14T20:22:20+00:00"
  },
  "contentType": null,
  "id": "https://warevillesecrets.vault.azure.net/secrets/aoc2024/7f6bf431a6a94165bbead372bca28ab4",
  "kid": null,
  "managed": null,
  "name": "aoc2024",
  "tags": {},
  "value": "WhereIsMyMind1999"
}
```
McSkidy had confirmed her nightmare that a regular user could escalate their way into the secrets of Wareville.