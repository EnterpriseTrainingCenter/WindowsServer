# Practice: Initialize disks

## Required VMs

* VN1-DC1
* CL1
* VN1-FS1
* VN1-Core1

## Task

Initialize the new disks on VN1-FS1 and VN1-Core1 with GPT partition style.

## Instructions

Perform these steps on VN1-CL1.

1. Sign in as **smart\Administrator**.
1. On the desktop, open **Basic Administration**.

1. Open **Server Manager**.
1. In Server Manager, in the left pane, click **File and Storage Services**.
1. In File and Storage Services > Servers, on the left, click **Disks**.
1. On **VN1-FS1**, in the context-menu of the disk with **Partition** **Unknown**, click **Initialize**.
1. In the message box Initialize Disk, click **Yes**.

    > What is the partition type now?

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **vn1-core1.smart.etc**.
1. Connected to vn1-core1.smart.etc, under Tools, click **Storage**.
1. In Storage, click **Disk 1**.
1. Click **Initialize Disk**.
1. Open **Windows Terminal**.
1. Create a remote PowerShell session to **VN1-Core1**.

    ````powershell
    Enter-PSSession VN1-Core1
    ````

1. List the disks.

    ````powershell
    Get-Disk
    ````

    Notice disk with **Partition style** **RAW**. Take note of the number. It should be **2**.

1. Initialize disk the raw disk.

    ````powershell
    Initialize-Disk -Number 2 -PartitionStyle GPT
    ````
