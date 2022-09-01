# Practice: Extend volumes

## Required VMs

* VN1-DC1
* CL1
* VN1-FS1
* VN1-Core1

## Task

Resize the NTFS 512GB volume on disk 2 of VN1-FS1 to 768 GB. Resize the ReFS 512GB volume on disk 1 of VN1-CORE1 to 768 GB.

## Instructions

Perform these steps on VN1-CL1.

1. Sign in as **smart\Administrator**.
1. In Server Manager, in the left pane, click **File and Storage Services**.
1. In File and Storage Services > Servers, on the left, click **Disks**.
1. On **VN1-FS1**, click disk 2 with a **Capacity** of **1 TB** and the **Bus Type** **File Backed Virtual**.
1. Under **VOLUMES**, in the context-menu of the volume with **Capacity** of **512 GB**, click **Extend Volume...**
1. In Extend Volume, in **New size**, type 768. Ensure **GB** is selected and click **OK**.
1. Open **Windows Terminal**.
1. Create a remote PowerShell session to **VN1-Core1**.

    ````powershell
    Enter-PSSession VN1-Core1
    ````

1. List the disks.

    ````powershell
    Get-Disk
    ````

1. List the partitions on disk **1**.

    ````powershell
    Get-Partition -DiskNumber 1
    ````

1. Extend partition **2** to **768 GB**.

    ````powershell
    Resize-Partition -DiskNumber 1 -PartitionNumber 2 -Size 768GB
    ````

    > Why does this fail?

1. Exit the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````
