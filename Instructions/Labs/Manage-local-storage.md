# Lab: Manage local storage

## Required VMs

* VN1-SRV1
* VN1-SRV4
* VN1-SRV5
* VN1-SRV10
* CL1

## Setup

1. On **CL1**, sign in as **ad\Administrator**.
1. On **VN1-SRV10**, sign in as **ad\Administrator**.

## Introduction

You want to create virtual disks with volumes, extend volumes and validate mount points.

## Exercises

1. [Manage Disks](#exercise-1-manage-disks)
1. [Manage volumes](#exercise-2-manage-volumes)
1. [Manage mount points](#exercise-3-manage-mount-points)
1. [Manage links and junctions](#exercise-4-manage-links-and-junctions)

## Exercise 1: Manage disks

[Initialize disks](#task-initialize-disks) on VN1-SRV10 and VN1-SRV5 with GPT partition style

> What is the partition style, if you initialize a disk in Server Manager?

### Task: Initialize disks

Intitialize the new disks on VN1-SRV10 and VN1-SRV5 with the GPT partition style. Use the different administration tools for this task. Below, generic instructions for each tool are provided.

#### Disk Management

Perform these steps on CL1.

1. On the desktop, open **Basic Administration**.
1. In Basic Administration, click **Computer Management**.
1. In the context-menu of **Computer Management**, click **Connect to another computer...**
1. In Select Computer, in **Another computer**, enter the server name and click **OK**.
1. Expand **Computer Management**, **Storage** and click **Disk Management**.
1. In the context menu of a disk in the state **Offline**, click **Online**.
1. In the context menu of a disk in the state **Not initialized**, click **Initialize Disk**.
1. In the dialog Initialize Disk, ensure the disk is selected, ensure **GPT (GUID Partition Table)** is selected, and click **OK**.

#### Server Manager

Perform these steps on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the left pane, click **File and Storage Services**.
1. In File and Storage Services > Servers, on the left, click **Disks**.
1. Under the respective server, in the context menu of a disk with **Status** **Offline**, click **Bring Online**.
1. In the message Box **Bring Disk Online**, click **Yes**.
1. In **Server Manager**, in the context-menu of a disk with **Partition** **Unknown**, click **Initialize**.
1. In the message box Initialize Disk, click **Yes**.

    > The disk will be initialized in GPT partition style.

#### Windows Admin Center

Perform these steps on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click the respective server.
1. Connected to the server, under Tools, click **Storage**.
1. In Storage, click the disk with **Status** **Offline**.
1. Click **Initialize Disk**.
1. In the pane Initialize Disk, ensure **GPT (GUID Partition Table)** is selected and click **Submit**.

#### PowerShell

Perform these steps on CL1.

1. Open **Terminal**.
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

    Notice disks with the **OperationalStatus** **Offline**.

1. Bring the offline disks online.

    ````powershell
    Get-Disk | 
    Where-Object { $PSItem.OperationalStatus -eq 'Offline' } |
    Set-Disk -IsOffline $false
    ````

1. List the disks again.

    ````powershell
    Get-Disk
    ````

    Notice disks Operational **Partition style** **RAW**.

1. Initialize disk the raw disks.

    ````powershell
    Get-Disk | 
    Where-Object { $PSItem.PartitionStyle -eq 'RAW' } | 
    Initialize-Disk -PartitionStyle GPT
    ````

## Exercise 2: Manage volumes

1. [Create volumes](#task-1-create-volumes) according to the table below

    | Computer  | Disk | Size    | File system | Label      | Drive Letter |
    |-----------|------|---------|-------------|------------|--------------|
    | VN1-SRV10   | 1    | Maximum | NTFS        | NTFS 1TB   | D            |
    | VN1-SRV10   | 2    | 512 GB  | ReFS        | ReFS 512GB | E            |
    | VN1-SRV5 | 1    | 512 GB  | REFS        | ReFS 512GB | D            |
    | VN1-SRV5 | 1    | 128 GB  | NTFS        | NTFS 128GB | E            |

1. [Extend volume](#task-2-extend-volume) NTFS 512GB on VN1-SRV10 to 768GB

### Task 1: Create volumes

Create the volumes according to the table above. Use the different administration tools for this task. Below, generic instructions for each tool are provided.

> Note: Volumes without a drive letter cannot be created using Windows Admin Center.

#### Desktop Experience

Perform these steps on CL1.

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

Perform these steps on CL1.

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

Perform these steps on CL1.

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

### Task 2: Extend volume

#### Desktop experience

Perform these steps on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the left pane, click **File and Storage Services**.
1. In File and Storage Services > Servers, on the left, click **Disks**.
1. On **VN1-SRV10**, click disk 2 with a **Capacity** of **1 TB** and the **Bus Type** **File Backed Virtual**.
1. Under **VOLUMES**, in the context-menu of the volume with **Capacity** of **512 GB**, click **Extend Volume...**
1. In Extend Volume, in **New size**, type 768. Ensure **GB** is selected and click **OK**.

#### Windows Admin Center

Perform these steps on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click the **VN1-SRV10.ad.adatum.com**.
1. Connected to VN1-SRV10.ad.adatum.com, under Tools, click **Storage**.
1. In Storage, click **Disk 2**.
1. In the tab **Volumes**, click **ReFS 512GB (E:)**.
1. Click **Resize**.
1. In the pane Resize Volume (E:), in **New volume size (GB)**, type **768**.
1. Click **Submit**.

#### PowerShell

Perform these steps on CL1.

1. Open **Terminal**.
1. Create a remote PowerShell session to **VN1-SRV10**.

    ````powershell
    Enter-PSSession VN1-SRV10
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

## Exercise 3: Manage mount points

1. [Create a mount point](#task-1-create-a-mount-point) on VN1-SRV10 for E:\ in D:\ITData
1. [Validate mount points](#task-2-validate-mount-points)

Perform these steps on CL1.

### Task 1: Create a mount point

#### Desktop Experience

Perform these steps on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the left pane, click **File and Storage Services**.
1. In File and Storage Services > Servers, under **SERVICES** (scroll down), in the context-menu of the service **SERVER**, click **Restart Services**.
1. On the left, click **Disks**.
1. On **VN1-SRV10**, click disk 2 with a **Capacity** of **1 TB** and the **Bus Type** **File Backed Virtual**.
1. Under **VOLUMES**, in the context-menu of the volume **E:**, click **Manage Drive Letter and Access Paths...**
1. In Manage drive letter and access paths, click **Browse...**.
1. In Select Folder, click **NTFS 1TB (D:)**.
1. Click **New Folder**.
1. Enter **ITData** and click **Select Folder**.
1. In **Manage drive letter and access paths**, click **Add**.
1. Click **OK**.

#### PowerShell

Perform these steps on CL1.

1. Create a remote PowerShell session to **VN1-SRV10**.

    ````powershell
    Enter-PSSession VN1-SRV10
    ````

1. Create the folder **D:\ITData**.

    ````powershell
    $path = 'D:\ITData'
    New-Item -Type Directory -Path $path
    ````

1. Mount the volume labelled **ReFS 512GB** to **D:\ITData**.

    ````powershell
    Get-Volume -FileSystemLabel 'ReFS 512GB' | 
    Get-Partition | 
    Add-PartitionAccessPath -AccessPath $path
    ````

### Task 2: Validate mount points

#### Desktop Experience

Perform these steps on CL1.

1. Using **File Explorer**, copy the contents of the folder **\\\\VN1-SRV10\\c$\\LabResources\\Sample Documents\\IT** to **\\\\VN1-SRV10\\d$\\ITData**.
1. Open a second instance of **File Explorer**, navigate to **\\\\VN1-SRV10\\e$** and compare the contents with **\\\\VN1-SRV10\\d$\\ITData**.

#### PowerShell

1. Create a remote PowerShell session to **VN1-SRV10**.

    ````powershell
    Enter-PSSession VN1-SRV10
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

## Exercise 4: Manage links and junctions

1. [Create a hard link](#task-1-create-a-hard-link) targeting C:\BootStrap\BootStrap.ps1
1. [Compare contents of the hard link with the original file](#task-2-compare-contents-of-the-hard-link-with-the-original-file)

    > Are the files identical?

1. [Delete the original](#task-3-delete-the-original-file) file C:\BootStrap\BootStrap.ps1
1. [View the content of the hard link](#task-4-view-the-content-of-the-hard-link)

    > Can you still view the content of the hard link?

1. [Create a new hard link to recover the original file](#task-5-create-a-new-hard-link-to-recover-the-original-file)
1. [Create a junction](#task-6-create-a-junction) on VN1-SRV10 at D:\\Setup targeting C:\\BootStrap
1. [Verify the junction](#task-7-verify-the-junction) by comparing the content of the folders before and after deleting a file

    > Do you see the deleted file on the junction?

1. [Create and verify a symbolic link](#task-8-create-and-verify-a-symbolic-link) to \\\\VN1-SRV1\\Sysvol

### Task 1: Create a hard link

Perform this task on CL1.

1. Open **Terminal**.
1. Create a hard link **C:\\setupscript.ps1** targeting **C:\\BootStrap\\BootStrap.ps1** on VN1-SRV10.

    ````powershell
    Invoke-Command -ComputerName VN1-SRV10 -ScriptBlock {
        $target = 'C:\BootStrap\BootStrap.ps1'
        $name = 'SetupScript.ps1'
        $path = 'C:\'
        New-Item -ItemType HardLink -Target $target  -Name $name -Path $path
    }
    ````

### Task 2: Compare contents of the hard link with the original file

Perform this task on CL1.

1. Open **Terminal**.
1. Compare the content of **C:\\setupscript.ps1** with **C:\\BootStrap\\BootStrap.ps1**.

    ````powershell
    Invoke-Command -ComputerName VN1-SRV10 -ScriptBlock {
        $referenceObject = Get-Content 'C:\BootStrap\BootStrap.ps1'
        $differenceObject = Get-Content 'C:\SetupScript.ps1'
        Compare-Object `
            -ReferenceObject $referenceObject `
            -DifferenceObject $differenceObject
    }
    ````

    > You should receive True, which means, the files are identical.

### Task 3: Delete the original file

Perform this task on CL1.

1. Open **Terminal**.
1. Delete the original file **C:\\BootStrap\\BootStrap.ps1**.

    ````powershell
    Invoke-Command -ComputerName VN1-SRV10 -ScriptBlock {
        Remove-Item 'C:\BootStrap\BootStrap.ps1'
    }
    ````

### Task 4: View the content of the hard link

Perform this task on CL1.

1. Open **Terminal**.
1. Get the content of **C:\\setupscript.ps1**.

    ````powershell
    Invoke-Command -ComputerName VN1-SRV10 -ScriptBlock {
        Get-Content 'C:\SetupScript.ps1'
    }
    
    ````

    > You should still receive the content.

### Task 5: Create a new hard link to recover the original file

Perform this task on CL1.

1. Open **Terminal**.
1. Recover the original file by creating a hard link.

    ````powershell
    Invoke-Command -ComputerName VN1-SRV10 -ScriptBlock {
        New-Item `
            -ItemType HardLink `
            -Target 'C:\SetupScript.ps1' `
            -Name 'BootStrap.ps1' `
            -Path 'C:\BootStrap'
    }
    ````

### Task 6: Create a junction

Perform this task on CL1.

1. Open **Terminal**.
1. On VN1-SRV10, create a junction **D:\\Setup** targeting **C:\\BootStrap**.

    ````powershell
    Invoke-Command -ComputerName VN1-SRV10 -ScriptBlock {
        New-Item `
            -ItemType Junction `
            -Target 'C:\BootStrap\' `
            -Name 'Setup' `
            -Path 'D:\'
    }
    ````

### Task 7: Verify the junction

Perform this task on CL1.

1. Open **Terminal**.
1. Open a remote PowerShell session to VN1-SRV10.

    ````powershell
    Enter-PSSession VN1-SRV10
    ````

1. List the contents of **D:\\Setup**.

    ````powershell
    Get-ChildItem D:\Setup\
    ````

1. Delete **D:\\Setup\\LabRoot.cer**.

    ````powershell
    Remove-Item D:\Setup\LabRoot.cer
    ````

1. List the content of **C:\\Bootstrap**

    ````powershell
    Get-ChildItem C:\Bootstrap
    ````

    > You should not see **Labroot.cer** anymore.

1. Close the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

### Task 8: Create and verify a symbolic link

Perform this task on VN1-SRV10.

1. Run **Terminal** or **Windows PowerShell** as Administrator.
1. Create a symbolic link **D:\\Sysvol** targeting **\\\\VN1-SRV1\\Sysvol**.

    ````powershell
    New-Item `
        -ItemType SymbolicLink `
        -Target \\VN1-SRV1\sysvol `
        -Name Sysvol `
        -Path D:\
    ````

1. List the contents of **D:\\Sysvol**.

    ````powershell
    Get-ChildItem D:\Sysvol -Recurse
    ````
