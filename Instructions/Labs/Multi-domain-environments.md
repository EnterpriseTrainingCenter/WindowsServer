# Lab: Multi-domain environments

## Required VMs

* VN1-SRV5
* VN1-SRV7
* VN1-SRV10
* VN2-SRV2
* PM-SRV1
* CL1
* CL2
* CL3
* CL4

If you did not complete the lab [Deploying domain controllers](Deploying-domain-controllers.md), in addition to the VMs above, **VN1-SRV1** is required. If VN1-SRV1 is already shut down after the lab, do not start it.

## Setup

1. On **CL1**, sign in as **ad\\Administrator**.
1. In the context menu of **Start**, click **Terminal**.
1. In Terminal, execute ````C:\LabResources\Solutions\New-Shares.ps1````.

    You do not have to wait for the script to finish. You can safely ignore the following warnings and error messages:

    ````text
    WARNING: Certificate cannot be requested!
    ````

    ````text
    Copy-Item : Cannot find path 'C:\Program Files\Windows Admin Center\PowerShell\Modules\*\' because it does not exist.
    ````

    ````text
    Import-Connection : The term 'Import-Connection' is not recognized as the name of a cmdlet, function, script file, or   operable program. Check the spelling of the name, or if a path was included, verify that the path is correct and try again.
    ````
    
    ````text
    WARNING: This script cannot add computers to Windows Admin Center
    ````

    ````text
    WARNING: Extension cannot be installed by script.
    ````

    ````text
    New-CimSession: WinRM cannot complete the operation.
    ````

    ````text
    WARNING: Junction cannot be created.
    ````

    You do not have to wait for the command to complete until exercise 2, task 3.

1. On **CL4**, sign in as **.\\Administrator**.
1. On **VN2-SRV2** sign in as **contoso\\Administrator**.

You must have completed the lab [Deploying Domain Controllers](Deploying-domain-controllers.md). If you skipped the lab, on **CL1**, in **Terminal**, run ````Get-WindowsCapability -Online -Name 'Rsat.Dns.Tools*' | Add-WindowsCapability -Online````. You do not have to wait for the command to complete until exercise 2, task 3.

For exercises 5 and 6, if you skipped exercise 6 of the lab [Deploying Domain Controllers](Deploying-domain-controllers.md#exercise-6-deploy-a-new-forest) (meaning, you do not have the CONTOSO domain), on **CL3**, in **Terminal**, on **CL3**, sign in as **.\\Administrator** and, in **Terminal**, run run ````C:\LabResources\Solutions\Install-Forest.ps1````. You do not have to wait for the command to complete until exercise 5.

## Introduction

Adatum wants to partition Active Directory in a domain for clients (users and client computers) and an extranet domain. The domain for the clients should be named clients.ad.adatum.com, the domain for the extranet should be named extranet.adatum.com.

The user principal names for users in Adatum and Contoso should not change, regardless of the user's domain.

Adatum discovers, that a failure of the root domain causes authentication problems between the new clients and extranet domains. Adatum wants to mitigate that problems.

Currently, Contoso users cannot access resources in Adatum. Because Contoso collaborates closely with Adatum, resource access between the forests should be enabled.

## Exercises

1. [Managing user principal names](#exercise-1-managing-user-principal-names)
1. [Deploy a child domain](#exercise-2-deploy-a-child-domain)
1. [Deploy a domain in a new tree](#exercise-3-deploy-a-domain-in-a-new-tree)
1. [Create and validate shortcut trusts](#exercise-4-create-and-validate-shortcut-trusts)
1. [Create and validate a forest trust](#exercise-5-create-and-validate-a-forest-trust)
1. [Migrating users between domains](#exercise-6-migrating-users-between-domains)

## Exercise 1: Managing user principal names

1. [Add an UPN suffix](#task-1-add-an-upn-suffix) adatum.com to the forest ad.adatum.com
1. [Change the UPN of all users](#task-2-change-the-upn-of-all-users) in the organizational units

    * Development
    * IT
    * Managers
    * Marketing
    * Research
    * Sales

1. [Verify the login with the user principal name](#task-3-verify-the-login-with-the-user-principal-name) Larry@adatum.com
1. [Add an UPN suffix](#task-4-add-an-upn-suffix) contoso.com to the forest ad.contoso.com
1. [Create a new user with an alternative UPN suffix](#task-5-create-a-new-user-with-an-alternative-upn-suffix) in ad.contoso.com

### Task 1: Add an UPN suffix

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Domains and Trusts**.
1. In Active Directory Domains and Trusts, in the context-menu of **Active Directory Domain and Trusts**, click **Properties**.
1. In Active Directory Domains and Trusts, on tab UPN Suffixes, under **Alternative UPN suffixes**, enter **adatum.com** and click **Add**.
1. Click **OK**.

#### PowerShell

Perform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. Add the UPN suffix **adatum.com** to the forest **ad.adatum.com**.

    ````powershell
    Set-ADForest -Identity ad.adatum.com -UPNSuffixes @{ add = 'adatum.com' }
    ````

### Task 2: Change the UPN of all users

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **ad (local)**.
1. In ad (local), double-click **Development**.
1. In Development, click the column header **Type**, click any object in Development and press CTRL + A to select all objects. Hold down CTRL and click the group **Development** to unselect it.
1. In the context menu of any selected user object, click **Properties**.
1. In Multiple Users, activate **Logon UPN Suffix**, click **adatum.com**, and click **OK**.

Repeat the steps 2 - 6 for the organizational units

* IT
* Managers
* Marketing
* Research
* Sales

#### PowerShell

Perform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. For users in the organizational units **Development**, **IT**, **Marketing**, **Research**, and **Sales**, replace the domain part of the user principal name with **adatum.com**.

    ````powershell
    @('Development', 'IT', 'Marketing', 'Research', 'Sales') | ForEach-Object { 
        Get-ADUser `
            -SearchBase "ou=$PSItem, dc=ad, dc=adatum, dc=com" `
            -Filter * | 
        ForEach-Object { 
            $PSItem | Set-ADUser `
                -UserPrincipalName (
                    $PSItem.UserPrincipalName -replace `
                        # https://regex101.com/r/DaI5XD/1
                        '^(.*)(@ad.adatum.com)$', '$1@adatum.com'
                )
        }
    }
    ````

### Task 3: Verify the login with the user principal name

Perform this task on CL2.

1. Sign in as **Larry@adatum.com**

    > Larry Rayford should be able to sign in successfully.

1. Sign out.

### Task 5: Create a new user with an alternative UPN suffix

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **ad (local)**.
1. In ad (local), double-click **IT**.
1. In the pane **Tasks**, under **IT**, click **New**, **User**.
1. Create User, in **First name**, type **Wil**. In **Last name**, type **Ruiz**. In **User UPN logon**, type **Wil** and, beside **@**, click **adatum.com**. In User SamAccountName logon, before the backslash, ensure **ad** is typed in, and after the backslash, ensure **Wil** is typed in. In **Password** and **Confirm password**, type a secure password and take a note. Click **OK**.

#### PowerShell

Perform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. In Terminal, set up parameter variables for the new user.

    ````powershell
    $firstName = 'Wil'
    $lastName = 'Ruiz'

    $name = "$firstName $lastName"
    $userPrincipalName = "$firstname@adatum.com"
    ````

1. Create the new user.

    ````powershell
    $aDUser = New-ADUser `
        -Path 'ou=IT, dc=ad, dc=adatum, dc=com' `
        -Name $name `
        -GivenName $firstName `
        -Surname $lastName `
        -DisplayName  $name `
        -UserPrincipalName $userPrincipalName `
        -SamAccountName $firstName `
        -PassThru
    ````

1. Reset the password.

    ````powershell
    $aDUser | Set-ADAccountPassword -Reset
    $aDUser | Set-ADUser -ChangePasswordAtLogon $true
    ````

    Beside **Password** and **Repeat Password**, enter a secure password.

1. Enable the user.

    ````powershell
    $aDUser | Enable-ADAccount
    ````

## Exercise 2: Deploy a child domain

1. [Install Active Directory Domain Services](#task-1-install-active-directory-domain-services) on VN1-SRV7
1. [Configure Active Directory Domain Services as new child domain](#task-2-configure-active-directory-domain-services-as-new-child-domain) clients.ad.adatum.com on VN1-SRV7
1. [Optimize name resolution using conditional forwarders](#task-3-optimize-name-resolution-performance-using-conditional-forwarders) on VN1-SRV7

    > Why does the name resolution from the child domain to the root domain work without further configuration?

    > Why should you replace the general forwarder with a conditional forwarder?

1. [Configure DNS client settings on the new domain controller](#task-4-configure-dns-client-settings-on-the-new-domain-controller) to point to 10.1.1.56 and localhost
1. [Verify name resolution on the DNS server within the child domain](#task-5-verify-name-resolution-on-the-dns-server-within-the-child-domain)

    > Which IP addresses are returned when executing a query for ad.adatum.com on VN1-SRV7?

    > Which IP address is returned when executing a query for clients.ad.adatum.com on 10.1.1.8 (10.1.1.8 is either VN1-SRV1 or VN1-SRV5)?

    > Why does name resolution for clients.ad.adatum.com work on VN1-SRV1 or VN1-SRV5 without further configuration?

1. [Change the DNS client settings](#task-6-change-the-dns-client-settings) on CL4 to use 10.1.1.56 (VN1-SRV7)
1. [Connect to domain](#task-7-connect-to-domain) clients.ad.contoso.com on CL4.

### Task 1: Install Active Directory Domain Services

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before You Begin, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-basedd installation** is selected and click **Next >**.
1. On page Server Selection, click **VN1-SRV7** and click **Next >**.
1. On page Server Roles, activate **Active Directory Domain Services**.
1. In the dialog **Add features that are required for Active Directory Domain Services?**, click **Add Features**
1. On page **Server Roles**, click **Next >**.
1. On page Features, click **Next >**.
1. On page **AD DS**, click **Next >**.
1. On page **Confirmation**, activate the checkbox **Restart the destination server automatically if required** and click **Install**.
1. On page **Results**, click **Close**.

#### PowerShell

Peform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. Install the windows feature **Active Directory Domain Services** on **VN1-SRV5**.

    ````powershell
    Install-WindowsFeature `
        -Name AD-Domain-Services `
        -IncludeManagementTools `
        -ComputerName VN1-SRV7 `
        -Restart
    ````

### Task 2: Configure Active Directory Domain Services as new child domain

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click *Notifications* (the flag with the yellow warning triangle), and under the message **Configuration required for Active Directory Domain Services at VN1-SRV7**, click **Promote this server to a domain controller**.
1. In Active Directory Domain Services Configuration Wizard, on page Deployment Configuration, click **Add a new domain to an existing forest**. In **Select domain type**, ensure **Child Domain** is selected. In **Parent domain name**, ensure **ad.adatum.com** is filled in. In **New domain name**, type **clients**. Beside **\<No credentials provided\>**, click **Change...**.
1. In the dialog Credentials for deployment operation, enter the credentials for **Administrator@ad.adatum.com** and click **OK**.
1. On page **Deployment Configuration**, click **Next >**.
1. On page **Domain Controller Options**, ensure **Domain Name System (DNS) server** is activated and deactivate **Global Catalog (GC)**. Under **Type the Directory Services Restore Mode (DSRM) password**, in **Password** and **Confirm password**, type a secure password and take a note. You will need the password for a later lab. Click **Next >**.
1. On page DNS Options, click **Next >**.
1. On page Additional Options, in **The NetBIOS domain name**, ensure **CLIENTS** is filled in and click **Next >**.
1. On page **Paths**, click **Next >**.

    Note: In real world, it is recommended to have the paths on a separate drive.

1. On page Review Options, click **Next >**.
1. On page Prerequisites Check, click **Install**.
1. On page Results, click **Close**.

#### PowerShell

Perform this task on CL1.

1. In the context menu of **Start**, click **Terminal (Admin)**.
1. Store the Directory Services Restore Mode (DSRM) password in a variable.

    ````powershell
    $safeModeAdministratorPasswordSecure = Read-Host `
        -Prompt 'Directory Services Restore Mode (DSRM) password' `
        -AsSecureString
    ````

1. At the prompt **Directory Services Restore Mode (DSRM) password** enter a secure password and take a note.
1. Store the credentials for the Enterprise admin in variables.

    ````powershell
    $username = "Administrator@ad.adatum.com"
    $securePassword = Read-Host -Prompt "Password for $username" -AsSecureString
    ````

1. When prompted, enter the credentials for **Administrator@ad.adatum.com**.
1. Install a child domain **clients** with the parent domain **ad.adatum.com** on VN1-SRV7. Install DNS at the same time, but do not make it a Global Catalog server.

    ````powershell
    # Convert the secure strings back to a plain text string

    $password = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto(
        [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR(
            $securePassword
        )
    ) 

    $safeModeAdministratorPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto(
        [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR(
            $safeModeAdministratorPasswordSecure
        )
    ) 

    $job = Invoke-Command `
        -ComputerName VN1-SRV7.ad.adatum.com `
        -AsJob `
        -ScriptBlock {
        # Convert the passwords into a secure strings
        
        $securePassword = `
            ConvertTo-SecureString -String $using:password -AsPlainText -Force
        $secureSafeModeAdministratorPassword = `
            ConvertTo-SecureString `
                -String $using:safeModeAdministratorPassword `
                -AsPlainText `
                -Force

        # Create credentials
        $credential = New-Object `
            -TypeName pscredential `
            -ArgumentList $using:username, $securePassword

        Install-ADDSDomain `
            -DomainType ChildDomain `
            -ParentDomainName ad.adatum.com `
            -NewDomainName clients `
            -Credential $credential `
            -SafeModeAdministratorPassword `
                $secureSafeModeAdministratorPassword `
            -InstallDns `
            -Force
    }
    ````

1. Wait for the job to complete.

    ````powershell
    $job | Wait-Job
    ````

    This will take a few minutes.

1. Read the output of the job.

    ````powershell
    $job | Receive-Job
    ````

    The error message ````[VN1-SRV7.ad.adatum.com] Closing the remote server shell instance failed with the following error message : Access is denied.```` can be ignored safely.

Before continuing to the next task, wait for the sign in prompt to appear on **VN1-SRV7**.

### Task 3: Optimize name resolution performance using conditional forwarders

#### Desktop experience

Perform this task on CL1.

1. Open **DNS**.
1. In **Connect to DNS Server**, click **The following computer**, type **VN1-SRV7.clients.ad.adatum.com**, and click **OK**.
1. In DNS Manager, click and expand **VN1-SRV7.clients.ad.adatum.com**, and click **Conditional Forwarders**.
1. In the context-menu of **Conditional Forwarders**, click **New Conditional Forwarder...**
1. In New Conditional Forwarder, under **DNS Domain**, type **ad.adatum.com**. Under **IP addresses of the master servers**, click **\<Click here to add an IP Address or DNS Name\>**, and enter **10.1.1.8** and **10.1.2.8**. Activate the checkbox **Store this conditional forwarder in Active Directory** and, in the drop-downbelow, click **All DNS servers in this forsst**. Click **OK**.
1. In **DNS Manager**, click **VN1-SRV7.clients.ad.adatum.com**.
1. In the right pane, double-click **Forwarders**.
1. In VN1-SRV7.clients.ad.adatum.com Properties, on tab Forwarders, click **Edit...**
1. In Edit Forwarders, click **10.1.1.8**, and click **Delete**.

    > The name resolution to the root domain worked, because of the general forwarder.

1. In **\<Click here to add an IP Address or DNS Name\>**, enter **8.8.8.8**. Repeat this step with **8.8.4.4** and click **OK**.
1. In **VN1-SRV7.clients.ad.adatum.com Properties**, click **OK**.

> You should prefer conditional forwarders, because general forwarding of all unresolved queries may impact the performance of external Internet services negatively.

#### PowerShell

Perform this task on CL1.

1. In the context menu of **Start**, click **Terminal (Admin)**.
1. Store the name of the DNS server **VN1-SRV7** in a variable.

    ````powershell
    $computerName = 'VN1-SRV7.clients.ad.adatum.com'
    ````

1. On **VN1-SRV7**, add a conditional forwarder for zone **ad.adatum.com** pointing to **10.1.1.8** and **10.1.2.8**. The forwarder should be replicated forest-wide.

    ````powershell
    Add-DnsServerConditionalForwarderZone `
        -Name ad.adatum.com `
        -MasterServers 10.1.1.8, 10.1.2.8 `
        -ReplicationScope Forest `
        -ComputerName $computerName
    ````

1. On **VN1-SRV7**, set the DNS forwarders to **8.8.8.8** and **8.8.4.4**.

    ````powershell
    Set-DnsServerForwarder -IPAddress 8.8.8.8, 8.8.4.4 -ComputerName $computerName 
    ````

> The name resolution to the root domain worked, because of the general forwarder.

> You should prefer conditional forwarders, because general forwarding of all unresolved queries may impact the performance of external Internet services negatively.

### Task 4: Configure DNS client settings on the new domain controller

#### SConfig

Perform this task on VN1-SRV7.

1. Sign in as **clients\administrator**.
1. In SConfig, enter **8**.
1. In Network settings, enter **1**.
1. In Network adapter settings, enter **2**.
1. Beside Enter new preferred DNS server, enter **10.1.1.56**.

    Note: In real world, you should enter the IP address of a another DC of the same domain.

1. Beside Enter alternate DNS server, enter **127.0.0.1**.
1. Press ENTER to continue.
1. In SConfig, enter **12**.
1. Beside Are you sure you want to log off, enter **y**.

#### PowerShell

Perform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. Create a CIM session to **VN1-SRV7**.

    ````powershell
    $cimSession = New-CimSession -ComputerName VN1-SRV7.clients.ad.adatum.com
    ````

1. Set the DNS client server address for **VN1-SRV7** to **10.1.1.56** and **127.0.0.1**.

    ````powershell
    Set-DnsClientServerAddress `
        -InterfaceAlias VNet1 `
        -ServerAddresses 10.1.1.56, 127.0.0.1 `
        -CimSession $cimSession
    ````

    Note: In real world, you should enter the IP address of a another DC of the same domain.

1. Close and remove the CIM session.

    ````powershell
    Remove-CimSession $cimSession
    ````

1. Sign out.

### Task 5: Verify name resolution on the DNS server within the child domain

Perform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. Try to resolve the DNS name **ad.adatum.com** on the DNS server **VN1-SRV7.clients.ad.adatum.com**.

    ````powershell
    Resolve-DnsName -Name ad.adatum.com -Server VN1-SRV7.clients.ad.adatum.com
    ````

    > The IP addresses 10.1.1.8, 10.1.1.40, and 10.1.2.8 should be returned.

1. Try to resolve the DNS name **clients.ad.adatum.com** on the DNS server **VN1-SRV5.ad.adatum.com**.

    ````powershell
    Resolve-DnsName -Name clients.ad.adatum.com -Server 10.1.1.8
    ````

    > The IP address 10.1.1.56 should be returned.

    > The name resolution works, because the Active Directory Domain Services Configuration Wizard added a delegation for the clients domain to ad.adatum.com.

### Task 6: Change the DNS client settings

#### Desktop experience

Perform this task on CL4.

1. Open **Settings**.
1. In Settings, in the left pane, click **Network & internet**.
1. In Network & internet, click **Ethernet**.
1. In Ethernet, beside **DNS server assignment**, click **Edit**.
1. In Edit IP Settings, under **Preferred DNS**, type **10.1.1.56** and click **Save**.
1. Sign out.

#### PowerShell

Perform this task on CL4.

1. In the context menu of **Start**, click **Terminal (Admin)**.
1. Set the DNS client server address on the interface **Ethernet** to **10.1.2.16**.

    ````powershell
    Set-DnsClientServerAddress -InterfaceAlias Ethernet -ServerAddresses 10.1.1.56
    ````

### Task 7: Connect to domain

#### Desktop experience

Perform this task on CL4.

1. Open **Settings**.
1. In Settings, in the left pane, click **Accounts**.
1. In Accounts, click **Access work or school**.
1. In Access work or school, beside **Add a work or school account**, click **Connect**.
1. In Set up a work or school account, click the link **Join this device to a local Active Directory domain**.
1. In Join a domain, under **Domain name**, type **clients.ad.adatum.com** and click **Next**.
1. In Windows Security, enter the credentials for **Administrator@clients.ad.adatum.com**.
1. In Add an account, click **Skip**.
1. In Restart your PC, click **Restart now**.

#### PowerShell

1. In the context menu of **Start**, click **Terminal (Admin)**.
1. Add the computer to the domain **ad.clients.adatum.com** and restart it.

    ````powershell
    Add-Computer -DomainName clients.ad.adatum.com -Restart
    ````

1. In **Windows PowerShell credential request**, enter the the credentials of **Administrator@clients.ad.adatum.com**.

## Exercise 3: Deploy a domain in a new tree

1. [Add Conditional Forwarders](#task-1-add-conditional-forwarders) on 10.1.1.8 (VN1-SRV1 or VN1-SRV5) for extranet.adatum.com pointing to the IP address of PM-SRV1 (10.1.200.8)

    > Why do you need to add this conditional forwarder before deploying the new tree?

1. [Install Active Directory Domain Services on PM-SRV1](#task-2-install-active-directory-domain-services)
1. [Configure Active Directory Domain Services as new tree](#task-3-configure-active-directory-domain-services-as-new-tree) named extranet.adatum.com on PM-SRV1
1. [Configure forwarders](#task-4-configure-forwarders) on PM-SRV1 as 8.8.8.8 and 8.8.4.4
1. [Configure DNS client settings](#task-5-configure-dns-client-settings) on PM-SRV1 to point to its own IP address and localhost
1. [Verify name resolution of the new tree](#task-6-verify-name-resolution-of-the-new-tree)

    > Which IP address is returned when querying for extranet.adatum.com on VN1-SRV5.ad.adatum.com?

    > Which IP address is returned when querying for extranet.adatum.com on VN1-SRV7.clients.ad.adatum.com?

    > Which IP address is returned when querying for ad.adatum.com on PM-SRV1.extranet.adatum.com?

    > Which IP address is returned when querying for clients.ad.adatum.com on PM-SRV1.extranet.adatum.com?

### Task 1: Add Conditional Forwarders

#### Desktop experience

Perform this task on CL1.

1. Sign in as **Administrator@ad.adatum.com**.
1. Open **DNS**.
1. In **Connect to DNS Server**, click **The following computer**, type **10.1.1.8**, and click **OK**.

    10.1.1.8 is either VN1-SRV1 or VN1-SRV5.

1. In **DNS Manager**, click and expand **10.1.1.8**, and click **Conditional Forwarders**.
1. In the context-menu of **Conditional Forwarders**, click **New Conditional Forwarder...**
1. In New Conditional Forwarder, under **DNS Domain**, type **extranet.adatum.com**. Under **IP addresses of the master servers**, click **\<Click here to add an IP Address or DNS Name\>**, and enter **10.1.200.8**. Activate the checkbox **Store this conditional forwarder in Active Directory** and, below, click **All DNS servers in this forest**. Click **OK**.

> You need to add this conditional forwarder to ensure name resolution for the new tree from the existing root domain. Without this name resolution, creation of the trust between the root domain and the new tree fails.

#### PowerShell

Perform this task on CL1.

1. Sign in as **Administrator@ad.adatum.com**.
1. In the context menu of **Start**, click **Terminal (Admin)**.
1. On **VN1-SRV5**, add a conditional forwarder for zone **extranet.adatum.com** pointing to **10.1.200.8**. The forwarder should be replicated forest-wide.

    ````powershell
    Add-DnsServerConditionalForwarderZone `
        -Name extranet.adatum.com `
        -MasterServers 10.1.200.8 `
        -ReplicationScope Forest `
        -ComputerName 10.1.1.8
    ````

> You need to add this conditional forwarder to ensure name resolution for the new tree from the existing root domain. Without this name resolution, creation of the trust between the root domain and the new tree fails.

### Task 2: Install Active Directory Domain Services

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before You Begin, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-basedd installation** is selected and click **Next >**.
1. On page Server Selection, click **PM-SRV1** and click **Next >**.
1. On page Server Roles, activate **Active Directory Domain Services**.
1. In the dialog **Add features that are required for Active Directory Domain Services?**, click **Add Features**
1. On page **Server Roles**, click **Next >**.
1. On page Features, click **Next >**.
1. On page **AD DS**, click **Next >**.
1. On page **Confirmation**, activate the checkbox **Restart the destination server automatically if required** and click **Install**.
1. On page **Results**, click **Close**.
1. Run **Terminal**.
1. In Terminal, configure the inbound rule for Windows Remote Management (HTTP-In) for public networks in Windows Firewall on PM-SRV1 to allow for connections beyond the local subnet.

    ````powershell
    Invoke-Command -ComputerName PM-SRV1 -ScriptBlock {
        Set-NetFirewallRule `
            -Name WINRM-HTTP-In-TCP-PUBLIC `
            -Profile Public `
            -RemoteAddress Any
    }
    ````

    *Important:* This is only necessary in the lab environment. Do not configure this firewall rule in real world. In the lab environment, because there will be only one domain controller for the new domain, the Windows Firewall will enable the public profile instead of the domain profile. The public profile only allows connections to WinRM from the local subnet by default. For this reason, in the lab environment only, we configure the rule to allow connections from any remote address.

#### PowerShell

Peform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. In Terminal, install the Windows feature **Active Directory Domain Services** on **PM-SRV1**.

    ````powershell
    Install-WindowsFeature `
        -Name AD-Domain-Services, DNS `
        -IncludeManagementTools `
        -Restart `
        -ComputerName PM-SRV1
    ````

1. Configure the inbound rule for Windows Remote Management (HTTP-In) for public networks in Windows Firewall on PM-SRV1 to allow for connections beyond the local subnet.

    ````powershell
    Invoke-Command -ComputerName PM-SRV1 -ScriptBlock {
        Set-NetFirewallRule `
            -Name WINRM-HTTP-In-TCP-PUBLIC `
            -Profile Public `
            -RemoteAddress Any
    }
    ````

    *Important:* This is only necessary in the lab environment. Do not configure this firewall rule in real world. In the lab environment, because there will be only one domain controller for the new domain, the Windows Firewall will enable the public profile instead of the domain profile. The public profile only allows connections to WinRM from the local subnet by default. For this reason, in the lab environment only, we configure the rule to allow connections from any remote address.

### Task 3: Configure Active Directory Domain Services as new tree

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click *Notifications* (the flag with the yellow warning triangle), and under the message **Configuration required for Active Directory Domain Services at PM-SRV1**, click **Promote this server to a domain controller**.
1. In Active Directory Domain Services Configuration Wizard, on page Deployment Configuration, click **Add a new domain to an existing forest**. In **Select domain type**, click **Tree Domain**. In **Forest name**, ensure **ad.adatum.com** is filled in. In **New domain name**, type **extranet.adatum.com**. Beside **\<No credentials provided\>**, click **Change...**.
1. In the dialog Credentials for deployment operation, enter the credentials for **Administrator@ad.adatum.com** and click **OK**.
1. On page **Deployment Configuration**, click **Next >**.
1. On page **Domain Controller Options**, ensure **Domain Name System (DNS) server** is activated and deactivate **Global Catalog (GC)**. Under **Type the Directory Services Restore Mode (DSRM) password**, in **Password** and **Confirm password**, type a secure password and take a note. You will need the password for a later lab. Click **Next >**.
1. On page DNS Options, click **Next >**.
1. On page Additional Options, in **The NetBIOS domain name**, ensure **EXTRANET** is filled in and click **Next >**.
1. On page **Paths**, click **Next >**.

    Note: In real world, it is recommended to have the paths on a separate drive.

1. On page Review Options, click **Next >**.
1. On page Prerequisites Check, click **Install**.
1. On page Results, click **Close**.
1. Sign out.

#### PowerShell

Perform this task on CL1.

1. Run **Terminal**.
1. Store the Directory Services Restore Mode (DSRM) password in a variable.

    ````powershell
    $safeModeAdministratorPassword = Read-Host `
        -Prompt 'Directory Services Restore Mode (DSRM) password' `
        -AsSecureString
    ````

1. At the prompt **Directory Services Restore Mode (DSRM) password** enter a secure password and take a note.
1. Store the credentials for the Enterprise admin in variables.

    ````powershell
    $username = "Administrator@ad.adatum.com"
    $securePassword = Read-Host -Prompt "Password for $username" -AsSecureString
    ````

1. When prompted, enter the credentials for **Administrator@ad.adatum.com**.
1. Install a new tree **extranet.adatum.com** with the parent domain **ad.adatum.com** on VN1-SRV7. Install DNS at the same time, but do not make it a Global Catalog server.

    ````powershell
    # Convert the secure strings back to a plain text string

    $password = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto(
        [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR(
            $securePassword
        )
    ) 

    $safeModeAdministratorPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto(
        [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR(
            $safeModeAdministratorPasswordSecure
        )
    ) 

    $job = Invoke-Command -ComputerName PM-SRV1 -AsJob -ScriptBlock {
        # Convert the passwords into a secure strings
        
        $securePassword = `
            ConvertTo-SecureString -String $using:password -AsPlainText -Force
        $secureSafeModeAdministratorPassword = `
            ConvertTo-SecureString `
                -String $using:safeModeAdministratorPassword `
                -AsPlainText `
                -Force

        # Create credentials

        $credential = New-Object `
            -TypeName pscredential `
            -ArgumentList $using:username, $securePassword


        Install-ADDSDomain `
            -DomainType TreeDomain `
            -ParentDomainName ad.adatum.com `
            -NewDomainName extranet.adatum.com `
            -Credential $credential `
            -SafeModeAdministratorPassword $secureSafeModeAdministratorPassword `
            -InstallDns `
            -Force
    }
    ````

1. Wait for the job to complete.

    ````powershell
    $job | Wait-Job
    ````

    This will take a few minutes.

1. Read the output of the job.

    ````powershell
    $job | Receive-Job
    ````

    The value of the property **Status** should be **Success**.

    The error message ````[PM-SRV1] Closing the remote server shell instance failed with the following error message : Access is denied.```` can be ignored safely.

Wait until the sign in screen appears on PM-SRV1.

### Task 4: Configure forwarders

#### Desktop experience

Perform this task on CL1.

1. Open **DNS**.
1. In **Connect to DNS Server**, click **The following computer**, type **PM-SRV1.extranet.adatum.com**, and click **OK**.
1. In DNS Manager, click **PM-SRV1.extranet.adatum.com**.
1. In PM-SRV1.extranet.adatum.com, double-click **Forwarders**.
1. In PM-SRV1.extranet.adatum.com, on tab Forwarders, click **Edit...**
1. In Edit Forwarders, click **10.1.1.8** and click **Delete**.
1. In **\<Click here to add an IP Address or DNS Name\>**, enter **8.8.8.8**. Repeat this step with **8.8.4.4** and click **OK**.
1. In **PM-SRV1.extranet.adatum.com**, click **OK**.

#### PowerShell

Perform this task on CL1.

1. Sign in as **Administrator@extranet.adatum.com**.
1. Run **Terminal**.
1. In Terminal, configure the forwarder on PM-SRV1 to **8.8.8.8** and **8.8.4.4**.

    ````powershell
    Set-DnsServerForwarder `
        -IPAddress 8.8.8.8, 8.8.4.4 -ComputerName PM-SRV1.extranet.adatum.com
    ````

### Task 5: Configure DNS client settings

#### SConfig

Perform this task on PM-SRV1.

1. Sign in as **Administrator@extranet.adatum.com**.
1. In SConfig, enter **8**.
1. In Network settings, enter **1**.
1. In Network adapter settings, enter **2**.
1. Beside Enter new preferred DNS server, enter **10.1.200.8**.
1. Beside Enter alternate DNS server, enter **127.0.0.1**.
1. Press ENTER to continue.
1. In SConfig, enter **12**.
1. Beside Are you sure you want to log off, enter **y**.

#### PowerShell

Perform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. In Terminal, create a CIM session to **PM-SRV1**.

    ````powershell
    $cimSession = New-CimSession -ComputerName PM-SRV1.extranet.adatum.com
    ````

    If you receive the following error message, wait a few minutes and try again or use the SConfig method.

    ````text
    New-CimSession : WinRM cannot process the request. The following error occurred while using Kerberos authentication: Cannot find the computer PM-SRV1.extranet.adatum.com. Verify that the computer exists on the network and that the name provided is spelled correctly.
    ````

1. For all net adapters, set the DNS client server address for **PM-SRV1** to **10.1.200.8** and **127.0.0.1**.

    ````powershell
    Get-NetAdapter -CimSession $cimSession |
    Set-DnsClientServerAddress `
        -ServerAddresses 10.1.200.8, 127.0.0.1 -CimSession $cimSession
    ````

    Note: In real world, you should enter the IP address of a another DC of the same domain.

1. Close and remove the CIM session.

    ````powershell
    Remove-CimSession $cimSession
    ````

### Task 6: Verify name resolution of the new tree

Perform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. Resolve the DNS name **extranet.adatum.com** on server **10.1.1.8**.

    ````powershell
    Resolve-DnsName -Name extranet.adatum.com -Server 10.1.1.8
    ````

    > The IP addresses 10.1.200.8 and 10.1.200.9 should be returned.

    Note: If you do not get the IP address, restart the DNS service on 10.1.1.8 and try again.

    ````powershell
    $computerName = (Resolve-DnsName 10.1.1.8).NameHost
    Invoke-Command -ComputerName $computerName -ScriptBlock {
        Restart-Service -Name DNS
    }
    ````

1. Resolve the DNS name **extranet.adatum.com** on server **VN1-SRV7.clients.ad.adatum.com**.

    ````powershell
    Resolve-DnsName -Name extranet.adatum.com -Server VN1-SRV7.clients.ad.adatum.com
    ````

    > The IP addresses 10.1.200.8 and 10.1.200.9 should be returned.

1. Resolve the DNS name **ad.adatum.com** on server **PM-SRV1.extranet.adatum.com**.

    ````powershell
    Resolve-DnsName -Name ad.adatum.com -Server PM-SRV1.extranet.adatum.com
    ````

    > The IP addresses 10.1.1.8 should be returned. Depending on the number of domain controllers, additional IP addresses may be returned.

1. Resolve the DNS name **clients.ad.adatum.com** on server **PM-SRV1.extranet.adatum.com**.

    ````powershell
    Resolve-DnsName -Name clients.ad.adatum.com -Server PM-SRV1.extranet.adatum.com
    ````

    > The IP address 10.1.1.56 should be returned.

## Exercise 4: Create and validate shortcut trusts

1. [Simulate a failure of an intermediate domain](#task-1-simulate-a-failure-of-an-intermediate-domain)
1. [Validate the effect of a missing intermediate domain](#task-2-validate-the-effects-of-an-failure-of-an-intermediate-domain) on CL2

    > Can a user of the domain clients.ad.adatum.com sign in?

    > Can a user of domain clients.ad.adatum.com access the shares on VN1-SRV7?

    > Can a user of domain clients.ad.adatum.com access the shares on PM-SRV1?

    > Can a user of domain extranet.adatum.com sign in?

1. [Recover from the failure of the intermediate domain](#task-3-recover-from-the-failure-of-the-intermediate-domain)
1. [Create a shortcut trust](#task-4-create-a-shortcut-trust) between clients.ad.adatum.com and extranet.adatum.com
1. [Simulate a failure of an intermediate domain](#task-5-simulate-a-failure-of-an-intermediate-domain)
1. [Validate the effect of the shortcut trust](#task-6-validate-the-effects-of-the-shortcut-trust) on CL2

    > Can a user of domain clients.ad.adatum.com access the shares on PM-SRV1?

    > Can a user of domain extranet.adatum.com sign in?

1. [Recover from the failure of the intermediate domain](#task-7-recover-from-the-failure-of-the-intermediate-domain)

### Task 1: Simulate a failure of an intermediate domain

#### Desktop experience

Perform this task on the host.

1. Open **Hyper-V-Manager**.
1. In Hyper-V-Manager click **WIN-VN1-SRV1**, **WIN-VN1-SRV5**, hold down CTRL and click **WIN-VN2-SRV1**.

    If WIN-VN1-SRV1 is not running anymore, do not select it.

1. In the context menu of **WIN-VN1-SRV1**, **WIN-VN1-SRV5**, or **WIN-VN2-SRV1**, click **Suspend**.

#### PowerShell

Perform this task on the host.

1. In the context menu of **Start**, click **Windows PowerShell (Admin)**.
1. Suspend the virtual machines **WIN-VN1-SRV1**, **WIN-VN1-SRV5** and **WIN-VN2-SRV1** if they are running.

    ````powershell
    Get-VM -Name WIN-VN1-SRV1, WIN-VN1-SRV5, WIN-VN2-SRV1 |
    Where-Object { $PSItem.State -eq 'Running' } |
    Suspend-VM
    ````

### Task 2: Validate the effects of an failure of an intermediate domain

Perform this task on CL4.

1. Sign in as **Administrator@clients.ad.adatum.com**.
1. Using **File Explorer**, try to navigate to **\\\\VN1-SRV7**.

    > You should see the shares NETLOGON and SYSVOL.

1. Using **File Explorer**, try to navigate to **\\\\PM-SRV1.extranet.adatum.com**.

    > You will receive a prompt to enter network credentials with an error message that the system cannot contact a domain controller to service the authentication request.

1. In Enter network credentials, click **Cancel**.
1. Sign out.
1. Sign in as **Administrator@extranet.adatum.com**.

    > The sign in will fail with an error message that the domain is not available.

1. Click **OK**.

### Task 3: Recover from the failure of the intermediate domain

#### Desktop experience

Perform this task on the host.

1. Open **Hyper-V-Manager**.
1. In Hyper-V-Manager click **WIN-VN1-SRV1, **WIN-VN1-SRV5**, hold down CTRL and click **WIN-VN2-SRV1**.

    Select WIN-VN1-SRV1 only, if it is in the suspended state and not turned off.

1. In the context menu of **WIN-VN1-SRV5** or **WIN-VN2-SRV1**, click **Resume**.

#### PowerShell

Perform this task on the host.

1. In the context menu of **Start**, click **Windows PowerShell (Admin)**.
1. Resume the paused virtual machines **WIN-VN1-SRV1**, **WIN-VN1-SRV5**, and **WIN-VN2-SRV1**.

    ````powershell
    Get-VM -Name WIN-VN1-SRV1, WIN-VN1-SRV5, WIN-VN2-SRV1 |
    Where-Object { $PSItem.State -eq 'Paused' } |
    Resume-VM
    ````

### Task 4: Create a shortcut trust

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Domains and Trusts**.
1. In Active Directory Domains and Trusts, in the context-menu of **extranet.adatum.com**, click **Properties**.
1. In extranet.adatum.com Properties, click the tab **Trusts**.
1. On the tab Trusts, click **New Trust...**
1. In the New Trust Wizard, on page Welcome to the New Trust Wizard, click **Next >**.
1. On the page Trust Name, under **Name**, type **clients.ad.adatum.com** and click **Next >**.
1. On the page Direction of Trust, ensure **Two-way** is selected and click **Next >**.
1. On the page Sides of Trust, select **Both this domain and the specified domain** and click **Next >**.
1. On the page User Name and password, for **Specified domain: clients.ad.adatum.com** type the credentials of **Administrator@clients.ad.adatum.com** and click **Next >**.
1. On the page Trust Selections Complete, click **Next >**.
1. On the page Trust Creation Complete, click **Next >**.
1. On the page Confirm Outgoing Trust, click **Yes, confirm the outgoing trust** and click **Next >**.
1. On the page Confirm Incoming Trust, click **Yes, confirm the incoming trust** and click **Next >**.
1. On the page Completing the New Trust Wizard, click **Finish**.
1. In **extranet.adatum.com Properties**, click **OK**.

#### PowerShell

Perform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. Create DirectoryContext objects for the domains **clients.ad.adatum.com** and **extranet.adatum.com**.

    ````powershell
    $clientsContext = New-Object `
        -TypeName System.DirectoryServices.ActiveDirectory.DirectoryContext `
        -ArgumentList 'Domain', 'clients.ad.adatum.com'
    $extranetContext = New-Object `
        -TypeName System.DirectoryServices.ActiveDirectory.DirectoryContext `
        -ArgumentList 'Domain', 'extranet.adatum.com'
    ````

1. Get the domains **clients.ad.adatum.com** and **extranet.adatum.com**.

    ````powershell
    $clientsDomain = `
        [System.DirectoryServices.ActiveDirectory.Domain]::GetDomain(
            $clientsContext
        )
    $extranetDomain = `
        [System.DirectoryServices.ActiveDirectory.Domain]::GetDomain(
            $extranetContext
        )
    ````

1. Create a bidrectional trust relationship between the clients and the extranet domain.

    ````powershell
    $clientsDomain.CreateTrustRelationship($extranetDomain, 'Bidirectional')
    ````

### Task 5: Simulate a failure of an intermediate domain

#### Desktop experience

Perform this task on the host.

1. Open **Hyper-V-Manager**.
1. In Hyper-V-Manager click **WIN-VN1-SRV1**, **WIN-VN1-SRV5**, hold down CTRL and click **WIN-VN2-SRV1**.

    If WIN-VN1-SRV1 is not running anymore, do not select it.

1. In the context menu of **WIN-VN1-SRV1**, **WIN-VN1-SRV5**, or **WIN-VN2-SRV1**, click **Suspend**.

#### PowerShell

Perform this task on the host.

1. In the context menu of **Start**, click **Windows PowerShell (Admin)**.
1. Suspend the virtual machines **WIN-VN1-SRV1**, **WIN-VN1-SRV5** and **WIN-VN2-SRV1** if they are running.

    ````powershell
    Get-VM -Name WIN-VN1-SRV1, WIN-VN1-SRV5, WIN-VN2-SRV1 |
    Where-Object { $PSItem.State -eq 'Running' } |
    Suspend-VM
    ````

### Task 6: Validate the effects of the shortcut trust

Perform this task on CL4.

1. Sign in as **Administrator@clients.ad.adatum.com**.
1. Using **File Explorer**, try to navigate to **\\\\PM-SRV1.extranet.adatum.com**.

    > You should see the shares NETLOGON and SYSVOL.

1. Sign out.
1. Sign in as **Administrator@extranet.adatum.com**.

    > The sign in should succeed.

1. Sign out.

### Task 7: Recover from the failure of the intermediate domain

#### Desktop experience

Perform this task on the host.

1. Open **Hyper-V-Manager**.
1. In Hyper-V-Manager click **WIN-VN1-SRV1**, **WIN-VN1-SRV5**, hold down CTRL and click **WIN-VN2-SRV1**.

    If WIN-VN1-SRV1 is not running anymore, do not select it.

1. In the context menu of **WIN-VN1-SRV1**, **WIN-VN1-SRV5**, or **WIN-VN2-SRV1**, click **Suspend**.

#### PowerShell

Perform this task on the host.

1. In the context menu of **Start**, click **Windows PowerShell (Admin)**.
1. Suspend the virtual machines **WIN-VN1-SRV1**, **WIN-VN1-SRV5** and **WIN-VN2-SRV1** if they are running.

    ````powershell
    Get-VM -Name WIN-VN1-SRV1, WIN-VN1-SRV5, WIN-VN2-SRV1 |
    Where-Object { $PSItem.State -eq 'Running' } |
    Suspend-VM
    ````

## Exercise 5: Create and validate a forest trust

1. [Implement DNS name resolution of ad.contoso.com](#task-1-implement-dns-name-resolution-of-adcontosocom)
1. [Implement DNS name resolution of ad.adatum.com and extranet.adatum.com](#task-2-implement-dns-name-resolution-of-adadatumcom)
1. [Verify DNS name resolution between the forests](#task-3-verify-dns-name-resolution-between-the-forests)
1. [Create a forest trust](#task-4-create-a-forest-trust)
1. [Add a principal from an external forest to a domain-local group](#task-5-add-a-principal-from-an-external-forest-to-a-domain-local-group): Add Wil to Marketing Read.
1. [Verify the effect of selective authentication accessing resources](#task-6-verify-the-effect-of-selective-authentication-accessing-resources) by trying to access \\\\VN1-SRV10 with the user Wil@contoso.com.
1. [Verify the effect of selective authentication on sign in](#task-7-verify-the-effect-of-selective-authentication-on-sign-in) by traing to sign in to CL4 as Wil@contoso.com.
1. [Allow users from the external forest to access computers](#task-8-allow-users-from-the-external-forest-to-access-computers) VN1-SRV10 and CL4
1. [Verify sign in and resource access over a forest trust](#task-9-verify-sign-in-and-resource-access-over-a-forest-trust) with the user Wil@contoso.com signing into CL4 and accessing \\\\VN1-SRV10\\Marketing.

### Task 1: Implement DNS name resolution of ad.contoso.com

#### Desktop experience

Perform this task on CL1.

1. Open **DNS**.
1. In **Connect to DNS Server**, click **The following computer**, type **10.1.1.8**, and click **OK**.
1. In **DNS Manager**, click and expand **10.1.1.8**, and click **Conditional Forwarders**.
1. In the context-menu of **Conditional Forwarders**, click **New Conditional Forwarder...**
1. In New Conditional Forwarder, under **DNS Domain**, type **ad.contoso.com**. Under **IP addresses of the master servers**, click **\<Click here to add an IP Address or DNS Name\>**, and enter **10.1.2.16**. Activate the checkbox **Store this conditional forwarder in Active Directory** and, below, click **All DNS servers in this forest**. Click **OK**.

#### PowerShell

Perform this task on CL1.

1. In the context menu of **Start**, click **Terminal (Admin)**.
1. On **10.1.1.8**, add a conditional forwarder for zone **ad.contoso.com** pointing to **10.1.2.16**. The forwarder should be replicated forest-wide.

    ````powershell
    Add-DnsServerConditionalForwarderZone `
        -Name ad.contoso.com `
        -MasterServers 10.1.2.16 `
        -ReplicationScope Forest `
        -ComputerName 10.1.1.8
    ````

### Task 2: Implement DNS name resolution of ad.adatum.com

#### Desktop experience

Perform this task on VN2-SRV2.

1. Open **DNS**.
1. In **DNS Manager**, click and expand **VN2-SRV2**, and click **Conditional Forwarders**.
1. In the context-menu of **Conditional Forwarders**, click **New Conditional Forwarder...**
1. In New Conditional Forwarder, under **DNS Domain**, type **ad.adatum.com**. Under **IP addresses of the master servers**, click **\<Click here to add an IP Address or DNS Name\>**, and enter **10.1.1.8** and **10.1.2.8**. Activate the checkbox **Store this conditional forwarder in Active Directory** and, below, click **All DNS servers in this forest**. Click **OK**.
1. In the context-menu of **Conditional Forwarders**, click **New Conditional Forwarder...**
1. In New Conditional Forwarder, under **DNS Domain**, type **extranet.adatum.com**. Under **IP addresses of the master servers**, click **\<Click here to add an IP Address or DNS Name\>**, and enter **10.1.200.8**. Activate the checkbox **Store this conditional forwarder in Active Directory** and, below, click **All DNS servers in this forest**. Click **OK**.

#### PowerShell

Perform this task on VN2-SRV2.

1. In the context menu of **Start**, click **Windows PowerShell**.
1. Add a conditional forwarder for zone **ad.adatum.com** pointing to **10.1.1.40** and **10.1.2.8**. The forwarder should be replicated forest-wide.

    ````powershell
    Add-DnsServerConditionalForwarderZone `
        -Name ad.adatum.com `
        -MasterServers 10.1.1.8, 10.1.2.8 `
        -ReplicationScope Forest
    ````

1. Add a conditional forwarder for zone **extranet.adatum.com** pointing to **10.1.200.8**. The forwarder should be replicated forest-wide.

    ````powershell
    Add-DnsServerConditionalForwarderZone `
        -Name extranet.adatum.com `
        -MasterServers 10.1.200.8 `
        -ReplicationScope Forest
    ````

### Task 3: Verify DNS name resolution between the forests

Perform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. Verify the DNS name resolution for **ad.contoso.com** on the server **10.1.1.8**.

    ````powershell
    Resolve-DnsName -Name ad.contoso.com -Server 10.1.1.40
    ````

    > You should get the IP address 10.1.2.16.

1. Verify the DNS name resolution for **ad.adatum.com** on the server **10.1.2.16**.

    ````powershell
    Resolve-DnsName -Name ad.adatum.com -Server 10.1.2.16
    ````

    > You should get the IP address 10.1.1.8. Additional IP addresses may be returned.

1. Verify the DNS name resolution for **clients.ad.adatum.com** on the server **10.1.2.16**.

    ````powershell
    Resolve-DnsName -Name clients.ad.adatum.com -Server 10.1.2.16
    ````

    > You should get the IP address 10.1.1.56.

1. Verify the DNS name resolution for **extranet.adatum.com** on the server **10.1.2.16**.

    ````powershell
    Resolve-DnsName -Name extranet.adatum.com -Server 10.1.2.16
    ````

    > You should get the IP address 10.1.200.8 and 10.1.200.9.

### Task 4: Create a forest trust

Perform this task on CL1.

1. Open **Active Directory Domains and Trusts**.
1. In the context-menu of **ad.adatum.com**, click **Properties**.
1. In ad.adatum.com Properties, click the tab **Trusts**.
1. On tab Trusts, click **New Trust...**.
1. In New Trust Wizard, on page Welcome to the New Trust Wizard, click **Next >**.
1. On page Trust Name, in **Name**, type **ad.contoso.com** and click **Next >**.
1. On page Trust Type, click **Forest trust** and click **Next >**.
1. On page Direction of Trust, ensure Two-way is selected and click **Next >**.
1. On page Side of Trust, click **Both this domain and the specified domain** and click **Next >**.
1. On page User name and Password, type the credentials for **Administrator@ad.contoso.com**.
1. On page **Outgoing Trust Authentication Level-Local Forest**, click **Selective authentication** and click **Next >**.
1. On page **Outgoing Trust Authentication Level-Specified Forest**, click **Forest-wide authentication** and click **Next >**.
1. On page Trust Selections Complete, click **Next >**.
1. On page Trust Creation Complete, click **Next >**.
1. On page Confirm Outgoing Trust, click **Yes, confirm the outgoing trust** and click **Next >**.
1. On page Confirm Incoming Trust, click **Yes, confirm the incoming trust** and click **Next >**.
1. On page Completing the New Trust Wizard, click **Finish**.
1. In **ad.adatum.com Properties**, click **OK**.

### Task 5: Add a principal from an external forest to a domain-local group

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **ad (local)**.
1. In ad (local), double-click **Entitling groups**.
1. In Entitling groups, double-click **Marketing Read**.
1. In Marketing Read, in the left pane, click **Members**.
1. Under Members, click **Add...**.
1. In Select Users, Contacts, or Other Objects, click **Locations...**.
1. In Locations, click **ad.contoso.com** and click **OK**.
1. In **Select Users, Contacts, or Other Objects**, in **Enter the object names to select**, type Administrator and click **Check Names**.
1. In **Select Users, Contacts, or Other Objects**, click **OK**.
1. In **Marketing Read**, click **OK**.

### Task 6: Verify the effect of selective authentication accessing resources

Perform this task on CL3.

1. Sign in as **Administrator@ad.contoso.com**.
1. Using **File Explorer**, navigate to \\\\VN1-SRV10.ad.adatum.com.

    > You will receive an error message like in [figure 1].

### Task 7: Verify the effect of selective authentication on sign in

Perform this task on CL4.

1. Sign in as **Administrator@ad.contoso.com**.

    > You will receive an error message like in [figure 2].

### Task 8: Allow users from the external forest to access computers

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **Global Search**.
1. In Global Search, in **Search**, type **VN1-SRV10** and click **Search**.
1. Double-click **VN1-SRV10**.
1. In VN1-SRV10, click **Extensions**.
1. In Extensions, on tab **Security**, click **Add...**.
1. In **Select Users, Contacts, or Other Objects**, in **Enter the object names to select**, type **Marketing Read** and click **OK**.
1. In **VN1-SRV10**, under **Permissions for Marketing Read**, in column **Allow**, activate the checkbox **Allowed to authenticate** and click **OK**.
1. In **Active Directory Administrative Center**, on the menu, click **Manage**, **Add Navigation Nodes...**
1. In Add Navigation Nodes, in the middle pane, click **clients**, click **>>**, and click **OK**.
1. In **Active Directory Administrative Center**, click **clients**.
1. In clients, double-click **Computers**.
1. In Computers, double-click **CL4**.
1. In CL4, click **Extensions**.
1. In Extensions, on tab **Security**, click **Add...**.
1. In Select Users, Computers, Service Accounts, or Groups, click **Locations...**.
1. In Locations, click **ad.contoso.com** and click **OK**.
1. In **Select Users, Contacts, or Other Objects**, in **Enter the object names to select**, type Administrator and click **Check Names**.
1. In **Select Users, Contacts, or Other Objects**, click **OK**.
1. In **CL4**, under **Permissions for Administrator**, in column **Allow**, activate the checkbox **Allowed to authenticate** and click **OK**.

### Task 9: Verify sign in and resource access over a forest trust

Perform this task on CL4.

1. Sign in as **Administrator@ad.contoso.com**.

    > You should be able sign in.

1. Using File Explorer, navigate to **\\\\VN1-SRV10.ad.adatum.com\\Marketing**.

    > You should be able to access the share.

1. Sign out.

## Exercise 6: Migrating users between domains

1. [Install the Active Directory Migration Tool](#task-1-install-the-active-directory-migration-tool)
1. [Create the target organizational group](#task-2-create-the-target-organizational-group) Marketing in domain clients.ad.adatum.com
1. [Migrate users within a forest](#task-3-migrate-users-within-a-forest): Migrate all users in the organizational unit Marketing to the clients domain
1. [Verify the results of users migration within a forest](#task-4-verify-the-migration-of-users-within-a-forest)
1. [Verify resource access after user migration within a forest](#task-5-verify-resource-access-after-user-migration-within-a-forest)
1. [Set permissions in the target forest](#task-6-grant-permissions-in-the-target-forest) ad.contoso.com
1. [Create the target organizational group](#task-7-create-the-target-organizational-group) Development in domain ad.contoso.com
1. [Migrate a user between forests](#task-8-migrate-users-between-forests): Migrate all users in the organizational unit Development to the contoso domain
1. [Verify the migration of users between forests](#task-9-verify-the-migration-of-users-between-forests)
1. [Verify sign in after user migration between forests](#task-10-verify-sign-in-after-user-migration-between-forests) on CL3

### Task 1: Install the Active Directory Migration Tool

Perform this task on CL1.

1. With Microsoft Edge, navigate to <https://www.microsoft.com/en-us/download/details.aspx?id=56570>.
1. On page Active Directory Migration Tool version 3.2 (has known problems and limited support), click **Download**.
1. Open the downloaded file.
1. In Active Directory Migratin Tool Installation Wizard, on page Welcome to the Active Directory Migration Tool Installation, click **Next >**.
1. On page License Agreement, click **I agree** and click **Next >**.
1. On page Customer Experience Improvement Program, click **I don't want to join the program at this time** and click **Next >**.
1. On page Database Selection, in **Database (Server\Instance)**, type **VN1-SRV3** and click **Next >**.
1. On the next page, click **Finish**.

### Task 2: Create the target organizational group

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **clients**.

    If you do not have a **clients** node in Active Directory Administrative Center, add the navigation node:

    1. In **Active Directory Administrative Center**, on the menu, click **Manage**, **Add Navigation Nodes...**
    1. In Add Navigation Nodes, in the middle pane, click **clients**, click **>>**, and click **OK**.

1. In the context-menu of **clients (local)**, click **New**, **Organizational Unit**.
1. In Create Organizational Unit, in **Name**, type **Marketing** and click **OK**.

### Task 3: Migrate users within a forest

Perform this task on CL1.

1. Open the **Active Directory Migration Tool**.
1. In migrator - [Active Directory Migration Tool], in the context-menu of **Active Directory Migration Tool**, click **User Account Migration Wizard**.
1. In User Account Migration Wizard, on page Welcome to the User Account Migration Wizard, click **Next >**.
1. On page Domain Selection, under **Source**, in **Domain**, type **ad.adatum.com**. Under **Target**, in **Domain**, type **clients.ad.adatum.com**. Click **Next >**.
1. On page User Selection Option, ensure **Select users from domain** is selected and click **Next >**.
1. On page User Selection, click **Add...**
1. In Select users, click **Locations...**
1. In Locations, expand **ad.adatum.com**, click **Marketing** and click **OK**.
1. In **Select Users**, cick **Advanced...**
1. In Select Users (Advanced), click **Find Now**.
1. Below **Search results** select all users: Click **Ada Russell**, hold down SHIFT, scroll down and click **Zan Kustrin**. Click **OK**.
1. In **Select Users**, click **OK**.
1. On page **User Selection**, click **Next >**.
1. On page Organizational Unit Selection, click **Browse...**
1. In Browse for Container, click **Marketing** and click **OK**.
1. On page **Organizational Unit Selection**, click **Next >**.
1. On page User Options, activate the checkbox **Migrate associated user groups** and **Update previously migrated objects**, and click **Next >**.
1. On page Conflict Management, ensure **Do no migrate source object if a conflict is detected in the target domain** is selected and  click **Next >**.
1. On page Completing the User Account Migration Wizard, click **Finish**.
1. In Migration Process, wait until **Status** changes to **Completed** and click **Close**.

### Task 4: Verify the migration of users within a forest

Perform this task on CL1.

1. Open the **Active Directory Migration Tool**.
1. In migrator - [Active Directory Migration Tool], in the context-menu of **Active Directory Migration Tool**, click **Reporting Wizard**.
1. In Reporting Wizard, on page **Welcome to the Reporting Wizard**, click **Next >**.
1. On page Domain Selection, under **Source**, in **Domain**, ensure **ad.adatum.com** is filled in. Under **Target**, in **Domain**, ensure **clients.ad.adatum.com** is filled in. Click **Next >**.
1. On page Folder Selection, click **Next >**.
1. On page Report Selection, click **Migrated accounts**, and click **Next >**.
1. On page Completing the Reporting Wizard, click **Finish**.
1. In **migrator - [Active Directory Migration Tool]**, expand **Reports** and click **Migrated User, Group and Managed Service Accounts**. Review the report.
1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **ad (local)**.
1. In ad (local), double-click **Marketing**.

    > The organizational unit Marketing should not contain objects anymore.

1. Click **ad (local)** and double-click **Entitling Groups**.
1. In Entitling Groups, double-click **Marketing Modify**.
1. In Marketing Modify, click **Members** and review the members.

    > The migrated group Marketing should be a member. The Active Directory Domain Services Folder reads clients-Marketing-Marketing.

1. Click **Cancel**.
1. In **Active Directory Administrative Center**, click **clients**.
1. In clients, double-click **Marketing**.

    > You should see the migrated users as well as the migrated group **Marketing**.

1. Double-click the **Group** **Marketing**.
1. In Marketing Properties, click the tab **Members**.

    > All migrated users should be member of Marketing.

1. Click **Cancel**.
1. In **Active Directory Administrative Center**, in the organizational unit Marketing, double-click **Ada Russell**.
1. In **Ada Russel**, click **Extensions**.
1. In Extensions, click the tab **Attribute Editor**.
1. On tab Attribute Editor, click **Filter**, **Show only attributes that have values**.
1. Find the attribute **sIDHistory** and notice the value. Find the attribute **objectSid** and notice the value. Compare the number after **S-1-5-21-**.

    > The number after S-1-5-21- is the domain SID. It is different in the attrributes objectSid and sIDHistory, which indicates, that the value in in sIDHistory is the SID from a different domain.

1. Click **Cancel**.

### Task 5: Verify resource access after user migration within a forest

Perform this task on CL2.

1. Sign in as **ada@adatum.com**.

    > You will have to change the password.

1. Using **File Explorer**, navigate to \\\\VN1-SRV10\\Marketing.

    > Ada should still have access.

1. Sign out.

### Task 6: Grant permissions in the target forest

Perform this task on VN2-SRV2.

1. Open **Active Directory Users and Computers**.

    Note: Because of a bug in Active Directory Administrative center, you must use Active Directory Users and Computers in this task.

1. In Active Directory Users and Computers, expand **ad.contoso.com** and click **Builtin**.
1. In Builtin, double-click **Administrators**.
1. In Administrators Properties, click the tab **Members**.
1. On tab Members, click **Add...**.
1. In Select Users, Contacts, Computers, Service Accounts, or Groups, click **Locations...**
1. In Locations, click **ad.adatum.com** and click **OK**.
1. In **Select Users, Contacts, Computers, Service Accounts, or Groups**, in Enter the object names to select, type **Administrator** and click **Check Names**.
1. In Windows Security, enter the credentials of **Administrator@ad.adatum.com**.
1. In Multiple Names Found, in the column **In Folder**, click **ad.adatum.com/Users** and click **OK**.
1. In **Select Users, Contacts, Computers, Service Accounts, or Groups**, click **OK**.
1. In **Administrators Properties**, click **OK**.

### Task 7: Create the target organizational group

Perform this task on VN2-SRV2.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, in  the context-menu of **ad (local)**, click **New**, **Organizational Unit**.
1. In New Object - Organizational Unit, in **Name**, type **Development** and click **OK**.

### Task 8: Migrate users between forests

Perform this task on CL1.

1. Sign in as **Administrator@ad.adatum.com**.
1. Open the **Active Directory Migration Tool**.
1. In migrator - [Active Directory Migration Tool], in the context-menu of **Active Directory Migration Tool**, click **User Account Migration Wizard**.
1. In User Account Migration Wizard, on page Welcome to the User Account Migration Wizard, click **Next >**.
1. On page Domain Selection, under **Source**, in **Domain**, ensure **ad.adatum.com** is filled in. Under **Target**, in **Domain**, type **ad.contoso.com**. Click **Next >**.
1. On page User Selection Option, ensure **Select users from domain** is selected and click **Next >**.
1. On page User Selection, click **Add...**
1. In Select users, click **Locations...**
1. In Locations, expand **ad.adatum.com**, click **Development** and click **OK**.
1. In **Select Users**, cick **Advanced...**
1. In Select Users (Advanced), click **Find Now**.
1. Below **Search results** select all users: Click **Anete Auzina**, hold down SHIFT, scroll down and click **Zoja Bobanec**. Click **OK**.
1. In **Select Users**, click **OK**.
1. On page **User Selection**, click **Next >**.
1. On page Organizational Unit Selection, click **Browse...**
1. In Browse for Container, click **Development** and click **OK**.
1. On page **Organizational Unit Selection**, click **Next >**.
1. On page Password Options, click **Generate complex passwords**, and activate **Do not update passwords for existing users**. Take a note of the path displayed under **Location to store password file**. Optionally, you can change the location. Click **Next >**.
1. On page Account Transition Options, ensure **Target same as source** is selected. Activate **Disable source accounts** and **Migrate user SIDs to target domain**. Click **Next >**.
1. In the message box **Auditing is currently not enabled on the source domain.  Would you like to enable auditing?**, click **Yes**.
1. In the message box **Auditing is currently not enabled on the target domain.  Would you like to enable auditing?**, click **Yes**.
1. In the message box **The local group AD$$$ does not exist on ad.adatum.com.  This group is required to migrate SIDs.  Would you like to create it?**, click **Yes**.
1. In **User Account Migration Wizard**, on page **User Account**, type the credentials of **Administrator** in the Domain **AD**.
1. On page User Options, ensure the checkboxes **Migrate associated user groups**, **Update previously migrated objects** and **Fix users' group memberships** are activated, and click **Next >**.
1. On page Object Property Exclusion, ensure the checkbox **Exclude specific object properties from migration** is deactivated and click **Next >**.
1. On page Conflict Management, ensure **Do not migrate source object if a conflict is detected in the target domain** is selected and click **Next >**.
1. On page Completing the User Account Migration Wizard, click **Finish**.
1. In Migration Process, wait until **Status** changes to **Completed** and click **Close**.

### Task 9: Verify the migration of users between forests

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **ad (local)**.
1. In ad (local), double-click **Development**.

    > All users in the organizational unit should be disabled.

1. On the menu, click **Manage**, **Add Navigation Nodes...**
1. In Add Navigation Nodes, click **Connect to other domains...** (in the bottom right). Beside **Connect to**, type **ad.contoso.com** and click **OK**.
1. In **Active Directory Users and Computers**, in the context-menu of **ad.adatum.com**, click **Change Domain...**
1. In Change domain, type **ad.contoso.com** and click **OK**.
1. In the left pane, ensure **ad** is selected and click **>>** and click **OK**.
1. In **Active Directory Administrative Center**, in the context-menu of **ad**, click **Rename...**
1. In Rename, under **Please input the new name**, type **ad.contoso.com** and click **OK**.
1. In **Active Directory Administrative Center**, click **ad.contoso.com**.
1. In ad.contoso.com, double-click **Development**.

    > You should see the migrated users as well as the migrated group **Development**. The users should be enabled.

1. Double-click the **Group** **Development**.
1. In Development, click **Members**.

    > All migrated users should be member of Development.

1. Click **Cancel**.
1. In **Active Directory Administrative Center**, in the organizational unit **Development**, double-click **Anete Auzina**.

    If you receive an error message Failed to retrieve the object, close Active Directory Administrative Center and open it again.

    > The User UPN logon shuld be Anete@ad.contoso.com.

1. Click **Extensions**.
1. Click the tab **Attribute Editor**.
1. Find the attribute **sIDHistory** and notice the value. Find the attribute **objectSid** and notice the value. Compare the number after **S-1-5-21-**.

    > The number after S-1-5-21- is the domain SID. It is different in the attrributes objectSid and sIDHistory, which indicates, that the value in in sIDHistory is the SID from a different domain.

1. Click **Cancel**.
1. Open the password file you took note of (probably **C:\\Windows\\ADMT\\Logs\\passwords.txt**). Take a note of the password of **Anete**.

### Task 10: Verify sign in after user migration between forests

Perform this task on CL3.

1. Sign in as **anete@ad.contoso.com**.

    You will have to change the password.

    > The sign in should succeed.

1. Sign out.

[figure 1]: /images/Authentication-Firewall-Error.png
[figure 2]: /images/Authentication-Firewall-Error-signin.png
