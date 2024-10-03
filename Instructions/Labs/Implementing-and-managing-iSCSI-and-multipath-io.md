# Lab: Implementing and managing iSCSI and Multipath I/O

## Required VMs

* CL1
* VN1-SRV1
* VN1-SRV4
* VN1-SRV5
* VN1-SRV10

## Setup

1. On **CL1**, sign in as **ad\Administrator**.
1. On **VN1-SRV5**, sign in as **.\Administrator**.

## Introduction

Adatum wants to use iSCSI for a fault-tolerance cluster. For this reason, you will configure an iSCSI target server and test it with an iSCSI initiator. The connection between the initiator and the target should be fault-tolerant and scalable. Therefore, you should use Multipath I/O.

## Exercises

1. [Configure an iSCSI target server](#exercise-1-configure-an-iscsi-target-server)
1. [Configure the iSCSI initiator](#exercise-2-configure-the-iscsi-initiator)
1. [Examine performance and fault tolerance of Multipath I/O](#exercise-3-examine-performance-and-fault-tolerance-of-multipath-io)

## Exercise 1: Configure an iSCSI target server

1. [Install the iSCSI Target Server role](#task-1-install-the-iscsi-target-server-role) on VN1-SRV10
1. [Configure an iSCSI target](#task-2-configure-an-iscsi-target) named VN1-CLST1 with two virtual disks of 10 GB each, named VN1-CLST1-Quorum and VN1-CLST1-CSV for the initiators on VN1-SRV4 and VN1-SRV5

### Task 1: Install the iSCSI Target Server role

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before You Begin, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-basedd installation** is selected and click **Next >**.
1. On page Server Selection, click **VN1-SRV10.ad.adatum.com** and click **Next >**.
1. On page Server Roles, expand **File and Storage Services (1 of 12 installed)**, **File and iSCSI Services**, and activate **iSCSI Target Server**.
1. In the dialog **Add features that are required for iSCSI Target Server?**, click **Add Features**
1. On page **Server Roles**, click **Next >**.
1. On page Features, click **Next >**.
1. On page **Confirmation**, activate the checkbox **Restart the destination server automatically if required** and click **Install**.
1. On page **Results**, click **Close**.

#### PowerShell

Peform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. Install the windows feature **iSCSI Target Server** on **VN1-SRV10**.

    ````powershell
    Install-WindowsFeature `
      -ComputerName VN1-SRV10 `
      -Name FS-iSCSITarget-Server `
      -IncludeManagementTools `
      -Restart
    ````

### Task 2: Configure an iSCSI target

#### Desktop Experience

Perform these steps on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the left pange, click **File and Storage Services**.
1. In File and Storage Services, click **iSCSI**.
1. In iSCSI, in the right pane, in the drop-down **Tasks**, click **New iSCSI Virtual Disk...**.
1. In New iSCSI Virtual Disk Wizard, on page iSCSI Virtual Disk Location, under **Server**, click **VN1-SRV10**. Under **Storage location**, click **D:**. Click **Next >**.
1. On page Specify iSCSI virtual disk name, in **Name**, type **VN1-CLST1-Quorum** and click **Next >**.
1. On page Specify iSCSI virtual disk size, in **Size**, type **1** and ensure **GB** is selected. Ensure, **Dynamically expanding** is selected and click **Next >**.
1. On page Assign iSCSI target, click **Next >**.
1. On page Specify target name, in **Name**, type **VN1-CLST1** and click **Next >**.
1. On page Specify access servers, click **Add...**.
1. In Add initiator ID, ensure **Query initiator computer for ID** is selected and click **Browse...**.
1. In Select Computer, under **Enter the object name to select**, type **VN1-SRV4** and click **OK**.
1. In **Add initiator ID**, click **OK**.
1. In **New iSCSI Virtual Disk Wizard**, on page **Specify access servers**, click **Add...**.
1. In Add initiator ID, ensure **Query initiator computer for ID** is selected and click **Browse...**.
1. In Select Computer, under **Enter the object name to select**, type **VN1-SRV5** and click **OK**.
1. In **Add initiator ID**, click **OK**.
1. In **New iSCSI Virtual Disk Wizard**, on page **Specify access servers**, click **Next >**.
1. On page Enable Authentication, click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.
1. In **Server Manager**, in **iSCSI**, in the right pane, in the drop-down **Tasks**, click **New iSCSI Virtual Disk...**.
1. In New iSCSI Virtual Disk Wizard, on page iSCSI Virtual Disk Location, under **Server**, click **VN1-SRV10**. Under **Storage location**, click **D:**. Click **Next >**.
1. On page Specify iSCSI virtual disk name, in **Name**, type **VN1-CLST1-CSV1** and click **Next >**.
1. On page Specify iSCSI virtual disk size, in **Size**, type **10** and ensure **GB** is selected. Ensure, **Dynamically expanding** is selected and click **Next >**.
1. On page Assign iSCSI target, ensure **Existing iSCSI target** and **vn1-clst1** is selected and click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.
1. In **Server Manager**, in **iSCSI**, in the right pane, in the drop-down **Tasks**, click **New iSCSI Virtual Disk...**.
1. In New iSCSI Virtual Disk Wizard, on page iSCSI Virtual Disk Location, under **Server**, click **VN1-SRV10**. Under **Storage location**, click **D:**. Click **Next >**.
1. On page Specify iSCSI virtual disk name, in **Name**, type **VN1-CLST1-CSV2** and click **Next >**.
1. On page Specify iSCSI virtual disk size, in **Size**, type **80** and ensure **GB** is selected. Ensure, **Dynamically expanding** is selected and click **Next >**.
1. On page Assign iSCSI target, ensure **Existing iSCSI target** and **vn1-clst1** is selected and click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.
1. In **Server Manager**, in **iSCSI**, in the right pane, in the drop-down **Tasks**, click **New iSCSI Virtual Disk...**.
1. In New iSCSI Virtual Disk Wizard, on page iSCSI Virtual Disk Location, under **Server**, click **VN1-SRV10**. Under **Storage location**, click **D:**. Click **Next >**.
1. On page Specify iSCSI virtual disk name, in **Name**, type **VN1-CLST1-Shares** and click **Next >**.
1. On page Specify iSCSI virtual disk size, in **Size**, type **100** and ensure **MB** is selected. Ensure, **Dynamically expanding** is selected and click **Next >**.
1. On page Assign iSCSI target, ensure **Existing iSCSI target** and **vn1-clst1** is selected and click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.

#### PowerShell

Perform these steps on CL1.

1. Open **Terminal**.
1. In Terminal, create a directory named **iSCSIVirtualDisks** on drive **D** of **VN1-SRV10**.

   ````powershell
   $computerName = 'VN1-SRV10'
   $driveLetter = 'D'
   $name = 'iSCSIVirtualDisks'
   New-Item `
      -Path "\\$computerName\$driveLetter$\" `
      -Name $name `
      -ItemType Directory
   ````

1. In the new directory, create two new iSCSI virtual disks.

   | File name              | Size   |
   |------------------------|--------|
   | VN1-CLST1-Quorum.vhdx  | 1 GB   |
   | VN1-CLST1-CSV1.vhdx    | 10 GB  |
   | VN1-CLST1-CSV2.vhdx    | 80 GB  |
   | VN1-CLST1-Shares.vhdx  | 100 MB |

   ````powershell
   $path = "$($driveLetter):\$name"
   $diskParams = @(
      @{ Path = "$path\VN1-CLST1-Quorum.vhdx"; SizeBytes = 1GB }
      @{ Path = "$path\VN1-CLST1-CSV1.vhdx"; SizeBytes = 10GB }
      @{ Path = "$path\VN1-CLST1-CSV2.vhdx"; SizeBytes = 80GB }
      @{ Path = "$path\VN1-CLST1-Shares.vhdx"; SizeBytes = 100MB }
   )
   $iscsiVirtualDisk = $diskParams | ForEach-Object {
      New-IscsiVirtualDisk `
         -Path $PSItem.Path `
         -SizeBytes $PSItem.SizeBytes `
         -ComputerName $computerName 
   }
   ````

1. On **VN1-SRV10**, Create a new iSCSI server target with the name **VN1-CLST1** for the initiators on **VN1-SRV4** and **VN1-SRV5**.

   ````powershell
   $initiatorIdPrefix = 'IQN:iqn.1991-05.com.microsoft:'
   $initiatorIds = @(
      "$($initiatorIdPrefix)vn1-srv4.ad.adatum.com"
      "$($initiatorIdPrefix)vn1-srv5.ad.adatum.com"
   )
   $targetName = 'VN1-CLST1'
   New-IscsiServerTarget `
      -TargetName $targetName `
      -InitiatorIds $initiatorIds `
      -ComputerName $computerName
   ````

1. Add the iSCSI virtual disks to the iSCSI target.

   ````powershell
   $diskParams | ForEach-Object { 
      Add-IscsiVirtualDiskTargetMapping `
         -TargetName $targetName `
         -Path $PSItem.Path `
         -ComputerName $computerName 
      }
   ````

## Exercise 2: Configure the iSCSI initiator

1. [Install the Multipath feature](#task-1-install-the-multipath-feature) on VN1-SRV5
1. [Configure Multipath I/O](#task-2-configure-multipath-io) on VN1-SRV5
1. [Connect to the iSCSI target](#task-3-connect-to-the-iscsi-target) from VN1-SRV5 using Multipath I/O
1. [Create a volumes on the iSCSI disks](#task-4-create-volumes-on-the-iscsi-disks) according to the table below

   | Disk size | Drive letter | File system label |
   |-----------|--------------|-------------------|
   | 1 GB      | D            | Quorum            |
   | 10 GB     | E            | WAC               |
   | 80 GB     | F            | Hyper-V           |
   | 100 MB    | G            | Witness Shares    |

### Task 1: Install the Multipath feature

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before You Begin, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-basedd installation** is selected and click **Next >**.
1. On page Server Selection, click **VN1-SRV5.ad.adatum.com** and click **Next >**.
1. On page Server Roles, click **Next >**.
1. On page Features, activate **Multipath I/O** and click **Next >**.
1. On page **Confirmation**, click **Install**.
1. On page **Results**, click **Close**.

#### PowerShell

Peform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. In Terminal, install the windows feature **iSCSI Target Server** on **VN1-SRV10**.

    ````powershell
    Install-WindowsFeature `
      -ComputerName VN1-SRV5 `
      -Name MultiPath-IO `
      -IncludeManagementTools
    ````

### Task 2: Configure Multipath I/O

#### Desktop experience

Perform this task on VN1-SRV5.

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
1. In Terminal, activate the Multipath I/O support for iSCSI devices on **VN1-SRV5**.

   ````powershell
   Invoke-Command -ComputerName VN1-SRV5 -ScriptBlock {
      Enable-MSDSMAutomaticClaim -BusType iSCSI
   }
   ````

### Task 3: Connect to the iSCSI target

#### Desktop experience

Perform this task on VN1-SRV5.

1. In SConfig, enter **15**.
1. Open **MPIO properties**.

   ````shell
   iscsicpl.exe
   ````

1. In the message box The Microsoft iSCSI service is not running. The service is required to be started for iSCSI to function correctly. To start the service now and have the service start automatically each time the computer restarts, click the Yes button, click **Yes**.
1. In iSCSI Initiator Properties, on tab Targets, in the text box **Target**, type **vn1-srv10.ad.adatum.com**, and click **Quick Connect**.
1. In Quick Connect, click **Done**.
1. In **iSCSI Initiator Properties**, on tab **Targets**, click **Disconnect** and click **Connect**.
1. In Connect To Target, activate **Enable multi-path** and click on **Advanced**.
1. In Advanced Settings, in **Local adapter**, click **Microsoft iSCSI Initiator**. In **Initiator IP**, click **10.1.128.40**. In **Target portal IP**, click **10.1.128.80 / 3260**. Click **OK**.
1. In **Connect To Target**, click **OK**.
1. In **iSCSI Initiator Properties**, on tab **Targets**, click on **Connect**.
1. In Connect To Target, activate **Enable multi-path** and click on **Advanced**.
1. In Advanced Settings, in **Local adapter**, click **Microsoft iSCSI Initiator**. In **Initiator IP**, click **10.1.144.40**. In **Target portal IP**, click **10.1.144.80 / 3260**. Click **OK**.
1. In **Connect To Target**, click **OK**.
1. In **iSCSI Initiator Properties**, click **OK**.

#### PowerShell

Perform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. In Terminal, create a remote PowerShell session to **VN1-SRV5**.

   ````powershell
   Enter-PSSession VN1-SRV5
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
   $iscsiTarget = Get-IscsiTarget -IscsiTargetPortal $IscsiTargetPortal
   ````

1. Connect to the iSCSI targets.

   ````powershell
   $iscsiTarget | Connect-IscsiTarget -IsPersistent $true
   ````

1. Disconnect the connection.

   ````powershell
   $iscsiTarget | Disconnect-IscsiTarget
   ````

1. At the prompt Are you sure you want to perform this action, enter **Y**.
1. Connect to the target using multi-path. Use the IP address **10.1.128.40** on the initiator side, and **10.1.128.80** on the target side.

   ````powershell
   $iscsiTarget | Connect-IscsiTarget `
      -IsMultipathEnabled $true `
      -InitiatorPortalAddress 10.1.128.40 `
      -TargetPortalAddress 10.1.128.80 `
      -IsPersistent $true
      ````

1. Connect to the target using multi-path. Use the IP address **10.1.128.40** on the initiator side, and **10.1.144.80** on the target side.

   ````powershell
   $iscsiTarget | Connect-IscsiTarget `
      -IsMultipathEnabled $true `
      -InitiatorPortalAddress 10.1.144.40 `
      -TargetPortalAddress 10.1.144.80 `
      -IsPersistent $true
   ````

1. Close the remote PowerShell session.

   ````powershell
   Exit-PSSession
   ````

### Task 4: Create volumes on the iSCSI disks

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the left pane, click **File and Storage Services**.
1. In File and Storage Services, click **Disks**.
1. In Disks, under **DISKS**, under **VN1-SRV5 (4)**, in the context-menu of the disks with the **Bus Type** **iSCSI**, cick **Bring Online**.
1. In the message box Bring Disk Online, click **Yes**.
1. In **Server Manager**, under **VOLUMES**, click **TASKS**, **New Volume...**.
1. In New Volume Wizard, on page Before you begin, click **Next >**.
1. On page Select the server and disk, under **Server**, click **VN1-SRV5**. Under **Disk**, click the disk with **Capacity** according to an entry in the table above. Click **Next >**.
1. In the message box **Offline or Uninitialized Disk**, click **OK**.
1. In **New Volume Wizard**, on page **Size**, click **Next >**.
1. On page Assign to a drive letter or folder, ensure **Drive letter** according to the table is selected and click **Next >**.
1. On page Select file system settings, in **Volume label**, type the file system label from the table and click **Next >**.
1. On page Confirm selections, click **Create**.
1. On page Completion, click **Close**.

Repeat from step 6 for the remaining disks.

#### PowerShell

Perform this task on CL1

1. In the context menu of **Start**, click **Terminal**.
1. In Terminal, create a CIM session to **VN1-SRV5**.

   ````powershell
   $cimSession = New-CimSession -ComputerName VN1-SRV5
   ````

1. Store all physical disks on VN1-SRV5, with the bus type **iSCSI** in a variable.

   ````powershell
   $physicalDisk = Get-PhysicalDisk -CimSession $cimSession |
      Where-Object { $PSItem.BusType -eq 'iSCSI' }
   ````

1. Initialize the disks as **GPT** disks.

   ````powershell
   Initialize-Disk `
      -UniqueId $physicalDisk.UniqueId `
      -PartitionStyle GPT `
      -CimSession $cimSession
   ````

1. Store the data from the table above in a variable.

   ````powershell
   $volumeParams = @(
      @{ Size = 1GB; DriveLetter = 'D'; FileSystemLabel = 'Quorum' }
      @{ Size = 10GB; DriveLetter = 'E'; FileSystemLabel = 'WAC' }
      @{ Size = 80GB; DriveLetter = 'F'; FileSystemLabel = 'Hyper-V' }
      @{ Size = 100MB; DriveLetter = 'G'; FileSystemLabel = 'Witness Shares' }
   )
   ````

1. Create volumes and format them using the variable.

   ````powershell
   $volumeParams | ForEach-Object {
      $size = $PSItem.Size
      $physicalDisk = Get-PhysicalDisk -CimSession $cimSession  | 
         Where-Object { $PSItem.Size -eq $size }
      New-Partition `
         -DiskId $physicalDisk.UniqueId `
         -DriveLetter $PSItem.DriveLetter `
         -UseMaximumSize `
         -CimSession $cimSession |
      Format-Volume `
         -NewFileSystemLabel $PSItem.FileSystemLabel `
         -FileSystem NTFS `
         -CimSession $cimSession
   }
   ````

1. Remove the CIM session.

   ````powershell
   Remove-CimSession $cimSession
   ````

## Exercise 3: Examine performance and fault tolerance of Multipath I/O

1. [Install the File Service role](#task-1-install-the-file-service-role) on VN1-SRV5
1. [Start a continuous copy process](#task-2-start-a-continuous-copy-process) with a large file and the volume on the iSCSI target as destination
1. [Examine the performance gain of MultiPath I/O](#task-3-examine-the-performance-gain-of-multipath-io)

   > How is the traffic to the iSCSI target distributed?

1. [Examine the fault tolerance of MultiPath I/O](#task-4-examine-the-fault-tolerance-of-multipath-io)

   > What happens, if you disconnect the SAN1 network adapter?

   > What happens after you reconnect the SAN1 network adapter?

   > What happens, if you disconnect the SAN2 network adapter?

   > What happens after you reconnect the SAN2 network adapter?

### Task 1: Install the File Service role

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before You Begin, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-basedd installation** is selected and click **Next >**.
1. On page Server Selection, click **VN1-SRV5.ad.adatum.com** and click **Next >**.
1. On page Server Roles, expand **File and Storage Services (1 of 12 installed)**, **File and iSCSI Services**, and activate **File Server** and click **Next >**.
1. On page Features, click **Next >**.
1. On page **Confirmation**, click **Install**.
1. On page **Results**, click **Close**.

#### PowerShell

Peform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. Install the windows feature **File Server** on **VN1-SRV5**.

    ````powershell
    Install-WindowsFeature `
      -ComputerName VN1-SRV5 `
      -Name FS-FileServer `
      -IncludeManagementTools
    ````

### Task 2: Start a continuous copy process

Perform this task on the host.

1. In the context-menu of *Start*, click **Windows PowerShell (Admin)** or **Terminal (Administrator)**.
1. In Windows PowerShell (Admin) or Terminal, create a drive with the name **V** using the **FileSystem** provider with the root **\\\\vn1-srv5\\d$** using the credentials of **Administrator**.

   ````powershell
   New-PSDrive `
      -Name V `
      -PSProvider FileSystem `
      -Root \\vn1-srv5\f$ `
      -Credential Administrator
   ````

1. Enter the credentials of **Administrator** on VN1-SRV5.
1. Copy **C:\\Labs\\ISOs\\2022_x64_EN_Eval** from the host to **D:\\** on WIN-VN1-SRV5 in an infinite loop. Pause for 10 seconds after each iteration.

   ````powershell
   while ($true) { 
      Copy-Item `
         -Path 'C:\Labs\ISOs\2022_x64_EN_Eval.iso' -Destination 'V:\' -Force
   }
   ````

Leave **Windows PowerShell (Admin)** or **Terminal** open with the copy process running, while continuing with the next tasks.

### Task 3: Examine the performance gain of MultiPath I/O

Perform this task on VN1-SRV5.

1. In SConfig, enter **15**.
1. Start the Task Manager.

   ````powershell
   taskmgr.exe
   ````

1. In Task Manager, click **More details**.
1. Click the tab **Performance**.

   > The distribution of traffic between the ethernet adapters SAN1 and SAN2 should be close to 50:50. The used bandwidth on each of the SAN adapters should be around half of the bandwidth on the adapter VNet1.

Leave Task Manager and the connection to the virtual computer open, so that you can monitor the the network performance while continuing with the next tasks.

### Task 4: Examine the fault tolerance of MultiPath I/O

Perform this task on the host.

1. Open another instance of **Windows PowerShell (Admin)** or, in Terminal, split the current tab horizontally by pressing ALT + SHIFT + -.
1. In the new instance of Windows PowerShell (Admin), or in the bottom pane of Terminal, while the copy process is running, disconnect the network adapter connected to the switch named **SAN1**.

   ````powershell
   $vMName = 'WIN-VN1-SRV5'
   $switchName = 'SAN1'
   Get-VMNetworkAdapter -VMName $vMName | 
   Where-Object { $PSItem.SwitchName -eq $switchName } |
   Disconnect-VMNetworkAdapter
   ````

   > After a few seconds, in Task Manager on VN1-SRV5, you should see that the copy process continues.  The load on SAN2 will be increased and closer to the load on VNet1.

1. Reconnect the disconnected network adapter.

   ````powershell
   Get-VMNetworkAdapter -VMName $vMName | 
   Where-Object { -not $PSItem.Connected } | 
   Connect-VMNetworkAdapter -SwitchName $switchName
   ````

   > After a moment, in Task Manager on VN1-SRV5, the load between SAN1 and SAN2 will be distributed evenly again.

1. Disconnect the network adapter connected to the switch named **SAN2**.

   ````powershell
   $switchName = 'SAN2'
   Get-VMNetworkAdapter -VMName $vMName | 
   Where-Object { $PSItem.SwitchName -eq $switchName } |
   Disconnect-VMNetworkAdapter
   ````

   > After a few seconds, in Task Manager on VN1-SRV5, you should see that the copy process continues.  The load on SAN1 will be increased and closer to the load on VNet1.

1. Reconnect the disconnected network adapter.

   ````powershell
   Get-VMNetworkAdapter -VMName $vMName | 
   Where-Object { -not $PSItem.Connected } | 
   Connect-VMNetworkAdapter -SwitchName $switchName
   ````

   > After a moment, in Task Manager on VN1-SRV5, the load between SAN1 and SAN2 will be distributed evenly again.

1. In the other instance of **Windows PowerShell (Admin)** or the upper pane of Terminal, stop the copy process by pressing CTRL + C.
1. Delete all files from **V:**

   ````powershell
   Remove-Item v:\* -Force
   ````

1. Remove the PowerShell drive **V**.

   ````powershell
   Remove-PSDrive V
   ````
