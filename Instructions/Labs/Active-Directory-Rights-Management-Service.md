# Lab: Audit file server events

## Required VMs

* VN1-SRV1
* VN1-SRV3
* VN1-SRV4
* VN1-SRV6
* VN1-SRV7
* VN1-SRV8
* CL1

## Setup

1. On **CL1**, sign in as **ad\\Administrator**.

## Introduction


## Exercises

1. Create an Active Directory Rights Management Cluster
1. Configure the Active Directory Rights Management Cluster
1. Validate Active Directory Rights Management
1. Backup the Active Directory Rights Managment Cluster
1. Monitor the Rights Management Usage

## Exercise 1: Create an Active Directory Rights Management Cluster

1. Create a service account
1. Install Active Directory Rights Managment Server Role on VN1-SRV7 and VN1-SRV7

### Task 1: Create a service account

#### Desktop Experience

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **ad (local)**
1. In the context-menu of **ad (local)**, click **New**, **Organizational Unit**.
1. In Create Organizational Unit, in **Name**, type **Service Accounts** and click **OK**.
1. In **Active Directory Administrative Center**, in the middle pane, double-click **Service Accounts**.
1. In the context-menu of an empty space in the middle pane, click **New**, **User**.
1. In Create User, in **Full name**, type **Active Directory Rights Management Service Account**. In **User UPN logon**, type **SvcRMS**. In **Password** and **Confirm password**, type a secure password and click **OK**.

#### Windows Admin Center

Perform these steps on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click *Settings*.
1. In Settings, click **Extensions**.
1. In Extensions, on tab **Available extensions**, click **Active Directory** and click **Install**. Wait for Windows Admin Center page to refresh.
1. On the top-left, click **Windows Admin Center** to return to the connections page.
1. On the tab **Add one**, in **Server name**, enter **VN1-SRV1**.
1. After VN1-SRV1 was found, click **Add**.
1. Click **VN1-SRV7**.
1. Connected to VN1-SRV7.ad.adatum.com, under **Tools**, click **Roles & features**.


### Task 2: Install Active Directory Rights Managment Server Role

#### Desktop Experience

Perform this task on CL1.

1. Open Server Manager.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on the page **Before You Begin**, click **Next >**.
1. On the page **Installation Type**, ensure **Role-based or feature-base installation** is selected and click **Next >**.
1. On the page **Server Selection**, click **VN1-SRV7.ad.adatum.com** and click **Next >**.
1. On the page **Server Roles**, activate the checkbox next to **Active Directory Rights Management Services**
1. In the dialog **Add Roles and Features Wizard**, click **Add Features**.
1. On the page **Server Roles**, click **Next >**.
1. On the page **Features**, click **Next >**.
1. On the page **AD RMS**, click **Next >**.
1. On the page **Role Services**, click **Next >**.
1. On the page **Web Server Role (IIS)**, click **Next >**.
1. On the page **Role Services**, click **Next >**.
1. On the page **Confirmation**, verify your selection and click **Install**.
1. On the page **Results**, wait for the installation to succeed, then click **Close**.

Repeat the steps of this task to install the role on **VN1-SRV8**.

#### Windows Admin Center

Perform these steps on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, on the connections page, click **Add**.
1. In the panel **Add or create resources**, under **Server**, click **Add**.
1. On the tab **Add one**, in **Server name**, enter **VN1-SRV7**.
1. After VN1-SRV7 was found, click **Add**.
1. Click **VN1-SRV7**.
1. Connected to VN1-SRV7.ad.adatum.com, under **Tools**, click **Roles & features**.
1. In Roles and features, expand **Active Directory Rights Management Services**.
1. Activate the checkbox beside  **Active Directory Rights Management Server** and click **Install**.
1. In the pane Install Role and Features, activate the checkbox **Reboot the server automatically, if required** and click **Yes**.
1. After a few minutes, a notification **Install Roles and Features** appears. If you missed the notification, a small number appears beside the icon *Notifications* (in form of a bell) at the top-right of Windows Admin Center. Click the icon *Notification* and the notification **Install Roles and Features**.
1. In the pane **Notification details**, click **Close**.
1. In Roles and features, verify the **State** of **Active Directory Rights Management Server** is **Installed**.

Repeat the steps of this task to install the role on **VN1-SRV8**.

#### PowerShell

### Task 2:

#### Desktop Experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, at the top-right, click *Notifications* (the flag icon with the yellow exclamation mark).
1. Under **Configuration required for Active Directory Rights Management Services at VN1-SRV7**, click **Perform additional configuration.**
1. In AD RMS Configuration: VN1-SRV7.ad.adatum.com, on page **AD RMS**, click **Specify...**
1. In the dialog Type User Account Name and Password, enter the credentials for **AD\Administrator**.
1. On page **AD RMS**, click **Next >**.
1. On page **AD RMS Cluster**, click **Next >**.
1. On page **Configuration Database**, ensure **Specify a database server and a database instance** is selected. Under **Server**, type **VN1-SRV3** and click **List**.
1. Click the drop-down under **Database Instance**, click **DefaultInstance** and click **Next >**.
1. 