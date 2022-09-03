# Lab: Manage local storage

## Required VMs

* VN1-DC1
* VN1-GW1
* VN1-CL1
* VN1-FS1
* VN1-CORE1

## Setup

On **CL1**, logon as **smart\Administrator**.

## Introduction

You want to create virtual disks with volumes, extend volumes and validate mount points.

## Exercises

1. [Create and initialize virtual disks](#exercise-1-manage-disks)
1. [Manage volumes](#exercise-2-manage-volumes)
1. [Manage mount points (optional)](#exercise-3-manage-mount-points-optional)

## Exercise 1: Manage disks

1. [Create virtual disks](#task-1-create-virtual-disks) according to the table below.

    | Server    | Path         | Size |
    |-----------|--------------|------|
    | VN1-FS1   | C:\VHD1.vhdx | 1 TB |
    | VN1-FS1   | C:\VHD2.vhdx | 1 TB |
    | VN1-CORE1 | C:\VHD3.vhdx | 1 TB |

1. [Initialize disks](#task-2-initialize-disks) with GPT partition style

    > What is the partition style, if you initialize a disk in Server Manager?

### Task 1: Create virtual disks

Create the virtual disks according to the table above. Use the different administration tools for this task. Below, generic instructions for each tool are provided.

#### Desktop Experience

Perform these steps on VN1-CL1.

1. On the desktop, open **Basic Administration**.
1. In Basic Administration, click **Computer Management**.
1. In the context-menu of **Computer Management**, click **Connect to another computer...**
1. In Select Computer, in **Another computer**, enter the server name and click **OK**.
1. Expand **Computer Management**, **Storage** and click **Disk Management**.
1. In the menu, click **Action**, **Create VHD**.
1. In Create and Attach Virtual Hard Disk, in **Location**, type the path.
1. In Virtual hard disk size, type and select the size.
1. Under **Virtual hard disk format**, click **VHDX** and click **OK**.

    > Due to a bug in Disk Management, new disks are not shown if connected remotely. You have to reconnect to the computer to see the new disk.

1. In the context-menu of **Computer Management**, click **Connect to another computer...**
1. In Select Computer, in **Another computer**, enter the server name and click **OK**.
1. Expand **Computer Management**, **Storage** and click **Disk Management**.
1. In **Initialize Disk**, click **Cancel**

    > The disk will be initialized in the next task.

#### Windows Admin Center

Perform these steps on VN1-CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click the server.
1. Connected to the server, under Tools, click **Storage**.
1. In Storage, click **Create VHD**.
1. In the pane Create a VHD, under **VHD folder path**, type the path without the file name, e.g., ***C:\\**
1. In **new VHD file name**, type the file name, e.g., **VHD2.vhdx**
1. In **File extension**, click **VHDX**.
1. In **Size (GB)**, type the size.
1. Under **Virtual hard disk type**, click **Dynamic**.
1. Click **Submit**.

### Task 2: Initialize disks

Intitialize the new disks with the GPT partition style. Use the different administration tools for this task. Below, generic instructions for each tool are provided.

#### Disk Management

Perform these steps on VN1-CL1.

1. On the desktop, open **Basic Administration**.
1. In Basic Administration, click **Computer Management**.
1. In the context-menu of **Computer Management**, click **Connect to another computer...**
1. In Select Computer, in **Another computer**, enter the server name and click **OK**.
1. Expand **Computer Management**, **Storage** and click **Disk Management**.
1. If a dialog **Initialize Disk** appear, for the moment, click **Cancel**.
1. In the context menu of a disk in the state **Not initialized**, click **Initialize Disk**.
1. In the dialog Initialize Disk, ensure the disk is selected, ensure **GPT (GUID Partition Table)** is selected, and click **OK**.

#### Server Manager

Perform these steps on VN1-CL1.

1. Open **Server Manager**.
1. In Server Manager, in the left pane, click **File and Storage Services**.
1. In File and Storage Services > Servers, on the left, click **Disks**.
1. On the respective server, in the context-menu of a disk with **Partition** **Unknown**, click **Initialize**.
1. In the message box Initialize Disk, click **Yes**.

    > The disk will be initialized in GPT partition style.

#### Windows Admin Center

Perform these steps on VN1-CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click the respective server.
1. Connected to the server, under Tools, click **Storage**.
1. In Storage, click the disk.
1. Click **Initialize Disk**.
1. In the pane Initialize Disk, ensure **GPT (GUID Partition Table)** is selected and click **Submit**.

#### PowerShell

Perform these steps on VN1-CL1.

1. Open **Windows Terminal**.
1. Create a remote PowerShell session to the respective server.

    ````powershell
    # TODO: Fill the server name in the quotes
    $computerName = ''

    Enter-PSSession $computerName
    ````

1. List the disks.

    ````powershell
    Get-Disk
    ````

    Notice disks with **Partition style** **RAW**.

1. Initialize disk the raw disk.

    ````powershell
    Get-Disk | 
    Where-Object { $PSItem.PartitionStyle -eq 'RAW' } | 
    Initialize-Disk -PartitionStyle GPT
    ````

## Exercise 2: Manage volumes

1. [Create volumes](#task-1-create-volumes) according to the table below

    | Computer  | Disk | Size    | File system | Label      | Drive Letter |
    |-----------|------|---------|-------------|------------|--------------|
    | VN1-FS1   | 1    | Maximum | ReFS        | ReFS 1TB   | D            |
    | VN1-FS1   | 2    | 512 GB  | NTFS        | NTFS 512GB | E            |
    | VN1-CORE1 | 1    | 512 GB  | REFS        | ReFS 512GB | D            |
    | VN1-CORE1 | 1    | 128 GB  | NTFS        | NTFS 128GB | E            |

1. [Extend volume](#task-2-extend-volume-optionalme) NTFS 512GB on VN1-FS1 to 768GB (optional)

### Task 1: Create volumes

Create the volumes according to the table above. Use the different administration tools for this task. Below, generic instructions for each tool are provided.

> Note: Volumes without a drive letter cannot be created using Windows Admin Center.

#### Desktop Experience

Perform these steps on VN1-CL1.

1. Open **Server Manager**.
1. In Server Manager, in the left pane, click **File and Storage Services**.
1. In File and Storage Services > Servers, on the left, click **Disks**.
1. Under the respective server, click the disk with the respective numer.
1. Under **Volumes**, click **TASKS**, **New Volume...**.
1. In the New Volume Wizard, on page **Before you begin**, click **Next >**.
1. On page **Select the server and disk**, ensure the correct server and disk are selected and click **Next >**.
1. On page **Specify the size of the volume**, in **Volume size**, type and click the size.
1. On page **Assign to a drive letter or folder**, beside **Drive letter:**, click the drive letter or click **Don't assign to a drive letter or folder**.
1. On page **Select file system settings**, beside **File system**, click the file system.
1. In **Volume label**, type the label and click **Next >**.
1. On page **Confirm selections**, click **Create**.
1. On page **Completion**, click **Close**.

#### Windows Admin Center

> Note: Volumes without a drive letter cannot be created using Windows Admin Center.

Perform these steps on VN1-CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click the server.
1. Connected to the server, under Tools, click **Storage**.
1. In Storage, click the disk.
1. Click **Create Volume**.
1. In the pane Create Volume, in **Drive Letter**, click the drive letter.
1. In **Volume label**, type the label.
1. In **File system**, click the file system.
1. In **Volume size (GB)**, type the size in GB (1 TB is 1024 GB).
1. Click **Submit**.

#### PowerShell

Perform these steps on VN1-CL1.

1. Create a remote PowerShell session to the respective server.

    ````powershell
    # TODO: Fill the server name in the quotes
    $computerName = ''

    Enter-PSSession $computerName
    ````

1. List the disks.

    ````powershell
    Get-Disk
    ````

1. Create a a partition.

    ````powershell
    # TODO: Change the disk number and the size
    $diskNumber = 1
    $size = 512GB

    $partition = New-Partition -DiskNumber $diskNumber -Size $size
    ````

1. Format the new partition.

    ````powershell
    # TODO: Change the file system to NTFS or ReFS
    $fileSystem = 'ReFS'

    # TODO: Fill the label between the quotes
    $label = ''

    Format-Volume `
        -Partition $partition `
        -FileSystem $fileSystem `
        -NewFileSystemLabel $label
    ````

1. Exit the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

### Task 2: Extend volume (optional)

#### Desktop experience

Perform these steps on VN1-CL1.

1. Open **Server Manager**.
1. In Server Manager, in the left pane, click **File and Storage Services**.
1. In File and Storage Services > Servers, on the left, click **Disks**.
1. On **VN1-FS1**, click disk 2 with a **Capacity** of **1 TB** and the **Bus Type** **File Backed Virtual**.
1. Under **VOLUMES**, in the context-menu of the volume with **Capacity** of **512 GB**, click **Extend Volume...**
1. In Extend Volume, in **New size**, type 768. Ensure **GB** is selected and click **OK**.

#### Windows Admin Center

Perform these steps on VN1-CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click the **vn1-fs1.smart.etc**.
1. Connected to vn1-fs1.smart.etc, under Tools, click **Storage**.
1. In Storage, click **Disk 2**.
1. In the tab **Volumes**, click **NTFS 512GB (E:)**.
1. Click **Resize**.
1. In the pane Resize Volume (E:), in **New volume size (GB)**, type **768**.
1. Click **Submit**.

#### PowerShell

Perform these steps on VN1-CL1.

1. Open **Windows Terminal**.
1. Create a remote PowerShell session to **VN1-FS1**.

    ````powershell
    Enter-PSSession VN1-FS1
    ````

1. List the disks.

    ````powershell
    Get-Disk
    ````

1. List the partitions on disk **2**.

    ````powershell
    Get-Partition -DiskNumber 2
    ````

1. Extend partition **2** to **768 GB**.

    ````powershell
    Resize-Partition -DiskNumber 2 -PartitionNumber 2 -Size 768GB
    ````

1. Exit the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

## Exercise 3: Manage mount points (optional)

1. [Create a mount point](#task-1-create-a-mount-point) on VN1-FS1 for E:\ in D:\ITData
1. [Validate mount points](#task-2-validate-mount-points)

Perform these steps on VN1-CL1.

### Task 1: Create a mount point

#### Desktop Experience

Perform these steps on VN1-CL1.

1. Open **Server Manager**.
1. In Server Manager, in the left pane, click **File and Storage Services**.
1. In File and Storage Services > Servers, under **SERVICES** (scroll down), in the context-menu of the service **SERVER**, click **Restart Services**.
1. On the left, click **Disks**.
1. On **VN1-FS1**, click disk 2 with a **Capacity** of **1 TB** and the **Bus Type** **File Backed Virtual**.
1. Under **VOLUMES**, in the context-menu of the volume **E:**, click **Manage Drive Letter and Access Paths...**
1. In Manage drive letter and access paths, click **Browse...**.
1. In Select Folder, click **ReFS 1TB (D:)**.
1. Click **New Folder**.
1. Enter **ITData** and click **Select Folder**.
1. In **Manage drive letter and access paths**, click **Add**.
1. Click **OK**.

#### PowerShell

Perform these steps on VN1-CL1.

1. Create a remote PowerShell session to **VN1-FS1**.

    ````powershell
    Enter-PSSession VN1-FS1
    ````

1. Create the folder **D:\ITData**.

    ````powershell
    $path = 'D:\ITData'
    New-Item -Type Directory -Path $path
    ````

1. Mount the volume labelled **NTFS 512GB** to **D:\ITData**.

    ````powershell
    Get-Volume -FileSystemLabel 'NTFS 512GB' | 
    Get-Partition | 
    Add-PartitionAccessPath -AccessPath $path
    ````

### Task 2: Validate mount points

#### Desktop Experience

Perform these steps on VN1-CL1.

1. Using **File Explorer**, copy the contents of the folder **\\\\vn1-fs1\\c$\\LabResources\\Sample Documents\\IT** to **\\\\vn1-fs1\\d$\\ITData**.
1. Open a second instance of **File Explorer**, navigate to **\\\\vn1-fs1\\e$** and compare the contents with **\\\\vn1-fs1\\d$\\ITData**.

#### PowerShell

1. Create a remote PowerShell session to **VN1-FS1**.

    ````powershell
    Enter-PSSession VN1-FS1
    ````

1. Copy the contents of **C:\LabResources\\Sample Documents\IT** to **D:\ITData**.

    ````powershell
    $path = 'D:\ITData'
    Copy-Item 'C:\LabResources\Sample Documents\IT\*' $path -Recurse
    ````

1. List the content of **D:\\ITData**

    ````powershell
    Get-ChildItem $path
    ````

1. List the contents of **E:\\**.

    ````powershell
    Get-ChildItem E:\
    ````

    > Compare the contents with **D:\\ITData**.

1. Exit the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````
