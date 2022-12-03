# Practice: Configure basic Hyper-V settings

## Required VMs

* VN1-SRV1
* PM-SRV1
* PM-SRV2
* CL1

## Task

On PM-SRV1 and PM-SRV2, set the default path for virtual machines to C:\\Hyper-V and, for virtual hard disks, to C:\\Hyper-V\\Virtual Hard Disks. Moreover, on both servers, enable live migrations and create an external virtual switch.

## Instructions

Perform these steps on CL1.

1. Sign in as **ad\Administrator**.
1. Open **Terminal**.
1. In Terminal, create a remote PowerShell session to PM-SRV1.

    ````powershell
    Enter-PSSession PM-SRV1
    ````

1. On PM-SRV1, create a directory **C:\\Hyper-V\\Virtual Hard Disks**

    ````powershell
    New-Item -Path 'C:\Hyper-V\Virtual Hard Disks' -ItemType Directory
    ````

1. Exit from the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, in the context menu of **Hyper-V Manager**, click **Connect to Server...**
1. In Select Computer, ensure **Another computer** is selected, type **PM-SRV1**, and click **OK**.
1. In **Hyper-V Manager**, click **PM-SRV1**.
1. In the context-menu of **PM-SRV1**, click **Hyper-V Settings**.
1. In Hyper-V Settings for PM-SRV1, ensure **Virtual Hard Disks** is selected. Under **Virtual Hard Disks**, cick **Browse...**
1. In Select Folder, expand **pm-srv1.ad.adatum.com**, **Local Disk (C:)**, **hyper-v** and click **Virtual Hard Disks**. Click **Select Folder**.
1. In **Hyper-V Settings for PM-SRV1**, in the left pane, click **Virtual Machines**.
1. Under Virtual Machines, click **Browse...**.
1. In Select Folder, expand **pm-srv1.ad.adatum.com**, **Local Disk (C:)** and click **hyper-v**. Click **Select Folder**.
1. In **Hyper-V Settings for PM-SRV1**, click **Live Migrations**.
1. Under Live Migrations, activate **Enable incoming and outgoing live migrations** and click **Use any available network for the live migration.
1. In the left pane, expand **Live Migrations** and click **Advanced Features**.
1. Under Advanced Features, ensure **Use Credential Security Support Provider (CredSSP)** is selected. Under **Performance options**, ensure **Compression** is selected.
1. Click **OK**.
1. In **Hyper-V Manager**, in the context-menu of **PM-SRV1**, click **Virtual Switch Manager...**
1. In Virtual Switch Manager for PM-SRV1, in the left pane, ensure **New virtual network switch** is selected. In the right pane, under **Create virtual switch**, ensure **External** is selected and click **Create Virtual Switch**.
1. Under Name, type **External**. Under **Connection type**, ensure **External network** and **Microsoft Hyper-V Network Adapter** is selected. Ensure, **Allow management operating system to share this network adapter** is activated. Click **OK**.
1. In the message box Apply Networking Changes, click **Yes**.

Repeat this task for **PM-SRV2**.
