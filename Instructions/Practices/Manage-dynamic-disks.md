# Practice: Manage dynamic disks

## Required VMs

* VN1-SRV1
* PM-SRV1
* CL1

## Task

Create a dynamically expanding virtual hard disk with a size of 100 GB and attach it to PM-SRV20. Create and format a volume. Expand the virtual hard disk by 50 GB and resize the partition.

## Instructions

Perform these steps on CL1.

1. Sign in as **ad\Administrator**.
1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. Under Virtual machines, in the context-menu of **PM-SRV20**, click **Settings...**.
1. In Settings for PM-SRV20 on PM-SRV1, click **SCSI Controller**.
1. Under SCSI Controller, click **Hard Drive** and click **Add**.
1. Under Hard Drive, under **Media**, ensure **Virtual hard disk** is selected and click **New**.
1. In New Virtual Hard Disk Wizard, on page Before Your Begin, click **Next >**.
1. On page Choose Disk type, ensure **Dynamically expanding** is selected and click **Next >**.
1. On page Specify Name and Location, In **Name** type **PM-SRV20 Data.vhdx** and click **Next >**.
1. On page Configure Disk, ensure **Create a new blank virtual hard disk** is selected. In **Size**, type **100**. Click **Next >**.
1. On page Summary, click **Finish**.
1. In **Settings for PM-SRV20 on PM-SRV1**, click **OK**.
1. Open **Terminal**.
1. In Terminal, create a CIM session to PM-SRV20.

    ````powershell
    $cimSession = New-CimSession -ComputerName PM-SRV20
    ````

1. Initialize all uninitialized disks on PM-SRV20.

    ````powershell
    Get-Disk -CimSession $cimSession | 
    Where-Object { $PSItem.PartitionStyle -eq 'RAW' } | 
    Initialize-Disk -PartitionStyle GPT
    ````

1. Read the disks on PM-SRV20 and note the disk with a total size of 100 GB.

    ````powershell
    Get-Disk -CimSession $cimSession
    ````

1. On the disk with a size of 100 GB, create an NTFS volume with the name **Data** and assign it to drive letter **E**.

    ````powershell
    # Replace the disk number as appropriate
    New-Volume `
        -DiskNumber 1 `
        -FileSystem NTFS `
        -FriendlyName Data `
        -DriveLetter E `
        -CimSession $cimSession
    ````

1. Switch to  **Hyper-V Manager**.
1. In Hyper-V Manager, in the context-menu of **PM-SRV1**, click **Edit Disk...**.
1. In Edit Virtual Hard Disk Wizard, on page Before You Begin, click **Next >**.
1. On page Locate Disk, click **Browse...**
1. In Open, ensure the path reads **Remote File Browser > pm-srv1.ad.adatum.com > C: > hyper-v > Virtual Hard Disks**. Click **PM-SRV20 Data.vhdx** and click **Open**.
1. In **Edit Virtual Hard Disk Wizard**, on page **Locate Disk**, click **Next >**.
1. On page Choose Action, ensure **Expand** is selected and click **Next >**.
1. On page Configure Disk, in **New size**, type **150** and click **Next >**.
1. On page Summary, click **Finish**.
1. Switch to **Terminal**.
1. Read the partition **E** on PM-SRV20 on disk 1 and store it in variable.

    ````powershell
    $partition = Get-Partition -DriveLetter E -CimSession $cimSession
    ````

1. Read the content of the variable.

    ````powershell
    $partition
    ````

    The partition **E** still has a size of under 100 GB.

1. Get the supported size of the partition and store it in a variable.

    ````powershell
    $partitionSupportedSize = $partition | Get-PartitionSupportedSize
    ````

1. Resize the partition to its maximum size.

    ````powershell
    $partition | Resize-Partition -Size $partitionSupportedSize.SizeMax
    ````

1. Verify the new size of the partition.

    ````powershell
    $partition | Get-Partition
    ````

    The partition **E** has a size of more than 100 GB.

1. Remove the CIM session.

    ````powershell
    Remove-CimSession $cimSession
    ````
