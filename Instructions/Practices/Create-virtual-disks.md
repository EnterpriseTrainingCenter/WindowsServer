# Practice: Create virtual disks

## Required VMs

* VN1-DC1
* CL1
* VN1-FS1
* VN1-Core1

## Task

Create two virtual disks in VHDX format of size 1 TB on VN1-FS1 and one on VN1-CORE1.

## Instructions

Perform these steps on VN1-CL1.

1. Sign in as **smart\Administrator**.
1. On the desktop, open **Basic Administration**.
1. In Basic Administration, click **Computer Management**.
1. In the context-menu of **Computer Management**, click **Connect to another computer...**
1. In Select Computer, in **Another computer**, enter **VN1-FS1** and click **OK**.
1. Expand **Computer Management**, **Storage** and click **Disk Management**.
1. In the menu, click **Action**, **Create VHD**.
1. In Create and Attach Virtual Hard Disk, in **Location**, type **C:\VHD1.vhdx**.
1. In Virtual hard disk size, type 1 and click **TB**.
1. Under **Virtual hard disk format**, click **VHDX** and click **OK**.

    > Due to a bug in Disk Management, new disks are not shown if connected remotely. You have to reconnect to the computer to see the new disk.

1. In the context-menu of **Computer Management**, click **Connect to another computer...**
1. In Select Computer, in **Another computer**, enter **VN1-FS1** and click **OK**.
1. Expand **Computer Management**, **Storage** and click **Disk Management**.
1. In **Initialize Disk**, click **Cancel**

    > The disk will be initialized in the next practice.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **vn1-fs1.smart.etc**.
1. Connected to vn1-fs1.smart.etc, under Tools, click **Storage**.
1. In Storage, click **Create VHD**.
1. In the pane Create a VHD, under **VHD folder path**, click **Browse**.
1. In Select a folder, double-click **(C:)**.
1. Click **OK**.
1. In **new VHD file name**, type **VHD2.vhdx**.
1. In **File extension**, click **VHDX**.
1. In **Size (GB)**, type **1024**.
1. Under **Virtual hard disk type**, click **Dynamic**.
1. Click **Submit**.
1. Click **Windows Admin Center** to return to the connections page.
1. In Windows Admin Center, click **vn1-core11.smart.etc**.
1. In Storage, click **Create VHD**.
1. In the pane Create a VHD, under **VHD folder path**, click **Browse**.
1. In Select a folder, double-click **(C:)**.
1. Click **OK**.
1. In **new VHD file name**, type **VHD3.vhdx**.
1. In **File extension**, click **VHDX**.
1. In **Size (GB)**, type **1024**.
1. Under **Virtual hard disk type**, click **Dynamic**.
1. Click **Submit**.
