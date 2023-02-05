# Lab: Microsoft Deployment Toolkit

## Required VMs

* VN1-SRV1
* VN1-SRV7
* CL1

## Setup

1. In the menu of **"WIN-CL1" on "..." - Virtual Machine Connection**, click **Media**, **DVD Drive**, **Insert disk...**
1. In **Open**, open **C:\Labs\ISOs\2022_x64_EN_Eval.iso**.
1. On **CL1**, sign in as **ad\administrator**.
1. Open **Terminal**.
1. Configure DHCP.

    ````powershell
    C:\LabResources\Solutions\Authorize-DHCP.ps1
    ````

    You do not have to wait for the script to finish. This takes a few minutes.

## Introduction

Adatum wants to automate some steps when deploying new servers. For this reason, you install and configure Microsoft Deployment Toolkit.

## Exercices

1. Configure a Windows Server installation in Microsoft Deployment Toolkit
1. Install a Windows Server using the Microsoft Deployment Toolkit

## Exercise 1: Configure a Windows Server installation in Microsoft Deployment Toolkit

1. [Prepare a file share](#task-1-prepare-a-file-share) for Microsoft Deployment Toolkit on VN1-SRV7
1. [Configure a deployment share](#task-2-configure-a-deployment-share) and configure localization settings both for the Windows PE phase, and the final operating system
1. [Import the operating system](#task-3-import-the-operating-system) Windows Server 2022
1. [Create a task sequence](#task-4-create-a-task-sequence) to install Windows Server 2022 Datacenter

### Task 1: Prepare a file share

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, on the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before You begin, click **Next >**.
1. On page Installation Type, ensure **Role-based or reature-based installation** is selected, and click **Next >**.
1. On page Server Selection, click **VN1-SRV7.ad.adatum.com** and click **Next >**.
1. On page Server Roles, expand **File and Storage Services (1 of 12 installed)**, **File and iSCSI Services**, and activate the checkbox beside **File Server**. Click **Next >**.
1. On page Select features, click **Next >**.
1. On page Confirm installation selections, activate the checkbox **Restart the destination server automatically if required** and click **Install**.

    Wait for the installation to complete. This takes less than a minute.

1. On page Results, click **Close**.
1. In **Server Manager**, click **File and Storage Services**, **Shares**.
1. Under SHARES, click **Tasks**, **New Share...**
1. In New Share Wizard, on page Select Profile, ensure **SMB Share - Quick** is selected, and click **Next >**.
1. On page Share Location, under **Server**, click **VN1-SRV7** and click **Next >**.
1. On page Share Name, beside **Share name**, type **MDT** and click **Next >**.
1. On page Configure share settings, click **Next >**.

    You may change the settings on this page, if you want.

1. On page Permissions, click **Customize permissions...**
1. In Advanced Security Settings for MDT, on tab Permissions, click **Disable inheritance...**
1. In Block inheritance, click **Convert inherited permissions into explicit permissions on this object.**
1. In **Advanced Security Settings for MDT**, on tab **Permissions**, under **Permission entries**, click the entry **Users (VN1-SRV7\Users) | Allow | Special** and click **Remove**.
1. Click the tab **Share**.
1. On the tab Share, click **Add**.
1. In Permission Entry for MDT, click **Select a principal**.
1. In Select User, Computer, Service Account, or Group, click **Locations...**.
1. In Locations, click **VN1-SRV7.ad.adatum.com** and click **OK**.
1. In **Select User, Computer, Service Account, or Group**, under **Enter the object name to select**, type **Users** and click **OK**.
1. In **Permission Entry for MDT**, ensure that beside **Type** **Allow** is selected, and below **Permissions**, **Read** is activated only. Click **OK**.
1. In **Advanced Security Settings for MDT**, on tab **Share**, click **Add**.
1. In Permission Entry for MDT, click **Select a principal**.
1. In Select User, Computer, Service Account, or Group, under **Enter the object name to select**, type **Administrators** and click **OK**.
1. In **Permission Entry for MDT**, ensure that beside **Type** **Allow** is selected. Activate the checkbox beside **Full Control** and click **OK**.
1. In **Advanced Security Settings for MDT**, on tab **Share**, under **Permission entries**, click then entry **Everyone** and click **Remove**. Click **OK**.
1. In **New Share Wizard**, on page **Permissions**, click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.

### Task 2: Configure a deployment share

Perform this task on CL1.

1. Open **Deployment Workbench**.
1. In DeploymentWorkbench, in the context-menu of **Deployment Shares**, click **New Deployment Share**.
1. In New Deployment Share Wizard, on page Path, under **Deployment share path**, type **\\\\VN1-SRV7\\MDT** and click **Next**.
1. On page Descriptive Name, click **Next**.
1. On page Options, deactivate all checkboxes and click **Next**.
1. On page Summary, click **Next**.

    Wait for the progress. This should take a few seconds only.

1. On page Confirmation, click **Finish**.
1. In **DeploymentWorkbench**, in the context-menu of **MDT Deployment Share (\\\\VN1-SRV7\\MDT))**, click **Properties**.
1. In MDT Deployment Share (\\\\VN1-SRV7\\MDT)) Properties, on tab General, under **Platforms Supported**, deactivate the checkbox **x86**. Click the tab **Rules**.
1. On the tab Rules, at the end, add the following lines and click **Apply**.

    ````ini
    KeyboardLocalePE=0407:00000407
    SkipLocaleSelection=YES
    SkiptimeZone=YES
    UserLocale=de-AT
    KeyboardLocale=0407:00000407
    TimeZoneName=W. Europe Standard Time
    ````

    *Note*: The code above configures a German keyboard, an Austria locale, and the CET time zone. You may want to customize these values for your locale. For **KeyboardLocalePE** and **KeyboardLocale**, find the keyboard identifiers under <https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/windows-language-pack-default-values>. If the code on that page is ````0x00000807````, then use ````0807:00000807```` in the rules. For UserLocale, you can find out your current locale by executing the following PowerShell command.

    ````powershell
    Get-WinSystemLocale
    ````

    To find out your current time zone, use this PowerShell command

    ````powershell
    Get-TimeZone
    ````

1. Click **Edit Bootstrap.ini**.
1. In Bootstrap - Editor, at the end, add the following line.

    ````ini
    KeyboardLocalePE=0407:00000407
    ````

    *Note*: Ensure, you use the same code as in rules above.

1. Close ***Bootstrap - Editor** and save the file.
1. In **MDT Deployment Share (\\\\vn1-srv7\\MDT) Properties**, click **OK**.
1. In **DeploymentWorkbench**, in the context-menu of **MDT Deployment Share (\\\\VN1-SRV7\\MDT))**, click **Update Deployment Share**.
1. In Update Deployment Share Wizard, on page **Options**, click **Next**.
1. On page Summary, click **Next**.

    Wait for the progress. This will take about two minutes.

1. On page Confirmation, click **Finish**.

### Task 3: Import the operating system

Perform this task on CL1.

1. Open **Deployment Workbench**.
1. In **DeploymentWorkbench**, in the context-menu of **Operating Systems**, click **Import Operating System**.
1. In the Import Operating System Wizard, on page OS Type, ensure **Full set of source files** is selected, and click **Next**.
1. On page Source, under **Source directory**, click **Browse...**
1. In Browse for Folder, click **DVD Drive (D:) SSS_X64FREE_EN-US-DV9** and click **OK**.
1. In the **Import Operating System Wizard**, on page **Source**, click **Next**.
1. On page Destination, click **Next**.
1. On page Summary, click **Next**.

    The import will take about a minute. Wait for it.

1. On page Confirmation, click **Finish**.

### Task 4: Create a task sequence

Perform this task on CL1.

1. Open **Deployment Workbench**.
1. In **DeploymentWorkbench**, in the context-menu of **Task Sequences**, click **New Task Sequence**.
1. In the New Task Sequence Wizard, on page General Settings, under **Task sequence ID**, type **WS2022Datacenter**. Under **Task sequence name**, type, **Install Windows Server 2022 Datacenter**. Under Task sequence comments, type **without desktop experience**. Click **Next**.
1. On page Select Template, under **The following task sequence templates are available. Select the one you would like to use as a starting point**, click **Standard Server Task Sequence**. Click **Next**.
1. On page Select OS, click **Windows Server 2022 SERVERDATACENTERCORE in Windows Server 2022 SERVERSTANDARDCORE x64 install.wim** and click **Next**.
1. On page Specify a Product Key, ensure **Do not specify a product key at this time** is selected, and click **Next**.
1. On page OS Settings, under **Full Name**, type your name. Under **Organization**, type your organization. Click **Next**.
1. On page Admin Password, click **Use the specified local Administrator password**. In **Administrator Password** and **Please confirm Administrator Password**, type a secure password and click **Next**.
1. On page Summary, click **Next**.
1. On page Confirmation, click **Finish**.

## Exercise 2: Install a Windows Server using the Microsoft Deployment Toolkit

1. [Prepare a new virtual machine](#task-1-prepare-a-new-virtual-machine) by copying LiteTouchPE_x64.iso from the deployment share to the host and creating a new virtual machine with the name WIN-VN1-SRV20
1. [Run the Microsoft Deployment Kit Wizard](#task-2-run-the-microsoft-deployment-kit-wizard) on WIN-VN1-SRV20 to install Windows Server 2022 Datacenter and join it to the domain
1. [Review logs](#task-3-review-logs) created bey the Microsoft Deployment Tookit on VN1-SRV20
1. [Edit the task sequence (optional)](#task-4-edit-the-task-sequence-optional) to automatically lock the computer during deployment
1. [Verify the deployment (optional)](#task-5-verify-the-deployment-with-automatic-lock-optional) with automatic lock

### Task 1: Prepare a new virtual machine

Perform this task on the host.

1. Create a remote PowerShell to the virtual machine **WIN-VN1-SRV7** and store it in a variable.

    ````powershell
    $pSSession = New-PSSession -VMName WIN-VN1-SRV7
    ````

1. At the prompt for credentials, enter the credentials of **ad\\Administrator**.
1. From the virtual machine, copy **C:\\Shares\\MDT\Boot\\LiteTouchPE_x64.iso** to the host's **C:\\Labs\ISOs**.

    ````powershell
    Copy-Item -FromSession $pSSession -Path C:\Shares\MDT\Boot\LiteTouchPE_x64.iso -Destination C:\Labs\ISOs
    ````

1. Remove the remote PowerShell session

    ````powershell
    Remove-PSSession -Session $pSSession
    ````

1. Create the virtual machine.

    ````powershell
    C:\Labs\Resources\New-VMVN1SRV20.ps1
    ````

### Task 2: Run the Microsoft Deployment Kit Wizard

Perform this task on the host.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, double-click **WIN-VN1-SRV20** to open the console.
1. In WIN-VN1-SRV20 on ... - Virtual Machine Connection, in the menu, click **Media**, **DVD Drive**, **Eject "2022_x64_EN_Eval.iso"**.
1. In the menu, click **Media**, **DVD Drive**, **Insert Disk...**
1. In Open, open **C:\\Labs\\ISOs\\LiteTouchPE_x64.iso**.
1. Click **Start**.
1. As soon as the message Press any key to boot from CD or DVD appears, press any key.

    You have a few seconds time only to press the key. If you fail to press the key on time, restart the virtual machine.

1. In Microsoft Deployment Toolkit, click **Run the Deployment Wizard to install a new Operating System**.

    The keyboard layout should be the configured layout from Bootstrap.ini.

1. In User Credentials, beside **User Name**, type **Administrator**. Beside **Domain**, type **AD**. Beside Password, type the password of **AD\Administrator**. Click **OK**.
1. In Windows Deployment Wizard, on page Task Sequence, click **Install Windows Server 2022 Datacenter** and click **Next**.
1. On page Computer Details, beside **Computer name**, type **VN1-SRV20**. Click **Join a domain**. Beside **Domain to join**, type **ad.adatum.com**. Click **Next**.

    The credentials for the domain join should be filled in already.

    *Note*: Because we configured localization settings in CustomSettings.ini, the wizard will not ask for them.

1. On page Ready, click **Begin**.

    Observe the steps the Lite Touch Installation takes to install Windows Server 2022. This will take a few minutes.

1. Close dialog box **Connect to "WIN-VN-SRV20"** (do not use enhanced session mode for now).

    Lite Touch Installation will take some final steps.

1. In the message box Successful Deployment, click **OK**.

    You are automatically signed in as local administrator. in the optional

1. In WIN-VN1-SRV20 on ... - Virtual Machine Connection, in the menu, click **View**, **Enhanced Session**.
1. In Connect to "WIN-VN1-SRV20", select a resolution and click **Connect**.

### Task 3: Review logs

Perform this task on VN1-SRV20.

1. Sign in using the local administrator password.
1. In SConfig, enter **15**.
1. Navigate to **C:\\Windows\\Temp\\DeploymentLogs**.

    ````powershell
    Set-Location -Path C:\\Windows\\Temp\\DeploymentLogs
    ````

1. Review the file **Wizard.log** in Notepad.

    ````powershell
    notepad.exe Wizard.log
    ````

    > The file contains all the steps, the Windows Deployment Wizard has taken, including your selections.

1. Close Notepad.

1. Review the file **LiteTouch.log** in Notepad.

    ````powershell
    notepad.exe LiteTouch.log
    ````

    > The file contains all the steps, the Lite Touch Installation has taken to deploy Windows Server.

1. Close Notepad.

    > This file consolidates all steps taken to deploy Windows Server, including the Windows PE initialization, the Windows Deployment Wizard, and the Lite Touch Installation.

### Task 4: Edit the task sequence (optional)

Perform this task on CL1.

1. Open **Deployment Workbench**.
1. In **DeploymentWorkbench**, click **Task Sequences**.
1. Under Task Sequences, double-click **Install Windows Server 2022 Datacenter**.
1. In Install Windows Server 2022 Datacenter Properties, under **State Restore**, click **Gather local only**
1. With Gater local only selected, click **Add**, **General**, **Run Command Line**.
1. With Run Command Line selected, on tab Properties, beside **Name**, type **Lock computer**. Under **Command line**, type **rundll32.exe user32.dll,LockWorkStation**.

    *Important*: The term LockWorkStation is case-sensitive.

1. With Run Command Line or Lock Computer selected, click **Up**.

    The task should be the first task in the group State Restore.

1. Click **OK**.

### Task 5: Verify the deployment with automatic lock (optional)

Perform this task on the host computer.

1. Turn off the virtual machine **WIN-VN1-SRV20**.

    ````powershell
    $vMName = 'WIN-VN1-SRV20'
    Stop-VM -VMName $vMName -Force
    ````

1. Delete and recreate the virtual hard disk drive.

    ````powershell
    $vMHardDiskDrive = Get-VMHardDiskDrive -VMName $vMName
    Remove-Item -Path $vMHardDiskDrive.Path
    New-VHD -Dynamic -Path $vMHardDiskDrive.Path -SizeBytes 127GB
    ````

1. Perform [Task 2: Run the Microsoft Deployment Kit Wizard](#task-2-run-the-microsoft-deployment-kit-wizard) again. You may skip steps 3 - 5.

    During the deployment, the computer should remain locked now.

If time permits, you could try to create and verify an additional task sequence to install Windows Server with desktop experience. You may skip verification, as you can verify the task sequence in the next lab.
