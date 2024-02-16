# Lab: Managing hybrid servers using Azure Arc

## Required VMs

* VN1-SRV1
* VN1-SRV5
* CL1

## Setup

On **CL1**, sign in as **ad\\Administrator**.

You need sign-in credentials for an active Azure Subscription in which you have the permissions to create resources. Moreover, you need to have the Global Administrator role in Azure Active Directory. Ask your instructor for help, if you are unsure.

## Introduction

To increase manageability and security of servers, Adatum wants to add on-premises servers as Azure resources using Azure Arc. Policies should be implemented to monitor configuration glitches. Data should be collected from the connected servers for further analysis in the Azure Portal. Moreover, Adatum wants to manage the on-premises server using Windows Admin Center without an on-premises Windows Admin Center installation. Finally, Adatum wants to manage updates for servers using the Azure Portal and ensure, the servers are up-to-date.

## Known issues

In some cases, the permissions to create policies must be assigned in the Azure subscription. the role **Resource Policy Contributor** must be assigned to the student's account. It can take 5 - 15 minutes until the change is applied. See <https://learn.microsoft.com/en-us/answers/questions/8713/what-is-the-min-iam-role-required-to-create-azure>.

## Exercises

1. [Working with policies](#exercise-1-working-with-policies)
1. [Monitor a hybrid machine with VM insights](#exercise-2-monitor-a-hybrid-machine-with-vm-insights)
1. [Tracking changes](#exercise-3-tracking-changes)
1. [Using Windows Admin Center in the Azure Portal](#exercise-4-using-windows-admin-center-in-the-azure-portal)
1. [Azure update management](#exercise-5-azure-update-management)

## Exercise 1: Working with policies

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

## Exercise 2: Monitor a hybrid machine with VM insights

1. [Enable VM insights](#task-1-enable-vm-insights) for VN1-SRV5
1. [View data collected](#task-2-view-data-collected)

### Task 1: Enable VM insights

Perform this task on the host computer.

1. Open **Microsoft Edge** and navigate to <https://portal.azure.com>
1. Sign in to Azure.
1. In **Search resources, services and docs (G+/)**, type **VN1-SRV5** and click it.
1. In VN1-SRV5, from the left-pane under the **Monitoring** section, click **Insights**.
1. In VN1-SRV5 | Insights, click **Enable**.
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

## Exercise 3: Tracking changes

1. [Enable change tracking](#task-1-enable-change-tracking) for VN1-SRV5
1. [Validate change tracking](#task-2-validate-change-tracking)

### Task 1: Enable change tracking

Perform this task on the host computer.

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

Perform this task on the host computer.

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

## Exercise 4: Using Windows Admin Center in the Azure Portal

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

## Exercise 5: Azure update management

1. [Using Update management center, check for updates](#task-1-using-update-management-center-check-for-updates) on VN1-SRV5
1. [Enable periodic assessement](#task-2-enable-periodic-assessement) on VN1-SRV5
1. [Install updates](#task-3-install-updates) on VN1-SRV5
1. [Create a maintenance plan](#task-4-create-a-maintenance-plan) for VN1-SRV5 to automatically install all updates of all classifications daily at 7:00 PM
1. [Assign a policy](#task-5-assign-a-policy) to assign the maintenance plan to all machines added the the resource group WinFAD in the future

### Task 1: Using Update management center, check for updates

Perform this task on the host computer.

1. Open **Microsoft Edge**.
1. Navigate to <https://portal.azure.com> and sign in to Azure, if necessary.
1. In Microsoft Azure, in **Search resource, service, and docs (G+/)**, type **Azure Update Manager** and click it.
1. In Azure Update Manager, click **Get started**.
1. In Azure Update Manager | Get started, under **On-demand assessment and updates**, click **Check for updates**.
1. Under Select resources and check for updates, activate the checkbox left to **VN1-SRV5** and click **Check for updates**.

    If you do not see VN1-SRV5, ensure that the filter **Subscription** shows the correct subscription.

    Wait for the assessment to complete. You will be notified in the *Notications* icon. This will take some minutes.

### Task 2: Enable periodic assessement

Perform this task on the host computer.

1. Open **Microsoft Edge**.
1. Navigate to <https://portal.azure.com> and sign in to Azure, if necessary.
1. In Microsoft Azure, in **Search resource, service, and docs (G+/)**, type **Azure Update Manager** and click it.
1. In Azure Update Manager, click **Overview**.

    Review the data on the page.

1. Click **Settings** and then **Update settings**.
1. In Change update settings, click **Add machine**.
1. In Select resources, activate the checkbox left to **VN1-SRV5** and click **Add**.
1. In **Change update settings**, under **Windows (1)**, in the top row, in column **Periodic assessment**, click **Enable**. Click **Save**.

    You might want to review the options in columns **Hotpatch** and **Patch orchetration**. However, these options are available for native Azure VMs only.

### Task 3: Install updates

Perform this task on the host computer.

1. Open **Microsoft Edge**.
1. Navigate to <https://portal.azure.com> and sign in to Azure, if necessary.
1. In Microsoft Azure, in **Search resource, service, and docs (G+/)**, type **Azure Update Manager** and click it.
1. In Azure Update Manager, under **Manage**, click **Machines**.
1. Under Azure Update Manager | Machines, activate the checkbox **Select all**, click **One-time update** and click **Install Now**.
1. In Install one-time updates, on tab Machines, ensure **VN1-SRV5** was added, and click **Next**.
1. On tab Updates, review the Windows updates to install and click **Next**.
1. On tab Properties, beside **Reboot option**, click **Reboot of required**. Beside **Maintenance windows (in minutes)**, type **60**. Click **Next**.
1. On tab Review + install, click **Install**.

### task 4: Create a maintenance plan

Perform this task on the host computer.

1. Open **Microsoft Edge**.
1. Navigate to <https://portal.azure.com> and sign in to Azure, if necessary.
1. In Microsoft Azure, in **Search resource, service, and docs (G+/)**, type **Azure Update Manager** and click it.
1. In Azure Update Manager, under **Manage**, click **Machines**.
1. In Azure Update Manager | Machines, activate the checkbox **Select all**.
1. Click **Schedule updates**.
1. In Create a maintenance configuration, on the tab Basics, ensure that beside **Subscription** the subscription for this lab is selected. Beside **Resource Group**, click **WinFAD**. Beside **Configuration name**, type **adatum-server-updates**. Beside **Region**, click a region close to you, e.g. (Europe) West Europe. Beside **Maintenance scope**, ensure **Guest (Azure VM, Arc-enabled VMs/servers)** is selected. Beside **Reboot setting**, ensure **Reboot if required** is selected. Click **Add a schedule**.
1. In the pane Add/Modify schedule, beside **Start on**, select today's date and enter **7:00 PM**. Click **Save**.
1. In **Create a maintenance configuration**, on tab **Basics**, click **Next: DynamicScopes>**.
1. On tab Dynamic scopes, click **Add a dynamic scope**.
1. In Add a dynamic scope, beside **Subscriptions**, select the subscription for this lab. Beside Filter by, click **Select**.
1. In the pane Select filter by, under **Resource groups**, click **No items selected**, then activate the checkbox beside **WinFAD**. Click on the pane **Select filter by**, then click **OK**.
1. In **Add a dynamic scope**, ensure **VN1-SRV5** is listed. Click **Save**.
1. Back in **Create a maintenance configuration**, on tab **Dynamic scopes**, click **Next: Resources >**.
1. On tab Resources, ensure **VN1-SRV5** is listed and click **Next: Updates**.
1. On tab Updates, click **Include update classification**.
1. In the pane Include update classification, under **Windows machines**, activate **Select all** and click **Add**.
1. In **Create a maintenance configuration**, click **Review + create**.
1. On tab Review + Create, click **Create**.

    Wait for the deployment to complete. This should take less than a minute.

### Task 5: Assign a policy

Perform this task on the host computer.

1. Open **Microsoft Edge**.
1. Navigate to <https://portal.azure.com> and sign in to Azure, if necessary.
1. In Microsoft Azure, in **Search resource, service, and docs (G+/)**, type **Maintenance Configurations** and click it.
1. Under Maintenance Configurations, click **adatum-server-updates**.
1. In adatum-server-updates, under **Settings**, click **Properties**.
1. Under adatum-server-updates | Properties, beside **Id**, click the icon *Copy to clipboard*.
1. In Microsoft Azure, in **Search resource, service, and docs (G+/)**, type **Azure Update Manager** and click it.
1. In Azure Update Manager, click **Getting started**.
1. Under Update management center (Previes) | Getting started, under **Update automatically at scale**, click **Assign policy**.
1. In Definitions, click **[Preview]: Schedule recurring updates using Update Management Center**.
1. In [Preview]: Schedule recurring updates using Update Management Center, click **Assign**.
1. In [Preview]: Schedule recurring updates using Update Management Center, on tab Basics, under **Scope**, click the ellipsis.
1. In the pane Scope, under **Subscription**, click the subscription for this lab. Under **Resource Group**, click **WinFAD**. Click **Select**.
1. In **[Preview]: Schedule recurring updates using Update Management Center**, click the tab **Parameters**.
1. On tab Parameters, under **Maintenance Configuration ARM ID**, paste the id from the clipboard.
1. Click **Review + Create**.
1. On tab Review + Create, click **Create**.

### Task 6: Review the update status of server

Perform this task on the host computer.

1. Open **Microsoft Edge**.
1. Navigate to <https://portal.azure.com> and sign in to Azure, if necessary.
1. In Microsoft Azure, in **Search resource, service, and docs (G+/)**, type **VN1-SRV5** and click it.
1. In VN1-SRV5, under **Operations**, click **Updates**.

    Review the data on the tab **Recommende updates**.

1. Click the tab **History** and review the data.
