# Lab: Windows Deployment Services

## Required VMs

* VN1-SRV1
* VN1-SRV6
* VN1-SRV7
* VN1-SRV8
* CL1

## Setup

1. In the menu of **"WIN-VN1-SRV8" on "..." - Virtual Machine Connection**, click **File**, **Settings**.
1. In Settings for "WIN-VN1-SRV8" on "...", click **SCSI-Controller**.
1. Under SCSI-Controller, click **DVD drive**, click **Add**.
1. Under **Media**, click **Image file** and click **Browse...**.
1. In **Open**, open **C:\Labs\ISOs\2022_x64_EN_Eval.iso**.
1. In **Settings for "WIN-VN1-SRV8" on "..."**, click **OK**.
1. On **VN1-SRV8**, sign in ad **ad\administrator**.

## Introduction

Adatum does not want to handle ISO images or physical media anymore to deploy operating systems. For this reason, Windows Deployment Services should be implemented to enable boot from the network.

## Exercises

1. Install and configure Windows Deployment Services
1. Use Windows Deployment Services and Microsoft Deployment Toolkit together
1. Configure Windows Deployment Services for operating system deployment
1. Install Windows Server using Windows Deployment Services

## Exercise 1: Install and configure Windows Deployment Services

1. [Install Windows Deployment Services](#task-1-install-windows-deployment-services) on VN1-SRV8
1. [Create a volume][#task-2] on VN1-SRV8 using one of the additional disks

### Task 1: Install Windows Deployment Services

Perform this task on VN1-SRV8.

1. Open **Server Manager**.
1. In Server Manager, on the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before You begin, click **Next >**.
1. On page Installation Type, ensure **Role-based or reature-based installation** is selected, and click **Next >**.
1. On page Server Selection, ensure **VN1-SRV8.ad.adatum.com** is selected, and click **Next >**.
1. On page Server Roles, activate the checkbox beside **Windows Deployment Services**.
1. In the dialog Add features that are required for Windows Deployment Services, click **Add Features**.
1. In **Add Roles and Features Wizard**, on page **Server Roles**, click **Next >**.
1. On page Select features, click **Next >**.
1. On page WDS, click **Next >**.
1. On page Role Services, ensure **Deployment Server** and **Transport Server** are activated, and click **Next >**.
1. On page Confirmation, activate the checkbox **Restart the destination server automatically if required** and click **Install**.
1. In the message box, click **Yes**.

    Wait for the installation to complete. This takes a minutes or two.

    If the server restarts, sign in as **ad\\Administrator** again.

1. On page Results, click **Close**.

### Task 2: Create a volume

Perform this task on VN1-SRV8.

1. Open **Server Manager**.
1. In Server Manager, click **File and Storage Services**.
1. Under File and Storage Services > Servers, click **Disks**.
1. Under DISKS, in the context-menu of the first disk with a **Status** of **Offline** and a **Capacity** of **1,00 TB**, click **Bring Online**.
1. In the message box Bring Disk Online, click **Yes**.
1. In **Server Manager**, under **DISKS**, in the context-menu of the first disk with a **Status** of **Online** and a **Capacity** of **1,00 TB**, click **Initialize**.
1. In the message box Initialize Disk, click **Yes**.
1. Under VOLUMES, click **TASKS**, **New Volume...**
1. In New Volume Wizard, on page Before You Begin, click **Next >**.
1. On page Server and Disk, ensure **VN1-SRV8** and **Disk 1** is selected, and click **Next >**.
1. On page Size, beside **Volume szie**, ensure **1 024** **GB** is filled in, and click **Next >**.
1. On page Drive Letter or Folder, beside **Drive letter**, click **R**. Click **Next >**.
1. On page File System Settings, beside **File System**, ensure **NTFS** is selected. Beside **Allocation unit size**, ensure **Default** is selected. Beside **Volume label**, type **WDS**. Click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.

### Task 3: Configure Windows Deployment Services

Perform this task on VN1-SRV8.

1. Open **Windows Deployment Services**.
1. In Windows Deployment Services, expand **Servers**.
1. In the context-menu of **VN1-SRV8.ad.adatum.com**, click **Configure Server**.
1. In the Windows Deployment Services Configuration Wizard, on page Before You Begin, click **Next >**.
1. On page Install Options, ensure **Integrated with Active Directory** is selected, and click **Next >**.
1. On page Remote Installation Folder Location, under **Path**, type **R:\\RemoteInstall** and click **Next >**.
1. On page PXE Server Initial Settings, ensure **Do not respond to any client computers** is selected and click **Next >**.
1. On page Operation Complete, click **Finish**.

### Task 4: Enable PXE responses

Perform this task on VN1-SRV8.

1. Open **Windows Deployment Services**.
1. In Windows Deployment Services, expand **Servers**.
1. In the context-menu of **VN1-SRV8.ad.adatum.com**, click **Properties**.
1. In VN1-SRV8 Properties, click the tab **PXE Response**.
1. On the tab PXE Response, click **Respond to all client computers (known and unknown)**.
1. Click the tab **Advanced**.
1. On the tab Advanced, under **DHCP Authorization**, click **Authorize this Windows Deployment Services server in DHCP**.
1. Click **OK**.

## Exercise 2: Use Microsoft Deployment Toolkit and Windows Deployment Services together

### Task 1: Add the Microsoft Deployment Toolkit Lite Touch boot image

Perform this task on VN1-SRV8.

1. Open **Windows Deployment Services**.
1. In Windows Deployment Services, expand **Servers**, **VN1-SRV8.ad.adatum.com**, and click **Boot Images**.
1. In the context-menu of **Boot Images**, click **Add Boot Image...**.
1. In Add Image Wizard, on page Image File, click **Browse...**
1. In Select Windows Image File, open **\\\\vn1-srv7\\MDT\\Boot\\LiteTouchPE_x64.wim**.
1. In **Add Image Wizard**, on page **Image File**, click **Next >**.
1. On page Image Metadata, click **Next >**.
1. On page Summary, click **Next >**.
1. On page Task Progress, click **Finish**.

### Task 2: Run Microsoft Deployment Wizard from the network

Perform these steps on the host computer.

1. Run **Windows PowerShell** as Administrator.
1. In Windows PowerShell, execute

    ````powershell
    C:\Labs\Resources\New-VM.ps1 -Name VN1-SRV21
    ````

1. In **Hyper-V Manager**, in the context menu of **WIN-VN1-SRV21**, click **Start**.
1. Double-click **WIN-VN1-SRV21** to open the console. After a few seconds, you should see a screen with this information (the **Client IP** may vary):

    ````txt
    WDS Boot Manager version 0800
    Client IP: 10.1.1.6
    Server IP: 10.1.1.64
    Server Name: VN1-SRV8.ad.adatum.com

    Press ENTER for network boot service.
    ````

1. Press ENTER.

    You have a few seconds time only to press ENTER. If you fail to press the key on time, restart the virtual machine.

1. In Microsoft Deployment Toolkit, click **Run the Deployment Wizard to install a new Operating System**.
1. In User Credentials, beside **User Name**, type **Administrator**. Beside **Domain**, type **AD**. Beside Password, type the password of **AD\Administrator**. Click **OK**.
1. In Windows Deployment Wizard, on page Task Sequence, click **Install Windows Server 2022 Datacenter** and click **Next**.

    If you have added an additional task sequence, you may choose that one.

1. On page Computer Details, beside **Computer name**, type **VN1-SRV21**. Click **Join a domain**. Beside **Domain to join**, type **ad.adatum.com**. Click **Next**.

    The credentials for the domain join should be filled in already.

1. On page Ready, click **Begin**.

You do not have to wait for the installation to complete.

## Exercise 3: Configure Windows Deployment Services for operating system deployment (optional)

### Task 1: Add the Windows Server setup boot image

Perform this task on VN1-SRV8.

1. Open **Windows Deployment Services**.
1. In Windows Deployment Services, expand **Servers**, **VN1-SRV8.ad.adatum.com**, and click **Boot Images**.
1. In the context-menu of **Boot Images**, click **Add Boot Image...**.
1. In Add Image Wizard, on page Image File, click **Browse...**
1. In Select Windows Image File, open **D:\\sources\boot.wim**.
1. In **Add Image Wizard**, on page **Image File**, click **Next >**.
1. On page Image Metadata, click **Next >**.
1. On page Summary, click **Next >**.
1. On page Task Progress, click **Finish**.

### Task 2: Add the Windows Server installation image

Perform this task on VN1-SRV8.

1. Open **Windows Deployment Services**.
1. In Windows Deployment Services, expand **Servers**, **VN1-SRV8.ad.adatum.com**, and click **Install Images**.
1. In the context-menu of **Install Images**, click **Add Install Image...**.
1. In Add Image Wizard, on page Image Group, beside **Create an image group named**, type **Windows Server 2022** and click **Next >**.
1. On page Image File, click **Browse...**
1. In Select Windows Image File, open **D:\\sources\install.wim**.
1. On page Available Images, leave all images acitvated and click **Next >**.
1. On page Summary, click **Next >**.
1. On page Task Progress, click **Finish**.

### Task 3: Install Windows Server using Windows Deployment Services

Perform these steps on the host computer.

1. Run **Windows PowerShell** as Administrator.
1. In Windows PowerShell, execute

    ````powershell
    C:\Labs\Resources\New-VM.ps1 -Name VN1-SRV22
    ````

1. In **Hyper-V Manager**, in the context menu of **WIN-VN1-SRV22**, click **Start**.
1. Double-click **WIN-VN1-SRV21** to open the console. After a few seconds, you should see a screen with this information (the **Client IP** may vary):

    ````txt
    WDS Boot Manager version 0800
    Client IP: 10.1.1.7
    Server IP: 10.1.1.64
    Server Name: VN1-SRV8.ad.adatum.com

    Press ENTER for network boot service.
    ````

1. Press ENTER.

    You have a few seconds time only to press ENTER. If you fail to press the key on time, restart the virtual machine.

1. In **Windows Boot Manager (Server IP: 10.1.1.64)** use the arrow keys to select **Microsoft Windows Setup (amd64)** and press ENTER.
1. In Microsoft Server Operating System Setup, on page Deprecation Notice, click **Next**.
1. On page Windows Deployment Services, configure **Locale** and the **Keyboard or input method** as you wish and click **Next**.
1. In connect to VN1-SRV8.ad.adatum.com, enter the credentials of **AD\Administrator**.
1. On page Select the operating system you want to install, click **Windows Server 2022 SERVERDATACENTER** and click **Next**.
1. On page Where do you want to install the operating system, click **Next**.

    Applying the image will take a few minutes.

1. In Hi there, make selections at your choice and click **Next**.
1. In License terms, click **Accept**.
1. In Customize settings, beside **Password** and **Reenter password**, type a secure password for the local administrator and click **Finish**.
