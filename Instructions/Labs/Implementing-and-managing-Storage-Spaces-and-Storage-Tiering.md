# Lab: Implementing and managing Storage Spaces and storage tiering

## Required VMs

* VN1-SRV1
* VN1-SRV10
* CL1

## Setup

On CL1 sign in as ad\administrator.

## Introduction

Adatum wants to evaluate the resiliency of a storage pool. Moreover, Adatum wants to create a tiered virtual disk in a storage pool to make optimal use of the performance of SSDs and the low cost of magnetic hard drives.

## Exercises

1. [Configuring a storage pool](#exercise-1-configuring-a-storage-pool)
1. [Testing storage pool resilience](#exercise-2-testing-storage-pool-resilience)
1. [Storage Tiering](#exercise-3-storage-tiering)

## Exercise 1: Configuring a storage pool

1. [Create a storage Pool](#task-1-create-a-storage-pool) on VN1-SRV10 using the 1 TB disks.
1. [Create a virtual disk](#task-2-create-a-virtual-disk) in the storage pool with three-way-mirroring and 1 TB capacity.

### Task 1: Create a storage pool

#### Desktop Experience

Perform these steps on CL1.

1. Open **Server Manager**.
1. In Server Manager, click on **File and Storage Services**
1. In File and Storage Services, click on **Storage Pools**.
1. In Storage Pools, under **STORAGE POOLS**, click **Tasks**, **New Storage Pool...**.
1. In new Storage Pool Wizard, on page Before you begin, click **Next >**.
1. On page Specify a storage pool name and subsystem, in **Name**, type **Pool1**. Under **Select the group of available disks (also known as a primordial pool) that you want to use**, click **VN1-SRV10**. Click **Next >**.
1. On page Select physical disks for the storage pool, activate all disks with a **Capacity** of **1,00 TB** and click **Next >**.
1. On page Confirmation, click **Create**.
1. On page results, click **Close**.

#### Powershell

Perform these steps on CL1.

1. Open **Terminal**.
1. In Terminal, create a CIM session to **VN1-SRV10**.

   ````powershell
   $cimSession = New-CimSession -ComputerName VN1-SRV10
   ````

1. On VN1-SRV10, select the physical disks available for a storage pool with a size of **1 TB**.

   ````powershell
   $physicalDisks = Get-PhysicalDisk -CanPool $true -CimSession $cimSession |
      Where-Object { $PSItem.Size -eq 1TB }
   ````

1. Create a new Storage Pool **Pool1** using the physical disks you selected.

   ````powershell
   New-StoragePool `
      -FriendlyName 'Pool1' `
      -PhysicalDisks $physicalDisks `
      -StorageSubSystemFriendlyName 'Windows Storage*' `
      -CimSession $cimSession
   ````

1. Remove the CIM session.

   ````powershell
   Remove-CimSession $cimSession
   ````

### Task 2: Create a virtual disk

#### Desktop Experience

Perform these steps on CL1.

1. Open **Server Manager**.
1. In Server Manager, click on **File and Storage Services**
1. In File and Storage Services, click on **Storage Pools**.
1. Under **STORAGE POOLS**, click **TieredPool1**.
1. In Storage Pools, under **VIRTUAL DISKS**, click **Tasks**, **New Virtual Disk...**
1. In Select the storage pool, click **Pool1** and click **OK**.
1. In new Virtual Disk Wizard, on page Before You Begin, click **Next >**.
1. On page Virtual Disk Name, in **Name**, type **Data** and click **Next >**.
1. On page Enclosure Awareness, click **Next >**.
1. On page Storage layout, under **Layout**, click **Mirror** and click **Next >**.
1. On page Resiliency Settings, click **Three-way-mirror** and click **Next >**.
1. On page Provisioning, click **Thin** and click **Next >**.
1. On page Specify size, under **Specify size**, type **1** and click **TB**. Click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.
1. In **New Volume Wizard**, on page Before You Begin, click **Next >**.
1. On page Server and Disk, under **Server**, ensure **VN1-SRV10** is selected. Under Disk, ensure the **Virtual Disk** **Data** is selected. Click **Next >**.
1. On page Size, ensure, in **Volume size**, **1024** is filled in and **GB** is selected. Click **Next >**.
1. On page Drive Letter or Folder, ensure, beside **Drive letter**, **D** is selected and click **Next >**.
1. On page File System settings, beside **File System**, click **ReFS**. In **Volume label**, type **Data**. Click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.

#### PowerShell

Perform these steps on CL1.

1. Open **Terminal**.
1. In Terminal, create a CIM session to **VN1-SRV10**.

   ````powershell
   $cIMSession = New-CimSession -ComputerName VN1-SRV10
   ````

1. On VN1-SRV10, select the storage pool **Pool1** and store it in a variable.

   ````powershell
   $storagePool = Get-StoragePool -FriendlyName 'Pool1' -CimSession $cIMSession
   ````

1. In the storage pool, create a new three-way-mirror virtual disk of **1 TB** in size. Use thin provisioning. Store the new virtual disk in a variable.

   ````powershell
   $virtualDisk = $storagePool | New-VirtualDisk `
      -FriendlyName 'Data' `
      -ResiliencySettingName 'Mirror'`
       -NumberOfDataCopies 3 `
       -ProvisioningType Thin `
       -Size 1TB `
       -CimSession $cIMSession
   ````

1. Create a new volume on the new virutal disk with the ReFS file system and assign it to the drive letter D.

   ```powershell
   New-Volume `
      -FriendlyName 'Data' `
      -FileSystem ReFS `
      -DriveLetter D `
      -DiskUniqueId $virtualDisk.UniqueId `
      -CimSession $cIMSession
   ````

1. Remove the CIM session.

   ````powershell
   Remove-CimSession $cIMSession
   ````

## Exercise 2: Testing storage pool resilience

1. [Install the File Server role](#task-1-install-the-file-server-role) on VN1-SRV10
1. [Start a continuous copy process](#task-2-start-a-continuous-copy-process) from the host to the virtual disk on VN1-SRV10
1. [Simulate a disk failure](#task-3-simulate-a-disk-failure) on VN1-SRV10

   > Does the copy process continue?

1. [Validate the results from a failed disk](#task-4-validate-the-results-from-a-failed-disk)

   > What is the status of the storage pool, the virtual disk and the physical disks on VN1-SRV10?

1. [Simulate another disk failure](#task-5-simulate-another-disk-failure)

   > Does the copy process still continue?

1. [Add new virtual hard disks](#task-6-add-new-virtual-hard-disks) to VN1-SRV10 as replacement of the failed disks
1. [Repair the storage pool](#task-7-repair-the-storage-pool) on VN1-SRV10
1. [Simulate a fatal failure](#task-8-simulate-a-fatal-failure) by removing three physical disks in the storage pool of VN1-SRV10

   > What happens to the copy process?

1. [Remove the storage pool](#task-9-remove-the-storage-pool) from VN1-SRV10

### Task 1: Install the File Server role

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before You Begin, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-based installation** is selected and click **Next >**.
1. On page Server Selection, click **VN1-SRV10.ad.adatum.com** and click **Next >**.
1. On page Server Roles, expand **File and Storage Services (1 of 12 installed)**, **File and iSCSI Services**, and activate **File Server** and click **Next >**.
1. On page Features, click **Next >**.
1. On page **Confirmation**, click **Install**.
1. On page **Results**, click **Close**.
1. In **Server Manager**, in **File and Storage Services**, click **Servers**.
1. In Servers, under **SERVERS**, click **VN1-SRV10**.
1. Under **SERVICES** (you might have to scroll down), in the context-menu of **LanmanServer**, click **Restart services**.

#### PowerShell

Peform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. Install the windows feature **File Server** on **VN1-SRV10**.

    ````powershell
    $computerName = 'VN1-SRV10'
    Install-WindowsFeature `
      -ComputerName $computerName `
      -Name FS-FileServer `
      -IncludeManagementTools
    ````

1. Restart the service LanmanServer on VN1-SRV10.

   ````powershell
   Invoke-Command -ComputerName $computerName -ScriptBlock { 
      Restart-Service LanmanServer
   }

### Task 2: Start a continuous copy process

Perform this task on the host.

1. In the context-menu of *Start*, click **Windows PowerShell (Admin)** or **Terminal (Administrator)**.
1. In Windows PowerShell (Admin) or Terminal, create a drive with the name **V** using the **FileSystem** provider with the root **\\\\vn1-srv10\\d$** using the credentials of **Administrator**.

   ````powershell
   New-PSDrive `
      -Name V `
      -PSProvider FileSystem `
      -Root \\vn1-srv10\d$ `
      -Credential Administrator
   ````

1. Enter the credentials of **Administrator** on VN1-SRV10.
1. Copy **C:\\Labs\\ISOs\\2022_x64_EN_Eval** from the host to **D:\\** on WIN-VN1-SRV5 in an infinite loop. Pause for 10 seconds after each iteration.

   ````powershell
   while ($true) { 
      Copy-Item `
         -Path 'C:\Labs\ISOs\2022_x64_EN_Eval.iso' -Destination 'V:\' -Force
   }
   ````

Leave the instances **Windows PowerShell (Admin)** or **Terminal** open with the copy process running while continuing with the next tasks.

### Task 3: Simulate a disk failure

Perform this task on the host.

1. Open a new instance of **Windows PowerShell (Admin)** or, in Terminal, open a new tab.
1. Get the hard disks connected to VM WIN-VN1-SRV10 and store them in a variable.

   ````powershell
   $vMName = 'WIN-VN1-SRV10'
   $vhd = Get-VMHardDiskDrive -VMName $vMName | Get-VHD | Where-Object { 
      $PSItem.Size -eq 1TB
   }
   $vMHardDiskDrive = Get-VMHardDiskDrive -VMName $vMName | Where-Object {
      $PSItem.Path -in $vhd.Path 
   }
   ````

1. Remove one of the hard disks from the virtual machine. You may use a different index number of your choice.

   ````powershell
   $vMHardDiskDrive[0] | Remove-VMHardDiskDrive
   ````

   > The copy process should continue without an error.

Leave all instances **Windows PowerShell (Admin)** or **Terminal** open with the copy process running while continuing with the next tasks.

### Task 4: Validate the results from a failed disk

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click on **File and Storage Services**
1. In File and Storage Services, click on **Storage Pools**.
1. In Storage Pools, under **STORAGE POOLS**, click **TASKS**, **Refresh**.

   > A warning sign will be displayed beside the storage pool **Data**, the virtual disk **Data**, and one of the physical disks. When hovering with the mouse over the warning signs, you receive a more detailed error description. The status will be **Degraded**.

### Task 5: Simulate another disk failure

Perform this task on the host.

In the instance of Windows PowerShell or Terminal, where you removed the hard disk, remove another hard disk. You may use a different index number of your choice.

   ````powershell
   $vMHardDiskDrive[2] | Remove-VMHardDiskDrive
   ````

   > The copy process should continue without an error.

Leave all instances **Windows PowerShell (Admin)** or **Terminal** open with the copy process running, while continuing with the next tasks.

Repeat [task 3](#task-3-validate-the-results-from-a-failed-disk).

### Task 6: Add new virtual hard disks

Perform this task on the host.

1. In the instance of Windows PowerShell or Terminal, where you removed the hard disk, create two new virtual hard disks of 1 TB in size.

   ````powershell
   $vhd = 0..1 | ForEach-Object { 
      New-VHD `
         -Path `
            "C:\Labs\WS2022\VMVirtualHardDisks\WIN-VN-SRV10-Replacement-$PSItem.VHDX" `
         -SizeBytes 1TB
      }
   ````

1. Add the new virtual hard disks to WIN-VN1-SRV10.

   ````powershell
   $vhd | ForEach-Object { 
      Add-VMHardDiskDrive -VMName $vMName -Path $PSItem.Path
   }
   ````

### Task 7: Repair the storage pool

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click on **File and Storage Services**
1. In File and Storage Services, click on **Storage Pools**.
1. In Storage Pools, under **STORAGE POOLS**, click **TASKS**, **Refresh**.
1. In the context-menu of the storage pool **Pool1**, click **Add Physical Disk...**
1. In Add Physical Disk, activate one disk with **Capacity** of **1,00 TB** and click **OK**.
1. In **Server Manager**, **Storage Pools**, under **STORAGE POOLS**, ensure **Pool1** is selected. Under **PHYSICAL DISKS** in the context menu of one disk with the warning sign, click **Remove Disk**.
1. In the message box Remove Physical Disk, click **Yes**.
1. In the message box Remove Physical Disk, click **OK**.

   The **Usage** of the physical disk will change to **Retired**.

1. In **Server Manager**, **Storage Pools**, under **STORAGE POOLS**, ensure **Pool1** is selected. Under **PHYSICAL DISKS** in the context menu of the disk with the **Usage** of **Retired**, click **Remove Disk**.
1. In the message box Remove Physical Disk, click **Yes**.
1. In the message box Remove Physical Disk, click **OK**.

   Repeat from step 5 for the second disk with warning sign.

1. In **Server Manager**, **Storage Pools**, under **STORAGE POOLS**, click **TASKS**, **Refresh**.

   > The warning disappears from the pool and the virtual disk.

### Task 8: Simulate a fatal failure

Perform this task on the host.

1. In the instance of Windows PowerShell or Terminal, where you removed the hard disks, remove three hard disk. You may use a different index numbers of your choice.

   ````powershell
   $vhd = Get-VMHardDiskDrive -VMName $vMName | Get-VHD | Where-Object { 
      $PSItem.Size -eq 1TB
   }
   $vMHardDiskDrive = Get-VMHardDiskDrive -VMName $vMName | Where-Object {
      $PSItem.Path -in $vhd.Path 
   }
   $vMHardDiskDrive[0, 2, 4] | Remove-VMHardDiskDrive
   ````

1. Switch to the Windows PowerShell instance or Terminal tab with the running copy process.

   > The copy process will throw error messages.

1. In the Windows PowerShell instance or Terminal tab with the running copy process, press CTRL + C.
1. Remove the PowerShell drive **V**.

   ````powershell
   Remove-PSDrive V
   ````

### Task 9: Remove the storage pool

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click on **File and Storage Services**
1. In File and Storage Services, click on **Storage Pools**.
1. In Storage Pools, under **STORAGE POOLS**, click **TASKS**, **Refresh**.

   > Beside the storage pool and the virtual disk, a red error sign will be displayed.

1. Open **Terminal (Admin)**.
1. Open a CIM session to **VN1-SRV10**.

   ````powershell
   $cimSession = New-CimSession -ComputerName VN1-SRV10
   ````

1. Remove the storage pool **Pool1**.

   ````powershell
   Get-StoragePool -FriendlyName Pool1 -CimSession $cimSession |
   Remove-StoragePool
   ````

1. Remove the CIM session

   ````powershell
   Remove-CimSession $cimSession
   ````

## Exercise 3: Storage Tiering

1. [Add new disks](#task-1-add-new-disks): 3 disks with 1 TB capacity as replacement for the failed disks on VN1-SRV10
1. [Set the media type of physical disks](#task-2-set-the-media-type-of-physical-disks): SSD for all 100 GB disks and HDD for all 1 TB disks
1. [Create a storage pool](#task-3-create-a-storage-pool) with all available disks on VN1-SRV10
1. [Create a tiered virtual disk](#task-4-create-a-tiered-virtual-disk) with 32 GB in the faster tier and 64 GB in the standard tier and assign it the drive letter D
1. [Review the storage tiers management tasks](#task-5-review-the-storage-tiers-management-tasks) on VN1-SRV10
[Epilog](#epilog)

### Task 1: Add new disks

Perform these steps on the host.

1. In the context-menu of *Start*, click **Windows PowerShell (Admin)** or **Terminal (Administrator)**.
1. In Windows PowerShell (Admin) or Terminal, create three new virtual hard disks of 1 TB size each.

   ````powershell
   $vhd = 2..4 | ForEach-Object { 
      New-VHD `
         -Path `
            "C:\Labs\WS2022\VMVirtualHardDisks\WIN-VN-SRV10-Replacement-$PSItem.VHDX" `
         -SizeBytes 1TB
      }

1. Add the new virtual hard disks to WIN-VN1-SRV10.

   ````powershell
   $vhd | ForEach-Object { 
      Add-VMHardDiskDrive -VMName WIN-VN1-SRV10 -Path $PSItem.Path
   }
   ````

### Task 2: Set the media type of physical disks

Perform these steps on CL1.

1. Open **Terminal**.
1. In Terminal, open a CIM session to **VN1-SRV10**.

   ````powershell
   $cimSession = New-CimSession -ComputerName VN1-SRV10
   ````

1. On VN1-SRV10, set the media type of the **100 GB** physical disks to **SSD**.

   ````powershell
   Get-PhysicalDisk -CimSession $cimSession | 
   Where-Object { $PSItem.Size -eq 100GB } | 
   Set-PhysicalDisk -MediaType SSD -CimSession $cimSession
   ````

1. On VN1-SRV10, set the media type of the **1 TB** physical disks to **SSD**.

   ````powershell
   Get-PhysicalDisk -CimSession $cimSession | 
   Where-Object { $PSItem.Size -eq 1TB } | 
   Set-PhysicalDisk -MediaType HDD -CimSession $cimSession
   ````

1. Verify the media type of the physical disks.

   ````powershell
   Get-PhysicalDisk -CimSession $cimSession
   ````

1. Remove the SIM session.

   ````powershell
   Remove-CimSession $cimSession
   ````

This simulates our virtual disks being SSDs and HDDs.

### Task 3: Create a storage pool

#### Desktop Experience

Perform these steps on CL1.

1. Open **Server Manager**.
1. In Server Manager, click on **File and Storage Services**
1. In File and Storage Services, click on **Storage Pools**.
1. In Storage Pools, under **STORAGE POOLS**, click **Tasks**, **New Storage Pool...**.
1. In new Storage Pool Wizard, on page Before you begin, click **Next >**.
1. On page Storage Pool name, in **Name**, type **TieredPool1**. Under **Select the group of available disks (also known as a primordial pool) that you want to use**, click **VN1-SRV10**. Click **Next >**.
1. On page Physical disks, verify the **Media Type** of the available disks (you may have to scroll right or resize the **New Storage Pool Wizard**).

   There should be 5 disks with **Media Type** of **HDD** and two of **SSD**. If you see **Uknown**, do the following:

   1. Click **Cancel**.
   1. In **Server Manager**, **Storage Pools**, under **STORAGE POOLS**, click **TASKS**, **Refresh**.
   1. Restart the creation of the storage pool at step 4.

   Activate all available disks and click **Next >**.

1. On page Confirmation, click **Create**.
1. On page results, click **Close**.

#### Powershell

Perform these steps on CL1.

1. Open **Terminal**.
1. In Terminal, create a CIM session to **VN1-SRV10**.

   ````powershell
   $cimSession = New-CimSession -ComputerName VN1-SRV10
   ````

1. On VN1-SRV10, select the physical disks available for a storage pool.

   ````powershell
   $physicalDisks = Get-PhysicalDisk -CanPool $true -CimSession $cimSession
   ````

1. Create a new Storage Pool **TieredPool1** using the physical disks you selected.

   ````powershell
   New-StoragePool `
      -FriendlyName 'TieredPool1' `
      -PhysicalDisks $physicalDisks `
      -StorageSubSystemFriendlyName 'Windows Storage*' `
      -CimSession $cimSession
   ````

1. Remove the CIM session.

   ````powershell
   Remove-CimSession $cimSession
   ````

### Task 4: Create a tiered virtual disk

#### Desktop Experience

Perform these steps on CL1.

1. Open **Server Manager**.
1. In Server Manager, click on **File and Storage Services**
1. In File and Storage Services, click on **Storage Pools**.
1. Under **STORAGE POOLS**, click **TieredPool1**.
1. In Storage Pools, under **VIRTUAL DISKS**, click **Tasks**, **New Virtual Disk...**
1. In Select the storage pool, click **TieredPool1** and click **OK**.
1. In new Virtual Disk Wizard, on page Before You Begin, click **Next >**.
1. On page Virtual Disk Name, in **Name**, type **Tiered Disk 1**. Activate **Create storage tiers on this virtual disk**. Click **Next >**.
1. On page Enclosure Awareness, click **Next >**.
1. On page Storage layout, under **Layout**, click **Mirror** and click **Next >**.
1. On page Size, under **Faster Tier**, type **32** and click **GB**. Under **Standard Tier**, type **64** and click **GB**. Click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.
1. In **New Volume Wizard**, on page Before You Begin, click **Next >**.
1. On page Server and Disk, under **Server**, ensure **VN1-SRV10** is selected. Under Disk, ensure the **Virtual Disk** **Data** is selected. Click **Next >**.
1. On page Size, click **Next >**.
1. On page Drive Letter or Folder, ensure, beside **Drive letter**, **D** is selected and click **Next >**.
1. On page File System settings, in **Volume label**, type **Data**. Click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.

#### PowerShell

Perform these steps on CL1.

1. Open **Terminal**.
1. In Terminal, create a CIM session to **VN1-SRV10**.

   ````powershell
   $cIMSession = New-CimSession -ComputerName VN1-SRV10
   ````

1. On VN1-SRV10, select the storage pool **Pool1** and store it in a variable.

   ````powershell
   $storagePool = `
      Get-StoragePool -FriendlyName 'TieredPool1' -CimSession $cIMSession
   ````

1. Create storage tiers for **SSD** and **HDD** media types.

   ````powershell
   $storageTiers = @('SSD', 'HDD') | ForEach-Object {
      $storagePool | New-StorageTier `
         -FriendlyName "$($PSItem)_Tier" `
         -MediaType $PSItem `
         -CimSession $cimSession
   }

1. In the storage pool, create a new mirrored virtual disk with **32 GB** in the SSD and **64 GB** in the HDD tier and the name **Tiered Disk 1**.

   ````powershell
   $virtualDisk = $storagePool | New-VirtualDisk `
      -FriendlyName 'Tiered Disk 1' `
      -ResiliencySettingName 'Mirror' `
      -StorageTiers $storageTier `
      -StorageTierSizes 32GB, 64GB
   ````

1. Create a new volume on the new virutal disk and assign it to the drive letter D.

   ```powershell
   New-Volume `
      -FriendlyName 'Data' `
      -DriveLetter D `
      -DiskUniqueId $virtualDisk.UniqueId `
      -CimSession $cimSession
   ````

1. Remove the CIM session.

   ````powershell
   Remove-CimSession $cIMSession
   ````

### Task 5: Review the storage tiers management tasks

Perform this task on CL1.

1. Open **Terminal**.
1. In Terminal, open a remote PowerShell session to **VN1-SRV10**.

   ````powershell
   Enter-PSSession VN1-SRV10
   ````

1. Display the tasks in the path **\\Microsoft\\Windows\\Storage Tiers Management\\**.

   ````powershell
   Get-ScheduledTask -TaskPath '\Microsoft\Windows\Storage Tiers Management\'
   ````

   > You should see details about two tasks: Storage Tiers Management Initialization and Storage Tiers Optimization.

   If time permits, use PowerShell to explore the actions and triggers of the **Storage Tiers Optimization**.

1. Exit the remote PowerShell session.

   ````powershell
   Exit-PSSession
   ````

### Epilog

As our HDDs are simulated, we will not see any acceleration in this lab. Note that you could assign files permanently to the SSD tier, for example:

````powershell
$filePath = 'D:\VM1\Virtual hard Disks\VM1.vhdx'
Set-FileStorageTier `
    -FilePath $filePath `
    -DesiredStorageTierFriendlyName 'SSD_Tier'
````
