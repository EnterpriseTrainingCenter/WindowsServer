# Practice: Install Windows Server Update Services

## Required VMs

* VN1-SRV1
* VN1-SRV4
* VN1-SRV5
* VN2-SRV1
* CL1

## Task

Install the Windows Server Update Services role on VN1-SRV5 and VN2-SRV1.

Note: In real world, it is recommended to use a separate volume for WSUS.

Note: Depending on previous labs, the Windows Server Update Services role might already be installed on VN1-SRV5. In this case, in Windows Admin Center, skip steps 4 - 6, or, in PowerShell, skip step 2.

## Instructions

### Desktop experience

Perform this task on CL1.

1. Sign in as **ad\Administrator**.
1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on the page **Before You Begin**, click **Next >**.
1. On the page Installation Type, ensure **Role-based or feature-based installation** is selected and click **Next >**.
1. On the page Server Selection, click **VN1-SRV5.ad.adatum.com** and click **Next >**.
1. On the page Server Roles, activate the checkbox next to **Windows Server Update Services**.
1. In Add features that are required for Windows Server Update Services, click **Add Features**.
1. In the **Add Rules and Features Wizard**, on the page **Server Roles**, click **Next >**.
1. On the page Features, click **Next >**.
1. On the page WSUS Server, click **Next >**.
1. On the page Role Services, ensure **WID Connectivity** and **WSUS Services** are activated, while **SQL Server Connectivity** is deactivated. Click **Next >**.
1. On page Content, ensure the check box **Store updates in the following location (choose) a valid local path on VN1-SRV5.ad.adatum.com, or a remote path** is activated, and, below, type C:\WSUS. Click **Next >**.

1. On page Web Server Role (IIS), click **Next >**.
1. On page Role Services, click **Next >**.
1. On the page Confirmation, verify your selection and click **Install**.
1. On the page **Results**, click **Close**.

Repeat the steps of this task to install the role on **VN2-SRV1**.

### Windows Admin Center

Perform this task on CL1.

1. Sign in as **ad\Administrator**.
1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, on the connections page, click **vn1-srv6.ad.adatum.com**.
1. Connected to vn1-srv5.ad.adatum.com, under **Tools**, click **Roles & features**.
1. In Roles and Features, click **Windows Server Update Services**. Deactivate the checkbox beside **SQL Server Connectivity**. Click **Install**.
1. In the pane Install Role and Features, activate the checkbox **Reboot the server automatically, if required** and click **Yes**.

    After a few minutes, a notification **Install Roles and Features** appears. If you missed the notification, a small number appears beside the icon *Notifications* (in form of a bell) at the top-right of Windows Admin Center.

1. Under **Tools**, click **PowerShell**.
1. Under PowerShell, enter the password for AD\Administrator.
1. Run postinstall for the WSUS server using the WID role to store downloaded update files in the directory C:\WSUS.

    ````powershell
    & 'C:\Program Files\Update Services\Tools\WsusUtil.exe' postinstall CONTENT_DIR=C:\WSUS
    ````

1. Notify Server Manager that post-install WSUS configuration is complete.

    ````powershell
    Set-ItemProperty `
        –Path HKLM:\SOFTWARE\Microsoft\ServerManager\Roles\10 `
        –Name ConfigurationState `
        –Value 2
    ````

1. Exit from the PowerShell session.

    ````powershell
    Exit-PSSession
    ````

Repeat the steps of this task to install the role on **VN2-SRV1**.

### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install Windows Server Update Services role on VN1-SRV5 and VN2-SRV1.

    ````powershell
    $computerName = 'VN1-SRV5', 'VN2-SRV1'
    Invoke-Command -ComputerName $computerName -ScriptBlock {
        Install-WindowsFeature `
            -Name UpdateServices-WidDB, UpdateServices-Services `
            -IncludeManagementTools `
            -Restart
    }
    ````

1. Run postinstall for the WSUS server using the WID role to store downloaded update files in the directory C:\WSUS.

    ````powershell
    Invoke-Command -ComputerName $computerName -ScriptBlock {
        & 'C:\Program Files\Update Services\Tools\WsusUtil.exe' postinstall CONTENT_DIR=C:\WSUS
    }
    ````


1. Add security groups to DHCP servers.

    ````powershell
    Invoke-Command -ComputerName $computerName -ScriptBlock {
        Add-DhcpServerSecurityGroup 
    }

1. Notify Server Manager that post-install WSUS configuration is complete.

    ````powershell
    Invoke-Command -ComputerName $computerName -ScriptBlock {
        Set-ItemProperty `
            –Path HKLM:\SOFTWARE\Microsoft\ServerManager\Roles\10 `
            –Name ConfigurationState `
            –Value 2
    }
    ````
