# Practice: Create mount points

## Required VMs

* VN1-DC1
* CL1
* VN1-FS1

## Task

Mount the volume NTFS 512GB into D:\\ITData on VN1-FS1. Copy the contents of C:\\LabResources\\Sample Documents\\IT to D:\\ITData. Assign the volume NTFS 512 GB the drive letter E. Compare the contents of E:\\ with the contents of D:\\ITData.

Mount the volume NTFS 128GB into D:\\ISO on VN1-CORE1. Copy the contents of c:\\LabResources to D:\\ISO. Assign the volume NTFS 128GB the drive letter E. Compare the contents of E:\\ with the contents of D:\\ISO.

## Instructions

Perform these steps on VN1-CL1.

1. Sign in as **smart\Administrator**.
1. Open **Server Manager**.
1. In Server Manager, in the left pane, click **File and Storage Services**.
1. In File and Storage Services > Servers, on the left, click **Disks**.
1. On **VN1-FS1**, click disk 2 with a **Capacity** of **1 TB** and the **Bus Type** **File Backed Virtual**.
1. Under **VOLUMES**, in the context-menu of the volume with the Capacity of **512GB**, click **Manage Drive Letter and Access Paths...**
1. In Manage drive letter and access paths, click **Browse...**.
1. In Select Folder, click **ReFS 1TB (D:)**.
1. Click **New Folder**.
1. Enter **ITData** and click **Select Folder**.
1. In **Manage drive letter and access paths**, click **Add**.
1. Click **OK**.
1. Using **File Explorer**, copy the contents of the folder **\\\\vn1-fs1\\c$\\LabResources\\Sample Documents\\IT** to **\\\\vn1-fs1\\d$\\ITData**.
1. Switch to **Server Manager**.
1. Under **VOLUMES**, in the context-menu of the volume **D:\\ITData**, click **Manage Drive Letter and Access Paths...**
1. In Manage drive letter and access paths, beside **Drive letter**, click **E** and click **OK**.
1. Open a second instance of **File Explorer**, navigate to **\\\\vn1-fs1\\e$** and compare the contents with **\\\\vn1-fs1\\d$\\ITData**.
1. Create a remote PowerShell session to **VN1-Core1**.

    ````powershell
    Enter-PSSession VN1-Core1
    ````

1. Create the folder **D:\ISO**.

    ````powershell
    $path = 'D:\ISO'
    New-Item -Type Directory -Path $path
    ````

1. Mount the volume labelled **NTFS 128GB** to **D:\ISO**.

    ````powershell
    Get-Volume -FileSystemLabel 'NTFS 128GB' | 
    Get-Partition | 
    Add-PartitionAccessPath -AccessPath $path
    ````

1. Copy the contents of **C:\LabResources** to **D:\ISO**.

    ````powershell
    Copy-Item C:\LabResources\ $path -Recurse
    ````

1. List the content of **D:\ITData**

    ````powershell
    Get-ChildItem $path -Recurse
    ````

    > Notice the large ISO file.

1. Assign the volume **NTFS 128GB** the drive letter **E**.

    ````powershell
    Get-Volume -FileSystemLabel 'NTFS 128GB' | 
    Get-Partition | 
    Set-Partition -NewDriveLetter E
    ````

1. List the contents of **E:\\**.

    ````powershell
    Get-ChildItem E:\ -Recurse
    ````

    > Compare the contents with **D:\ISO**.

1. Exit the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````
