# Lab: Managing Windows Server Update Services

## Required VMs

* VN1-SRV1
* VN1-SRV5
* VN2-SRV1
* CL1
* CL2
* CL3

1. On **CL1**, sign in as **ad\administrator**.
1. On **CL2**, sign in as **ad\administrator**.
1. On **CL3**, sign in as **ad\administrator**.

*Note*: This lab is fully functional only if the synchronization started in [Practice: Create Windows Server Update Services automatic approval rules](/Instructions/Practices/Create-Windows-Server-Update-Services-automatic-approval-rules.md) has completed. You can check this in the **Update Services** console, under **Synchronizations**. You might have problems with the following exercises and tasks if synchronization has not completed yet:

* [Exercise 2: Manage update approvals](#exercise-2-manage-update-approvals): You might not find critical updates for Windows 11 to approve.
* [Exercise 3: Configure Windows Server Update Services for a branch office](#exercise-3-configure-windows-server-update-services-for-a-branch-office)
    * [Task 1: Configure Windows Server Update Services as replica](#task-1-configure-windows-server-update-services-as-replica): The synchronization might fail.
* [Exercise 4: Use Reports](#exercise-4-use-reports)
    * [Task 2: View reports](#task-2-view-reports): You might see an incomplete report.

You may revisit this lab and complete these tasks later.

## Introduction

To finish configuration of Windows Server Update Services, you need to configure the clients. To save on bandwith, Adatum decides to install a replica server in the branch office (VNet2). Because Adatum does not want to have a Windows Server Update Services server in the remote office VNet3, clients in this location should use Windows Update for Business. To validate the benefits of Windows Server Update Services, you want to test the manual approval of a critical updates, as well as the reporting features.

## Exercises

1. [Configure clients for Windows Server Update Services](#exercise-1-configure-clients-for-windows-server-update-services)
1. [Manage update approvals](#exercise-2-manage-update-approvals)
1. [Configure a replica Windows Server Update Services server](#exercise-3-configure-windows-server-update-services-for-a-branch-office)
1. [Use Reports](#exercise-4-use-reports)
1. [Configure Windows Update for Business](#exercise-5-configure-windows-update-for-business)
1. [Run maintenance tasks on Windows Update Services](#exercise-6-run-maintenance-tasks-on-windows-update-services)

## Exercise 1: Configure clients for Windows Server Update Services

1. [Configure Windows Update settings](#task-1-configure-windows-update-settings):

    * Auto-restart must not happen during 8 AM and 6 PM.
    * Quality updates must be installed within 7 days, and the restart must happen within 1 day.
    * Feature updates must be installed within 30 days, and the restart must happen within 7 days.
    * Pause update must not be allowed.

1. [Configure clients to use WSUS server](#task-2-configure-clients-to-use-wsus-server) VN1-SRV5
1. [Update group policies on clients](#task-3-update-group-policies-on-clients) CL1, CL2, and CL3
1. [Check for Windows updates](#task-4-check-for-windows-updates) on CL1, CL2, and CL3
1. [Move computers into computer groups](#task-5-move-computers-into-groups): CL1 to Insider, CL2 to Target, and CL3 to Standard

### Task 1: Configure Windows Update settings

Perform this task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Domains**, **ad.adatum.com**, **Devices**, and click **Clients**.
1. In the context-menu of **Clients**, click **Create a GPO in this domain, and Link it here...**
1. In New GPO, under **Name**, type **Custom Computer Windows Update** and click **OK**.
1. In **Group Policy Management**, in the context-menu of **Custom Computer Windows Update**, click **Edit**.
1. In Group Policy Management Editor, expand **Computer Configuration**, **Polcicies**, **Administrative Templates**, **Windows Components**, **Windows Update** and click **Manage end user experience**.
1. Under Manage end user experience, double-click **Turn off auto-restart for updates during active hours**.
1. In Turn off auto-restart for updates during active hours, click **Enabled**. Beside **End**, click **6 PM**. Click **OK**.
1. In **Group Policy Management Editor**, under **Manage end user experience**, double-click **Specify deadlines for automatic updates and restarts**.
1. In Specify deadlines for automatic updates and restarts, click **Enabled**. Under **Quality Updates**, ensure that for **Deadline (days)** **7** is selected. Beside **Grace period (days)**, click **1**. Under **Feature Uupdates**, beside **Deadline (days)**, click **30**. Beside **Grace period (days)**, click **7**. Click **OK**.
1. In **Group Policy Management Editor**, under **Manage end user experience**, double-click **Remove access to "Pause updates" feature**.
1. In Remove access to "Pause updates" feature, click **Enabled** and click **OK**.
1. In **Group Policy Management Editor**, under **Administrative Templates**, click **Sytem**.
1. Under System, double-click **Specify settings for optional component installation and component repair**.
1. In Specify settings for optional component installation and component repair, click **Enabled** and **Download repair content and optional features directly from Windows Update instead of Windows Server Update Services (WSUS)**. Click **OK**.
1. Close **Group Policy Management Editor**.

### Task 2: Configure clients to use WSUS server

Perform this task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Domains**, **ad.adatum.com**, **Devices**, and click **Clients**.
1. In the context-menu of **Clients**, click **Create a GPO in this domain, and Link it here...**
1. In New GPO, under **Name**, type **Custom Computer WSUS HQ client** and click **OK**.
1. In **Group Policy Management**, in the context-menu of **Custom Computer WSUS HQ client**, click **Edit**.
1. In Group Policy Management Editor, expand **Computer Configuration**, **Polcicies**, **Administrative Templates**, **Windows Components**, **Windows Update** and click **Manage updates offered from Windows Server Update Service**.
1. Under Manage updates offered from Windows Server Update Service, double-click **Specify intranet Microsoft update service location**.
1. In Specify intranet Microsoft update service location, click **Enabled**. Under **Set the intranet update service for detecting updates** and beside **Set the intranet statics server**, type **http://vn1-srv5.ad.adatum.com:8530**. Click **OK**.
1. Close **Group Policy Management Editor**.

### Task 3: Update group policies on clients

Perform this task on CL1.

1. Open **Terminal**.
1. Query computers in the OU **clients** and store them in a variable.

    ```powershell
    $aDComputerClients = Get-ADComputer `
        -Filter * -SearchBase 'ou=Clients, ou=devices, dc=ad, dc=adatum, dc=com'
    ````

1. Update the group policies on the computers found in the previous step.

    ````powershell
    Invoke-Command -ComputerName $aDComputerClients.DNSHostName -ScriptBlock {
        gpupdate.exe /force 
    }
    ````

### Task 4: Check for Windows updates

Perform this task on CL1, CL2, and CL3.

1. Open **Settings**.
1. In Settings, click **Windows Update**.
1. Under Windows Update, click **Check for Updates**.

This might take some time. Do not wait for the process to finish.

### Task 5: Move computers into groups

Perform this task on CL1.

1. Open **Windows Server Update Services**.
1. In Update Services, expand **VN1-SRV5**, **Computers**, and click **All Computers**.

    If you do not see **VN1-SRV5**, perform these steps:

    1. In Update Services, in the context-menu of **Update Services**, click **Connect to Server...**
    1. In Connect to Server, beside **Server name**, type **VN1-SRV5** and click **Connect**.

1. Under All Computers, beside **Status**, click **Any** and click **Refresh**.
1. In the context-menu of **cl1.ad.adatum.com**, click **Change Membership...**
1. In Set Computer Group Membership, activate **Insider** and click **OK**.
1. In the context-menu of **cl2.ad.adatum.com**, click **Change Membership...**
1. In Set Computer Group Membership, activate **Target** and click **OK**.
1. In the context-menu of **cl3.ad.adatum.com**, click **Change Membership...**
1. In Set Computer Group Membership, activate **Standard** and click **OK**.

## Exercise 2: Manage update approvals

1. [Create an update view](#task-1-create-an-update-view) to find critical updates for Windows 11
1. [Manually approve or decline updates](#task-2-manually-approve-or-decline-updates): Approve a critical update for Windows 11 to install immediately on all computers

### Task 1: Create an update view

Perform this task on CL1.

1. Open **Windows Server Update Services**.
1. In Update Services, expand **VN1-SRV5** and click **Updates**.

    If you do not see **VN1-SRV5**, perform these steps:

    1. In Update Services, in the context-menu of **Update Services**, click **Connect to Server...**
    1. In Connect to Server, beside **Server name**, type **VN1-SRV5** and click **Connect**.

1. In the context-menu of **Updates**, click **New Update View...**
1. In Add Update View, under **Step 1: Select properties**, activate **Updates are in a specific classification** and **Updates are for a specific product**.
1. Under **Step 2: Edit the properties (click an underlined value)**, click **any classification**.
1. In Choose Update Classification, deactivate **All Classifications**, activate **Critical Updates**, and click **OK**.
1. In **Add Update View**, under **Step 2: Edit the properties (click an underlined value)**, click **any product**.
1. In Choose Products, deactivate **All Products**, activate **Windows 11 Dynamic Update**, and click **OK**.
1. In **Add Update View**, under **Step 3: Specify a name**, type **Critical Windows 11 updates** and click **OK**.
1. In **Update Services**, click **Critical Windows 11 updates**.
1. Under Critical Windows 11 updates, beside **Approval**, click **Any Except Declined**, beside **Status**, click **Any**, and click **Refresh**.

    You should see some updates.

### Task 2: Manually approve or decline updates

Perform this task on CL1.

1. Open **Windows Server Update Services**.
1. In Update Services, expand **VN1-SRV5**, **Updates**, and click **Critical Windows 11 updates**.

    If you do not see **VN1-SRV5**, perform these steps:

    1. In Update Services, in the context-menu of **Update Services**, click **Connect to Server...**
    1. In Connect to Server, beside **Server name**, type **VN1-SRV5** and click **Connect**.

1. Under Critical Windows 11 updates, beside **Approval**, click **Any Except Declined**, beside **Status**, click **Any**, and click **Refresh**.
1. In the context-menu of an update for x64-based Systems not superseded by another update (e.g., KB5007575), click **Approve...**.
1. In Approve Updates, click the icon left to **All Computers**, and click **Aproved for Install**. Click the icon again, and cick **Apply to Children**.
1. Click **OK**.
1. In Approval Progress, wait for the approval to have a result of **Success** and click **Close**.

## Exercise 3: Configure Windows Server Update Services for a branch office

1. [Configure Windows Server Update Services as replica](#task-1-configure-windows-server-update-services-as-replica): VN2-SRV1 should be a replica of VN1-SRV5
1. [Create organizational unit](#task-2-create-organizational-unit) Branch office under the organizational unit Clients
1. [Move client to organizational unit](#task-3-move-client-to-organizational-unit): CL2 to Branch Office
1. [Configure branch office clients to use replica WSUS server](#task-4-configure-branch-office-clients-to-use-replica-wsus-server)
1. [Update group policies on branch office client](#task-5-update-group-policies-on-branch-office-client) CL2
1. [Check for Windows updates on branch office computer](#task-6-check-for-windows-updates-on-branch-office-computer) CL2
1. [Verify the branch office client is connected to the downstream server](#task-7-verify-the-branch-office-client-is-connected-to-the-downstream-server)

### Task 1: Configure Windows Server Update Services as replica

#### Desktop experience

Perform these steps on CL1.

1. Open **Terminal**.
1. On VN2-SRV1, increase the private memory limit of the WsusPool application pool to 3 GB.

    ````powershell
    Invoke-Command -ComputerName VN2-SRV1 -ScriptBlock {
        Import-Module WebAdministration
        $path = 'IIS:\AppPools\WsusPool'
        Set-ItemProperty `
            -Path $path `
            -Name queueLength `
            -Value 2000
        Set-ItemProperty `
            -Path $path `
            -Name processModel.idleTimeout `
            -Value ([TimeSpan]::FromMinutes(0))
        Set-ItemProperty `
            -Path $path `
            -Name processModel.pingingEnabled `
            -Value $false
        Set-ItemProperty `
            -Path $path `
            -Name recycling.periodicRestart.privateMemory `
            -Value 0
        Set-ItemProperty `
            -Path $path `
            -Name recycling.periodicRestart.time `
            -Value ([TimeSpan]::FromMinutes(0))
    }
    ````

1. Open **Windows Server Update Services**.
1. In Update Services, in the context-menu of **Update Services**, click **Connect to Server...**
1. In Connect to Server, beside **Server name**, type **VN2-SRV1** and click **Connect**.

    The Windows Server Update Services Configuration Wizard:VN2-SRV1 opens. If not, follow these steps:

    1. In Update Services, expand **Update Services**, **VN2-SRV1** and click **Options**.
    1. In Options, click **WSUS Server Configuration Wizard**.

1. In Windows Server Update Services Configuration Wizard:VN2-SRV1, on page Before You Begin, click **Next >**.
1. On page Microsoft Update Improvement Program, click **Next >**.

    Optionally, you might deactivate **Yes, I would like to join the Microsoft Update Improvement Program** before clicking **Next >**.

1. On page Choose Upstream Server, click **Synchronize from another Windows Server Update Services server**. Beside **Server name**, type **vn1-srv5.ad.adatum.com**. Activate **This is a replica of the upstream server**. Click **Next >**.
1. On page Specify Proxy Server, ensure, **Use a proxy server when synchronizing** is deactivated and click **Next >**.
1. Click **Start Connecting**.

    Wait for products and languages to finish synchronizing. This will take a few seconds.

1. On page Connect to Upstream Server, click **Next >**.
1. On page Choose Languages, ensure **Download updates in all languages, including new languages** is selected and click **Next >**.
1. On page Configure Sync Schedule, click **Synchronize automatically**. Beside **First synchronization**, enter a time during off-peak hours, about 1 or 2 hours after the time of VN1-SRV5, e.g. 20:00:00. Beside **Synchronizations per day**, ensure **1** is filled in. Click **Next >**.
1. On page Finished, activate **Begin initial synchronization** and click **Next >**.
1. On page What's Next, click **Finish**.

#### PowerShell

Perform these steps on CL1.

1. Open **Terminal**.
1. Increase the private memory limit of the WsusPool application pool to 3 GB.

    ````powershell
    Invoke-Command -ComputerName VN1-SRV5 -ScriptBlock {
        Import-Module WebAdministration
        Set-ItemProperty `
            -Path IIS:\AppPools\WsusPool `
            -Name recycling.periodicRestart.privateMemory `
            -Value (3GB) 
    }
    ````

1. Connect to Update Services on **VN2-SRV1**

    ````powershell
    $wsusServer = Get-WsusServer -Name vn2-srv1 -PortNumber 8530
    ````

1. Configure VN2-SRV1 as replica of **vn1-srv5.ad.adatum.com**.

    ````powershell
    Set-WsusServerSynchronization `
        -UpdateServer $wsusServer `
        -UssServerName vn1-srv5.ad.adatum.com `
        -PortNumber 8530 `
        -Replica
    ````

1. Configure VN2-SRV1 to synchronize once a day starting approximately 2 hours after the synchronization of VN1-SRV5, e.g. 8 pm.

    ````powershell
    $subscription = $wsusServer.GetSubscription()
    $subscription.SynchronizeAutomatically = $true
    $subscription.SynchronizeAutomaticallyTimeOfDay = `
        (New-TimeSpan -Hours 20 -Minutes 0 -Seconds 0)
    $subscription.NumberOfSynchronizationsPerDay = 1
    $subscription.Save()
    ````

1. Start the synchronization.

    ````powershell
    $subscription.StartSynchronization()
    ````

### Task 2: Create organizational unit

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, cick **ad (local)**.
1. Under **ad (local)**, double-click **Devices**.
1. Under **devices**, in the context-menu of **Clients**, click **New**, **Organizational Unit**.
1. In Create Organizational Unit, beside **Name**, type **Branch office** and click **OK**.

### Task 3: Move client to organizational unit

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **Global Search**.
1. Under Global Search, in **Search**, type **CL2** and click **Search**.
1. In the context-menu of **CL2**, click **Move...**
1. In Move, click **Devices**, **Clients**, **Branch office**. Click **OK**.

### Task 4: Configure branch office clients to use replica WSUS server

Perform this task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Domains**, **ad.adatum.com**, **Devices**, **Clients**, and click **Branch office**.
1. In the context-menu of **Branch office**, click **Create a GPO in this domain, and Link it here...**
1. In New GPO, under **Name**, type **Custom Computer WSUS branch office client** and click **OK**.
1. In **Group Policy Management**, in the context-menu of **Custom Computer WSUS branch office client**, click **Edit**.
1. In Group Policy Management Editor, expand **Computer Configuration**, **Polcicies**, **Administrative Templates**, **Windows Components**, **Windows Update** and click **Manage updates offered from Windows Server Update Service**.
1. Under Manage updates offered from Windows Server Update Service, double-click **Specify intranet Microsoft update service location**.
1. In Specify intranet Microsoft update service location, click **Enabled**. Under **Set the intranet update service for detecting updates** and beside **Set the intranet statics server**, type **http://vn2-srv1.ad.adatum.com:8530**. Click **OK**.
1. Close **Group Policy Management Editor**.

### Task 5: Update group policies on branch office client

Perform this task on CL1.

1. Open **Terminal**.
1. Update the group policies on CL2.

    ````powershell
    Invoke-Command -ComputerName CL2 -ScriptBlock {
        gpupdate.exe /force 
    }
    ````

### Task 6: Check for Windows updates on branch office computer

Perform this task on CL2.

1. Open **Settings**.
1. In Settings, click **Windows Update**.
1. Under Windows Update, click **Check for Updates**.

    This might take a minute or two.

### Task 7: Verify the branch office client is connected to the downstream server

Perform this task on CL1.

1. Open **Windows Server Update Services**.
1. In Update Services, expand **VN2-SRV1**, **Computers**, and click **All Computers**.

    If you do not see **VN2-SRV1**, perform these steps:

    1. In Update Services, in the context-menu of **Update Services**, click **Connect to Server...**
    1. In Connect to Server, beside **Server name**, type **VN2-SRV1** and click **Connect**.

1. Under All Computers, beside **Status**, click **Any** and click **Refresh**.

    > You should see cl2.ad.adatum.com.

## Exercise 4: Use Reports

1. [Install Microsoft Report Viewer 2012 Runtime](#task-1-install-microsoft-report-viewer-2012-runtime) on CL1
1. [View reports](#task-2-view-reports)

### Task 1: Install Microsoft Report Viewer 2012 Runtime

Perform this task on CL1.

1. Open **Microsoft Edge**.
1. Navigate to <https://www.microsoft.com/en-us/download/details.aspx?id=29065>.
1. On page Download MICROSOFT SQL SERVER 2012 Feature Pack, beside **Select Language**, ensure **English** is selected.
1. Click on **Install Instructions**.
1. Under **Microsoft System CLR Types for Microsoft SQL Server 2012**, click **X64 Package**.
1. Under Microsoft Edge's **Downloads** icon, beside the message **SQLSysClrTypes.msi can't be downloaded securely**, click the ellipsis and click **Keep**.
1. In This file can't be downloaded securely, click **Keep anyway**.
1. Click **Open file**.
1. In Microsoft System CLR Types for SQL Server 2012 Setup, on page Welcome to the Installation Wizard for Microsoft System CLR Types for SQL Server 2012, click **Next >**.
1. On page License Agreement, click **I accept the terms in the license agreement** and click **Next >**.
1. On page Ready to Install the Program, click **Install**.
1. On page Completing the Microsoft System CLR Types for SQL Server 2012 installation, click **Finish**.
1. Switch to **Microsoft Edge**.
1. Navigate to <https://www.microsoft.com/en-us/download/confirmation.aspx?id=35747>.
1. On page Download MICROSOFT REPORT VIEWER 2012 RUNTIME, beside **Select Language**, ensure **English** is selected and click **Download**.
1. Under Microsoft Edge's **Downloads** icon, under **ReportViewer.msi**, click **Open file**.
1. In Microsoft Report Viewer 2012 Runtime, on page Welcome to the Installation Wizard for Microsoft Report Viewer 2012 Runtime, click **Next >**.
1. On page License Agreement, click **I accept the terms in the license agreement** and click **Next >**.
1. On page Ready to Install the Program, click **Install**.
1. On page Completing the Microsoft Report Viewer 2012 Runtime installation, click **Finish**.

### Task 2: View reports

Perform this task on CL1.

1. Open **Windows Server Update Services**.
1. In Update Services, expand **VN1-SRV5**, and click **Reports**.

    If you do not see **VN1-SRV5**, perform these steps:

    1. In Update Services, in the context-menu of **Update Services**, click **Connect to Server...**
    1. In Connect to Server, beside **Server name**, type **VN1-SRV5** and click **Connect**.

1. Under Reports, click **Update Status Summary**.

    Updates Reports for VN2-SRV1 opens. You might want to configure report options by clicking the links at the top of the report.

1. In Reports for VN2-SRV1 opens, in the menu, click **Run Report**.

    Generation of the report take a few seconds. Review the report. Be sure to review the additional pages. You can click on the buttons of the page navigation icons at the top or click a specific update in the tree on the left-hand side.

    Click **Report Options** in the menu to show the report options again, where you can change the filters. You need to click **Run Report** to regenerate the report with the new filters.

    Moreover, with the menu command **Tasks**, **Approve...** or **Decline**, you could approve or decline all updates in the report. You can also click on the links in the **Approval** column of a certain update to change the status of an update.

1. Close **Updates Report for VN1-SRV5**.

If time permits, you might want to review other reports.

## Exercise 5: Configure Windows Update for Business

1. [Create organizational unit](#task-1-create-organizational-unit) Remote office under the organizational unit Clients
1. [Move client to organizational unit](#task-2-move-client-to-organizational-unit): CL3 to Remote Office
1. [Configure remote office clients for Windows Update for Business](#task-3-configure-remote-office-clients-for-windows-update-for-business) and enable delivery optimization
1. [Update group policies on remote office client](#task-4-update-group-policies-on-remote-office-client) CL3
1. [Verify Windows Update for Business on client](#task-5-verify-windows-update-for-business-on-client)

### Task 1: Create organizational unit

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, cick **ad (local)**.
1. Under **ad (local)**, double-click **Devices**.
1. Under **devices**, in the context-menu of **Clients**, click **New**, **Organizational Unit**.
1. In Create Organizational Unit, beside **Name**, type **Remote office** and click **OK**.

### Task 2: Move client to organizational unit

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **Global Search**.
1. Under Global Search, in **Search**, type **CL3** and click **Search**.
1. In the context-menu of **CL3**, click **Move...**
1. In Move, click **Devices**, **Clients**, **Remote office**. Click **OK**.

### Task 3: Configure remote office clients for Windows Update for Business

Perform this task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Domains**, **ad.adatum.com**, **Devices**, **Clients**, and click **Remote office**.
1. In the context-menu of **Branch office**, click **Create a GPO in this domain, and Link it here...**
1. In New GPO, under **Name**, type **Custom Computer Windows Update remote office client** and click **OK**.
1. In **Group Policy Management**, in the context-menu of **Custom Computer Windows Update remote office client**, click **Edit**.
1. In Group Policy Management Editor, expand **Computer Configuration**, **Polcicies**, **Administrative Templates**, **Windows Components** and click **Delivery Optimization**.
1. Under Delivery Optimization, double-click **Download Mode**.
1. In Download Mode, click **Enabled**, beside **Download Mode**, click **LAN (1)**, and click **OK**.
1. In **Group Policy Management Editor**, expand **Windows Update** (under **Windows Components**) and click **Manage updates offered from Windows Server Update Service**.
1. Under Manage updates offered from Windows Server Update Service, double-click **Specify intranet Microsoft update service location**.
1. In Specify intranet Microsoft update service location, click **Disabled** and click **OK**.
1. In **Group Policy Management Editor**, click **Manage updates offered from Windows Update**.
1. Under Manage updates offered from Windows Update, double-click **Select when Preview Builds and Feature Updates are received**.
1. In Select when Preview Builds and Feature Updates are received, click **Enabled**. Under **How many days after a Feature Update is release would you like to defer the update before it is offered to this device?**, type **30**. Click **OK**.
1. In **Group Policy Management Editor**, under **Manage updates offered from Windows Update**, double-click **Select when Quality Updates are received**.
1. In Select when Quality Updates are received, click **Enabled**. Under **After a qulity update is release, defer receiving it for this many days**, type **14**. Click **OK**.
1. Close **Group Policy Management Editor**.

### Task 4: Update group policies on remote office client

Perform this task on CL1.

1. Open **Terminal**.
1. Update the group policies on CL3 found in the previous step.

    ````powershell
    Invoke-Command -ComputerName CL3 -ScriptBlock {
        gpupdate.exe /force 
    }
    ````

### Task 5: Verify Windows Update for Business on client

Perform this task on CL3.

1. Open **Settings**.
1. In Settings, click **Windows Update**.
1. Under Windows Update, click **Advanced options**.
1. Under Advanced Options, click **Delivery Optimization**.

    The option **Allow downloads from other PCs** should be **On** and not changeable. Under **Allow downloads from**, **Devices on my local network** should be selected and not changeable.

1. Click **Windows Update**.
1. Under Windows Update, click **Check for Updates**.

    This might take a minute or two. Probably, some additional updates will appear. You can install them, if you want.

## Exercise 6: Run maintenance tasks on Windows Update Services

On VN1-SRV5, run the server cleanup wizard with all options.

*Note*: Using PowerShell, the additional option ````-CompressUpdates```` is available. Therefore, it is recommended to use PowerShell for this exercise. Moreover, in real world, you might want to run this task automatically using Task Scheduler.

### Task

#### Desktop experience

Perform this task on CL1.

1. Open **Windows Server Update Services**.
1. In Update Services, expand **VN1-SRV5**, and click **Options**.
1. Under Options, click **Server Cleanup Wizard**.
1. In WSUS Server Cleanup Wizard, on page Welcome to the Server Cleanup Wizard, ensure all checkboxes are activated and click **Next >**.

    Wait on page Clean Server for the cleanup to finish.

1. On page Finished, click **Finish**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Connect to Update Services on **VN1-SRV5**

    ````powershell
    $wsusServer = Get-WsusServer -Name vn1-srv5 -PortNumber 8530
    ````

1. Invoke a cleanup with all options.

    ````powershell
    Invoke-WsusServerCleanup `
        -UpdateServer $wsusServer `
        -CleanupObsoleteComputers `
        -CleanupObsoleteUpdates `
        -CleanupUnneededContentFiles `
        -CompressUpdates `
        -DeclineExpiredUpdates `
        -DeclineSupersededUpdates
    ````
