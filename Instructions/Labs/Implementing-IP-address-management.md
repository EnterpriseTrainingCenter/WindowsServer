# Lab: Implementing IP address management

## Required VMs

* VN1-SRV1
* VN1-SRV4
* VN1-SRV5
* VN1-SRV6
* VN1-SRV7
* VN1-SRV8
* VN2-SRV1
* VN2-SRV2
* CL1

## Setup

On CL1 sign in as **ad\Administrator**.

## Introduction

## Exercises

## Exercise 1:

### Task 1: Install the IP Address Management (IPAM Client)

#### Desktop experience

Perform this task on CL1.

1. Open **Settings**.
1. In Settings, click **Apps**.
1. In Apps, click **Optional features**.
1. In Optional features, click the button **View features**.
1. In Add an optional feature, in the text field **Find an available optional feature**, type **RSAT**.
1. Activate the check box beside **RSAT: IP Address Management (IPAM) Client**.
1. Click **Next**.
1. Click **Install**.
1. If required, restart the computer.

You do not need to wait for the completion of the installation.

#### PowerShell

Perform these steps on CL1.

1. In the context menu of **Start**, click **Terminal (Admin)**.
1. Add the windows capabilities **RSAT: IP Address Management (IPAM) Client**.

    ````powershell
    Get-WindowsCapability -Online -Name 'Rsat.IPAM.Client.Tools*' |
    Add-WindowsCapability -Online
    ````

You do not need to wait for the completion of the installation.

## Task 2: Install the IP Address Management (IPAM) feature

Perform this task on CL1.

### Desktop Experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on the page **Before You Begin**, click **Next >**.
1. On the page Installation Type, ensure **Role-based or feature-based installation** is selected and click **Next >**.
1. On the page Server Selection, click **VN1-SRV8.ad.adatum.com** and click **Next >**.
1. On the page Server Roles, click **Next >**.
1. On the page Features, activate **IP Address Management (IPAM) Server**.
1. In Add features that are required for IP Address Management (IPAM) Server, click **Add Features**.
1. In the **Add Rules and Features Wizard**, on the page **Features** click **Next >**.
1. On the page DHCP Server, click **Next >**.
1. On the page Confirmation, verify your selection, activate **Restart the destination server automatically if required**, and click **Install**.
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
    Invoke-Command -ComputerName VN1-SRV8, VN1-SRV9 -ScriptBlock {
        Install-WindowsFeature -Name DHCP -IncludeManagementTools -Restart 
    }
    ````
