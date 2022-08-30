# Practice: Create a mount point

## Required VMs

* VN1-DC1
* CL1
* VN1-Core1

## Task

Mount the volume ReFS 100GB into D:\ITData on VN1-CORE1. Copy the contents of C:\LabResources to D:\ITData. Assign the volume ReFS 100 GB the drive letter E. Compare the contents of E:\ with the contents of D:\ITData.

## Instructions

Perform these steps on VN1-CL1.

1. Sign in as **smart\Administrator**.
1. Create a remote PowerShell session to **VN1-Core1**.

    ````powershell
    Enter-PSSession VN1-Core1
    ````

1. Create the folder **D:\ITData**.

    ````powershell
    $path = 'D:\ITData'
    New-Item -Type Directory -Path $path
    ````

1. Mount the volume labelled **ReFS 100GB** to **D:\ITData**.

    ````powershell
    Get-Volume -FileSystemLabel 'ReFS 100GB' | 
    Get-Partition | 
    Add-PartitionAccessPath -AccessPath $path
    ````

1. Copy the contents of **C:\LabResources** to **D:\ITData**.

    ````powershell
    Copy-Item C:\LabResources\ D:\ITData\ -Recurse
    ````

1. List the content of **D:\ITData**

    ````powershell
    Get-ChildItem D:\ITData -Recurse
    ````

    > Notice the large ISO file.

1. Assign the partition **2** on disk **2** the drive letter **E**.

    ````powershell
    Get-Volume -FileSystemLabel 'ReFS 100GB' | 
    Get-Partition | 
    Set-Partition -NewDriveLetter E
    ````

1. List the contents of **E:\\**.

    ````powershell
    Get-ChildItem E:\ -Recurse
    ````

    > Compare the contents with **D:\ITData**.

1. Exit the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````
