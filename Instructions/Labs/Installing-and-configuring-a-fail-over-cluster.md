# Lab: Installing and configuring a failover cluster

## Required VMs

* CL1
* VN1-SRV1
* VN1-SRV2
* VN1-SRV4
* VN1-SRV5
* VN1-SRV10

## Setup

1. On **VN1-SRV2**, sign in as **ad\Administrator**.
1. In SConfig, enter **15**.
1. Execute these commands:

    ````powershell
    C:\LabResources\Solutions\New-CertTemplateWebServerExportable.ps1
    C:\LabResources\Solutions\Set-CertTemplatePermissions.ps1 `
        -Template WebServerExportable `
        -ComputerName VN1-SRV4
    ````

1. On **CL1**, sign in as **ad\Administrator**.
1. On **VN1-SRV4**, sign in as **ad\Administrator**.

## Introduction

Adatum wants you to install a failover cluster to run an important virtual machine with high availability. Moreover, Adatum also wants to install the Windows Admin Center highly available. Lastly, prepare the new cluster to be used as a file share witness for other clusters.

## Exercises

1. [Configure the iSCSI initiator](#exercise-1-configure-the-iscsi-initiator)
1. [Install and configure a failover cluster](#exercise-2-install-and-configure-a-failover-cluster)
1. [Use Hyper-V on a cluster](#exercise-3-use-hyper-v-on-a-cluster)
1. [Install Windows Admin Center on the cluster](#exercise-4-install-windows-admin-center-on-a-failover-cluster)
1. [Install File Server on a failover cluster](#exercise-5-install-file-server-on-a-failover-cluster)
1. [Test failover](#exercise-6-test-failover)
1. [Use cluster-aware updating](#exercise-7-use-cluster-aware-updating)

## Exercise 1: Configure the iSCSI initiator

1. [Install the Multipath feature](#task-1-install-the-multipath-feature) on VN1-SRV4
1. [Configure Multipath I/O](#task-2-configure-multipath-io) on VN1-SRV4
1. [Connect to the iSCSI target](#task-3-connect-to-the-iscsi-target) on VN1-SRV10 from VN1-SRV4 using Multipath I/O

### Task 1: Install the Multipath feature

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before You Begin, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-basedd installation** is selected and click **Next >**.
1. On page Server Selection, click **VN1-SRV4.ad.adatum.com** and click **Next >**.
1. On page Server Roles, click **Next >**.
1. On page Features, activate **Multipath I/O** and click **Next >**.
1. On page **Confirmation**, click **Install**.
1. On page **Results**, click **Close**.

#### PowerShell

Peform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. In Terminal, install the windows feature **iSCSI Target Server** on **VN1-SRV4**.

    ````powershell
    Install-WindowsFeature `
      -ComputerName VN1-SRV4 `
      -Name MultiPath-IO `
      -IncludeManagementTools
    ````

### Task 2: Configure Multipath I/O

#### Desktop experience

Perform this task on VN1-SRV4.

1. In SConfig, enter **15**.
1. Open **MPIO properties**.

   ````shell
   mpiocpl.exe
   ````

1. In MPIO Properties, on the tab **Discover Multi-Path**, activate **Add support for iSCSI devices** and click on **Add**
1. In the message box MPIO operation: Successful, click **OK**.
1. In **MPIO Properties** click **OK**.

#### PowerShell

Perform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. In Terminal, activate the Multipath I/O support for iSCSI devices on **VN1-SRV4**.

    ````powershell
    Invoke-Command -ComputerName VN1-SRV4 -ScriptBlock {
        Enable-MSDSMAutomaticClaim -BusType iSCSI
    }
    ````

### Task 3: Connect to the iSCSI target

#### Desktop experience

Perform this task on VN1-SRV4.

1. In SConfig, enter **15**.
1. Open **MPIO properties**.

   ````powershell
   iscsicpl.exe
   ````

1. In the message box The Microsoft iSCSI service is not running. The service is required to be started for iSCSI to function correctly. To start the service now and have the service start automatically each time the computer restarts, click the Yes button, click **Yes**.
1. In iSCSI Initiator Properties, on tab Targets, in the text box **Target**, type **vn1-srv10.ad.adatum.com**, and click **Quick Connect**.
1. In Quick Connect, click **Done**.
1. In **iSCSI Initiator Properties**, on tab **Targets**, click **Disconnect** and click **Connect**.
1. In Connect To Target, activate **Enable multi-path** and click on **Advanced**.
1. In Advanced Settings, in **Local adapter**, click **Microsoft iSCSI Initiator**. In **Initiator IP**, click **10.1.128.32**. In **Target portal IP**, click **10.1.128.80 / 3260**. Click **OK**.
1. In **Connect To Target**, click **OK**.
1. In **iSCSI Initiator Properties**, on tab **Targets**, click on **Connect**.
1. In Connect To Target, activate **Enable multi-path** and click on **Advanced**.
1. In Advanced Settings, in **Local adapter**, click **Microsoft iSCSI Initiator**. In **Initiator IP**, click **10.1.144.32**. In **Target portal IP**, click **10.1.144.80 / 3260**. Click **OK**.
1. In **Connect To Target**, click **OK**.
1. In **iSCSI Initiator Properties**, click **OK**.

#### PowerShell

Perform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. In Terminal, create a remote PowerShell session to **VN1-SRV5**.

    ````powershell
    Enter-PSSession VN1-SRV4
    ````

1. Set the service MSiSCSI to auto start.

    ````powershell
    $service = Get-Service -Name MSiSCSI
    $service | Set-Service -StartupType Automatic
    $service | Start-Service
    ````

1. Create a new iSCSI target portal **vn1-srv10.ad.adatum.com**.

    ````powershell
    $iscsiTargetPortal = New-IscsiTargetPortal `
        -TargetPortalAddress vn1-srv10.ad.adatum.com
    ````

1. Retrieve the available iSCSI targets on the portal and store them in a variable.

    ````powerShell
    $iscsiTarget = Get-IscsiTarget -IscsiTargetPortal $iscsiTargetPortal
    ````

1. Connect to the target using multi-path. Use the IP address **10.1.128.40** on the initiator side, and **10.1.128.80** on the target side.

    ````powershell
    $iscsiTarget | Connect-IscsiTarget `
      -IsMultipathEnabled $true `
      -InitiatorPortalAddress 10.1.128.32 `
      -TargetPortalAddress 10.1.128.80 `
      -IsPersistent $true
    ````

1. Connect to the target using multi-path. Use the IP address **10.1.128.40** on the initiator side, and **10.1.128.80** on the target side.

   ````powershell
   $iscsiTarget | Connect-IscsiTarget `
      -IsMultipathEnabled $true `
      -InitiatorPortalAddress 10.1.144.32 `
      -TargetPortalAddress 10.1.144.80 `
      -IsPersistent $true
    ````

1. Close the remote PowerShell session.

   ````powershell
   Exit-PSSession
   ````

## Exercise 2: Install and configure a failover cluster

1. [Install the Remote Server Administration Failover Clustering Tools](#task-1-install-the-remote-server-administration-failover-clustering-tools) on CL1
1. [Install the failover clustering feature](#task-2-install-the-failover-clustering-feature) on VN1-SRV4 and VN1-SRV5
1. [Validate the configuration](#task-3-validate-the-configuration) on VN1-SRV4 and VN1-SRV5
1. [Create a failover cluster](#task-4-create-a-virtual-machine) with VN1-SRV4 and VN1-SRV5 as nodes with the name VN1-CLST1 and the IP address 10.1.1.33
1. [Configure the quorum](#task-5-configure-the-quorum) to use the smallest disk as disk witness
1. [Configure Cluster Shared Volumes](#task-6-configure-cluster-shared-volumes) according to the table below.

    | Name | Capacity | Path |
    |------|----------|------|
    |      | 10,0 GB  |      |
    |      | 80,0 GB  |      |

1. [Configure cluster networks](#task-7-configure-cluster-networks) accoring to the table below

    | Name | Subnets       | Enabled Options                                                                                            |
    |------|---------------|------------------------------------------------------------------------------------------------------------|
    |      | 10.1.1.0/24   | **Allow cluster network communication on this network**, **Allow clients to connect through this network** |
    |      | 10.1.128.0/24 | **Do not allow cluster network communication on this network**                                             |
    |      | 10.1.144.0/24 | **Do not allow cluster network communication on this network**                                             |
    |      | 10.1.160.0/24 | **Allow cluster network communication on this network**                                                    |

### Task 1: Install the Remote Server Administration Failover Clustering Tools

#### Desktop experience

Perform these steps on CL1.

1. Open **Settings**.
1. In Settings, click **Apps**.
1. In Apps, click **Optional features**.
1. In Optional features, click the button **View features**.
1. In Add an optional feature, in the text field **Find an available optional feature**, type **RSAT**.
1. Activate the check box beside **RSAT: Failover Clustering Tools**.
1. Click **Next**.
1. Click **Install**.
1. If required, restart the computer.

You do not need to wait for the completion of the installation

#### PowerShell

Perform these steps on CL1.

1. In the context menu of **Start**, click **Terminal (Admin)**.
1. Add the windows capabilities **RSAT: Server DNS Server tools**.

    ````powershell
    Get-WindowsCapability `
        -Online `
        -Name 'Rsat.FailoverCluster.Management.Tools*' |
    Add-WindowsCapability -Online
    ````

You do not need to wait for the completion of the installation

### Task 2: Install the failover clustering feature

#### Desktop Experience

Perform this task on CL1.

1. Open Server Manager.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on page **Before You Begin**, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-based installation** is selected and click **Next >**.
1. On page Server Selection, click **VN1-SRV4.ad.adatum.com** and click **Next >**.
1. On page Server Roles, click **Next >**.
1. On page Features, activate **Failover Clustering**.
1. In Add features that are required for Failover Clustering, click **Add Features**.
1. In **Add Roles and Features Wizard**, on page **Features**, click **Next >**.
1. On page Confirmation, verify your selection and click **Install**.
1. On  page **Results**, wait for the installation to succeed, then click **Close**.

Repeat the steps of this task to install the role on **VN1-SRV5**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install **Failover Clustering** on **VN1-SRV4** and **VN1-SRV5**.

    ````powershell
    Invoke-Command -ComputerName VN1-SRV4, VN1-SRV5 -ScriptBlock {
        Install-WindowsFeature `
            -Name Failover-Clustering `
            -IncludeManagementTools `
            -Restart
    }
    ````

### Task 3: Validate the configuration

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In the left pane, in the context-menu of **Failover Cluster Manager**, click **Validate Configuration...**.
1. In Validate a Configuration Wizard, on page Before You Begin, click **Next >**.
1. On page Select Servers or a Cluster, in **Enter server name**, type **vn1-srv4.ad.adatum.com** and click **Add**. Repeat for **vn1-srv5.ad.adatum.com**. Click **Next >**.
1. On page Testing options, ensure **Run all tests (recommended)** is selected and click **Next >**.
1. On page Confirmation, click **Next >**.
1. On page Summary, click **View Report...**

If there are any warnings or error, you may discuss them with your instructor and/or try to fix them. You may repeat this task after you applied fixes.

### Task 4: Create a failover cluster

Perform this task on VN1-SRV4.

1. In SConfig, enter **15**.
1. Create a cluster with the name **VN1-CLST1** consisting of the nodes **VN1-SRV4** and **VN1-SRV5**. The static address for administrative purposes should be **10.1.1.33**.

    ````powershell
    New-Cluster `
        -Name VN1-CLST1 `
        -Node vn1-srv4.ad.adatum.com, vn1-srv5.ad.adatum.com `
        -StaticAddress 10.1.1.33
    ````

### Task 5: Configure the quorum

#### Desktop experience

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, in the context-menu of **Failover Cluster Manager**, click **Connect To Cluster...**
1. In Select Cluster, in **Cluster name**, type **VN1-CLST1** and click **OK**.
1. In **Failover Cluster Manager**, in the context-menu of **VN1-CLST1.ad.adatum.com**, click **More Actions**, **Configure Cluster Quorum Settings...**
1. In Configure Cluster Quorum Wizard, on page Before You Begin, click **Next >**.
1. On page Select Quorum Configuration Option, click **Advanced quorum configuration** and click **Next >**.
1. On page Select Voting Configuration, ensure **All Nodes** is selected and click **Next >**.
1. On page Select Quorum Witness, click **Configure a disk witness** and click **Next >**.
1. On page Configure Storage Witness, expand the disks, find the disk **D** with the capacity of around 1000 MB. Activate the checkbox beside this disk and ensure, the other checkboxes are deactivated. Click **Next >**.
1. On page Confirmation, click **Next >**.
1. On page Summary, click **Finish**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. In Terminal, query the disks on the cluster **VN1-CLST1**.

    ````powershell
    $cluster = 'VN1-CLST1'
    $cimSession = New-CimSession -ComputerName $cluster
    Get-ClusterResource -Cluster $cluster | 
    Where-Object { $PSItem.ResourceType -eq 'Physical Disk' } | 
    Get-ClusterParameter -Name DiskGuid | ForEach-Object { 
        $name = $PSItem.ClusterObject
        Get-Disk -CimSession $cimSession -Path "\\?\Disk$($PSItem.Value)" | 
        Select-Object `
            @{ label = 'Name'; expression = { $name } }, `
            PartitionStyle, `
            @{ 
                label = 'Total Size'
                expression = { "$($PSItem.Size / 1GB) GB" } 
            } 
    }
    Remove-CimSession -CimSession $cimSession
    ````

    Take a note of the GPT disk's name with a total size of 1 GB.

1. Set the cluster quorum to node and disk majority.

    ````powershell
    # Replace the name 'Cluster Disk 1' with the name you took note of.

    Set-ClusterQuorum -Cluster $cluster -NodeAndDiskMajority 'Cluster Disk 2'
    ````

### Task 6: Configure Cluster Shared Volumes

#### Desktop experience

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST1.ad.adatum.com**, **Storage** and click **Disks**.
1. Refer to the table above. Under Disks (4), in the context-menu of the first disk with the capacity noted in the table and **Assigned To** **Available Storage**, click **Add to Cluster Shared Volumes**.
1. Click the same disk again. In the bottom pane, take a note of the path of the volume, e.g., **C:\ClusterStorage\Volume1**. Record the paths in form of a table like the table above.

Repeat from step 3 for all disks from the table above and **Assigned To** **Available Storage**.

In result, one disk should be assigned to **Disk Witness in Quorum**, the disks with a capacity from the table above should be assigned to **Cluster Shared Volume**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. In Terminal, query the disks on the cluster **VN1-CLST1**.

    ````powershell
    $cluster = 'VN1-CLST1'
    $cimSession = New-CimSession -ComputerName $cluster
    $cluster = 'VN1-CLST1'
    $cimSession = New-CimSession -ComputerName $cluster
    Get-ClusterResource -Cluster $cluster | 
    Where-Object { $PSItem.ResourceType -eq 'Physical Disk' } | 
    Get-ClusterParameter -Name DiskGuid | ForEach-Object { 
        $name = $PSItem.ClusterObject
        Get-Disk -CimSession $cimSession -Path "\\?\Disk$($PSItem.Value)" | 
        Select-Object `
            @{ label = 'Name'; expression = { $name } }, `
            PartitionStyle, `
            @{ 
                label = 'Total Size'
                expression = { "$($PSItem.Size / 1GB) GB" } 
            } 
    }

    Remove-CimSession -CimSession $cimSession
    ````

    Take a note of the GPT disk's name with a total size corresponding to the capacity in the table above. Complete the column name in the table.

1. Add the disks from the table as cluster shared volume.

    ````powershell
    # Replace the names with the names from the table.

    Add-ClusterSharedVolume -Cluster $cluster -Name 'Cluster Disk 1'
    Add-ClusterSharedVolume -Cluster $cluster -Name 'Cluster Disk 4'
    ````

1. Query the paths of the cluster shared volumes and fill the column path in the table above.

    ````powershell
    Get-ClusterSharedVolume -Cluster $cluster | 
    Select-Object Name, SharedVolumeInfo
    ````

### Task 7: Configure cluster networks

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST1.ad.adatum.com**, and click **Networks**.
1. In the context-menu of each cluster network, click **Properties**.
1. In Properties of each network click the options according to the table above. Take a note of the names while configuring the networks. You will need them in the next step.
1. In **Failover Cluster Manager**, in the context-menu of **Networks**, click **Live Migration Settings**.
1. In Live Migration Settings, deactivate the cluster networks corresponding to 10.1.128.0/24 and 10.1.144.0/24. Click the cluster network corresponding to the subnet 10.1.160.0/24 and click **Up** until it appears at the top of the list. Click **OK**.

## Exercise 3: Use Hyper-V on a cluster

1. [Configure nested virtualization](#task-1-configure-nested-virtualization) by exposing virtualization extensions and enabling MAC address spoofing for WIN-VN1-SRV4 and WIN-VN1-SRV5. Configure the machines with static memory of 3 GB.
1. [Install Hyper-V](#task-2-install-hyper-v) on VN1-SRV4 and VN1-SRV5 and set the default locations to the 80 GB CSV
1. [Configure a virtual switch](#task-3-configure-a-virtual-switch) VN1-SRV4 and VN1-SRV5 connected to the network adapter VNet1
1. [Create a virtual machine](#task-4-create-a-virtual-machine) on the cluster using a diffencing disk based on TinyCorePure64.vhdx with 256 MB memory.
1. [Configure the virtual machine's operating system](#task-5-configure-the-virtual-machines-operating-system) in the virtual machine to use the IP address 10.1.1.184

### Task 1: Configure nested virtualization

Perform this task on the host.

1. Open **Windows PowerShell (Admin)**.
1. In Windows PowerShell (Admin), shut down **WIN-VN1-SRV4** and **WIN-VN1-SRV5**.

    ````powershell
    $vMName = @('WIN-VN1-SRV4', 'WIN-VN1-SRV5')
    $vMName | ForEach-Object { Stop-VM -VMName $PSItem }
    ````

1. Expose virtualization extensions to **WIN-VN1-SRV4** and **WIN-VN1-SRV5**.

    ````powershell
    Set-VMProcessor -VMName $vMName -ExposeVirtualizationExtensions $true
    ````

1. Enable MAC address spoofing for **WIN-VN1-SRV4** and **WIN-VN1-SRV5**.

    ````powershell
    Get-VMNetworkAdapter -VMName $vMName |
    Set-VMNetworkAdapter -MacAddressSpoofing On
    ````

1. Disable dynamic memory and set the startup memory to 3 GB.

    ````powershell
    Set-VM -VMName $vMName -StaticMemory -MemoryStartupBytes 3GB
    ````

1. Start the virtual machines.

    ````powershell
    Start-VM -VMName $vMName
    ````

### Task 2: Install Hyper-V

#### Desktop Experience

Perform this task on CL1.

1. Open Server Manager.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on page **Before You Begin**, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-based installation** is selected and click **Next >**.
1. On page Server Selection, click **VN1-SRV4.ad.adatum.com** and click **Next >**.
1. On page Server Roles, activate **Hyper-V**
1. In Add features that are required for Hyper-V, click **Add Features**.
1. In **Add Roles and Features Wizard**, on page **Server Roles**, click **Next >**.
1. On page Features, click **Next >**.
1. On page Hyper-V, click **Next >**.
1. On page Virtual Switches, click **Next >**.
1. On page Virtual Machine Migration, click **Next >**.
1. On page Default Stores, in **Default location for virtual hard disk files**, type **C:\\ClusterStorage\\Volume*x*\\Hyper-V\\Virtual Hard Disks**, where x is the volume number you recorded for the 80 GB disk in the previous exercise. In **Default location for virtualmachine configuration files**, type **C:\\ClusterStorage\\Volume*x*\\Hyper-V**, where x is the volume number you recorded for the 80 GB disk in the previous exercise. Click **Next >**.
1. On page Confirmation, activate **Restart the destination server automatically if required** and click **Install**.
1. On  page **Results**, wait for the installation to succeed, then click **Close**.

Repeat the steps of this task to install the role on **VN1-SRV5**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install **Hyper-V** on **VN1-SRV4** and **VN1-SRV5**.

    ````powershell
    $computerName = 'VN1-SRV4', 'VN1-SRV5'
    Invoke-Command -ComputerName $computerName -ScriptBlock {
        Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    }
    ````

1. On **VN1-SRV4** and **VN1-SRV5**, set the default stores to the cluster shared volume of 80 GB capacity, you recorded in the previous exercise.

    ````powershell
    $volumeNumber = 4 # Replace with the volume number recorded for 80 GB disk
    Set-VMHost `
        -ComputerName $computerName `
        -VirtualHardDiskPath `
            "C:\ClusterStorage\Volume$volumeNumber\Hyper-V\Virtual Hard Disks" `
        -VirtualMachinePath "C:\ClusterStorage\Volume$volumeNumber\Hyper-V"
    ````

### Task 3: Configure a virtual switch

Perform this task on CL1.

1. Open **Terminal**.
1. On **VN1-SRV4** and **VN1-SRV5**, create an external switch connected to the **VNet1** network adapter. Allow the management OS to use the adapter.

    ````powershell
    $computerName = 'VN1-SRV4', 'VN1-SRV5'
    New-VMSwitch `
        -ComputerName $computerName `
        -Name External `
        -NetAdapterName VNet1 `
        -AllowManagementOS $true
    ````

### Task 4: Create a virtual machine

Perform this task on CL1.

1. Open **File Explorer**.
1. In File Explorer, copy **\\\\VN1-SRV4\\C$\\LabResources\\TinyCorePure64.vhdx** to **\\\\VN1-SRV4\\C$\\ClusterStorage\\Volume4\\Hyper-V\\Virtual Hard Disks**. Replace 4 with the volume number of the 80 GB disk.
1. Rename **\\\\VN1-SRV4\\C$\\ClusterStorage\\Volume*x*\\Hyper-V\\Virtual Hard Disks\\TinyCorePure64.vhdx** to **VN1-SRV23.vhdx**
1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST1.ad.adatum.com** and click **Roles**.
1. In **Failover Cluster Manager**, in the context-menu of **Roles**, click **Virtual Machines...**, **New Virtual Machine...**.
1. In New Virtual Machine, click **VN1-SRV4** and click **OK**.
1. In New Virtual Machine Wizard, on page Before You Begin, click **Next >**.
1. On page Specify Name and Location, in **Name**, type **VN1-SRV23** and click **Next >**.
1. On page Specify Generation, click **Generation 1** and click **Next >**.
1. On page Assign Memory, in **Startup memory**, type **256** and click **Next >**.
1. On page Configure Networking, in **Connection**, click **External** and click **Next >**.
1. On page Connect Virtual Hard Disk, click **Use an existing virtual hard disk** and click **Browse...**.
1. In Open, click **VN1-SRV23.vhdx** and click **Open**.
1. In **New Virtual Machine Wizard**, on page **Connect Virtual Hard Disk**, click **Next >**.
1. On page Completing the New Virtual Machine Wizard, click **Finish**.
1. In the **High Availability Wizard**, on page **Summary**, click **Finish**.
1. In **Failover Cluster Manager**, under **Roles (1)**, in the context-menu of **VN1-SRV23**, click **Start**.

### Task 5: Configure the virtual machine's operating system

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST1.ad.adatum.com** and click **Roles**.
1. Under Roles (1), in the context-menu of **VN1-SRV23**, click **Connect...**.
1. In VN1-SRV23 on VN1-SRV4 - Virtual Machine Connection, click on the desktop, **System tools**, **ControlPanel**.
1. In ControlPanel, click **Network**.
1. In Network, under **IP Address**, type  **10.1.1.184**. Under **Gateway**, type **10.1.1.1**. Under **NameServers**, type **10.1.1.8**. Ensure that under **Save Configuration**, **Yes** is selected. Click **Apply** and click **Exit**.

### Task 6: Compare live migration and quick migration

Perform this task on CL1.

1. Open **Terminal**.
1. In Terminal, test the connection to 10.1.1.184 or a long period.

    ````powershell
    Test-Connection -ComputerName 10.1.1.184 -Count 1000
    ````

    > You should get a response every few seconds.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST1.ad.adatum.com** and click **Roles**.

    Note the current **Owner Node** of **VN1-SRV23**, probably VN1-SRV4.

1. In the context-menu of **VN1-SRV23**, click **Connect...**
1. Switch to **Failover Cluster Manager**.
1. In the context-menu of **VN1-SRV23**, click **Move**, **Live Migration**, **Select Node...**
1. In Move Virtual Machine, click the node, which is not the owner node, probably VN1-SRV5, and click **OK**.

    While the virtual machine moves, observe the running connection test and the Virtual Machine Connection.

    > The connection tests continue running without any error.

    > The Virtual Machine Connection to VN1-SRV23 will stay open and connected.

1. In the context-menu of **VN1-SRV23**, click **Move**, **Quick Migration**, **Select Node...**
1. In Move Virtual Machine, click the node, which was the original owner node, probably VN1-SRV4, and click **OK**.

    While the virtual machine moves, observe the running connection test and the Virtual Machine Connection.

    > The connection tests will throw a few errors before resuming to normal.

    > The Virtual Machine Connection to VN1-SRV23 will be dropped. However, you will be able to reconnect afert a few seconds.

## Exercise 4: Install Windows Admin center on a failover cluster

1. [Download the install script](#task-1-download-the-install-script) from <https://learn.microsoft.com/en-us/windows-server/manage/windows-admin-center/deploy/high-availability#prerequisites> and copy it to VN1-SRV4
1. [Uninstall Windows Admin Center](#task-2-uninstall-windows-admin-center) from VN1-SRV4
1. [Request and export a certificate](#task-3-request-and-export-a-certificate) for Windows Admin Center on VN1-SRV4 using the WebServerExportable template
1. [Install Windows Admin Center with high availability](#task-4-install-windows-admin-center-with-high-availability) on VN1-SRV4 with the IP address 10.1.1.34 and using the 10 GB volume
1. [Verify Windows Admin Center installation](#task-5-verify-windows-admin-center-installation)

    > Which roles were added to the failover cluster?

    > Which resources are used by the failover cluster?

    > How could you change the IP address of Windows Admin Center?

    > Does Windows Admin Center work?

### Task 1: Download the install script

Perform this task on CL1.

1. Open **Microsoft Edge**.
1. In Microsoft Edge, navigate to <https://learn.microsoft.com/en-us/windows-server/manage/windows-admin-center/deploy/high-availability#prerequisites>.
1. On page Deploy Windows Admin Center with high availability, under **Prerequisites**, click the link **Windows Admin Center HA Script zip file** (in the third bullet point).
1. Extract the downloaded file to **\\\\vn1-srv4\\c$\\ClusterStorage\\Volumex**. Replace x with the number of the volume with 10 GB capacity, e.g. 1.

### Task 2: Uninstall Windows Admin Center

Perform this task on VN1-SRV4.

1. In SConfig, enter **15**.
1. Uninstall Windows Admin Center.

    ````powershell
    msiexec.exe /uninstall C:\LabResources\WindowsAdminCenter.msi /quiet
    ````

1. Remove the DNS A record admincenter.

    ````powershell
    Remove-DnsServerResourceRecord `
        -ZoneName ad.adatum.com `
        -RRType A `
        -Name admincenter `
        -ComputerName VN1-SRV1
    ````

### Task 3: Request and export a certificate

Perform this task on VN1-SRV4.

1. In SConfig, enter **15**.
1. Request a certificate using the template **WebServerExportable** for **admincenter.ad.adatum.com**.

    ````powershell
    $hostname = 'admincenter'
    $zoneName = 'ad.adatum.com'
    $fQDN = "$hostname.$zoneName"
    $enrollmentResult = Get-Certificate `
        -Template WebServerExportable `
        -SubjectName "CN=$fQDN" `
        -DnsName $hostname, $fQDN `
        -CertStoreLocation Cert:\LocalMachine\My\
    ````

1. Read a password for the PFX file.

    ````powershell
    $password = Read-Host -Prompt 'Password for PFX file' -AsSecureString
    ````

1. At the prompt Password for PFX file, enter a secure password and take a note.
1. Export the certificate to a PFX file. Store the file on the CSV with 10 GB capacity.

    ````powershell
    # Replace x with the volume with 10 GB capacity, e.g., 1
    $filePath = 'C:\ClusterStorage\Volumex\admincenter.ad.adatum.com.pfx'

    Export-PfxCertificate `
        -Password $password `
        -FilePath $filePath `
        -Cert $enrollmentResult.Certificate

On VN1-SRV4, leave everything open for the next task.

### Task 4: Install Windows Admin Center with high availability

Perform this task on VN1-SRV4.

1. Unblock the installation script, so that it can be executed without changing the execution policy.

    ````powershell
    # Replace x with the volume with 10 GB capacity, e.g., 1
    Unblock-File C:\ClusterStorage\Volumex\Install-WindowsAdminCenterHA.ps1
    ````

1. Install Windows Admin Center with high availability using the CSV with 10 GB capacity and the static address 10.1.1.34.

    ````powershell
    $staticAddress = '10.1.1.34'
    # Replace x with the volume with 10 GB capacity, e.g., 1
    C:\ClusterStorage\Volumex\Install-WindowsAdminCenterHA.ps1 `
        -clusterStorage C:\ClusterStorage\Volume1\ `
        -clientAccessPoint $hostName `
        -staticAddress 10.1.1.34 `
        -msiPath C:\LabResources\WindowsAdminCenter.msi `
        -certPath $filePath `
        -certPassword $password
    ````

    This will take about 10 minutes.

1. On **VN1-SRV1**, add a DNS host record for the admincenter.

    ````powershell
    Add-DnsServerResourceRecordA `
        -Name $hostname `
        -IPv4Address $staticAddress `
        -ComputerName VN1-SRV1 `
        -ZoneName $zoneName
    ````

### Task 5: Verify Windows Admin Center installation

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST1.ad.adatum.com** and click **Roles**.

    > The role admincenter should have been added.

1. In Roles, click **admincenter**.
1. In the bottom pane, click the tab **Resources**.
1. On tab Resources, expand **Name: admincenter**

    > Windows Admin center uses a network name and an IP address as additonal resources.

1. Double click **IP Address: 10.1.1.34**.
1. In IP Address: 10.1.1.34 Properties, review the options and click **Cancel**.
1. Open **Microsoft Edge**.
1. In Microsoft Edge, navigate to <https://admincenter>.

    > Admin Center should load.

## Exercise 5: Install File Server on a failover cluster

1. [Install the File Server](#task-1-install-the-file-server-role) role on VN1-SRV4

    Note: On VN1-SRV5, the file server role was installed in a previous lab already.

1. [Configure the File Server role on the failover cluster](#task-2-configure-the-file-server-role-on-the-failover-cluster) with the name VN1-CLST1-FS and the IP address 10.1.1.35 using the 100 MB disk

### Task 1: Install the File Server role

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before You Begin, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-basedd installation** is selected and click **Next >**.
1. On page Server Selection, click **VN1-SRV4.ad.adatum.com** and click **Next >**.
1. On page Server Roles, expand **File and Storage Services (1 of 12 installed)**, **File and iSCSI Services**, and activate **File Server** and click **Next >**.
1. On page Features, click **Next >**.
1. On page **Confirmation**, click **Install**.
1. On page **Results**, click **Close**.
1. In **Server Manager**, in **File and Storage Services**, click **Servers**.

#### PowerShell

Peform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. Install the windows feature **File Server** on **VN1-SRV4**.

    ````powershell
    Install-WindowsFeature `
      -ComputerName 'VN1-SRV4' `
      -Name FS-FileServer `
      -IncludeManagementTools
    ````

### Task 2: Configure the File Server role on the failover cluster

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST1.ad.adatum.com** and click **Roles**.
1. In the context-menu of **Roles**, click **Configure Role...**.
1. In the High Availability Wizard, on page Before You Begin, click **Next >**.
1. On page Select Role, click **File Server** and click **Next >**.
1. On page File Server Type, ensure **File Server for general use** is selected and click **Next >**.
1. On page Client Access point, in **Name**, type **VN1-CLST1-FS**. Under **Address**, beside **10.1.1.0/24**, type **10.1.1.35**. Click **Next >**.
1. On page Select Storage expand the available disks and activate the disk with a capacity of about 100 MB. Click **Next >**.
1. On page Confirmation, click **Next >**.
1. On page Summary, click **Finish**.

## Exercise 6: Test failover

1. [Monitor cluster services](#task-1-monitor-cluster-services) by testing the network connection to 10.1.1.184 continously, monitoring the console of VN1-SRV23 and viewing the Windows Admin Center web site

1. [Simulate a failure](#task-2-simulate-a-failure) by turning off the node running the roles

    > What happens to the Windows Admin Center?

    > What happens to the virtual machine?

### Task 1: Monitor cluster services

Perform this task on CL1.

1. Open Microsoft Edge and navigate to <https://admincenter>.

    > Windows Admin Center should load.

1. Open **Terminal**.
1. In Terminal, test the connection to 10.1.1.184 or a long period.

    ````powershell
    Test-Connection -ComputerName 10.1.1.184 -Count 1000
    ````

    > You should get a response every few seconds.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST1.ad.adatum.com** and click **Roles**. Take a note of the **Owner Node** of each service. If the Owner Node is not the same for all services, perform these additional steps:

    1. In the context-menu of the service, click **Move**, **Select Node...** or **Move**, **Live Migration**, **Select Node...**.
    1. In the following dialog, select the other node and click **OK**.

1. In Roles, in the context-menu of **VN1-SRV23**, click **Connect...**.
1. In VN1-SRV23 on VN1-SRV4 - Virtual Machine Connection, open an application, e.g. Editor.
1. Arrange the window of **VN1-SRV23 on VN1-SRV4 - Virtual Machine Connection** so, that you can monitor it, while continuing with the next steps.

Keep all windows open for the next task.

### Task 2: Simulate a failure

Perform this task on the host.

1. Arrange the **VN1-CL1 on *host* Virtual Machine Connection** window so, that you can monitor it while continuing with the next steps.
1. Open **Hyper-V-Manager**.
1. In Hyper-V-Manager, in the context-menu of the virtual computer, running the cluster roles, click **Turn off...**.

> After a few seconds, in **Failover Cluster Manager**, **Roles** the **amincenter** should move to the other node and the Windows Admin Center should stay available.

> After a few seconds, **VN1-SRV23** will become **Unmonitored**. After some minutes, the virtual machine, will start on **VN1-SRV5**. The network connection should work again, but you will need to reconnect.

## Exercise 7: Use cluster-aware updating

### Task 1: Configure cluster-aware updating

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, in the context-menu of **VN1-CLST1.ad.adatum.com**, click **More Actions**, **Cluster-Aware Updating**.
1. In VN1-CLST1 - Cluster-Aware Updating, click **Preview updates for this cluster**.
1. In VN1-CLST1 - Preview Updates, click **Generate Update Preview List**.

    If there are any updates, review the list.

1. Click **Close**.
1. In **VN1-CLST1 - Cluster-Aware Updating**, click **Analyze cluster readiness**.
1. Review the results and click **Close**.
1. In **VN1-CLST1 - Cluster-Aware Updating**, click **Create or modify Updating Run Profile**.
1. In Updating Run Profile Editor, review the options and click **Close**.

### Task 2: Run cluster-aware updating

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, in the context-menu of **VN1-CLST1.ad.adatum.com**, click **More Actions**, **Cluster-Aware Updating**.
1. In VN1-CLST1 - Cluster-Aware Updating, click **Apply updates to this cluster**.
1. In VN1-CLST1 - Cluster-Aware Updating Wizard, on page Getting Started, click **Next >**.
1. On page Advanced Options, click **Next >**.
1. On page Additional Update Options, click **Next >**.
1. On page Confirmation, click **Update**.
1. On page Completion, click **Close**.
1. In **VN1-CLST1 - Cluster-Aware Updating**, observe the update progress.

While the updates progress, you may want to observe the nodes in **Failover Cluster Manager**. Moreover, you may open the console on VN1-SRV4 and VN1-SRV5 to observe the reboots. You may continue to the next task, while the updates are applied. The update process wil take more than 40 minutes in total.

### Task 3: Configure cluster self-updating options

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, in the context-menu of **VN1-CLST1.ad.adatum.com**, click **More Actions**, **Cluster-Aware Updating**.
1. In VN1-CLST1 - Cluster-Aware Updating, click **Configure cluster self-updating options**.
1. In VN1-CLST1 - Configure Self-Updating Options Wizard, on page Getting Startetd, click **Next >**.
1. On page Add CAU Clustered Role with Self-Updating Enabled, activate **Add the CAU clustered role, with self-updating mode enabled**, to this cluster and click **Next >**.
1. On page Specify self-updating schedule, click **Next >**.
1. On page Advanced Options, click **Next >**.
1. On page Additional Update Options, activate **Give me recommended updates the same way that I receive important updates** and click **Next >**.
1. On page Confirmation, click **Apply**.
1. On page Completion, click **Close**.
