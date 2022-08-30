# Practice: Create volumes

## Required VMs

* VN1-DC1
* CL1
* VN1-FS1
* VN1-Core1

## Task



## Instructions

Perform these steps on VN1-CL1.

1. Sign in as **smart\Administrator**.
1. Open **Server Manager**.
1. In Server Manager, in the left pane, click **File and Storage Services**.
1. In File and Storage Services > Servers, on the left, click **Disks**.
1. On **VN1-FS1**, click the disk with a **Capacity** of **1 TB** and the **Bus Type** **File Backed Virtual**.
1. Under **Volumes**, click **TASKS**, **New Volume...**.
1. In the New Volume Wizard, on page **Befor you begin**, click **Next >**.
1. On page **Select the server and disk**, ensure **VN1-FS1** and the disk with **Free Space** of **1 024 GB** are selected and click **Next >**.
1. On page **Specify the size of the volume**, in **Volume size**, ensure **1 024** **GB** is filled in and click **Next >**.
1. On page **Assign to a drive letter or folder**, beside **Drive letter:**, ensure **D** is selected and click **Next >**.
1. On page **Select file system settings**, beside **File system**, ensure **NTFS** is selected.
1. In **Volume label**, type **NTFS 1TB** and click **Next >**.
1. On page **Confirm selections**, click **Create**.
1. On page **Completion**, click **Close**.
1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **vn1-core1.smart.etc**.
1. Connected to vn1-core1.smart.etc, under Tools, click **Storage**.
1. In Storage, click **Disk 1** (**Capacity** is **1 TB**).
1. Click **Create Volume**.
1. In the pane Create Volume, in **Drive Letter**, click **D**.
1. In **Volume label**, type **ReFS 512GB**.
1. In **File system**, click **REFS**.
1. In **Volume size (GB)**, type **512** and click **Submit**.
1. Open **Windows Terminal**.
1. Create a remote PowerShell session to **VN1-Core1**.

    ````powershell
    Enter-PSSession VN1-Core1
    ````

1. List the disks.

    ````powershell
    Get-Disk
    ````

1. Create a a partition of **100 GB** on **disk 2**.

    ````powershell
    $partition = New-Partition -DiskNumber 2 -Size 100GB
    ````

1. Format the new partition with **ReFS** and the label **ReFS 100GB**.

    ````powershell
    Format-Volume `
        -Partition $partition `
        -FileSystem ReFS `
        -NewFileSystemLabel 'ReFS 100GB'
    ````

1. Exit the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````