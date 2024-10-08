# Lab: Managing sites and replication

## Required VMs

* VN1-SRV5
* VN1-SRV7
* VN2-SRV1
* VN3-SRV1
* CL1
* CL2

If you did not complete the lab [Deploying domain controllers](Deploying-domain-controllers.md), in addition to the VMs above, **VN1-SRV1** is required. If VN1-SRV1 is already shut down after the lab, do not start it.

## Setup

On **CL1**, sign in as **ad\\Administrator**.
On **CL2**, sign in as **ad\\Administrator**.

If you skipped the practice [Install Remote Server Administration Tools](Practices/Install-Remote-Server-Administration-Tools.md), on **CL1**, in **Terminal**, execute ````C:\LabResources\Solutions\Install-RemoteServerAdministrationTools.ps1````.

If you did not complete exercise 1 of lab [Deploying domain controllers](Deploying-domain-controllers.md#exercise-1-deploy-additional-domain-controllers) and exercises 2 and 3 of lab [Multi-domain environments](Multi-domain-environments.md), you will not be able to perform all steps in this lab. However, try as many steps as possible in this lab to get used to the tools.

## Introduction

The virtual networks in Adatum represent different locations. Adatum wants to minimize replication traffic between the locations and keep logon traffic local, if possible. For this purpose, Adatum wants you to implement sites and site links in Active Directory and optimize the replication infrastrucutre

## Exercises

1. [Create sites](#exercise-1-create-sites)
1. [Configure global catalogs](#exercise-2-configure-global-catalogs)
1. [Create site links](#exercise-3-create-site-links)
1. [Verify the replication topology changes](#exercise-4-verify-replication-topology-changes)

## Exercise 1: Create sites

1. [Verify the site of client](#task-1-verify-the-site-of-client) CL2

    > What is the site of CL2 before creating sites in Active Directory?

1. [Create sites](#task-2-create-sites) VNet1 (rename Default-First-Site-Name), VNet2, VNet3 and Perimeter
1. [Create subnets](#task-3-create-subnets) according to the table below

    | Subnet        | Site      |
    |---------------|-----------|
    | 10.1.1.0/24   | VNet1     |
    | 10.1.2.0/24   | VNet2     |
    | 10.1.3.0/24   | VNet3     |
    | 10.1.200.0/24 | Perimeter |

1. [Move domain controllers into sites](#task-4-move-domain-controllers-into-sites)
1. [Verify the site of client](#task-5-verify-the-site-of-client) CL2

    > What is the site of CL2 after configuring sites?

    > What is the domain controller, CL2 authenticated with after configuring sites?

1. [Verify sites in DNS](#task-6-verify-sites-in-dns)

### Task 1: Verify the site of client

#### Desktop experience

Perform this task on CL2.

1. Open **Registry Editor**.
1. In Registry Editor, expand **Computer**, **HKEY_LOCAL_MACHINE**, **SYSTEM**, **CurrentControlSet**, **Services**, **Netlogon**, **Parameters**.

    > The value DynamicSiteName should be Default-First-Site-Name.

#### PowerShell

Perform this task on CL1.

1. Create a remote PowerShell session to **CL2**.

    ````powershell
    Enter-PSSession CL2
    ````

1. Query the site of **CL2**.

    ````powershell
    nltest.exe /DSGETSITE
    ````

    > You should get Default-First-Site-Name.

1. Close the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

### Task 2: Create sites

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Sites and Services**.
1. In Active Directory Sites and Services, in the context-menu of **Default-First-Site-Name**, click **Rename** and enter **VNet1**.
1. In the context-menu of **Sites**, click **New Site...**
1. In New Object - Site, in **Name**, type **VNet2**, click **DEFAULTIPSITELINK** and click **OK**.
1. In the message box

    **Site VN2 has been created. To finish configuration of VN2:**

    **Ensure that VN2 is linked to other sites with site links as appropriate.**

    **Add subnets for VN2 to the Subnets container.**

    **Install one or more Domain Controllers in VN2, or move existing DCs into the site.**

    **You will not see this message again until the next time you start Active Directory Sites and Services.**

    click **OK**.

Repeat steps 3 - 5 to create the sites **VNet3** and **Perimeter**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Rename the site **Default-First-Site-Name** to **VNet1**.

    ````PowerShell
    Get-ADReplicationSite -Identity Default-First-Site-Name | Rename-ADObject -NewName VNet1
    ````

1. Create the sites **Vnet2**, **VNet3**, and **Perimeter**.

    ````powershell
    'VNet2', 'VNet3', 'Perimeter' | 
    ForEach-Object { New-ADReplicationSite -Name $PSItem }
    ````

### Task 3: Create subnets

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Sites and Services**.
1. In Active Directory Sites and Services, in the context-menu of **Subnets**, click **New Subnet...**
1. In New Object - Subnet, under **Prefix**, type 10.1.1.0/24. Click **VNet1** and click **OK**.

Repeat steps 2 and 3 to create the subnets according to the table above.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Create subnets and associate them with the sites according to the table above.

    ````powershell
    New-ADReplicationSubnet -Name 10.1.1.0/24 -Site VNet1
    New-ADReplicationSubnet -Name 10.1.2.0/24 -Site VNet2
    New-ADReplicationSubnet -Name 10.1.3.0/24 -Site VNet3
    New-ADReplicationSubnet -Name 10.1.200.0/24 -Site Perimeter
    ````

### Task 4: Move domain controllers into sites

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Sites and Services**.
1. In Active Directory Sites and Services, expand **VNet1** and **Servers**.
1. In the context-menu of **VN1-SRV1**, click **Delete**.
1. In the message box **Are you sure you want to delete the server named 'VN1-SRV1'?**, click **Yes**.
1. In the message box **Obbject VN1-SRV1 contains other objects. Are you sure you want to delete object VN1-SRV1 and all of the objects it contains?**, click **Yes**.
1. In the context-menu of **VN2-SRV1**, click **Move...**.
1. In Move Server, click **VNet2** and click **OK**.
1. In the context-menu of **PM-SRV1**, click **Move...**.
1. In Move Server, click **Perimeter** and click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. List domain controllers in site VNet1.

    ````powershell
    $adReplicationSite = Get-ADReplicationSite -Identity VNet1
    Get-ADObject `
        -SearchBase "CN=Servers, $($adReplicationSite.DistinguishedName)" `
        -Filter 'ObjectClass -eq "server"'

1. Delete VN1-SRV1.

    ````powershell
    Get-ADObject `
        -SearchBase "CN=Servers, $($adReplicationSite.DistinguishedName)" `
        -Filter 'ObjectClass -eq "server" -and Name -eq "VN1-SRV1"' |
    Remove-ADObject -Recursive
    ````

1. At the prompt

    ````text
    Are you sure you want to perform this action?
    Performing the operation "Remove" on target
    "CN=VN1-SRV1,CN=Servers,CN=VNet1,CN=Sites,CN=Configuration,DC=ad,DC=adatum,DC=com".
    ````

    enter **y**.

1. Move the domain controller **VN2-SRV1** to site **VNet2**.

    ````powershell
    Move-ADDirectoryServer -Identity VN2-SRV1 -Site VNet2
    ````

1. Move the domain controller **PM-SRV1** to site **Perimeter**.

    ````powershell
    Move-ADDirectoryServer -Identity PM-SRV1 -Site Perimeter
    ````

### Task 5: Verify the site of client

#### Desktop experience

Perform this task on CL2.

1. Restart the computer.
1. Sign in as **ad\Administrator**.
1. Open **Registry Editor**.
1. In Registry Editor, expand **Computer**, **HKEY_LOCAL_MACHINE**, **SYSTEM**, **CurrentControlSet**, **Services**, **Netlogon**, **Parameters**.

    > The value DynamicSiteName should be VNet2.

#### PowerShell

Perform this task on CL1.

1. Restart **CL2**.

    ````powershell
    Restart-Computer CL2 -WsmanAuthentication Default
    ````

1. Create a remote PowerShell session to **CL2**.

    ````powershell
    Enter-PSSession CL2
    ````

1. Query the site of **CL2**.

    ````powershell
    nltest.exe /DSGETSITE
    ````

    > You should get VNet2.

1. Query the domain controller for **ad.adatum.com**.

    ````powershell
    nltest /DSGETDC:ad.adatum.com
    ````

    > You should get \\\\VN2-srv1.ad.adatum.com.

1. Close the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

### Task 6: Verify sites in DNS

Perform this task on CL1.

1. Open **DNS**.
1. In **Connect to DNS Server**, click **The following computer**, type **VN1-SRV5**, and click **OK**.
1. In DNS, expand **Forward Lookup Zones**, **ad.adatum.com**, **_sites**, and the site names below.
1. For each site, click **_tcp**. Note the **Service Location (SRV)** records.

## Exercise 2: Configure global catalogs

[Remove the global catalog](#task-remove-the-global-catalog) from VN1-SRV5.

### Task: Remove the global catalog

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Sites and Services**.
1. In Active Directory Sites and Services, expand **VNet1**, **Servers**, and **VN1-SRV5**.
1. Under VN1-SRV5, in the context menu of **NTDS Settings**, click **Properties**.
1. In NTDS Settings Properties, on tab General, deactivate **Global Catalog**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Store site **VNet1** in a variable.

    ````powershell
    $adReplicationSite = Get-ADReplicationSite -Identity VNet1
    ````

1. Find the server **VN1-SRV5** in the site.

    ````powershell
    $server = Get-ADObject `
        -SearchBase "CN=Servers, $($adReplicationSite.DistinguishedName)" `
        -Filter 'ObjectClass -eq "server" -and Name -eq "VN1-SRV5"'

    ````

1. Disable the Global Catalog on the server.

    ````powershell
    Set-ADObject `
        -Identity "CN=NTDS Settings, $($server.distinguishedName)" `
        -Replace @{ options='0'} `
    ````

## Exercise 3: Create site links

1. [Create site links](#task-1-create-site-links) to create a hub and spoke topology connecting all sites with VNet1 as hub
1. [Disable the default site link bridging](#task-2-disable-the-default-site-link-bridging)
1. [Configure the site links for notification-based replication](#task-3-configure-the-site-links-for-notification-based-replication)
1. [Run the Inter-Site topology generator](#task-4-run-the-inter-site-topology-generator)

### Task 1: Create site links

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Sites and Services**.
1. In Active Directory Sites and Services, expand **Inter-Site Transports** and click **IP**.
1. In the context-menu of **IP**, click **New Site Link**.
1. In New Object - Site Link, in **Name**, type **VNet1 - VNet2**. In the list **Sites not in the site link**, click **VNet1** and click **Add \>\>**. Click **VNet2** and click **Add \>\>**. Click **OK**.
1. In **Active Directory Sites and Services**, under **IP**, double-click the **Site Link** **VNet1 - VNet2**.
1. In VNet1 - VNet2 Properties, in **Cost**, type **1**, in **Replicate every**, type **15**, and click **OK**.

Repeat steps 3 - 6 to create the site links **VNet1 - VNet3** and **VNet1 - Perimeter**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Create a new site link between VNet1 and VNet2 with the cost 1 replicating every 15 minutes.

    ````powershell
    New-ADReplicationSiteLink `
        -Name 'VNet1 - VNet2' `
        -SitesIncluded VNet1, VNet2 `
        -InterSiteTransportProtocol IP `
        -Cost 1 `
        -ReplicationFrequencyInMinutes 15
    ````

1. Create a new site link between VNet1 and VNet3 with the cost 1 replicating every 15 minutes.

    ````powershell
    New-ADReplicationSiteLink `
        -Name 'VNet1 - VNet3' `
        -SitesIncluded VNet1, VNet3 `
        -InterSiteTransportProtocol IP `
        -Cost 1 `
        -ReplicationFrequencyInMinutes 15
    ````

1. Create a new site link between VNet1 and Perimeter with the cost 1 replicating every 15 minutes.

    ````powershell
    New-ADReplicationSiteLink `
        -Name 'VNet1 - Perimeter' `
        -SitesIncluded VNet1, Perimeter `
        -InterSiteTransportProtocol IP `
        -Cost 1 `
        -ReplicationFrequencyInMinutes 15
    ````

### Task 2: Disable the default site link bridging

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Sites and Services**.
1. In Active Directory Sites and Services, expand **Inter-Site Transports** and click **IP**.
1. In IP, in the context-menu of DEFAULTIPSITELINK, click Delete.
1. In the message box **Are you sure you want to delete the Site link named 'DEFAULTIPSITELINK'?**, click **Yes**.
1. Under Inter-Site Transports, in the context-menu of **IP**, click **Properties**.
1. In IP Properties, deactivate **Bridge all site links** and click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Delete the site link **DEFAULTIPSITELINK**.

    ````powershell
    Remove-ADReplicationSiteLink -Identity DEFAULTIPSITELINK
    ````

1. At the prompt Performing the operation "Remove" on target "CN=DEFAULTIPSITELINK,CN=IP,CN=Inter-Site Transports,CN=Sites,CN=Configuration,DC=ad,DC=adatum,DC=com", enter **Y**.

1. Store the **IP** object including the property **options** of **Inter-Site Transports** in a variable.

    ````powershell
    $configurationNamingContext = 'CN=Configuration, DC=ad, DC=adatum,DC=com'
    $aDObject = Get-ADObject `
        -Identity `
            "CN=IP,CN=Inter-Site Transports,CN=Sites,$configurationNamingcontext" `
        -Properties options
    ````

1. On the options property, set the flag BR (NTDSTRANSPORT_OPT_BRIDGES_REQUIRED). The flag is bit # 1 (decimal value of 2).

    ````powershell
    $aDObject | Set-ADObject -Replace @{"options" = $aDObject.options -bor 2 }
    ````

### Task 3: Configure the site links for notification-based replication

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Sites and Services**.
1. In Active Directory Sites and Services, expand **Inter-Site Transports** and click **IP**.
1. In IP, double-click **VNet1 - Perimeter**.
1. In Vnet1 - Perimeter Properties, click the tab **Attribute Editor**.
1. On tab Attribute Editor, click under **Atributes**, click **options** and click **Edit**.

    If you do not see the attribute options, click **Filter**, **Show only attributes that have values** (deactivate this filter).

1. In Integer Attribute Editor, under **Value**, type one of the following and click **OK**.

    * If Value is **\<not set\>**, type **1**.
    * For any other value, in a **Terminal**, execute this command and type the result.

        ````powershell
        $value -bor 1 # replace $value with the current value of the attribute
        ````

1. In **Vnet1 - Perimeter Properties**, click **OK**.

Repeat steps 3 - 6 for **VNet1 - VNet2** and **VNet1 - VNet3**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. 1. On the options property of all site links, set the flag UN (NTDSSITELINK_OPT_USE_NOTIFY). The flag is bit # 0 (decimal value of 1).

    ````powershell
    Get-ADReplicationSiteLink -Filter * -Properties options | ForEach-Object { 
        $PSItem | Set-ADObject -Replace @{ 'options' = $PSItem.options -bor 1 } 
    }
    ````

### Task 4: Run the Inter-Site topology generator

#### Desktop Experience

Perform this task on CL1.

1. Open **Active Directory Sites and Services**.
1. In Active Directory Sites and Services, and click **VNet1**.
1. In **VNet1**, double-click **NTDS Site Settings**.
1. In NTDS Site Settings Properties, under **Inter-Site Topology Generator**, take a note of the value in **Server**. Click **Cancel**.
1. Expand **VNet1**, **Servers** and the server noted in the previous step.
1. Under the server noted, in the context-menu of **NTDS Settings**, click **All Tasks**, **Check Replication Topology**.
1. In the message box **Check Replication Topology**, click **OK**.

Repeat steps 2 - 7 for the sites **VNet2** and **VNet3**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Retrieve the DNS host names of the inter-site topology generators (ISTG) for each site and store them in a variable.

    ````powershell
    $istgDNSHostName = (
        Get-ADReplicationSite -Filter * | 
        Where-Object { $PSItem.InterSiteTopologyGenerator } | 
        Select-Object -ExpandProperty InterSiteTopologyGenerator
    ) -replace 'CN=NTDS Settings,', '' | 
    Get-ADObject -Properties dNSHostName | 
    Select-Object -ExpandProperty dNSHostName
    ````

1. Run the knowledge consistency checker on the ISTG servers.

    ````powershell
    $istgDNSHostName | ForEach-Object {  repadmin.exe /kcc $PSItem }
    ````

## Exercise 4: Verify replication topology changes

This is an optional exercise, if time permits.

Wait for at least 1 hour  after completing exercise 3 in this lab, before performing this exercise.

Refer to [Practice: Explore intra-site replication](/Instructions/Practices/Explore-intra-site-replication.md) to document the changes in replication topology caused by the new site design.
