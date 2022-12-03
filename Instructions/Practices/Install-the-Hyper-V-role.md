# Practice: Install the Hyper-V role

## Required VMs

* VN1-SRV1
* PM-SRV1
* PM-SRV2
* CL1

## Task

Install the Hyper-V role on PM-SRV1 and PM-SRV2.

## Instructions

### Desktop Experience

Perform these steps on CL1.

1. Sign in as **ad\Administrator**.
1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on page **Before You Begin**, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-base installation** is selected and click **Next >**.
1. On page Server Selection, click **PM-SRV1.ad.adatum.com** and click **Next >**.
1. On page Server Roles, activate **Hyper-V**
1. In Add features that are required for Hyper-V, click **Add Features**.
1. In **Add Roles and Features Wizard**, on page **Server Roles**, click **Next >**.
1. On page Features, click **Next >**.
1. On page Hyper-V, click **Next >**.
1. On page Virtual Switches, click **Next >**.
1. On page Migration, click **Next >**.
1. On page Default Stores, click **Next >**.
1. On page Confirmation, activate **Restart the destination server automatically if required** and click **Install**.
1. On  page **Results**, do not wait for the installation to succeed. Click **Close**.

Repeat the steps of this task to install the role on **PM-SRV2**.

### PowerShell

Perform this task on CL1.

1. Sign in as **ad\Administrator**.
1. Open **Terminal**.
1. Install **Hyper-V** on **PM-SRV1** and **PM -SRV2**.

   ````powershell
   Invoke-Command -ComputerName 'PM-SRV1', 'PM-SRV2' -ScriptBlock {
      Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
   }
   ````
