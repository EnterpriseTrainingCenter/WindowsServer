# Practice: Install the DNS server role

## Required VMs

* VN1-SRV1
* VN1-SRV4
* VN2-SRV1
* PM-SRV1
* PM-SRV2
* CL1

## Task

Install the DNS server role on VN2-SRV1, PM-SRV1, and PM-SRV2

## Instructions

### Desktop Experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on the page **Before You Begin**, click **Next >**.
1. On the page Installation Type, ensure **Role-based or feature-based installation** is selected and click **Next >**.
1. On the page Server Selection, click **VN2-SRV1.ad.adatum.com** and click **Next >**.
1. On the page Server Roles, activate the checkbox next to **DNS Server** and click **Next >**.
1. On the page Features, click **Next >**.
1. On the page DNS Server, click **Next >**.
1. On the page Confirmation, verify your selection and click **Install**.
1. On the page **Results**, click **Close**.

Repeat the steps of this task to install the role on **PM-SRV1** and **PM-SRV2**.

### Windows Admin Center

Perform this task on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, on the connections page, click **vn2-srv1.ad.adatum.com**.
1. Connected to vn1-srv5.ad.adatum.com, under **Tools**, click **Roles & features**.
1. In Roles and features, activate the checkbox beside **DNS Server** and click **Install**.
1. In the pane Install Role and Features, activate the checkbox **Reboot the server automatically, if required** and click **Yes**.

After a few minutes, a notification **Install Roles and Features** appears. If you missed the notification, a small number appears beside the icon *Notifications* (in form of a bell) at the top-right of Windows Admin Center.

Repeat the steps of this task to install the role on **PM-SRV1** and **PM-SRV2**.

### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install the DNS role on VN2-SRV1, PM-SRV1, and PM-SRV2.

    ````powershell
    Invoke-Command -ComputerName VN2-SRV1, PM-SRV1, PM-SRV2 -ScriptBlock {
        Install-WindowsFeature -Name DNS -IncludeManagementTools -Restart 
    }
    ````
