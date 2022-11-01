# Lab: Deploying domain controllers

## Required VMs

* VN1-SRV1
* VN1-SRV7
* VN2-SRV1
* CL1

## Setup

On **CL1**, sign in as **ad\\Administrator**.

## Introduction

The domain controller still running Windows Server 2019 must be replaced by a Windows Server 2022 domain controller. Moreover, Adatum is expanding to a new location. An additional domain controller must be installed at the new location.

## Exercises

1. [Deploy additional domain controllers](#exercise-1-deploy-additional-domain-controllers)
1. [Check domain controller health](#exercise-2-check-domain-controller-health)
1. [Decomission a domain controller]
1. Raise domain and forest functional level

## Exercise 1: Deploy additional domain controllers

1. [Install the Remote Server Administration DNS Server Tools](#task-1-install-the-remote-server-administration-dns-server-tools) on CL1
1. [Install Active Directory Domain Services](#task-2-install-active-directory-domain-services) on VN1-SRV7 and VN2-SRV1
1. [Configure Active Directory Domain Services as a additional domain controller in an existing domain](#task-3-configure-active-directory-domain-services-as-a-additional-domain-controller-in-an-existing-domain) on VN1-SRV7 and VN2-SRV1.
1. [Configure DNS client settings using core experience](#task-4-configure-dns-client-settings-using-core-experience) on VN1-SRV7 to use VN2-SRV1 as preferred DNS server
1. [Configure DNS client settings using desktop experience](#task-5-configure-dns-client-settings-using-desktop-experience) on VN2-SRV1 to use VN1-SRV7 as preferred DNS server

Note: It is recommended to use another domain controller as DNS server. However, in real world, you should choose a DNS server on the same network.

### Task 1: Install the Remote Server Administration DNS Server Tools

Peform this task on CL1.

Install the Remote Server Administration Tools for Active Directory Domain Services, File Services, and the Server Manager on CL1.

#### Desktop experience

Perform these steps on CL1.

1. Sign in as **ad\Administrator**.
1. Open **Settings**.
1. In Settings, click **Apps**.
1. In Apps, click **Optional features**.
1. In Optional features, click the button **View features**.
1. In Add an optional feature, in the text field **Find an available optional feature**, type **RSAT**.
1. Activate the check box beside **RSAT: DNS Server Tools**.
1. Click **Next**.
1. Click **Install**.
1. If required, restart the computer.

#### PowerShell

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. In the context menu of **Start**, click **Terminal (Admin)**.
1. Add the windows capabilities **RSAT: Server DNS Server tools**.

    ````powershell
    Get-WindowsCapability -Online -Name 'Rsat.Dns.Tools*' |
    Add-WindowsCapability -Online
    ````

### Task 2: Install Active Directory Domain Services

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before You Begin, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-based installation** is selected and click **Next >**.
1. On page Server Selection, click **VN1-SRV7** and click **Next >**.
1. On page Server Roles, activate **Active Directory Domain Services**.
1. In the dialog **Add features that are required for Active Directory Domain Services?**, click **Add Features**
1. On page **Server Roles**, click **Next >**.
1. On page Features, click **Next >**.
1. On page **AD DS**, click **Next >**.
1. On page **Confirmation**, click **Install**.
1. On page **Results**, click **Close**.

Repeat these steps, but on page **Server Select**, click **VN2-SRV1**.

### Task 3: Configure Active Directory Domain Services as an additional domain controller in an existing domain

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click *Notifications* (the flag with the yellow warning triangle), and under the message **Configuration required for Active Directory Domain Services at VN1-SRV7**, click **Promote this server to a domain controller**.
1. In Active Directory Domain Services Configuration Wizard, on page Deployment Configuration, ensure **Add a domain controller to an existing domain** is selected. In **Domain**, ensure **ad.adatum.com** is filled in. Beside **\<No credentials provided\>**, click **Change...**.
1. In the dialog Credentials for deployment operation, enter the credentials for **Administrator@ad.adatum.com** and click **OK**.
1. On page **Deployment Configuration**, click **Next >**.
1. On page **Domain Controller Options**, ensure **Domain Name System (DNS) server** and **Global Catalog (GC)** are activated. Under **Type the Directory Services Restore Mode (DSRM) password**, in **Password** and **Confirm password**, type a secure password and take a note. You will need the password for a later lab. Click **Next >**.
1. On page DNS Options, click **Next >**.
1. On page **Additional Options**, click **Next >**.
1. On page **Paths**, click **Next >**.

    Note: In real world, it is recommended to have the paths on a separate drive.

1. On page Review Options, click **Next >**.
1. On page Prerequisites Check, click **Install**.
1. On page Results, click **Close**.

Repeat these steps to promote VN2-SRV1 to a domain controller.

### Task 4: Configure DNS client settings using core experience

Perform this task on VN1-SRV7.

1. Sign in as **ad\administrator**.
1. In SConfig, enter **8**.
1. In Network settings, enter **1**.
1. In Network adapter settings, enter **2**.
1. Beside Enter new preferred DNS server, enter the value from the table above.
1. Beside Enter alternate DNS server, enter **127.0.0.1**.
1. Press ENTER to continue.
1. In SConfig, enter **12**.
1. Beside Are you sure you want to log off, enter **y**.

### Task 5: Configure DNS client settings using desktop experience

Perform this task on VN2-SRV1.

1. Sign in as **ad\administrator**.
1. In Server Manager, click **Local Server**.
1. Under PROPERTIES for VN2-SRV1, beside **Ethernet**, click **10.1.2.8, IPv6 enabled**.
1. In Network Connections, in the context-menu of **Ethernet**, click **Properties**.
1. In Ethernet Properties, click **Internet Protocol Version 4 (TCP/IPv4)** and click **Properties**.
1. In Internet Protocol Version 4 (TCP/IPv4) Properties, in **Preferred DN server**, type **10.1.1.56** and click **OK**.
1. In **Ethernet Properties**, click **Close**.
1. Sign out.

## Exercise 2: Check domain controller health

1. [Verify DNS entries for Active Directory](#task-1-verify-dns-entries-for-active-directory)

    > Which records were created in the DNS zone _msdcs.ad.adatum.com?

    > Which SRV records were created in the DNS zone ad.adatum.com?

1. [Verify shares for Active Directory](#task-2-verify-shares-for-active-directory)

    > Which shares where created by the configuration of Active Directory Domain Services on VN1-SRV7 and VN2-SRV1?

1. [Verify the health of AD DS](#task-3-verify-the-health-of-ad-ds)

    > Are there any warnings from the Best Practices Analyzer?

### Task 1: Verify DNS entries for Active Directory

Perform this task on CL1.

1. Open **DNS**.
1. In the dialog **Connect to DNS Server**, click **The following computer**, type **VN1-SRV7**, and click **OK**.
1. In DNS, click **VN1-SRV7**.
1. Expand **VN1-SRV7**, **Forward Lookup Zones** and click **_msdcs.ad.adatum.com**

    > There should be 3 CNAME records, pointing to VN1-SRV1.ad.adatum.com, vn1-srv7.ad.adatum.com, and vn2-srv1.ad.adatum.com.

1. Expand **ad.adatum.com**, and click **_tcp**.

    > There should 12 SRV records for the services _gc, _kerberos, _kpasswd, and _ldap, pointing to VN1-SRV1.ad.adatum.com, vn1-srv7.ad.adatum.com, and vn2-srv1.ad.adatum.com.

### Task 2: Verify shares for Active Directory

1. Open **Server Manager**.
1. In Server manager, click **File and Storage Services**.
1. In File and Storage Services, click **Shares**

    > The shares NETLOGON and SYSVOL should be present on VN1-SRV7 and VN2-SRV1.

### Task 3: Verify the health of AD DS

1. Open **Server Manager**.
1. In Server Manager, click **Dashboard**.
1. On Dashboard, ensure that under **ROLES AND SERVER GROUPS**, beside **AD DS** and **DNS**, the number **3** is written and there is a green arrow pointing up under these tiles.
1. In the left pane, click **AD DS**. Under **EVENTS**, there should be no error events from the current day (there might me older error events).
1. Under **BEST PRACTICES ANALYZER**, click **TASKS**, **Start BPA Scan**.
1. In the dialog Select Servers, activate all three domain controllers and click **Start Scan**.

    > Review any warnings, if present.

## Exercise 3: Decomission a domain controller

1. [Change the IP address of the domain controller to decommission](#task-1-change-the-ip-address-of-the-domain-controller-to-decommission) VN1-SRV1 to 10.1.1.200
1. [Add the IP address of the decommissioned domain controller to the new domain controller](#task-2-add-the-ip-address-of-the-decommissioned-domain-controller-to-the-new-domain-controller): Add 10.1.1.8 to VN1-SRV7
1. [Demote the old domain controller](#task-3-demote-the-old-domain-controller) VN1-SRV1
1. [Remove roles from the decommissioned domain controller](#task-4-remove-roles-from-the-decommissioned-domain-controller) VN1-SRV1

Note: In this exercise, we add the IP address of the decommissioned domain controller to the new domain controller, so we do not have to reconfigure the DNS client settings on the other computers on the network. If all computers use DHCP, you could reconfigure the DHCP option DNS server instead. You would do this before task 1 and then wait for the DHCP lease period to expire before proceeding. Moreover, you would skip task 2.

### Task 1: Change the IP address of the domain controller to decommission

Perform this task on VN1-SRV1.

1. Sign in with **ad\administrator**.
1. Run SConfig.

    ````shell
    SConfig
    ````

1. In sconfig, enter **8**.
1. In Network settings, enter **1**.
1. In Network Adapter settings, enter **1**.
1. Beside Select DHCP, Static IP, enter **s**.
1. Beside Enter static IP address, enter **10.1.1.200**.
1. Beside Enter subnet mask, enter **255.255.255.0**.
1. Beside Enter default gateway, enter **1.1.1.1**.
1. In Network Adapter settings, enter **2**.
1. Beside Enter new preferred DNS server, enter **10.1.1.56**.
1. In message box **Preferred DNS server set.**, click **OK**.
1. Beside Enter alternate DNS server, enter **10.1.2.8**.
1. In message box **Alternate DNS server set.**, click **OK**.
1. In Network Adapter settings, enter **4**.
1. In Server Configuration, enter **12**.
1. In message box **Are you sure to log off?**, click **Yes**.

### Task 2: Add the IP address of the decommissioned domain controller to the new domain controller

Perform this task on CL1.

1. Open **Terminal**.
1. Open a remote PowerShell session to **VN1-SRV7**.

    ````powershell
    Enter-PSSession -ComputerName VN1-SRV7
    ````

    Note: Currently, there is no valid DNS server. Therefore, this takes a bit longer than expected.

1. Add the IP address **10.1.1.8 **to the network interface **Ethernet**.

    ````powershell
    New-NetIPAddress -InterfaceAlias Ethernet -IPAddress 10.1.1.8
    ````

1. Exit form the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

### Task 3: Demote the old domain controller

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, on the menu, click **Manage**, **Remove Roles and Features**.
1. In Remove Roles and Features Wizard, on page Befor You Begin, click **Next >**.
1. On page Server Selection, click VN1-SRV1.ad.adatum.com and click **Next >**.
1. On page Remove server roles, deactivate **Active Directory Domain Services**.
1. In dialog Remove features that require Active Directory Domain Services, click **Remove Features**.
1. In dialog Validation Results, click **Demote this domain controller**.
1. In Active Directory Domain Services Configuration Wizard, on page Credentials, click **Change...**.
1. In the dialog Credentials for deployment operation, enter the credentials for **Administrator@ad.adatum.com** and click **OK**.
1. On page **Credentials**, click **Next >**.
1. On page Warnings, activate **Proceed with removal** and click **Next >**.
1. On page Removal Options, deactivate **Remove DNS delegation** and click **Next >**.
1. On page New Administrator Password, in **Password** and **Confirm password**, type a secure password, and take a note.
1. On page Review Options, click **Demote**.
1. On page Results, click **Close**.

### Task 4: Remove roles from the decommissioned domain controller

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, on the menu, click **Manage**, **Remove Roles and Features**.
1. In Remove Roles and Features Wizard, on page Befor You Begin, click **Next >**.
1. On page Server Selection, click VN1-SRV1.ad.adatum.com and click **Next >**.
1. On page Remove server roles, deactivate **Active Directory Domain Services**.
1. In dialog Remove features that require Active Directory Domain Services, click **Remove Features**.
1. On page **Remove server roles**, deactivate **DNS Server**.
1. Expand **File and Storage Services** and deactivate **File Server**.
1. Click **Next >**.
1. On page Remove features, expand **Remote Server Administration Tools**, **Role Administration Tools**, deactivate **AD DS and AD LDS Tools**, and click **Next >**.
1. On page Confirmation, click **Remove**.
1. On page Removal progress, wait for the removal to complete and click **Close**.
1. Click **All Servers**.
1. In the context-menu of **VN1-SRV1**, click **Remove Server**.
1. In the message box **Are you sure you want to remove these servers from Server Manager?**, click **OK**.
1. Open **Terminal**
1. Shut down **VN1-SRV1**.

    ````powershell
    Stop-Computer -ComputerName VN1-SRV1
    ````

## Exercise 4: Raise the domain and forest functional level

1. Raise the domain functional level

    > What is the highest possible domain functional level?

1. Raise the forest functional level

    > What is the highest possible forest functional level?

### Task 1: Raise the domain functional level

Perform this task on CL1.

1. Open **Active Directory Domains and Trusts**.
1. In Active Directory Domains and Trusts, in the context-menu of **ad.adatum.com**, click **Raise Domain Functional Level...**

    > The highest possible domain functional level is Windows Server 2016. The domain is already at that level.

1. In Raise domain function level, click **Close**.

### Task 2: Raise the forest functional level

Perform this task on CL1.

1. Open **Active Directory Domains and Trusts**.
1. In Active Directory Domains and Trusts, in the context-menu of **Active Directory Domains and Trusts**, click **Raise Forest Functional Level...**

    > The highest possible forest functional level is Windows Server 2016. The forest is already at that level.

1. In Raise forest function level, click **OK**.
