# Lab: Windows Deployment Services

## Required VMs

* VN1-SRV1
* VN1-SRV2
* VN1-SRV3
* VN1-SRV4
* VN1-SRV5
* VN1-SRV6
* VN1-SRV7
* VN1-SRV8
* VN1-SRV9
* VN1-SRV10
* CL1

Note: Most VMs are required during the setup only. After setup, only these VMs are required:

* VN1-SRV1
* VN1-SRV8
* CL1

## Setup

1. Connect to the virtual machine **WIN-CL1**.
1. In the WIN-CL1 Virtual Machine Connect, on the menu click **Media**, **DVD Drive**, **Insert Disk...**
1. In Open, open **C:\\Labs\\ISOs\\2022_x64_EN_Eval.iso**.
1. On **CL1**, sign in as **ad\Administrator**.
1. Run **Terminal** as Administrator.
1. In Terminal, execute ````C:\LabResources\Solutions\Authorize-DHCP.ps1````.
1. Connect to the virtual machine **WIN-VN1-SRV8**.
1. In the WIN-VN1-SRV8 Virtual Machine Connect, on the menu click **Media**, **DVD Drive**, **Insert Disk...**
1. In Open, open **C:\\Labs\\ISOs\\2022_x64_EN_Eval.iso**.
1. On **VN1-SRV8**, sign in as **ad\Administrator**.

## Introduction

Adatum does not want to handle ISO images or physical media anymore to deploy operating systems. For this reason, Windows Deployment Services should be implemented to enable boot from the network.

## Exercises

1. [Installing and configuring Windows Deployment Services](#exercise-1-installing-and-configuring-windows-deployment-services)
1. [Creating answer files](#exercise-2-creating-answer-files)
1. [Adding images and answer files to WDS](#exercise-3-adding-images-and-answer-files-to-wds)
1. [Using WDS to install operating systems](#exercise-4-using-wds-to-install-operating-systems)

## Exercise 1: Installing and configuring Windows Deployment Services

1. [Install Windows Deployment Services](#task-1-install-windows-deployment-services) on VN1-SRV8
1. [Create a volume](#task-2-create-a-volume) on VN1-SRV8 using one of the additional disks
1. [Configure Windows Deployment Services](#task-3-configure-windows-deployment-services) with the installation folder location R:\\RemoteInstall and to not respond to client computers yet
1. [Enable PXE responses](#task-4-enable-pxe-responses) and authorize the WDS server in DHCP

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
1. On page Size, beside **Volume size**, ensure **1 024** **GB** is filled in, and click **Next >**.
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
1. On page Operation Complete, deactivate the checkbox **Add images to the server now**, and click **Finish**.

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

## Exercise 2: Creating answer files

1. [Install the Windows ADK](#task-1-install-the-windows-adk) for Windows 10, version 2004
2. [Create an answer file for Windows Server setup](#task-2-create-an-answer-file-for-windows-server-setup) to configure input, system, and user locale
3. [Create an answer file for Windows PE](#task-3-create-an-answer-file-for-windows-pe) to configure partitions as follows

    | Type     | Size in MB      |
    |----------|-----------------|
    | MSR      | 16              |
    | EFI      | 100             |
    | Recovery | 500             |
    | Windows  | remaining space |

### Task 1: Install the Windows ADK

Perform this task on CL1.

1. Switch to **Microsoft Edge**.
1. Navigate to <https://learn.microsoft.com/en-us/windows-hardware/get-started/adk-install>.
1. On page Download and install the Windows ADK | Microsoft Learn, click the link **Download the ADK for Windows 11, version 22H2**.
1. Under Download the ADK for Windows 11, version 22h2, click the link **Download the Windows ADK**.
1. Under Downloads, under **adksetup.exe**, click **Open file**.
1. In Windows Assessment and Deployment Kit, on page Specify Location, click **Next**.
1. On page Windows Kits Privacy, make a selection of your choice and click **Next**.
1. On page License Agreement, click **Accept**.
1. On page Select the feature you want to install, deactivate all checkboxes except for the checkbox beside **Deployment Tools**, and click **Install**

    You do not have to wait for the download and installation to complete. The time is dependent on your internet connection.

1. On page Welcome to the Windows Assessment and Deployment Kit, click **Close**.

### Task 2: Create an answer file for Windows Server setup

Perform this task on CL1.

1. Open **File Explorer**.
1. In File Explorer, on **Local Disk (C:)**, create a folder **Windows Images**.
1. Copy **D:\\sources\\install.wim** to **C:\\Windows Images**.

    *Note:* You may rename install.wim to something like Server_2022.wim, but for this lab, it is not required.

1. Open **Windows System Image Manager**.
1. In Windows System Image Manager, in the menu, click **Tools**, **Create Catalog...**
1. In Open a Windows Image, open **C:\\Windows Images\\install.wim**.
1. In Select an Image, under **Select an image in the Windows image file**, click **Wndows Server 2022 SERVERDATACENTERCORE**. Ensure, only **Wndows Server 2022 SERVERDATACENTERCORE** is selected and click **OK**.

    The generation of the catalog file may take up to 5 minutes.

    If time permits, you can select the other images, but the generation of the catalogs will take 5 additional minutes per image.

1. In **Windows System Image Manager**, in the menu, click **File**, **Select Windows Image...**
1. In Select a Windows Image, open **C:\\Windows Images\\install_Windows Server 2022 SERVERDATACENTERCORE**.
1. In **Windows System Image Manager**, in the menu click **File**, **New Answer File...**
1. In the pane **Windows Image**, expand **Windows Server 2022 SERVERDATACENTERCORE(Catalog)**, **Components**.
1. In the context menu of **amd64_Microsoft-Windows-International-Core_10.0.20348.1_neutral**, click **Add Setting to Pass 4 specialize**.
1. In the pane **Answer file**, under **Component**, **4 specialize**, ensure **amd64_Microsoft-Windows-International-Core_10.0.20348.1_neutral** is selected.
1. In the pane **Microsoft-Windows-International-Core Properties**, beside **InputLocale**, type the language code for your keyboard, e.g., **de-DE** for German. Beside **SystemLocale**, and **UserLocale**, type the code for your locale, e.g., **de-AT** for Austria.

    For a list of valid language codes, press F1 or navigate to <https://learn.microsoft.com/en-us/previous-versions/windows/it-pro/windows-8.1-and-8/hh825682(v=win.10)>

1. On the menu, click **File, Save Answer File As...**.
1. Save the file to **\\\\vn1-srv8\\REMINST\\WdsClientUnattend** with the file name **server**.

    For your reference, this is the content of the file server.xml for Austrian locales:

    ````xml
    <?xml version="1.0" encoding="utf-8"?>
    <unattend xmlns="urn:schemas-microsoft-com:unattend">
        <settings pass="specialize">
            <component
                name="Microsoft-Windows-International-Core"
                processorArchitecture="amd64"
                publicKeyToken="31bf3856ad364e35"
                language="neutral"
                versionScope="nonSxS"
                xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State"
                xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            >
                <InputLocale>de-DE</InputLocale>
                <SystemLocale>de-AT</SystemLocale>
                <UserLocale>de-AT</UserLocale>
            </component>
        </settings>
        <cpi:offlineImage 
            cpi:source="catalog:c:/windows images/install_windows server 2022 serverdatacentercore.clg"
            xmlns:cpi="urn:schemas-microsoft-com:cpi"
        />
    </unattend>
    ````

### Task 3: Create an answer file for Windows PE

Perform this task on CL1.

1. Open **Windows System Image Manager**.
1. In Windows System Image Manager, in the menu, click **File**, **Select Windows Image...**.
1. In Select a Windows Image, open **C:\\Windows Images\\install_Windows Server 2022 SERVERDATACENTERCORE.clg**.
1. In **Windows System Image Manager**, click **File**, **New Answer File...**
1. In the pane **Windows Image**, in the context menu of **amd64_Microsoft-Windows-Setup_10.0.20348.1_neutral**, click **Add Setting to Pass 1 windowsPE**.
1. In the pane **Answer file**, expand **Component**, **1 windowsPE**, **amd64_Microsoft-Windows-Setup_10.0.20348.1_neutral**, and click **DiskConfiguration**.
1. In the context-menu of **DiskConfiguration**, click **Insert New Disk**.
1. Ensure **Disk** is selected. In the pane **Disk Properties**, beside **Action**, ensure **AddListItem** is selected. Beside **DiskID**, type **0**. Beside **WillWipeDisk**, click **true**.
1. In the pane **Answer File**, expand **Disk** and click **CreatePartitions**.
1. In the context-menu of **CreatePartitions**, click **Insert New CreatePartition**.
1. Ensure **CreatePartition** is selected. In the pane **CreatePartition Properties** set the properties according to the table below.

    | Property | Value       |
    |----------|-------------|
    | Action   | AddListItem |
    | Extend   | false       |
    | Order    | 1           |
    | Size     | 16          |
    | Type     | MSR         |

1. Repeat steps 10 and 11 to create partitions according to the table below. For all partitions, set **Action** to **AddListItem**.

    | Extend | Order | Size | Type    |
    |--------|-------|------|---------|
    | false  | 2     | 100  | EFI     |
    | false  | 3     | 500  | Primary |
    | true   | 4     |      | Primary |

    *Note:* Partition 3 is the recovery partition. This might not be required for servers. Therefore, you might skip this partition. Consequently, you will have to change the number of partition 4 to 3 in this step and all following steps.

1. In the pane **Answer File**, in the context-menu of **ModifyPartitions**, click **Inser New ModifyPartition**.
1. Ensure **ModifyPartition** is selected. In the pane **CreatePartition Properties** set the properties according to the table below.

    | Property    | Value       |
    |-------------|-------------|
    | Action      | AddListItem |
    | Active      |             |
    | Extend      |             |
    | Format      |             |
    | Label       |             |
    | Letter      |             |
    | Order       | 1           |
    | PartitionID | 1           |
    | TypeID      |             |

1. Repeat steps 16 and 17 to modify partitions according to the table below. For all partitions, set **Action** to **AddListItem**. Leave properties not listed in the table blank.

    | Format | Label    | Letter |Order | PartitionID | TypeID                               |
    |--------|----------|--------|------|-------------|--------------------------------------|
    | FAT32  | System   |        | 2    | 2           |                                      |
    | NTFS   | WinRE    |        | 3    | 3           | de94bba4-06d1-4d40-a16a-bfd50179d6ac |
    | NTFS   | Windows  | C      | 4    | 4           |                                      |

1. In the pane **Answer file**, expand **WindowsDeploymentServices** and click **InstallTo**.
1. In the pane **InstallTo Properties**, beside **DiskID**, type **0**. Beside **PartitionID**, type **4**.
1. On the menu, click **File, Save Answer File As...**.
1. Save the file to **\\\\vn1-srv8\\REMINST\\WdsClientUnattend** with the file name **windowspe**.

    For your reference, this is the content of the file windowpe.xml:

    ````xml
    <?xml version="1.0" encoding="utf-8"?>
    <unattend xmlns="urn:schemas-microsoft-com:unattend">
        <settings pass="windowsPE">
            <component 
                name="Microsoft-Windows-Setup"
                processorArchitecture="amd64"
                publicKeyToken="31bf3856ad364e35"
                language="neutral"
                versionScope="nonSxS"
                xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State"
                xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            >
                <DiskConfiguration>
                    <Disk wcm:action="add">
                        <CreatePartitions>
                            <CreatePartition wcm:action="add">
                                <Order>1</Order>
                                <Size>16</Size>
                                <Type>MSR</Type>
                            </CreatePartition>
                            <CreatePartition wcm:action="add">
                                <Order>2</Order>
                                <Size>100</Size>
                                <Type>EFI</Type>
                            </CreatePartition>
                            <CreatePartition wcm:action="add">
                                <Order>3</Order>
                                <Size>500</Size>
                                <Type>Primary</Type>
                            </CreatePartition>
                            <CreatePartition wcm:action="add">
                                <Order>4</Order>
                                <Extend>true</Extend>
                                <Type>Primary</Type>
                            </CreatePartition>
                        </CreatePartitions>
                        <ModifyPartitions>
                            <ModifyPartition wcm:action="add">
                                <Order>1</Order>
                                <PartitionID>1</PartitionID>
                            </ModifyPartition>
                            <ModifyPartition wcm:action="add">
                                <Order>2</Order>
                                <PartitionID>2</PartitionID>
                                <Label>System</Label>
                                <Format>FAT32</Format>
                            </ModifyPartition>
                            <ModifyPartition wcm:action="add">
                                <PartitionID>4</PartitionID>
                                <Order>4</Order>
                                <Letter>C</Letter>
                                <Label>Windows</Label>
                                <Format>NTFS</Format>
                            </ModifyPartition>
                            <ModifyPartition wcm:action="add">
                                <TypeID>
                                    de94bba4-06d1-4d40-a16a-bfd50179d6ac
                                </TypeID>
                                <PartitionID>3</PartitionID>
                                <Label>WinRE</Label>
                                <Format>NTFS</Format>
                                <Order>3</Order>
                            </ModifyPartition>
                        </ModifyPartitions>
                        <DiskID>0</DiskID>
                        <WillWipeDisk>true</WillWipeDisk>
                    </Disk>
                </DiskConfiguration>
                <WindowsDeploymentServices>
                    <ImageSelection>
                        <InstallTo>
                            <DiskID>0</DiskID>
                            <PartitionID>4</PartitionID>
                        </InstallTo>
                    </ImageSelection>
                </WindowsDeploymentServices>
            </component>
        </settings>
        <cpi:offlineImage 
            cpi:source="catalog:c:/windows images/install_windows server 2022 serverdatacentercore.clg"
            xmlns:cpi="urn:schemas-microsoft-com:cpi" 
        />
    </unattend>
    ````

## Exercise 3: Adding images and answer files to WDS

1. [Add a boot image and configure the answer file for it](#task-1-add-a-boot-image-and-configure-the-answer-file-for-it); use boot.wim from the Windows Server ISO
2. [Add installation images and configure the answer file for it](#task-2-add-an-installation-image-and-configure-the-answer-file-for-it); add images from the Windows Server ISO

### Task 1: Add a boot image and configure the answer file for it

Perform this task on VN1-SRV8.

1. Open **Windows Deployment Services**.
1. In Windows Deployment Services, expand **Servers**, **VN1-SRV8.ad.adatum.com**, and click **Boot Images**.
1. In the context-menu of **Boot Images**, click **Add Boot Image...**.
1. In Add Image Wizard, on page Image File, click **Browse...**
1. In Select Windows Image File, open **D:\\sources\\boot.wim**.
1. In **Add Image Wizard**, on page **Image File**, click **Next >**.
1. On page Image Metadata, click **Next >**.
1. On page Summary, click **Next >**.
1. On page Task Progress, click **Finish**.
1. In **Windows Deployment Services**, in the context-menu of **VN1-SRV8.ad.adatum.com**, click **Properties**.
1. In VN1-SRV8 Properties, click the tab **Client**.
1. On the tab Client, activate the checkbox **Enable unattended installation**. Beside **x64 (UEFI) architecture**, click **Browse...**.
1. In Choose Unattend File, open **R:\\RemoteInstall\\WdsClientUnattend\\windowspe.xml**.
1. In **VN1-SRV8 Properties**, click **OK**.

### Task 2: Add an installation image and configure the answer file for it

Perform this task on VN1-SRV8.

1. Open **Windows Deployment Services**.
1. In Windows Deployment Services, expand **Servers**, **VN1-SRV8.ad.adatum.com**, and click **Install Images*.
1. In the context-menu of **Install Images**, click **Add Image Group...**
1. In Add Image Group, under **Enter a name for the image group**, type **Windows Server 2022** and click **OK**.
1. In **Windows Deployment Services**, in the context-menu of **Windows Server 2022**, click **Add Install Image...**
1. In Add Image Wizard, on page Image File, click **Browse...**
1. In Select Windows Image File, open **D:\\sources\\install.wim**.
1. In **Add Image Wizard**, on page **Image File**, click **Next >**.
1. On page Available Images, ensure all checkboxes are activated, and click **Next >**.
1. On page Summary, click **Next >**.
1. On page Task Progress, click **Finish**.
1. In **Windows Deployment Services**, in the context-menu of **Windows Server 2022 SERVERDATACENTERCORE**, click **Properties**.
1. In Image Properties, activate the checkbox **Allow image to install in unattended mode** and click **Select File...**
1. In Select Unattend File, click **Browse...**
1. In Select Unattend File, open **R:\\RemoteInstall\\WdsClientUnattend\\server.xml**.
1. In **Select Unattend File**, click **OK**.
1. In **Image Properties**, click **OK**.

## Exercise 4: Using WDS to install operating systems

Install a new server using WDS.

### Task: Install a new server using WDS

Perform these steps on the host computer.

1. Run **Windows PowerShell** as Administrator.
1. In Windows PowerShell, execute

    ````powershell
    C:\Labs\Resources\New-VM.ps1 -Name VN1-SRV21
    ````

1. In **Hyper-V Manager**, double-click **WIN-VN1-SRV21** to open the virtual machine connection.
1. In WIN-VN1-SRV21 on ... - Virtual Machine Connection, click **Start**.
    After a few seconds, you should see a screen with this information (the **Client IP** may vary):

    ````txt
    WDS Boot Manager version 0800
    Client IP: 10.1.1.6
    Server IP: 10.1.1.64
    Server Name: VN1-SRV8.ad.adatum.com

    Press ENTER for network boot service.
    ````

1. Press ENTER.

    You have a few seconds time only to press ENTER. If you fail to press the key on time, restart the virtual machine.

1. On page Windows Deployment Services, select a Locale and Keyboard or input method of your choice and click **Next**.
1. In Connect to VN1-SRV8.ad.adatum.com, enter the credentials of **ad\Administrator**.
1. In **Microsoft Server Operating Setup**, on page Select the operating system you want to install, click **Windows Server 2022 SERVERDATACENTERCORE** and click **Next**.

    Applying the image will take a few minutes.

1. In C:\\Windows\\system32\\LogonUI.exe, ensure **OK** is selected and press ENTER.
1. Under Enter new credentials for Administrator or hit ESC to cancel, beside **New Password** and **Confirm password**, enter a secure password for the local administrator.
1. Under Your password has been changed, press ENTER.
1. In SConfig, enter **15**.

    Verify you keyboard layout.
