# Lab: Azure Arc

<!-- Required time: 45 minutes -->

## Required VMs

* VN1-SRV1
* VN1-SRV5
* CL1

## Setup

On **CL1**, sign in as **ad\\Administrator**.

You need sign-in credentials for an active Azure Subscription in which you have the permissions to create resources. Moreover, you need to have the Global Administrator role in Azure Active Directory. Ask your instructor for help, if you are unsure.

## Introduction

To increase manageability and security of servers, Adatum wants to add on-premises servers as Azure resources using Azure Arc. Policies should be implemented to monitor configuration glitches. Data should be collected from the connected servers for further analysis in the Azure Portal. Finally, Adatum wants to manage the on-premises server using Windows Admin Center without an on-premises Windows Admin Center installation.

## Exercise 1: Onboarding to Azure Arc

1. [Create a Log Analytics Workspace](#task-1-create-a-log-analytics-workspace)
1. [Create an Automation account](#task-2-create-an-automation-account)
1. [Add server to Azure Arc](#task-3-add-server-to-azure-arc): VN1-SRV5
1. [Review recommendations](#task-4-review-recommendations) for VN1-SRV5

### Task 1: Create a Log Analytics Workspace

Perform this task on the host computer.

1. Open **Microsoft Edge** and navigate to <https://portal.azure.com>
1. Sign in to Azure.
1. In Microsoft Azure, click **Create a resource**.

    Note: Depending on your settings, this command is on the Home screen, in menu on the side, or in the hamburger menu.

1. In Create a resource, in **Search services and marketplace**, enter **Log Analytics Workspace**.
1. In Marketplace, click **Log Analytics Workspace**.
1. In Log Analytics Workspace, click **Create**.
1. On the tab Basics, beside **Subscription**, click the subscription you used for this lab. Beside Resource group, click **WinFAD**. Beside **Name**, type **Adatum-Log-Analytics-Workspace**. Beside **Region**, click a region close to your location, e.g., West Europe. Click **Review + Create**.
1. On the tab Review + Create, click **Create**.

    Wait for the deployment to complete. This takes less than a minute.

### Task 2: Create an Automation account

Perform this task on the host computer.

1. Open **Microsoft Edge** and navigate to <https://portal.azure.com>
1. Sign in to Azure.
1. In Microsoft Azure, click **Create a resource**.

    Note: Depending on your settings, this command is on the Home screen, in menu on the side, or in the hamburger menu.

1. In Create a resource, in **Search services and marketplace**, enter **Automation**.
1. In Marketplace, click **Automation**.
1. In Automation, click **Create**.
1. In Create an Automation Account, on the tab Basics, beside **Subscription**, click the subscription you used for this lab. Beside Resource group, click **WinFAD**. Beside **Name**, type **Adatum-Automation-Account**. Beside **Region**, click a region close to your location, e.g., West Europe. Click **Review + Create**.
1. On the tab Review + Create, click **Create**.

    Wait for the deployment to complete. This takes less than a minute.

### Task 3: Add server to Azure Arc

Perform this task on CL1.

1. Open **Microsoft Edge** and navigate to <https://portal.azure.com>
1. Sign in to Azure.
1. In **Search resources, services and docs (G+/)**, type **Azure Arc** and click it.
1. In Azure Arc, in Overview, on tab Get started, under **Add your infrastructure for free**, click **Add**.
1. On tab Infrastructure, under **Servers**, click **Add**.
1. Under **Add a single server**, click **Generate script**.
1. In the Add a server with Azure Arc wizard, on page 1 Prerequisites, click **Next**.
1. On page 2 Resource details, beside **Subscription** click the subscription of your choice. Beside **Resource group**, click **Create new**, type **WinFAD** and click **OK**.
1. Beside **Region**, select a region close to you, e.g., (Europe) West Europe. Beside **Operating System**, ensure **Windows** is selected. Beside **Connectivity method**, ensure **Puplic endpoint** is selected. Click **Next**.
1. On page 3 Tags, beside **Datacenter**, type **VN1**. Beside the other tags enter values of your choice. You may leave tags empty. Click **Next**.
1. On page 4 Download and run script, click **Download** and make sure, the download succeeds. Click **Close**.

    You may have to continue on security warnings for the file to download.

1. Open **Terminal**.
1. Open a remote PowerShell session to **VN1-SRV5**.

    ````powershell
    $pSSession = New-PSSession -ComputerName VN1-SRV5
    ````

1. Copy the onboarding script to VN1-SRV5.

    ````powershell
    Copy-Item `
        -Path ~\Downloads\OnboardingScript.ps1 `
        -ToSession $pSSession `
        -Destination c:\Labresources
    ````

1. Enter the remote PowerShell session.

    ````powershell
    Enter-PSSession -Session $pSSession
    ````

1. Unblock the onboarding script file.

    ````powershell
    Unblock-File C:\LabResources\OnboardingScript.ps1
    ````

1. Execute the onboarding script.

    ````powershell
    C:\LabResources\OnboardingScript.ps1
    ````

    Wait for the message **To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the the code...**.

1. Copy the code in the message to the clipboard.
1. Hold down CTRL while clicking on <https://microsoft.com/devicelogin> in the message.
1. Switch to **Microsoft Edge**, if necessary.
1. On the page Sign in to your account, unter **Enter code**, paste the copied code and click **Next**.
1. Sign in to your Azure subscription.
1. At the prompt Are you trying to sign in to Azure Connected Machine Agent, click **Continue**.

1. Switch to the **Terminal**.

    Wait for the script to complete successfully.

1. Exit from the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

1. Remove the remote PowerShell session.

    ````powershell
    Remove-PSSession -Session $pSSession
    ````

### Task 4: Review recommendations

Perform this task on the host computer.

1. Open **Microsoft Edge** and navigate to <https://portal.azure.com>
1. Sign in to Azure.
1. In **Search resources, services and docs (G+/)**, type **VN1-SRV5** and click it.
1. In **VN1-SRV5**, in **Overview**, click the tab **Recommendations**.

    Review the recommendations.

## Exercise 2: Working with policies

1. [Assign recommended policies](#task-1-assign-recommended-policies) to VN1-SRV5
1. [Create a policy assignment to identify non-compliant resources](#task-2-create-a-policy-assignment-to-identify-non-compliant-resources)
1. [Monitor compliance](#task-3-monitor-compliance)

    > What is the state of the policy Audit Windows VMs with a pending reboot and why?

    > What is the state of the policy Audit Windows machines that contain certificate expiring within the specified number of days and why?

    > What is the state of the policy Windows machines should have Log Analytics agent installed on Azure Arc and why?

### Task 1: Assign recommended policies

Perform this task on the host computer.

1. Open **Microsoft Edge** and navigate to <https://portal.azure.com>
1. Sign in to Azure.
1. In **Search resources, services and docs (G+/)**, type **VN1-SRV5** and click it.
1. In VN1-SRV5, in Overview, on tab Capabilities, click **Policies**.
1. In the pane Assign recommended policies, activate **Audit Windows VMs with a pending reboot** and **Audit Windows machines that contain certificates expiring with the specified number of days** and click **Assign Policies**.

### Task 2: Create a policy assignment to identify non-compliant resources

Perform this task on the host computer.

1. Open **Microsoft Edge** and navigate to <https://portal.azure.com>
1. Sign in to Azure.
1. In **Search resources, services and docs (G+/)**, type **Policy** and click it.
1. In Policy, under **Authoring**, click **Assignments**.
1. In Policy | Assignments, click **Assign policy**.
1. In Assign policy, on tab Basics, under **Scope**, click the ellipsis.
1. In the pane Scope, under **Subscription**, click the subscription you used for this lab. Under **Resource Group**, click **WinFAD**. Click **Select**.
1. In Assign policy, under **Basics**, **Policy definition**, click the ellipsis.
1. In the pane Available Definitions, in search, type **Windows machines should have Log Analytics agent installed on Azure Arc**. Click **Windows machines should have Log Analytics agent installed on Azure Arc** and click **Add**.
1. In Assign Policy, click the tab **Parameters**.
1. On the tab Parameters, under **Include Arc connected servers**, click **true**.
1. Click **Review + Create**.
1. On tab Review + Create, click **Create**.

### Task 3: Monitor compliance

Perform this task on the host computer.

1. Open **Microsoft Edge** and navigate to <https://portal.azure.com>
1. Sign in to Azure.
1. In **Search resources, services and docs (G+/)**, type **Policy** and click it.
1. In **Policy | Assignments**, click **Compliance**.

    > The policy Audit Windows VMs with a pending reboot will have the state Non-Compliant with a resource compliance of 0 out of 1, because VN1-SRV5 should not have a pending reboot.

    > The policy Audit Windows machines that contain certificate expiring within the specified number of days will have the state Non-Compliant with a resource compliance of 0 out of 1, because VN1-SRV5 should not have expiring certificates.

    > The policy Windows machines should have Log Analytics agent installed on Azure Arc will have the state Non-Compliant with a resource compliance of 0 out of 1, because VN1-SRV5 does not have an agent installed yet. You will fix that in an upcoming exercise.

    You may want to click one or the other of the policy to review details.

## Exercise 3: Monitor a hybrid machine with VM insights

1. [Enable VM insights](#task-1-enable-vm-insights) for VN1-SRV5
1. [View data collected](#task-2-view-data-collected)

### Task 1: Enable VM insights

Perform this task on the host computer.

1. Open **Microsoft Edge** and navigate to <https://portal.azure.com>
1. Sign in to Azure.
1. In **Search resources, services and docs (G+/)**, type **VN1-SRV5** and click it.
1. In VN1-SRV5, from the left-pane under the **Monitoring** section, click **Insights**.
1. In VN1-SRV5 | Insights, at the top, click **Azure Monitor**.
1. In Insights, on tab Get started, click **Configure Insights**.
1. In Insights, on tab Overview, expand your subscription, **winfad** and, beside **VN1-SRV5**, click **Enable**.
1. On the pane Azure Monitor, click **Enable**.
1. On the pane Monitoring configuration, beside **Subscription**, click the subscription you used for this lab. Beside **Data collection rule**, click **Create New**.
1. Under Create new rule, beside **Data collection rule name**, type **Adatum-Log-Analytics-Workspace**. Activate the checkbox **Enable processes and dependencies (Map)**. Beside **Subscription**, ensure the subsription used for this lab is selected. Beside **Log Analytics workspaces**, click **Adatum-Log-Analytics-Workspace**. Click **Create**.
1. On the pane **Monitoring configuration**, click **Configure**.

    Wait for the deployment to complete. This process takes 5 - 10 minutes.

### Task 2: View data collected

Perform this task on the host computer.

1. Open **Microsoft Edge** and navigate to <https://portal.azure.com>
1. Sign in to Azure.
1. In **Search resources, services and docs (G+/)**, type **VN1-SRV5** and click it.
1. In VN1-SRV5, from the left-pane under the **Monitoring** section, click **Insights**.
1. In VN1-SRV5 | Insights, click the tab **Performance**.

    You should see performance data. If not, wait a few minutes and click **Refresh**.

1. Click the tab **Map**.

    If the map cannot be displayed yet, continue with the lab and try later.

1. Click on VN1-SRV5 and review the information. Click on other elements in the map and review the information.
1. At the top, click **View Workbooks**, **Performance** and review the analysis.
1. In the breadcrumb navigation at the top, click **VN1-SRV5 | Insights**.
1. In VN1-SRV5 | Insights, at the top, click **View Workbooks**, **Connections Overview** and review the information.

## Exercise 4: Tracking changes

1. [Enable change tracking](#task-1-enable-change-tracking) for VN1-SRV5
1. [Validate change tracking](#task-2-validate-change-tracking)

### Task 1: Enable change tracking

1. Open **Microsoft Edge** and navigate to <https://portal.azure.com>
1. Sign in to Azure.
1. In **Search resources, services and docs (G+/)**, type **Adatum-Automation-Account** and click it.
1. Search for **Adatum-Automation-Account** and click it.
1. In Adatum-Automation-Account, under **Configuration Management**, click **Change tracking**.
1. In Adatum-Automation-Account | Change tracking, ensure under **Log Analytics workspace subscription** the subscription for this lab is selected. Under **Log Analytics workspace** click **Adatum-Log-Analytics-Workspace**. Click **Enable**.

    Wait for the deployment to complete. This takes less than 5 minutes.

1. Click **Manage machines**.
1. In the pane Manage machines, ensure **Enable on all available machines** is selected and click **Enable**.

### Task 2: Validate change tracking

1. Open **Microsoft Edge** and navigate to <https://portal.azure.com>
1. Sign in to Azure.
1. In **Search resources, services and docs (G+/)**, type **VN1-SRV5** and click it.
1. In VN1-SRV5, under **Operations**, click **Change tracking**.

    You will not see any changes to the machines, because we just enabled change tracking.

1. Click **Edit Settings**.
1. In Workspace Configuration, click the tab **Windows Services**.
1. On the tab Windows Services, under **Collection frequency**, drag the slider to the left to **10 sec**.
1. In the breadcrumb navigation at the top, click **VN1-SRV5 | Change tracking**.
1. Under **Operations** section, click **Inventory**.

    The tabs Software, Files, Windows Registry, and Windows Services will show no data, because no changes were detected recently

You might want to revisit this task at the end of the lab to see changes.

## Exercise 5: Using Windows Admin Center in the Azure Portal

1. [Install Windows Admin Center in the Azure portal](#task-1-install-windows-admin-center-in-the-azure-portal) for VN1-SRV5
1. [Assign user to the role Windows Admin Center Administrator login](#task-2-assign-user-to-the-role-windows-admin-center-administrator-login)
1. [Validate Windows Admin Center in the Azure portal](#task-3-validate-windows-admin-center-in-the-azure-portal)

    > Can you establish a Remote Desktop connection?
    
    > Can you establish a remote PowerShell connection?

    > How does the connection between the client and the server work?

### Task 1: Install Windows Admin Center in the Azure portal

Perform this task on the host computer.

1. Open **Microsoft Edge** and navigate to <https://portal.azure.com>
1. Sign in to Azure.
1. In **Search resources, services and docs (G+/)**, type **VN1-SRV5** and click it.
1. In VN1-SRV5, under **Settings**, click **Windows Admin Center (preview)**.
1. In VN1-SRV5 | Windows Admin Center (preview), click **Set up**.
1. In the pane Windows Admin Center, take a note of the **Listening port**, e.g., 6516 and click **Install**.

    The deployment will take a few minutes. You may continue with the lab. The deployment has to complete before you can use Windows Admin Center.

### Task 2: Assign user to the role Windows Admin Center Administrator login

Perform this task on the host computer.

1. Open **Microsoft Edge** and navigate to <https://portal.azure.com>
1. Sign in to Azure.
1. In **Search resources, services and docs (G+/)**, type **VN1-SRV5** and click it.
1. In VN1-SRV5, click **Access control (IAM)**.
1. In VN1-SRV5 | Access control (IAM), click **Add**, **Add role assignment**.
1. In Add role assignment, on tab Role, click **Windows Admin Center Administrator login** and click **Next**.
1. On tab Members, ensure **User, group, or service principal** is selected and click **+ Select members**.
1. In the pane Select members, search and click your Azure AD user account and click **Select**.
1. In **Add role assignment**, click **Review + assign**.
1. On tab Review + assign, click **Review + assign**.

### Task 3: Validate Windows Admin Center in the Azure portal

Before continuing with this task, ensure the deployment of Windows Admin Center in the portal has finished. You may continue with the lab and return to this task later.

Perform this task on the host computer.

1. Open **Microsoft Edge** and navigate to <https://portal.azure.com>
1. Sign in to Azure.
1. In **Search resources, services and docs (G+/)**, type **VN1-SRV5** and click it.
1. In VN1-SRV5, under **Settings**, click **Windows Admin Center (preview)**.
1. In VN1-SRV5 | Windows Admin Center (preview), click **Connect**.
1. In Windows Admin Center, unter **Tools**, click **Settings**.
1. In Settings, click **Remote Desktop**.
1. In Settings | Remote Desktop, click **Allow remote connections to this computer** and click **Save**.
1. Under **Tools**, click **Remote Desktop**.
1. In Remote Desktop, type the credentials of **ad\Administrator**, activate **Automatically connect with the certificate presented by this machine** and click **Connect**.

    > A Remote Desktop connection should be established successfully.

    > No direct connection between the computer connected to Windows Admin Center and the target computer is necessary.

1. In SConfig, enter **12**.
1. At the prompt Are You sure you want to log off, enter **y**.
1. Under **Tools**, click **PowerShell**.
1. In PowerShell, enter the credentials for **ad\Administrator**.

    > A remote PowerShell connection should be established successfully.

    > No direct connection between the computer connected to Windows Admin Center and the target computer is necessary.

1. Query computer info.

    ````powershell
    Get-ComputerInfo
    ````

    Confirm, that you see the information of VN1-SRV5.

1. Exit from the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

1. Under **Tools**, click **Roles & features**.
1. In Roles and Features, click **Windows Server Update Services**. Deactivate the checkbox beside **SQL Server Connectivity**. Click **Install**.
1. In the pane Install roles and Features, click **Yes**.