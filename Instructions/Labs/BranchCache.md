# Lab: BranchCache

## Required VMs

* VN1-SRV1
* VN1-SRV10
* VN2-SRV1
* CL1
* CL2
* CL3

## Setup

1. On the host, run **Windows PowerShell** as Administrator.
1. In Windows PowerShell, connect WIN-CL3, and WIN-CL4 to VNet3.

    ````powershell
    C:\Labs\WS2022\LabResources\Move-VMtoVNet.ps1 `
        -VMName WIN-CL3 -SwitchName VNet2 -NewSwitchName VNet3 -SubnetValue 3
    C:\Labs\WS2022\LabResources\Move-VMtoVNet.ps1 `
        -VMName WIN-CL4 -SwitchName VNet1 -NewSwitchName VNet3 -SubnetValue 3
    ````

1. At the prompt Enter the password of the local Administrator account of WIN-CL*, enter the local Administrator password of CL*.
1. Join CL3 and CL4 to the domain.

    ````powershell
    C:\Labs\WS2022\LabResources\Add-VMToDomain.ps1 -VMName WIN-CL3
    C:\Labs\WS2022\LabResources\Add-VMToDomain.ps1 -VMName WIN-CL4
    ````

1. At the prompt Enter the password of the local Administrator account of WIN-CL*, enter the local Administrator password of CL*.
1. At the prompt Enter credentials of an account with permissions to join the computer to domain ad.adatum.com, enter the credentials of **Administrator@ad.adatum.com**.
1. Limit the bandwidth of VN1-SRV10's network adapter to 1 MB/s.

    ````powershell
    Get-VMNetworkAdapter -VMName WIN-VN1-SRV10 | 
    Where-Object { $PSItem.SwitchName -eq 'VNet1' } |
    Set-VMNetworkAdapter -MaximumBandwidth 10MB
    ````

1. On CL1, sign in as **ad\Administrator**.
1. On CL2, sign in as **ad\Administrator**.
1. On CL3, sign in as **ad\Administrator**.
1. On CL4, sign in as **ad\Administrator**.

## Introduction

Adatum wants to improve the performance accessing the file server from VNet2 and VNet3 using BranchCache. Because on VNet2 several servers are installed, one of the servers should be configured in hosted cache mode. On VNet3 the distributed cache mode should be used and validated.

## Known Issues

[No effect can be discovered](https://github.com/EnterpriseTrainingCenter/WindowsServer/issues/201)

## Exercises

1. [Configuring the file server and Active Directory for BranchCache](#exercise-1-configuring-the-file-server-and-active-directory-for-branchcache)
1. [Configuríng and validating centralized BranchCache](#exercise-2-configuring-and-validating-branchcache-in-hosted-cache-mode)
1. [Configuring and validating peer-to-peer BranchCache](#exercise-3-validating-branchcache-in-distributed-cache-mode)

## Exercise 1: Configuring the file server and Active Directory for BranchCache

1. [Validate a slow network connection to VN1-SRV10](#task-1-validate-a-slow-network-connection-to-vn1-srv10)
1. [Install the BranchCache service role](#task-2-install-the-branchcache-service-role) on VN1-SRV10
1. [Enable hash publication for BranchCache](#task-3-enable-hash-publication-for-branchcache)
1. [Enable BranchCache for shares](#task-4-enable-branchcache-for-shares) IT and Marekting on VN1-SRV10

### Task 1: Validate a slow network connection to VN1-SRV10

Perform this task on CL2.

1. Open **Terminal**.
1. Copy the content of the IT share to the Documents folder while measuring the duration of the command.

    ````powershell
    Measure-Command -Expression { 
        Copy-Item \\vn1-srv10\IT\ ~\Documents\ -Recurse -Force
    }
    ````

    The command will run for about a minute. Take a note of the time measured to complete the command.

### Task 2: Install the BranchCache service role

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on the page **Before You Begin**, click **Next >**.
1. On the page Installation Type, ensure **Role-based or feature-based installation** is selected and click **Next >**.
1. On the page Server Selection, click **VN1-SRV10.ad.adatum.com** and click **Next >**.
1. On the page Server Roles, expand **File and Storage Services**, **File and iSCSI Services**, and activate the checkbox next to **BranchCache for Network Files** and click **Next >**.
1. On the page Features, click **Next >**.
1. On the page Confirmation, verify your selection and click **Install**.
1. On the page **Results**, click **Close**.

#### Windows Admin Center

Perform this task on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, on the connections page, click **vn1-srv10.ad.adatum.com**.
1. Connected to vn1-srv10.ad.adatum.com, under **Tools**, click **Roles & features**.
1. In Roles and features, expand **File and Storage Services**, **File and iSCSI Services**, click **BranchCache for Network Files**, and click **Install**.
1. In the pane Install Role and Features, activate the checkbox **Reboot the server automatically, if required** and click **Yes**.

    After a few minutes, a notification **Install Roles and Features** appears. If you missed the notification, a small number appears beside the icon *Notifications* (in form of a bell) at the top-right of Windows Admin Center.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install the BranchCache for Network Files role service on VN1-SRV10.

    ````powershell
    Install-WindowsFeature `
        -ComputerName VN1-SRV10 `
        -Name FS-BranchCache `
        -IncludeManagementTools `
        -Restart 
    ````

### Task 3: Enable hash publication for BranchCache

Perform this task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Group Policy Management**, **Forest: ad.adatum.com**, **Domains**, and **ad.adatum.com**.
1. In the context-menu of **ad.adatum.com**, click **Create a GPO in this domain, and Link it here...**
1. In New GPO, in **Name**, type **Custom Computer BranchCache File Server** and click **OK**.
1. In **Group Policy Management**, in the context-menu of **Custom Computer BranchCache File Server**, click **Edit...**
1. In Group Policy Management Editor, expand **Computer Configuration**, **Policies**, **Administrative Templates**, **Network**, and click **Lanman Server**.
1. In the right pane, double-click **Hash Publication for BranchCache**.
1. In Hash Publication for BranchCache, click **Enabled**. Under **Hash publication actions**, click **Allow hast publication only for shared folder on which BranchCach is enabled**. Click **OK**.
1. Close **Group Policy Management Editor**.
1. Open **Terminal**.
1. Update group policies on **VN1-SRV10**.

    ````powershell
    Invoke-GPUpdate -Computer VN1-SRV10
    ````

### Task 4: Enable BranchCache for shares

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click **File and Storage Services**.
1. In File and Storage Services, click **Shares**.
1. In Shares, in the context-menu of **IT**, click **Properties**.
1. In IT Properties, click the page **Settings**.
1. On page Settings, activate **Allow caching of share** and **Enable BranchCache on the file share**.

Repeat from step 4 for the share **Marketing**.

## Exercise 2: Configuring and validating BranchCache in hosted cache mode

1. [Install the BranchCache feature](#task-1-install-the-branchcache-feature) on VN2-SRV1
1. [Configure the hosted cache](#task-2-configure-the-hosted-cache) on VN2-SRV1
1. [Configure BranchCache for clients](#task-3-configure-branchcache-for-clients) on VNet2
1. [Prehash and export a BranchCache package](#task-4-prehash-and-export-a-branchcache-package) of share IT on VN1-SRV10
1. [Remove the bandwidth limit on virtual machine](#task-5-remove-the-bandwidth-limit-on-virtual-machine) WIN-VN1-SRV10
1. [Import the BrancCache package](#task-6-import-the-branchcache-package) on VN2-SRV1
1. [Set the bandwidth limit on virtual machine](#task-7-set-the-bandwidth-limit-on-virtual-machine) WIN-VN1-SRV10 to 10MB
1. [Validate BranchCache](#task-8-validate-branchcache) on CL2

### Task 1: Install the BranchCache feature

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on the page **Before You Begin**, click **Next >**.
1. On the page Installation Type, ensure **Role-based or feature-based installation** is selected and click **Next >**.
1. On the page Server Selection, click **VN2-SRV1.ad.adatum.com** and click **Next >**.
1. On the page Server Roles, click **Next >**.
1. On the page Features, activate **BranchCache** and click **Next >**.
1. On the page Confirmation, verify your selection and click **Install**.
1. On the page **Results**, click **Close**.

#### Windows Admin Center

Perform this task on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, on the connections page, click **vn2-srv1.ad.adatum.com**.
1. Connected to vn2-srv1.ad.adatum.com, under **Tools**, click **Roles & features**.
1. In Roles and features, under **Features**, click **BranchCache**, and click **Install**.
1. In the pane Install Role and Features, activate the checkbox **Reboot the server automatically, if required** and click **Yes**.

    After a few minutes, a notification **Install Roles and Features** appears. If you missed the notification, a small number appears beside the icon *Notifications* (in form of a bell) at the top-right of Windows Admin Center.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install the BranchCache feature on VN2-SRV1.

    ````powershell
    Install-WindowsFeature `
        -ComputerName VN2-SRV1 `
        -Name BranchCache `
        -IncludeManagementTools `
        -Restart
    ````

### Task 2: Configure the hosted cache

Perform this task on CL1.

1. Open **Terminal**.
1. Create a CIM session to **VN2-SRV1**.

    ````powershell
    $cimSession = New-CimSession -ComputerName VN2-SRV1
    ````

1. Configure VN2-SRV1 as a hosted cache server and register a service connection point in Active Directory for automatic hosted cache server discovery by client computers.

    ````powershell
    Enable-BCHostedServer -CimSession $cimSession -RegisterSCP
    ````

1. Verify the correct configuration of the hosted cache server.

    ````powershell
    Get-BCStatus -CimSession $cimSession
    ````

    Under **HostedCacheServerConfiguration**, **HostedCacheServerIsEnabled** and **HostedCacheScpRegistrationEnabled** should be **True**. Under **DataCache**, take a note of **CurrentActiveCacheSize**. This should be 0.

1. Remove the CIM session.

    ````powershell
    Remove-CimSession -CimSession $cimSession
    ````

### Task 3: Configure BranchCache for clients

Perform this task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Group Policy Management**, **Forest: ad.adatum.com**, **Domains**, **ad.adatum.com** and click **ad.adatum.com**.
1. In the context-menu of **ad.adatum.com**, click **Create a GPO in this domain and Link it here...**.
1. In New GPO, under **Name**, type **Custom Computer BranchCache Client hosted cache mode** and click **OK**.
1. In **Group Policy Management**, in the context-menu of **Custom Computer BranchCache Client**, click **Edit**.
1. In Group Policy Management Editor, expand **Computer Configuration**, **Policies**, **Administrative Templates**, **Network** and click **BranchCache**.
1. In the right pane, double-click **Turn on BranchCache**.
1. In Turn on BranchCache, click **Enabled** and click **OK**.
1. In **Group Policy Management Editor**, double-click **Enable Automatic Hosted Cache Discovery By Service Connection Point**.
1. In Enable Automatic Hosted Cache Discovery By Service Connection Point, click **Enabled** and click **OK**.
1. In **Group Policy Management Editor**, double-click **Configure BranchCache for network files**.
1. In Configure BranchCache for network files, click **Enabled**. Under **Type the maximum round trip network latency (milliseconds) after which caching begins**, type **0**. Click **OK**.
1. In **Group Policy Management Editor**, expand **Computer Configuration**, **Policies**, **Windows Settings**, **Security Settings**, **Windows Defender Firewall with Advanced Security**, **Windows Defender Firewall with Advanced Security**, and click **Outbound Rules**.
1. In the context-menu of **Outbound Rules**, cick **New Rule...**
1. In New Outbound Rule Wizard, in step Rule Type, click **Predefined**, **BranchCache - Hosted Cache Client (Uses HTTPS)**, and click **Next >**.
1. In step Predefined Rules, click **Next >**.
1. In step Action, click **Allow the connection** and click **Finish**.
1. Close **Group Policy Management Editor**.
1. Open **Terminal**.
1. Update the group policies on **CL2**.

    ````powershell
    Invoke-Command -ComputerName CL2 -ScriptBlock { gpupdate.exe }
    ````

### Task 4: Prehash and export a BranchCache package

Perform this task on CL1.

1. Open **Terminal**.
1. Create a CIM session to **VN1-SRV10**.

    ````powershell
    $cimSession = New-CimSession -ComputerName VN1-SRV10
    ````

1. On VN1-SRV10, generate hashes and stage data of **D:\Shares\IT**.

    ````powershell
    Publish-BCFileContent `
        -CimSession $cimSession -StageData -Path D:\Shares\IT -Recurse
    ````

1. Export a BranchCache package to **D:\Shares\BCCachePackage**.

    ````powershell
    Export-BCCachePackage -CimSession $cimSession -Destination D:\Shares\BCCachePackage
    ````

1. Remove the CIM session.

    ````powershell
    Remove-CimSession -CimSession $cimSession
    ````

### Task 5: Remove the bandwidth limit on virtual machine

Perform this task on the host.

1. Run **Windows PowerShell** as Administrator.
1. In Windows PowerShell, on the virtual machine **WIN-VN1-SRV10**, disable the maximum bandwidth on the network adapter connected to **VNet1**.

    ````powershell
    Get-VMNetworkAdapter -VMName WIN-VN1-SRV10 | 
    Where-Object { $PSItem.SwitchName -eq 'VNet1' } |
    Set-VMNetworkAdapter -MaximumBandwidth 0
    ````

### Task 6: Import the BranchCache package

Perform this task on CL1.

1. Open **Terminal**.
1. Create a remote PowerShell session to VN2-SRV1.

    ````powershell
    $pSSession = New-PSSession -ComputerName VN2-SRV1
    ````

1. Copy the BranchCache package from VN1-SRV10 to VN2-SRV1.

    ````powershell
    Copy-Item `
        -Path '\\vn1-srv10\d$\Shares\BCCachePackage' `
        -ToSession $pSSession `
        -Destination c:\
    ````

1. Enter the remote PowerShell session.

    ````powershell
    Enter-PSSession -Session $pSSession
    ````

1. Import the BranchCache package.

    ````powershell
    Import-BCCachePackage -Path C:\BCCachePackage
    ````

1. Verify the status of the hosted cache server.

    ````powershell
    Get-BCStatus
    ````

    Under **DataCache**, take a note of **CurrentActiveCacheSize**. This should be about 68 MB.

1. Exit and remove the remote PowerShell session

    ````powershell
    Exit-PSSession
    Remove-PSSession $pSSession
    ````

### Task 7: Set the bandwidth limit on virtual machine

Perform this task on the host.

1. Run **Windows PowerShell** as Administrator.
1. In Windows PowerShell, on the virtual machine **WIN-VN1-SRV10**, disable the maximum bandwidth on the network adapter connected to **VNet1**.

    ````powershell
    Get-VMNetworkAdapter -VMName WIN-VN1-SRV10 | 
    Where-Object { $PSItem.SwitchName -eq 'VNet1' } |
    Set-VMNetworkAdapter -MaximumBandwidth 10MB
    ````

### Task 8: Validate BranchCache

Perform this task on CL2.

1. Open **Terminal**.
1. Copy the content of the IT share to the Documents folder while measuring the duration of the command.

    ````powershell
    Measure-Command -Expression { 
        Copy-Item \\vn1-srv10\IT\ ~\Documents\ -Recurse -Force
    }
    ````

    > The command should complete within seconds.

## Exercise 3: Configuring and validating BranchCache in distributed cache mode

1. [Configure BranchCache for distributed cache mode](#task-1-configure-branchcache-for-distributed-cache-mode)
1. [Validate BranchCache](#task-2-validate-branchcache) on CL3
1. [Validate BranchCache](#task-3-validate-branchcache) on CL4

### Task 1: Configure BranchCache for distributed cache mode

Perform this task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Group Policy Management**, **Forest: ad.adatum.com**, **Domains**, **ad.adatum.com** and click **ad.adatum.com**.
1. In **Group Policy Management**, in the context-menu of **Custom Computer BranchCache Client**, click **Edit**.
1. In Group Policy Management Editor, expand **Computer Configuration**, **Policies**, **Administrative Templates**, **Network** and click **BranchCache**.
1. In **Group Policy Management Editor**, double-click **Set BranchCache Distributed Cache Mode**.
1. In Set BranchCache Distributed Cache Mode, click **Enabled** and click **OK**.
1. In **Group Policy Management Editor**, expand **Computer Configuration**, **Policies**, **Windows Settings**, **Security Settings**, **Windows Defender Firewall with Advanced Security**, **Windows Defender Firewall with Advanced Security**, and click **Inbound Rules**.
1. In the context-menu of **Inbound Rules**, cick **New Rule...**
1. In New Inbound Rule Wizard, in step Rule Type, click **Predefined**, **BranchCache - Content Retrieval (Uses HTTP)**, and click **Next >**.
1. In step Predefined Rules, click **Next >**.
1. In step Action, ensure **Allow the connection** is selected and click **Finish**.
1. In **Group Policy Management Editor**, in the context-menu of **Inbound Rules**, cick **New Rule...**
1. In New Inbound Rule Wizard, in step Rule Type, click **Predefined**, **BranchCache - Peer Discovery (Uses WSD)**, and click **Next >**.
1. In step Predefined Rules, click **Next >**.
1. In step Action, ensure **Allow the connection** is selected and click **Finish**.
1. In **Group Policy Management Editor**, click **Outbound Rules**.
1. In the context-menu of **Outbound  Rules**, cick **New Rule...**
1. In New Outbound Rule Wizard, in step Rule Type, click **Predefined**, **BranchCache - Content Retrieval (Uses HTTP)**, and click **Next >**.
1. In step Predefined Rules, click **Next >**.
1. In step Action, click **Allow the connection** is selected and click **Finish**.
1. In **Group Policy Management Editor**, in the context-menu of **Outbound Rules**, cick **New Rule...**
1. In New Outbound Rule Wizard, in step Rule Type, click **Predefined**, **BranchCache - Peer Discovery (Uses WSD)**, and click **Next >**.
1. In step Predefined Rules, click **Next >**.
1. In step Action, click **Allow the connection** and click **Finish**.
1. Close **Group Policy Management Editor**.

### Task 2: Validate BranchCache

Perform this task on CL3.

1. Open **Terminal**.
1. Update the group policies.

    ````powershell
    gpupdate.exe
    ````

1. Copy the content of the IT share to the Documents folder while measuring the duration of the command.

    ````powershell
    Measure-Command -Expression { 
        Copy-Item \\vn1-srv10\IT\ ~\Documents\ -Recurse -Force
    }
    ````

    Take a note of the duration of the command. The command will take about a minute.

1. Get the status of BranchCache.

    ````powershell
    Get-BCStatus
    ````

    Under **DataCache**, the **CurrentActiveCacheSize** should be around 60 MB.

### Task 3: Validate BranchCache

Perform this task on CL4.

1. Open **Terminal**.
1. Update the group policies.

    ````powershell
    gpupdate.exe
    ````

1. Copy the content of the IT share to the Documents folder while measuring the duration of the command.

    ````powershell
    Measure-Command -Expression { 
        Copy-Item \\vn1-srv10\IT\ ~\Documents\ -Recurse -Force
    }
    ````

    Compare the duration of the command with the previous copy process. It should have taken significantly shorter.

1. Get the status of BranchCache.

    ````powershell
    Get-BCStatus
    ````

    Under **DataCache**, the **CurrentActiveCacheSize** should be around 60 MB.

## Cleanup

Perform the cleanup on the host.

1. Run **Windows PowerShell** as Administrator.
1. In Windows PowerShell, on the virtual machine **WIN-VN1-SRV10**, disable the maximum bandwidth on the network adapter connected to **VNet1**.

    ````powershell
    Get-VMNetworkAdapter -VMName WIN-VN1-SRV10 | 
    Where-Object { $PSItem.SwitchName -eq 'VNet1' } |
    Set-VMNetworkAdapter -MaximumBandwidth 0
    ````
