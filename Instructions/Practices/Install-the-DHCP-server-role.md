# Practice: Install the DHCP server role

## Required VMs

* VN1-SRV1
* VN1-SRV6
* VN1-SRV7
* VN2-SRV2
* CL1

## Task

Install the DHCP server role on VN1-SRV6, VN1-SRV7, and VN2-SRV2.

## Instructions

### Desktop Experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on the page **Before You Begin**, click **Next >**.
1. On the page Installation Type, ensure **Role-based or feature-base installation** is selected and click **Next >**.
1. On the page Server Selection, click **VN1-SRV6.ad.adatum.com** and click **Next >**.
1. On the page Server Roles, activate the checkbox next to **DHCP Server** and click **Next >**.
1. On the page Features, click **Next >**.
1. On the page DHCP Server, click **Next >**.
1. On the page Confirmation, verify your selection and click **Install**.
1. On the page **Results**, click **Close**.

Repeat the steps of this task to install the role on **VN1-SRV7** and **VN2-SRV2**.

### Windows Admin Center

Perform this task on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, on the connections page, click **vn1-srv6.ad.adatum.com**.
1. Connected to vn1-srv5.ad.adatum.com, under **Tools**, click **Roles & features**.
1. In Roles and features, activate the checkbox beside **DHCP Server** and click **Install**.
1. In the pane Install Role and Features, activate the checkbox **Reboot the server automatically, if required** and click **Yes**.

After a few minutes, a notification **Install Roles and Features** appears. If you missed the notification, a small number appears beside the icon *Notifications* (in form of a bell) at the top-right of Windows Admin Center.

Repeat the steps of this task to install the role on **VN1-SRV7** and **VN2-SRV2**.

### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install Active Directory Rights Management Server on VN2-SRV1 and VN2-SRV2.

    ````powershell
    Invoke-Command -ComputerName VN1-SRV6, VN1-SRV7, VN2-SRV2 -ScriptBlock {
        Install-WindowsFeature -Name DHCP -IncludeManagementTools -Restart 
    }
    ````
