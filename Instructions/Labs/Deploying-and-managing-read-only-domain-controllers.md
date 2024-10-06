# Lab: Deploying and managing read-only domain controllers

## Required VMs

* VN1-SRV5
* VN2-SRV1
* VN2-SRV2
* VN3-SRV1
* CL1
* CL4

If you did not complete the lab [Deploying domain controllers](Deploying-domain-controllers.md), in addition to the VMs above, **VN1-SRV1** is required. If VN1-SRV1 is already shut down after the lab, do not start it.

## Setup

1. On VN3-SRV1, sign in as **ad\Administrator**.
1. On CL1, sign in as **ad\Administrator**.
1. On CL4, sign in as **.\Administrator**.

If you skipped the practice [Install Remote Server Administration Tools](Practices/Install-Remote-Server-Administration-Tools.md), on **CL1**, in **Terminal**, execute ````C:\LabResources\Solutions\Install-RemoteServerAdministrationTools.ps1````.

## Introduction

Adatum wants to install a domain controller for the ad.adatum.com domain at site VNet3, but the site is considered as insecure. For this reason, you will install a read-only domain controller at this site. The passwords for users and computers usually located at that site should replicate to the new domain controller.

Some time later, Adatum discovers that the domain controller at VNet3 might me compromised. Therefore, you must delete the domain controller and reset the passwords of all accounts replicated to that domain controller.

## Exercises

1. [Deploy a read-only domain controller](#exercise-1-deploy-a-read-only-domain-controller)
1. [Manage password replication](#exercise-2-manage-password-replication)
1. [Simulate a compromise of the read-only domain controller](#exercise-3-simulate-a-compromise-of-the-read-only-domain-controller)

## Exercise 1: Deploy a read-only domain controller

1. [Disconnect server from domain](#task-1-disconnect-server-from-domain): VN3-SRV1
1. [Delete computer account](#task-2-delete-computer-account) of VN3-SRV1
1. [Create a group for Administrators](#task-3-create-a-group-for-administrators) of read-only domain controllers in VNet3 in the domain ad.adatum.com and add Beth Burke and Logal Boyle to the group
1. [Pre-create the read-only domain controller computer account](#task-4-pre-create-the-read-only-domain-controller-computer-account) VN3-SRV1 in domain ad.adatum.com
1. [Install read-only domain controller](#task-5-install-read-only-domain-controller)
1. [Change DNS client settings](#task-6-change-dns-client-settings) on VN3-SRV1

### Task 1: Disconnect server from domain

#### Desktop experience

Perform this task on VN3-SRV1.

1. Open **Server Manager**.
1. In Server Manager, in the left pane, click **Local Server**.
1. In PROPERTIES For VN3-SRV1, beside **Domain**, click **ad.adatum.com**.
1. In System Properties, on tab Computer Name, click **Change...**
1. In Computer Name/Domain Changes, click **Workgroup** and below, type **WORKGROUP**. Click **OK**.
1. In message box Computer Name/Domain Changes, click **OK**.
1. In message box Welcome to the WORKGRUP workgroup, click **OK**.
1. In message box You must restart your computer to apply these changes, click **OK**.
1. In **System Properties**, click **Close**.
1. In message box You must restart your computer tot apply these changes, click **Restart Now**.

#### PowerShell

Perform this task on VN3-SRV1.

1. Open **Windows PowerShell**.
1. Remove the computer from the domain.

    ````powershell
    Remove-Computer -Restart
    ````

1. At the prompt After you leave the domain, you will need to know the password of the local Administrator account to log onto this computer. Do you wish to continue, enter **y**.

### Task 2: Delete computer account

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **Global Search**.
1. Under Global Search, in Search, type **VN3-SRV1** and click **Search**.
1. In the context-menu of **VN3-SRV1**, click **Delete**.
1. In the message box Delete Confirmation, click **Yes**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Delete the computer account **VN3-SRV1**.

    ````powershell
    Get-ADComputer -Identity VN3-SRV1 |
    Remove-ADObject -Recursive
    ````

1. At the prompt Performing recursive remove on Target: 'CN=VN3-SRV1,CN=Computers,DC=ad,DC=adatum,DC=com', enter **y**.

### Task 3: Create a group for administrators

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, in the left pane, click **ad**.
1. In the context-menu of **ad**, click **New**, **Organizational Unit**.
1. In Create Organizational Unit, in **Name**, type **Entitling groups** and click **OK**.
1. In **Active Directory Administrative Center**, in **ad**, double-click **Entitling groups**.
1. Under Entitling groups, in the context-menu blank space click **New**, **Group**.
1. In Create Group, in **Group name**, type **VNet3 RODC Administrators**. Under **Group type**, ensure **Security** is selected. Under **Group scope**, click **Domain local**. Click **Members**.
1. Under Members, click **Add...**.
1. In Select Users, Groups, Computers, Service Accounts, or Groups, under **Enter the object names to select**, type **Beth Burke; Logan Boyle** and click **OK**.
1. In **Create Group: VNet3 RODC Administrators**, click **OK**.

#### PowerShell

1. Open **Terminal**.
1. Create an organizational unit **Entitling groups** in the domain **ad.adatum.com**.

    ````powershell
    $server = 'ad.adatum.com'
    $aDOrganizationalUnit = New-ADOrganizationalUnit `
        -Name 'Entitling groups' `
        -Path 'dc=ad,dc=adatum,dc=com' `
        -Server $server `
        -Passthru
    ````

1. In the the new OU, create a domain-local security group named **VNet3 RODC Administrators**.

    ````powershell
    $aDGroup = New-ADGroup `
        -Path $aDOrganizationalUnit.DistinguishedName `
        -Name 'VNet3 RODC Administrators' `
        -GroupCategory Security `
        -GroupScope DomainLocal `
        -Server $server `
        -Passthru
    ````

1. Add **Beth Burke** and **Logan Boyle** from domain **ad.adatum.com** to the new group.

    ````powershell
    $aDUser = Get-ADUser `
        -Filter 'Name -eq "Beth Burke" -or Name -eq "Logan Boyle"'
    $aDGroup | Add-ADGroupMember -Members $aDUser -Server $server
    ````

### Task 4: Pre-create the read-only domain controller computer account

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, in the left pane, click **ad**.
1. In ad, double-click **Domain Controllers**.
1. In Domain Controllers, in the contextmenu of the blank space, click **Pre-create a Read-only domain controller account...**
1. In Active Directory Domain Services Installation Wizard, on page Welcome to the Active Directory Domain Services Wizard, activate **Use advanced mode installation** and click **Next >**.
1. On page network Credentials, verify **My current logged on credentials (AD\Administrator)** is selected and click **Next >**.
1. On page Specify the Computer name, in **Computer Name**, type **VN3-SRV1** and click **Next >**.
1. On page Select a Site, click **VNet3**, and click **Next >**.
1. On page Additional Domain Controller Options, verify **DNS server** and **Global catalog** are activated, and click **Next >**.
1. On page Specify the Password Replication Policy, click **Next >**.
1. On page Delegation of RODC Installation and Administration, click **Set...**.
1. In Select User or Group, under **Enter the object names to select**, type **Vnet3 RODC Administrators** and click **OK**.
1. In **Active Directory Domain Services Installation Wizard**, on page **Delegation of RODS Installation and Administration**, click **Next >**.
1. On page Summary, click **Next >**.
1. On page Completing the Active Directory Domain Services Installation Wizard, click **Finish**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Create a remote PowerShell session to **vn1-srv5.ad.adatum.com**.

    ````powershell
    Enter-PSSession vn1-srv5.ad.adatum.com
    ````

1. Pre-create a read-only domain controller account for **VN3-SRV1** in the domain **ad.adatum.com**, in site **VNet3**, and add the group **VNet3 RODC Administrators as delegated Administrators**.

````powershell
Add-ADDSReadOnlyDomainControllerAccount `
    -DomainControllerAccountName VN3-SRV1 `
    -DomainName ad.adatum.com `
    -SiteName VNet3 `
    -DelegatedAdministratorAccountName 'VNet3 RODC Administrators'
````

1. Exit from the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

### Task 5: Install read-only domain controller

#### Desktop experience

Perform this task on VN3-SRV1.

1. Sign in as **.\Administrator**.
1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before You Begin, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-basedd installation** is selected and click **Next >**.
1. On page Server Selection, click **Next >**.
1. On page Server Roles, activate **Active Directory Domain Services**.
1. In the dialog **Add features that are required for Active Directory Domain Services?**, click **Add Features**
1. On page **Server Roles**, click **Next >**.
1. On page Features, click **Next >**.
1. On page **AD DS**, click **Next >**.
1. On page **Confirmation**, click **Install**.
1. On page **Results**, click **Close**.

    Wait until the installation succeeds.

1. In **Server Manager**, click *Refresh*.
1. *Notifications* (the flag with the yellow warning triangle), and under the message **Configuration required for Active Directory Domain Services at VN3-SRV1**, click **Promote this server to a domain controller**.
1. In Active Directory Domain Services Configuration Wizard, on page Deployment Configuration, ensure **Add a domain controller to an existing domain** is selected. In **Domain**, type **ad.adatum.com**. Beside **\<No credentials provided\>**, click **Change...**.
1. In the dialog Credentials for deployment operation, enter the credentials for **ad\Beth** and click **OK**.
1. On page Domain Controller Options, under **Type the Directory Services Restore Mode (DSRM) password**, in **Password** and **Confirm password**, type a secure password and take a note. You will need the password for a later lab. Click **Next >**.
1. On page Additional Options, click **Next >**.
1. On page Paths, click **Next >**.

    Note: In real world, it is recommended to have the paths on a separate drive.

1. On page Review Options, click **Next >**.
1. On page Prerequisites Check, click **Install**.
1. On page Results, click **Close**.

#### PowerShell

Perform this task on VN3-SRV1.

1. Sign in as **.\Administrator**
1. Open **Windows PowerShell**.
1. Store the credentials of **ad\Beth** in a variable.

    ````powershell
    $credential = Get-Credential `
        -UserName ad\Beth -Message 'Credentials for DC installation'
    ````

1. In Windows PowerShell credential request, enter the password of Beth.
1. Install the windows feature **Active Directory Domain Services** including management tools.

    ````powershell
    Install-WindowsFeature `
        -Name AD-Domain-Services -IncludeManagementTools -Restart
    ````

1. Install the domain controller using an existing account.

    ````powershell
    Install-ADDSDomainController `
        -DomainName ad.adatum.com `
        -UseExistingAccount `
        -Credential $credential
    ````

1. At the prompts SafeModeAdministratorPassword and Confirm SafeModeAdministratorPassword, type a secure password and take a note.
1. At the prompt The target server will be configured as a domain controller and restarted when this operation is complete, enter **Y**.

### Task 6: Change DNS client settings

#### Desktop experience

Perform this task on VN3-SRV1.

1. Sign in as **ad\Beth**

    > Beth is allowed to sign in, because of membership in the local Administrators group of VN3-SRV1.

1. Open **Server Manager**.
1. In Server Manager, click **Local Server**.
1. Under Local Server, beside **Ethernet**, click **10.1.3.8, IPv6 enabled**.
1. In Network Connections, in the context-menu of **Ethernet**, click **Properties**.
1. In Ethernet Properties, click **Internet Protocol Version 4 (TCP/IPv4)** and click **Properties**.
1. In Internet Protocol Version 4 (TCP/IPv4) Properties, in **Preferred DNS server**, type **10.1.1.40**, in **Alternate DNS server**, type **127.0.0.1**, and click **OK**.
1. In **Ethernet Properties**, click **Close**.
1. Sign out.

#### PowerShell

Perform this task on VN3-SRV1.

1. Sign in as **ad\Beth**

    > Beth is allowed to sign in, because of membership in the local Administrators group of VN3-SRV1.

1. Open **Windows PowerShell**.
1. Set the DNS client server addresses of the network interface **Ethernet** to **10.1.1.40** and **127.0.0.1**.

    ````powershell
    Set-DnsClientServerAddress `
        -InterfaceAlias Ethernet -ServerAddresses 10.1.1.40, 127.0.0.1
    ````

## Exercise 2: Manage password replication

1. [Connect client to site with read-only domain controller](#task-1-connect-client-to-site-with-read-only-domain-controller): CL4
1. [Configure IP address of client for new site](#task-2-configure-ip-address-of-client-for-new-site) on CL4
1. [Verify sign in](#task-3-verify-sign-in) from client CL4
1. [Configure password replication](#task-4-configure-password-replication)
1. [Verify sign in](#task-5-verify-sign-in) from client CL4

### Task 1: Connect client to site with read-only domain controller

#### Desktop experience

Perform this task on the host.

1. Open **Hyper-V-Manager**.
1. In Hyper-V-Manager, in the left pane, under **Hyper-V-Manager**, click the name of your computer.
1. In the middle-pane, in the context-menu of **WIN-CL2**, click **Settings...**
1. In Settings for "Win-CL2", click **Network Adapter**.
1. Under Network Adapter, under **Virtual Switch**, click **VNet3** and click **OK**.

#### PowerShell

Perform this task on the host.

1. Open **Terminal** or **Windows PowerShell** as Administrator.
1. Connect the network adapter of VM **WIN-CL2** to the virtual switch **VNet3**.

    ````powershell
    Get-VMNetworkAdapter -VMName WIN-CL2 | 
    Connect-VMNetworkAdapter -SwitchName VNet3
    ````

### Task 2: Configure IP address of client for new site

#### Desktop experience

Perform this task on CL2.

1. Open **Settings**.
1. In Settings, click **Network & internet**.
1. In Network & internet, click **Ethernet**.
1. In Ethernet, beside **IPv4 address**, click **Edit**.
1. In Edit IP settings, under **IP address**, type **10.1.3.130**. Under **Gateway**, type **10.1.3.1**. Under **Preferred DNS**, type **10.1.3.8**. Under **Alternate DNS**, type **10.1.1.40**. Click **Save**.
1. Click **Start**, **Power**, **Restart**.

#### PowerShell

Perform this task on CL2.

1. Open **Terminal**.
1. Remove all IPv4 addresses from the interface Ethernet.

    ````powershell
    $interfaceAlias = 'Ethernet'
    Get-NetIPAddress -InterfaceAlias $interfaceAlias -AddressFamily IPv4 |
    Remove-NetIPAddress -Confirm:$false
    ````

1. Add the IP address **10.1.3.130** with the default gateway **10.1.3.1** and a prefix lenght of **24** to the network interface **Ethernet**.

    ````powershell
    New-NetIPAddress `
        -InterfaceAlias $interfaceAlias `
        -IPAddress 10.1.3.130 `
        -DefaultGateway 10.1.3.1 `
        -PrefixLength 24
    ````

1. Set the DNS client server addresses of the network interface **Ethernet** to **10.1.3.8** and **10.1.1.40**.

    ````powershell
    Set-DnsClientServerAddress `
        -InterfaceAlias Ethernet -ServerAddresses 10.1.3.8, 10.1.1.40
    ````

### Task 3: Verify sign in

Perform this task on CL2.

1. Sign in as **ad\ida**.
1. Open **Terminal**.
1. Verify the site for the client computer.

    ````powershell
    nltest.exe /DSGETSITE
    ````

    > This should return VNet3.

1. Verify the logon server.

    ````powershell
    $env:LOGONSERVER
    ````

    > This will return \\\\VN1-SRV5, because the password of Ida is not not present on VN1-SRV3.

1. Sign out.

### Task 4: Configure password replication

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **ad**.
1. Under ad, double-click **Entitling groups**.
1. Under Entitling groups, in the context-menu blank space click **New**, **Group**.
1. In Create Group, in **Group name**, type **VNet3 RODC password replication allowed**. Under **Group type**, ensure **Security** is selected. Under **Group scope**, click **Domain local**. Click **Members**.
1. Under Members, click **Add...**.
1. In Select Users, Groups, Computers, Service Accounts, or Groups, click **Object Types...**
1. In Object Types, activate **Computers** and click **OK**.
1. In **Select Users, Groups, Computers, Service Accounts**, under **Enter the object names to select**, type **Ida; CL4** and click **OK**.
1. In **Create Group: VNet3 RODC password replication allowed**, click **OK**.
1. In **Active Directory Administrative Center**, click **ad**.
1. Under ad, double-click **Domain Controllers**.
1. Under Domain Controllers, double-click **VN3-SRV1**.
1. In VN3-SRV1, click **Extensions**.
1. Under Extensions, click the tab **Password Replication Policy**.
1. On tab Password Replication Policy, click **Add...**.
1. In Add Groups, Users and Computers, click **Allow passwords for the account to replicate to this RODC** and click **OK**.
1. In Select Users, Computers, Service Accounts, or Groups, under **Enter the object names to select**, type **VNet3 RODC password replication allowed** and click **OK**.
1. In **VN3-SRV1**, on tab **Password Replication Policy**, click **Advanced...**
1. In Advanced Password Replication Policy for VN3-SRV1, click **Prepopulate Passwords...**
1. In Select Users or Computers, under **Enter the object names to select**, type **Ida;CL2** and click **OK**.
1. In Prepopulate Passwords, click **Yes**.
1. In Preopulate Password Success, click **OK**.
1. In **Advanced Password Replication Policy for VN3-SRV1**, click **Close**.
1. In **VN3-SRV1**, click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. In the the OU **Entitling groups** in domain **ad.adatum.com**, create a domain-local security group named **VNet3 RODC password replication allowed**.

    ````powershell
    $server = 'ad.adatum.com'
    $aDGroup = New-ADGroup `
        -Path 'OU=Entitling groups, DC=ad, DC=adatum, DC=com' `
        -Name 'VNet3 RODC password replication allowed' `
        -GroupCategory Security `
        -GroupScope DomainLocal `
        -Server $server `
        -Passthru
    ````

1. From the domain **ad.adatum.com**, add the user **ad\Ida** and the computer **CL2** to the new group.

    ````powershell
    $adObject = Get-AdUser -Identity 'Ida' -Server $server
    $aDGroup | Add-ADGroupMember -Members $aDObject -Server $server
    $adObject = Get-ADComputer -Identity 'CL2' -Server $server
    $aDGroup | Add-ADGroupMember -Members $aDObject -Server $server
    ````

1. Add the new group to the allowed list of the password replication policy of VN3-SRV1.

    ````powershell
    Add-ADDomainControllerPasswordReplicationPolicy `
        -Identity 'VN3-SRV1'  -AllowedList $aDGroup -Server $server
    ````

1. Replicate the password of **Ida** and **CL2** to **VN3-SRV1**.

    ````powershell
    Get-ADComputer -Identity CL2 -Server $server |
    Sync-ADObject -Destination VN3-SRV1.ad.adatum.com -PasswordOnly
    Get-ADUser -Identity Ida -Server $server |
    Sync-ADObject -Destination VN3-SRV1.ad.adatum.com -PasswordOnly
    ````

### Task 5: Verify sign in

Perform this task on CL4.

1. Click *Power*, **Restart**.
1. Sign in as **ad\Ida**.
1. Run **Terminal**.
1. Verify the site for the client computer.

    ````powershell
    nltest.exe /DSGETSITE
    ````

    > This should return VNet3.

1. Verify the logon server.

    ````powershell
    $env:LOGONSERVER
    ````

    > This will return \\\\VN3-SRV1.

1. Sign out.

## Exercise 3: Simulate a compromise of the read-only domain controller

1. [Take the read-only domain controller offline](#task-1-take-the-read-only-domain-controller-offline)
1. [Delete the read-only domain controller and reset passwords of cached user accounts](#task-2-delete-the-read-only-domain-controller-and-reset-passwords-of-cached-user-accounts)
1. [Verify the sign in after compromise](#task-3-verify-the-sign-in-after-compromise)
1. [Assign a new password](#task-4-assign-a-new-password) to Ada Russell
1. [Verify sign in after password reset](#task-5-verify-sign-in-after-password-reset)

### Task 1: Take the read-only domain controller offline

#### Desktop experience

Perform this task on the host.

1. Open **Hyper-V-Manager**.
1. In Hyper-V-Manager, under **Hyper-V-Manager**, click your computer name.
1. Under Virtual Computers, in the context-menu of **WIN-VN3-SRV1**, click **Turn off...**

#### PowerShell

Perform this task on the host.

1. Run **Terminal** or **Windows PowerShell** as Administrator.
1. Turn off the virtual machine **WIN-VN3-SRV1**.

    ````powershell
    Stop-VM -Name WIN-VN3-SRV1 -TurnOff
    ````

### Task 2: Delete the read-only domain controller and reset passwords of cached user accounts

Perform this task on CL1.

1. Open **Active Directory Users and Computers**.
1. In Active Directory Users and Computers, expand **ad.adatum.com** and click **Domain Controllers**.
1. In Domain Controllers, double-click **VN3-SRV1**.
1. In VN3-SRV1 Properties, click the tab **Password Replication Policy**.
1. On tab Password Replication Policy, click **Advanced...**

1. In Advanced Password Replication Policy for VN3-SRV1, under **Display users and computers that meet the following criteria**, ensure **Accounts whose password are stored on this Read-only Domain Controller** is selected.

    You should see the accounts **Ida Alksne** and **CL2** listed.

1. Click **Close**.
1. In **VN3-SRV1 Properties**, click the **Cancel**.
1. In the context-menu of **VN3-SRV1**, click **Delete**.
1. In the message box Are you sure you want to delete the Computer names 'VN3-SRV1', click **Yes**.
1. In Deleting Domain Controller, ensure **Reset all passwords for user accounts that were cached on this Read-only Domain Controller** and **Export the list of accoutns that were cached on this Read-only Domain Controller to this file** is activated. Click **Browse...**.
1. Enter a path and name for the CSV file and click **Save**.
1. In **Deleting Domain Controller**, click **Delete**.
1. In message box Delete Domain Controller, click **OK**.
1. In message box Delete Domain Controller, click **Yes**.
1. Open and review the exported file.

### Task 3: Verify the sign in after compromise

Perform this task on CL2.

1. Click *Power*, **Restart**.
1. Sign in as **ad\Ida**.

    > The sign in will fail, because the password of Ida Alksne was reset.

### Task 4: Assign a new password

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **ad**.
1. Under ad, double-click **IT**.
1. Under IT, in the context-menu of **Ida Alksne**, click **Reset password...**
1. In Reset password, in **Password** and **Confirm password**, type a secure password. Deactivate **User must change password at next log on** and click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Read a password from the host.

    ````powershell
    $newPassword = Read-Host -AsSecureString -Prompt 'New password'
    ````

1. Reset the password of **ad\Ida** in the domain **ad.adatum.com**.

    ````powershell
    Set-ADAccountPassword `
        -Identity 'Ida' `
        -Reset `
        -NewPassword $newPassword `
        -Server ad.adatum.com
    ````

### Task 5: Verify sign in after password reset

Perform this task on CL2.

1. Sign in as **ad\Ida**.

    > The sign in should succeed.
