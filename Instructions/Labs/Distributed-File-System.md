# Lab: Distributed File System

## Required VMs

* VN1-SRV1
* VN1-SRV4
* VN1-SRV6
* VN1-SRV10
* VN2-SRV1
* CL1
* CL2

## Setup

1. On **CL1**, sign in as **ad\Administrator**.
1. You need sign-in credentials for an active Azure Subscription in which you have the permissions to create resources. Moreover, you need to have the Global Administrator role in Azure Active Directory. Ask your instructor for help, if you are unsure.

## Introduction

Adatum wants unify the namespace of file servers using Distributed File System. Moreover, Adatum wants to increase performance of file access in VNet2. Adatum wants to evaluate DFS-R and Azure File Sync to replicate the file shares to a server in VNet2.

## Exercises

1. [Configuring and validating a DFS namespace](#exercise-1-configuring-and-validating-a-dfs-namespace)
1. [Replicating files with DFS-R and adding DFS targets](#exercise-2-replicating-files-with-dfs-r-and-adding-dfs-targets)
1. [Replicating files with Azure File Sync](#exercise-3-replicating-files-with-azure-file-sync)

## Exercise 1: Configuring and validating a DFS namespace

1. [Install the DFS Namespaces role service](#task-1-install-the-dfs-namespaces-role-service) on VN1-SRV6
1. [Create a domain-based namespace](#task-2-create-a-domain-based-namespace) with the name Company Data on VN1-SRV6
1. [Create DFS folders](#task-3-create-dfs-folders) in the namespace Company Data: Departments and, under Departments, Finance, IT, and Marketing, targeting the respective shares on VN1-SRV10
1. [Validate DFS](#task-4-validate-dfs)

    > What is the actual location of \\\\ad.adatum.com\\Company Data\\Departments?

    > What is the actual location of \\\\ad.adatum.com\\Company Data\\IT?

    > What is the content of \\\\ad.adatum.com\\Company Data\\IT?

### Task 1: Install the DFS Namespaces role service

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on the page **Before You Begin**, click **Next >**.
1. On the page Installation Type, ensure **Role-based or feature-based installation** is selected and click **Next >**.
1. On the page Server Selection, click **VN1-SRV6.ad.adatum.com** and click **Next >**.
1. On the page Server Roles, expand **File and Storage Services**, **File and iSCSI Services**, and activate the checkbox next to **DFS Namespaces**.
1. In Add features that are required for DFS Namespace, click **Add Features**.
1. In the **Add Rules and Features Wizard**, on the page **Server Roles**, click **Next >**.
1. On the page Features, click **Next >**.
1. On the page Confirmation, verify your selection and click **Install**.
1. On the page **Results**, click **Close**.

#### Windows Admin Center

Perform this task on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, on the connections page, click **vn1-srv6.ad.adatum.com**.
1. Connected to vn1-srv6.ad.adatum.com, under **Tools**, click **Roles & features**.
1. In Roles and features, expand **File and Storage Services**, **File and iSCSI Services**, click **DFS Namespaces**, and click **Install**.
1. In the pane Install Role and Features, activate the checkbox **Reboot the server automatically, if required** and click **Yes**.

    After a few minutes, a notification **Install Roles and Features** appears. If you missed the notification, a small number appears beside the icon *Notifications* (in form of a bell) at the top-right of Windows Admin Center.

1. Click **Windows Admin Center** to return to the connections page.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install the DFS Namespaces role service on **VN1-SRV6**.

    ````powershell
        Install-WindowsFeature `
            -ComputerName VN1-SRV6 `
            -Name FS-DFS-Namespace `
            -IncludeManagementTools `
            -Restart
    ````

### Task 2: Create a domain-based namespace

Perform this task on CL1.

1. Open **DFS Management**.
1. In DFS Management, in the context menu of **Namespaces**, click **New Namespace...**
1. In New Namespace Wizard, on page Namespace Server, under **Server**, type **VN1-SRV6** and click **Next >**.
1. On page Namespace Name and Settings, under Name, type **Company Data** and click **Next >**.
1. On page Namespace Type, ensure **Domain-based namespace** is selected and **Enable Windows Server 2008 mode** is activated. Click **Next >**.
1. On page Review and Create Namespace, click **Create**.
1. On page Confirmation, click **Close**.

### Task 3: Create DFS folders

Perform this task on CL1.

1. Open **DFS Management**.
1. In DFS Management, expand **Namespaces**.

    If you do not see **\\\\ad.adatum.com\\Company Data**, perform these steps:

    1. In the context-menu of **Namespaces**, click **Add Namespaces to Display...**
    1. In Add Namespaces to Display, ensure **\\\\ad.adatum.com\\Company Data** is selected. Click **OK**.

1. In the context-menu of **\\\\ad.adatum.com\\Company Data**, click **New Folder...**
1. In New Folder, under Name, type **Departments** and click **OK**.
1. In **DFS Management**, expand **\\\\ad.adatum.com\\Company Data** and **Departments**.
1. In the context-menu of **Departments**, click **New Folder...**
1. In New Folder, under Name, type **Finance**. Under **Folder targets**, click **Add...**
1. In Add Folder Target, click **Browse...**
1. In Browse for Shared Folders, under **Server**, type **VN1-SRV10** and click **Show Shared Folders**. Click **Finance** and click **OK**.
1. In **Add Folder Target**, under **Path to folder target**, ensure **VN1-SRV10\\Finance** is filled in, and click **OK**.
1. In **New Folder**,  click **OK**.

Repeat from step 6 for the shares **IT** and **Marketing**.

### Task 4: Validate DFS

Perform this task on CL1.

1. In **File Explorer**, navigate to **\\\\ad.adatum.com\\Company Data**. You will have to type the full path in the address bar.
1. In \\\\ad.adatum.com\\Company Data, in the context-menu of **Departments**, click **Properties**.
1. In Departments Properties, click the tab **DFS**.

    > You should see the actual locaiton of the Departments folder, which is \\\\vn1-srv6.ad.adatum.com\\Company Data.

1. Click **Cancel**.
1. In **File Explorer**, in \\\\ad.adatum.com\\Company Data, double-click **Departments**.
1. In \\\\ad.adatum.com\\Company Data\\Departments, in the context-menu of **IT**, click **Properties**.
1. In IT Properties, click the tab **DFS**.

    > You see the actual location of the IT folder, which is \\\\vn1-srv10\\IT.

1. Click **Cancel**.
1. In **File Explorer**, in \\\\ad.adatum.com\\Company Data\\Departments, double-click **IT**.

    > The content of the folder IT should be the same as in the share on \\\\vn1-srv10\\IT.

## Exercise 2: Replicating files with DFS-R and adding DFS targets

1. [Create an Active Directory site topology](#task-1-create-an-active-directory-site-topology) for VNet1, VNet2, and VNet3
1. [Install the DFS Namespaces role service on additional server](#task-2-install-the-dfs-namespaces-role-service-on-additional-server) VN2-SRV1
1. [Install the DFS Replication role service](#task-3-install-the-dfs-replication-role-service) on VN1-SRV10 and VN2-SRV1
1. [Add a namespace server](#task-4-add-namespace-server) VN2-SRV1 to the Company Data namespace
1. [Replicate files](#task-5-replicate-files) in shares Finance, IT, and Marketing between VN1-SRV10 and VN2-SRV1
1. [Create additional DFS targets](#task-6-create-additional-dfs-targets) for Finance, IT, and Marketing targeting VN2-SRV1
1. [Validate DFS-R and DFS targets](#task-7-validate-dfs-r-and-dfs-targets) on CL2

    > What is the active path of \\\\ad.adatum.com\\Company Data?

    > What is the active path of \\\\ad.adatum.com\\Company Data\\Departments\\IT?

    > What is the content of \\\\ad.adatum.com\\Departments\\IT?

1. [Delete replication group](#task-8-delete-replication-group)

<!-- 1. [Delete DFS artifacts](#task-9-delete-dfs-artifacts) from Finance, IT, and Marketing on VN1-SRV10. -->

### Task 1: Create an Active Directory site topology

Perform this task on CL1.

1. Open **Active Directory Sites and Services**.
1. In Active Directory Sites and Services, expand **Sites**.
1. In the context-menu of **Default-First-Site-Name**, click **Rename**.
1. Enter **VNet1**.
1. In the context-menu of Subnets, click **New Subnet...**
1. In New Object - Subnet, under **Prefix**, type **10.1.1.0/24**. Click **Vnet1** and click **OK**.
1. In **Active Directory Sites and Services**, in the context-menu of **Sites**, click **New Site...**
1. In New Object - Sites, beside **Name**, type **VNet2**. Click **DEFAULTIPSITELINK** and click **OK**.
1. In the message box Active Directory Domain Services, click **OK**.
1. In **Active Directory Sites and Services**, in the context-menu of Subnets, click **New Subnet...**
1. In New Object - Subnet, under **Prefix**, type **10.1.2.0/24**. Click **Vnet2** and click **OK**.
1. In the context-menu of **Sites**, click **New Site...**
1. In New Object - Sites, beside **Name**, type **VNet3**. Click **DEFAULTIPSITELINK** and click **OK**.
1. In **Active Directory Sites and Services**, in the context-menu of Subnets, click **New Subnet...**
1. In New Object - Subnet, under **Prefix**, type **10.1.3.0/24**. Click **Vnet3** and click **OK**.
1. Open **Terminal**.
1. Restart **VN1-SRV6**, **VN1-SRV10**, **VN2-SRV1**, and **CL2**.

    ````powershell
    Restart-Computer `
        -ComputerName VN1-SRV6, VN1-SRV10, VN2-SRV1, CL2 -Protocol WSMan -Force
    ````

1. Restart CL1.

    ````powershell
    Restart-Computer
    ````

1. After the restart of CL1, sign in as **ad\\Administrator**.

### Task 2: Install the DFS Namespaces role service on additional server

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on the page **Before You Begin**, click **Next >**.
1. On the page Installation Type, ensure **Role-based or feature-based installation** is selected and click **Next >**.
1. On the page Server Selection, click **VN2-SRV1.ad.adatum.com** and click **Next >**.
1. On the page Server Roles, expand **File and Storage Services**, **File and iSCSI Services**, and activate the checkbox next to **DFS Namespaces**.
1. In Add features that are required for DFS Namespace, click **Add Features**.
1. In the **Add Rules and Features Wizard**, on the page **Server Roles**, click **Next >**.
1. On the page Features, click **Next >**.
1. On the page Confirmation, verify your selection and click **Install**.
1. On the page **Results**, click **Close**.

#### Windows Admin Center

Perform this task on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, on the connections page, click **vn2-srv1.ad.adatum.com**.
1. Connected to vn2-srv1.ad.adatum.com, under **Tools**, click **Roles & features**.
1. In Roles and features, expand **File and Storage Services**, **File and iSCSI Services**, click **DFS Namespaces**, and click **Install**.
1. In the pane Install Role and Features, activate the checkbox **Reboot the server automatically, if required** and click **Yes**.

    After a few minutes, a notification **Install Roles and Features** appears. If you missed the notification, a small number appears beside the icon *Notifications* (in form of a bell) at the top-right of Windows Admin Center.

1. Click **Windows Admin Center** to return to the connections page.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install the DFS Namespaces role service on **VN2-SRV1**.

    ````powershell
        Install-WindowsFeature `
            -ComputerName VN2-SRV1 `
            -Name FS-DFS-Namespace `
            -IncludeManagementTools `
            -Restart
    ````

### Task 3: Install the DFS Replication role service

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on the page **Before You Begin**, click **Next >**.
1. On the page Installation Type, ensure **Role-based or feature-based installation** is selected and click **Next >**.
1. On the page Server Selection, click **VN1-SRV10.ad.adatum.com** and click **Next >**.
1. On the page Server Roles, expand **File and Storage Services**, **File and iSCSI Services**, and activate the checkbox next to **DFS Replication**.
1. If the dialog Add features that are required for DFS Namespace appears, click **Add Features**.
1. In the **Add Rules and Features Wizard**, on the page **Server Roles**, click **Next >**.
1. On the page Features, click **Next >**.
1. On the page Confirmation, verify your selection and click **Install**.
1. On the page **Results**, click **Close**.

Repeat from step 2 for **VN2-SRV1**.

#### Windows Admin Center

Perform this task on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, on the connections page, click **vn1-srv10.ad.adatum.com**.
1. Connected to vn1-srv10.ad.adatum.com, under **Tools**, click **Roles & features**.
1. In Roles and features, expand **File and Storage Services**, **File and iSCSI Services**, click **DFS Replication**, and click **Install**.
1. In the pane Install Role and Features, activate the checkbox **Reboot the server automatically, if required** and click **Yes**.

    After a few minutes, a notification **Install Roles and Features** appears. If you missed the notification, a small number appears beside the icon *Notifications* (in form of a bell) at the top-right of Windows Admin Center.

1. Click **Windows Admin Center** to return to the connections page.

Repeat from step 2 for **VN2-SRV1**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install the DFS Replication role service on **VN1-SRV10**, and **VN2-SRV1**.

    ````powershell
    Invoke-Command -ComputerName VN1-SRV10, VN2-SRV1 -ScriptBlock {
        Install-WindowsFeature `
            -Name FS-DFS-Replication -IncludeManagementTools -Restart
    }
    ````

### Task 4: Add namespace server

Perform this task on CL1.

1. Open **DFS Management**.
1. In DFS Management, expand **Namespaces** and click **\\\\ad.adatum.com\\Company Data**.

    If you do not see **\\\\ad.adatum.com\\Company Data**, perform these steps:

    1. In the context-menu of **Namespaces**, click **Add Namespaces to Display...**
    1. In Add Namespaces to Display, ensure **\\\\ad.adatum.com\\Company Data** is selected. Click **OK**.

1. In the context-menu of **\\\\ad.adatum.com\\Company Data**, click **Add Namespace Server...**
1. In Add Namespace Server, under **Namespace server**, type **VN2-SRV1** and click **OK**.
1. In **DFS Management**, in **\\\\ad.adatum.com\\Company Data**, click the tab **Namespace Servers**.

    You should see two namespace servers \\\\VN1-SRV6.ad.adatum.com\\Company Data and \\\\VN2-SRV1.ad.adatum.com\\Company Data.

### Task 5: Replicate files

Perform this task on CL1.

1. Open **DFS Management**.
1. In DFS Management, click **Replication**.
1. In the context-menu of **Replication** click **New Replication Group...**
1. In the New Replication Group Wizard, on page **Replication Group Type**, ensure **Multipurpose replication group** is selected and click **Next >**.
1. On page Name and Domain, under **Name of replication group**, type **Department folders** and click **Next >**.
1. On page Replication Group Members, click **Add...**
1. In Select Computers, under **Enter the object names to select**, type **VN1-SRV10; VN2-SRV1** and click **OK**.
1. In the **New Replication Group Wizard**, on page **Replication Group Members**, click **Next >**.
1. On page Topology Selection, ensure **Full mesh** is selected and click **Next >**.
1. On page Repliation Group Schedule, ensure **Replicate continuously using the specified bandwidth** is selected. Under **Bandwidth**, ensure **Full** is selected. Clic **Next >**.
1. On page Primary member, under **Primary member**, click **VN1-SRV10** and click **Next >**.
1. On page Folders to Replicate, click **Add...**
1. In Add folder to Replicate, click **Browse...**
1. In Browse For Folder, expand **D$**, **Shares**, and click **Finance**. Click **OK**.
1. In **Add folder to Replicate**, click **OK**.

    Repeat steps 12 - 15 for **IT** and **Marketing**.

1. In the **New Replication Group Wizard**, on page **Folders to Replicate**, click **Next >**.
1. On page Local Path of Finance on Other Members, click **Edit...**
1. In Edit, click **Enabled** and click **Browse...**
1. In Browse For Folder, click **C$** and click **Make New Folder**. Enter **Shares**.
1. Click **Make New Folder** and enter **Finance**.
1. Click **OK**.
1. In **Edit**, click **OK**.
1. In the **New Replication Group Wizard**, on page **Local Path of Finance on Other Members**, click **Next >**.
1. On page Local Path of IT on Other Members, click **Edit...**
1. In Edit, click **Enabled** and click **Browse...**
1. In Browse For Folder, under **C$** click **Shares**.
1. Click **Make New Folder** and enter **IT**.
1. Click **OK**.
1. In **Edit**, click **OK**.
1. In the **New Replication Group Wizard**, on page **Local Path of IT on Other Members**, click **Next >**.
1. On page Local Path of Marketing on Other Members, click **Edit...**
1. In Edit, click **Enabled** and click **Browse...**
1. In Browse For Folder, under **C$** click **Shares**.
1. Click **Make New Folder** and enter **Marketing**.
1. Click **OK**.
1. In **Edit**, click **OK**.
1. In the **New Replication Group Wizard**, on page **Local Path of Marketing on Other Members**, click **Next >**.
1. On page Review Settings and Create Replication Group, click **Create**.
1. On page Confirmation, click **Close**.
1. In the message box Replication Delay, click **OK**.
1. In **DFS Management**, click **Department folders**.
1. Open **File Explorer** and navigate to **\\\\VN2-SRV1\\C$\\Shares\\Finance**.

    After a few moments, you should see the same files as in \\\\VN1-SRV10\\Finance.

You may repeat the last step for the folders **IT** and **Marketing**.

### Task 6: Create additional DFS targets

Perform this task on CL1.

1. Open **DFS Management**.
1. In DFS Management, expand **Namespaces**, **\\\\ad.adatum.com\\Company Data**, **Departments**.

    If you do not see **\\\\ad.adatum.com\\Company Data**, perform these steps:

    1. In the context-menu of **Namespaces**, click **Add Namespaces to Display...**
    1. In Add Namespaces to Display, ensure **\\\\ad.adatum.com\\Company Data** is selected. Click **OK**.

1. Click **Finance**.
1. In the context-menu of **Finance**, click **Add Folder Target...**
1. In New Folder Target, click **Browse...**
1. In Browse for Shared Folders, under **Server**, type **VN2-SRV1** and click **Show Shared Folders**.

    There is no share for Finance yet. Therefore, you must create one.

1. Click **New Shared Folder...**
1. In Create Share, under **Share name**, type **Finance**.
1. Under **Local path of shared folder**, click **Browse...**
1. In Browse For Folder, expand **c$**, **Shares**, and click **Finance**. Click **OK**.
1. In **Create Share**, click **Use custom permissions** and click **Customize...**
1. In Permissions for Finance, click **Add...**.
1. In Select Users, Computers, Service Accounts, or Groups, under **Enter the object names to select**, type **Finance Read; Finance Modify; Administrators** and click **OK**.
1. In **Permissions for Finance**, click **Administrators** and activate the checkbox in the row **Full Control** and the column **Allow**.
1. Click **Finance Modify** and activate the checkbox in the row **Change** and the column **Allow**.
1. Click **Everyone** and click **Remove**.
1. Click **OK**.
1. In **Create Share**, click **OK**.
1. In **Browse for Shared Folders**, ensure **Finance** is selected and click **OK**.
1. In **New Folder Target**, ensure under **Path to folder target**, **\\\\VN2-SRV1\\Finance** is filled in and click **OK**.
1. In the message box **Replication**, click **No**.

    You created the replication group in the previous task.

Repeat from step 3 for **IT** and **Marketing**.

### Task 7: Validate DFS-R and DFS targets

Perform this task on CL2.

1. Sign in as **ad\\Administrator**
1. In **File Explorer**, navigate to **\\\\ad.adatum.com\\Company Data**. You will have to type the full path in the address bar.
1. In \\\\ad.adatum.com\\Company Data, in the context-menu of **Departments**, click **Properties**.
1. In Departments Properties, click the tab **DFS**.

    > The active path should be \\\\VN2-SRV1.ad.adatum.com\\Company Data.

1. Click **Cancel**.
1. In **File Explorer**, in \\\\ad.adatum.com\\Company Data, double-click **Departments**.
1. In \\\\ad.adatum.com\\Company Data\\Departments, in the context-menu of **IT**, click **Properties**.
1. In IT Properties, click the tab **DFS**.

    > The active path should be \\\\VN2-SRV1.ad.adatum.com\\Company Data\\Departments\\IT.

1. Click **Cancel**.
1. In **File Explorer**, in \\\\ad.adatum.com\\Company Data\\Departments, double-click **IT**.

    > The content of the folder IT should be the same as in the share on \\\\VN1-SRV10\\IT.

If time permits, you may also check the Finance and the Marketing folders.

### Task 8: Delete replication group

Perform this task on CL1.

1. Open **DFS Management**.
1. In DFS Management, click **Replication**.
1. In the middle pane, in the context-menu of **Department folders**, click **Delete**.

    If you do not see Department folders, perform these steps:

    1. In the context-menu of **Replication**, click **Add Replication Groups to Display**.
    1. In Add Replication Groups to Display, click **Department folders** and click **OK**

1. In Confirm Delete Replication Group, click **Yes, delete the replication group, stop replicating all associated replicated folders, and delete all members of the replication group** and click **OK**.

<!-- ### Task 9: Delete DFS artifacts

Perform this task on CL1.

1. Open **File Explorer**.
1. In File Explorer, at the top, click the ellipsis, and click **Options**.
1. In Folder Options, click the tab **View**.
1. On the tab View, under **Advanced settings**, click **Show hidden files, folders, and drives** and deactivate **Hide protected operting system files (Recommended)**.
1. In **File Explorer**, navigate to **\\\\VN1-SRV10\\Finance**.
1. In Finance, delete **\_\_DFSR_DIAGNOSTIS-TEST_FOLDER\_\_** and **DfsrPrivate**. -->

## Exercise 3: Replicating files with Azure File Sync

1. [Remove existing content in shares](#task-1-remove-existing-content-in-shares) on VN2-SRV1
1. [Register Windows Admin Center with Azure](#task-2-register-windows-admin-center-with-azure)
1. [Download Azure File Sync agent](#task-3-download-azure-file-sync-agent)
1. [Create a Storage Sync Service and register server](#task-4-create-a-storage-sync-service-and-register-server) VN1-SRV10
1. [Register additional server](#task-5-register-additional-server) VN2-SRV1
1. [Sync folder](#task-6-sync-folder) Finance on VN1-SRV10
1. [Add server endpoint](#task-7-add-server-endpoint) VN2-SRV1
1. [Validate DFS with Azure File Sync](#task-8-validate-dfs-with-azure-file-sync) on CL2

    > What is the content of the storage account's finance share?

    > What is the content of the Finance share on VN2-SRV1?

1. [Remove server endpoints]

### Task 1: Remove existing content in shares

Perform this task on CL1.

1. In File Explorer, navigate to **\\\\VN2-SRV1\\Finance**.
1. In Finance, delete all files and folders.

Repeat this task for **\\\\VN2-SRV1\\IT** and **\\\\VN2-SRV1\\Marketing**.

### Task 2: Register Windows Admin Center with Azure

Perform this task on CL1.

1. Open **Microsoft Edge** and navigate to <https://admincenter>.
1. In Windows Admin Center, click *Settings*.
1. In Settings, ensure **Account** is selected.
1. In Account, click **Register with Azure**.
1. Under Register with Azure, click **Register**.
1. In the pane Get started with Azure in Windows Admin Center, under **Select an Azure cloud**, ensure **Azure Global** is selected.
1. Under **Copy this code**, click **Copy** and click **Enter the code**.

    In Microsoft Edge, a new tab opens.

1. Under **Enter code**, paste the copied code and click **Next**.
1. Sign in to Microsoft Azure.
1. Under **Are you trying to sign in to Windows Admin Center**, click **Continue**.
1. If you see the message You have signed in to the Windows Admin Center application on your device. You may now close this window, close the tab.
1. In **Windows Admin Center**, on the pane **Get started with Azure in Windows Admin Center**, under **Azure Active Directory (tenant) ID**, ensure the correct ID is selected.

    If you are unsure about the corred ID, perform these steps:

    1. Open a new tab.
    1. Navigate to <https://portal.azure.com> and sign in if necessary.
    1. In the search box at the top, type **Azure Active Directory** and click **Azure Active Directory**.
    1. In Azure Active Directory, on the page **Overview**, take a note of **Tenant ID**.

1. Under **Azure Active Directory application**, ensure **Create new** is selected and click **Connect**.

    Wait for the message **Now connected to Azure AD** to appear.

1. Click **Sign in**.
1. In the dialog Permissions requested, click **Accept**.

### Task 3: Download Azure File Sync agent

Perform this task on CL1.

1. Open **Microsoft Edge**, navigate to <https://www.microsoft.com/en-us/download/confirmation.aspx?id=57159>
1. On the download page of Azure File Sync Agent, click **Download**.
1. In Choose the download you want, activate **StorageSyncAgent_WS2022.msi** and click **Next**.
1. Copy the downloaded file **StorageSyncAgent_WS2022.msi** to **\\\\VN1-SRV10\\IT**.

### Task 4: Create a Storage Sync Service and register server

Perform this task on CL1.

1. Open **Microsoft Edge** and navigate to <https://admincenter>.
1. In Windows Admin Center, on the connections page, click **vn1-srv10.ad.adatum.com**.
1. Connected to vn1-srv10.ad.adatum.com, under **Tools**, click **Azure File Sync**.
1. In Azure File Sync, click **Set up**.
1. In the pane Set up Azure File Sync, under **Sync with this Azure region**, select a region close to you, e.g., **West Europe**.
1. Under **Azure settings**, click **Edit**.
1. Under **Subscription**, select the correct subscription.
1. Under **Resource group**, click **Create new** and type **WinFSM**.
1. Under **Storage Sync Service**, ensure **Create new** is selected.

    Take a note of the name.

1. Click **Set up**.

    The setup take less than 5 minutes.

1. After the message You're all set! appears, click **Close**.

### Task 5: Register additional server

Perform this task on VN2-SRV1.

1. Sign in as **ad\\Administrator**.
1. Open **Server Manager**.
1. In Server Manager, click **Local Server**.
1. In Local Server, beside **IE Enhanced Security Configuration**, click **On**.
1. In Internet Explorer Enhanced Security Configuration, under **Administrators**, click **Off** and click **OK**.
1. Open **\\\\VN1-SRV10\\IT\StorageSyncAgent_WS2022.msi**.
1. In the message box Open File - Security Warning, click **Run**.
1. In Storage Sync Agent Setup, on page Welcome to the Storage Sync Agent Setup Wizard, click **Next**.
1. On page End-User License Agreement, ensure **I accept the terms in the License Agreement** is activated and click **Next**.
1. On page Feature Selection, ensure **Azure File Sync** is activated and click **Next**.
1. On page Proxy settings, ensure **Use the existing proxy settings configured on the server** is selected and click **Next**.
1. On page Microsoft Update, click **Next**.
1. On page Ready to install Storage Sync Agent, click **Install**.

    Wait for the setup to complete. This will take less than 2 minutes.

1. On page Completed the Storage Sync Agent Setup, click **Finish**.

    A new app **Azure Storage Sync Agent Updater** will launch.

1. In Azure File Sync - Agent Update, click **OK**.
1. On page Sign in and register this server, under **Azure Environment**, ensure **AzureCloud** is selected and click **Sign in**.
1. Sign in to your Azure account.
1. On page Choose a Storage Sync Service, click the **Azure Subscription**, **Resource Grou**, and **Storage Sync Service**, you used in this exercise and click **Register**.
1. On page Registrytion successful, click **Close**.

### Task 6: Sync folder

Peform this task on CL1.

1. Open **Microsoft Edge** and navigate to <https://admincenter>.
1. In Windows Admin Center, on the connections page, click **vn1-srv10.ad.adatum.com**.
1. Connected to vn1-srv10.ad.adatum.com, under **Tools**, click **Azure File Sync**.
1. In Azure File Sync, click **Sync a folder**.
1. In the pane Sync a folder, under **Local folder name**, click **Browse**.
1. In Select a folder to sync, double-click **NTFS 1TB (D:)**, double-click **Shares**, click **Finance**, and click **OK**.
1. Under **Sync group**, ensure **Create new** is selected and, below, type **Finance**.
1. Under **Azure file share to sync with**, ensure **Create new** is selected and, below, type **finance**.
1. Under **Resource group**, click **WinFSM**.
1. Under Storage account, ensure **Create new** is selected and, below, type a globally unique name, e.g. the first letters of your first and last name, followed by **dfs**. You must not upper case or special characters.
1. Under **Storage performance**, ensure **Standard** is selected.
1. Under **Data repliation**, click **Geo-redundant storage (GRS)**.
1. Click **Set up sync**.

    If the setup fails, perform [Alternative steps](#alternative-steps)

If time permits, repeat this task from step 4 for the IT and Marketing shares.

#### Alternative steps

Perform this task on CL1.

1. Open **Microsoft Edge**, navigate to <https://portal.azure.com> and sign in if necessary.
1. In Microsoft Azure, click **Create a resource**.

    Note: Depending on your settings, this command is on the Home screen, in menu on the side, or in the hamburger menu.

1. In Create a resource, click **Storage** and click **Storage account**.
1. In Create a storage account, in **Subscription**, ensure the correct subscription is selected.
1. Under **Resource group**, click **Create new**, type **WinFSM** and click **OK**.
1. Beside **Storage account name**, type a unique name, e.g., the first letters of your first and last name and **dfs**. You must not use upper case or any special caharacters. If you see an error message below the field, fix that error.
1. Beside **Region**, click a region close to you, e.g., **(Europe) West Europe**.1
1. Beside **Performance**, ensure **Standard** is selected.
1. Beside **Redundancy**, ensure **Geo-redundant storage (GRS)** is selected. Deactivate **Make read access to data available in the event of regional unavailability**.
1. Click **Review**.
1. Click **Create**.

    Wait until the deployment is complete. This should take less than 30 seconds.

1. Click **Go to resource** or, in the search box at the top, type the name of the storage account, you just created.
1. In the storage account, under **Data storage**, click **File shares**.
1. In File shares, click **+ File share**.
1. In the pane New file share, under **Name**, type **finance**. Under **Tier**, click **Hot**. Click **Create**.
1. In the search box at the top, type **Storage Sync Services** and click **Storage Sync Services**.
1. Under Storage Sync Services, click the storage sync service, you created in the previous task.
1. Under the Storage Sync Service, click the sync group **Finance**.

    If you do not see the sync group Finance, follow these steps:

    1. Click  **+ Sync group**.
    1. In Sync group, beside **Sync group name**, type **Finance**.
    1. Beside **Subscription**, select the subscription you used in the previous step.
    1. Click **Select storage account**.
    1. In Choose storage account, click the storage account, you just created.
    1. Under **Azure File Share**, click **finance**.
    1. Click **Create**.

    In the Sync group finance, if you do not see a Cloud endpoint **finance**, follow these steps:

    1. Click **Add cloud endpoint**.
    1. Beside **Subscription**, select the subscription you used in the previous step.
    1. Click **Select storage account**.
    1. In Choose storage account, click the storage account, you just created.
    1. Under **Azure File Share**, click **finance**.
    1. Click **Create**.

    Wait for the cloud endpoint to provision. This takes a few seconds.

1. Click **Add server endpoint**.
1. In the pane Add server endpoint, under **Registered Server**, click **VN1-SRV10.ad.adatum.com**. Under **Path**, type **D:\\Shares\\Finance**.
1. Expand **Initial Sync** and clcik **Authoritatively overwrite files and folders in the Azure file share with content in this server's path. This option avoids file conflicts**.
1. Click **Create**.

    Wait for the server endpoint to provision. This takes less than a minute.

If time permits, repeat this task from step 12 for the IT and Marketing shares.

### Task 7: Add server endpoint

Perform this task on CL1.

1. Open **Microsoft Edge**, navigate to <https://portal.azure.com> and sign in if necessary.
1. In the search box at the top, type **Storage Sync Services** and click **Storage Sync Services**.
1. Under Storage Sync Services, click the storage sync service, you created in the previous task.
1. Under **Sync**, click **Sync groups**.
1. In Sync groups, click **Finance**.
1. In Finance, click **Add server endpoint**.
1. In the pane Add server endpoint, under **Registered Server**, click **VN2-SRV1.ad.adatum.com**. Under **Path**, type **C:\\Shares\\Finance** and click **Create**.

If time permits, repeat this task from step 4 for the IT and Marketing sync groups.

### Task 8: Validate DFS with Azure File Sync

Perform this task on CL2.

1. Open **Microsoft Edge**, navigate to <https://portal.azure.com> and sign in if necessary.
1. In the search box at the top, type the name of the storage account, you created in this exercise.
1. In the storage account, under **Data storage**, click **File shares**.
1. In File shares, click **finance**.

    > You should see the same files as in \\\\VN1-SRV10\\Finance.

1. In \\\\ad.adatum.com\\Company Data\\Departments, in the context-menu of **Finance**, click **Properties**.
1. In Finance Properties, click the tab **DFS**.

    > The active path should be \\\\VN2-SRV1.ad.adatum.com\\Company Data\\Departments\\Finance.

1. Click **Cancel**.
1. In **File Explorer**, in \\\\ad.adatum.com\\Company Data\\Departments, double-click **Finance**.

    > The content of the folder Finance should be the same as in the share on \\\\VN1-SRV10\\Finance.

If time permits, repeat from step 4 for the Marketing and IT shares.

### Task 9: Remove server endpoints

1. Open **Microsoft Edge**, navigate to <https://portal.azure.com> and sign in if necessary.
1. In the search box at the top, type **Storage Sync Services** and click **Storage Sync Services**.
1. Under Storage Sync Services, click the storage sync service, you created in the previous task.
1. Under **Sync**, click **Sync groups**.
1. In Sync groups, click **Finance**.
1. In the sync group Finance, under **2 server endpoints**, click **VN1-SRV10.ad.adatum.com**.
1. In the pane Server Endpoint Properties, click **Delete**.
1. In Delete server endpoint, click **I want to delete my server endpoint and stop using this Azure file share**.
1. On tab Confirmation, review the steps, activate **I'm ready to delete the server endpoint** and click **Delete**.

Repeat from step 6 for **VN2-SRV1.ad.adatum.com**.
