# Practice: Extend volumes

## Required VMs

* VN1-DC1
* CL1
* VN1-FS1
* VN1-Core1

## Task

Resize the ReFS 512GB volume on disk 1 of VN1-CORE1 to 768 GB. Resize the ReFS 100GB volume on disk2 of VN1-CORE2 to 256 GB.

## Instructions

Perform these steps on VN1-CL1.

1. Sign in as **smart\Administrator**.
1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **vn1-core1.smart.etc**.
1. Connected to vn1-core1.smart.etc, under Tools, click **Storage**.
1. In Storage, click **Disk 1**.
1. In the bottom pane, click the volume **ReFS 512GB (D:)**.
1. Click **Resize**.
1. In **New volume size (GB)**, enter **768** and click **Submit**
1. Open **Windows Terminal**.
1. Create a remote PowerShell session to **VN1-Core1**.

    ````powershell
    Enter-PSSession VN1-Core1
    ````

1. List the disks.

    ````powershell
    Get-Disk
    ````

1. List the partitions on disk **2**.

    ````powershell
    Get-Partition -DiskNumber 2
    ````

1. Extend partition **2** to **256 GB**.

    ````powershell
    Resize-Partition -DiskNumber 2 -PartitionNumber 2 -Size 256GB
    ````

1. Exit the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````
