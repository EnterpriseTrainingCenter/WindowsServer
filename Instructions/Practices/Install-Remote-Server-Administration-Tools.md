# Practice: Install Remote Server Administration Tools

## Required VMs

* VN1-DC1
* CL1

## Introduction

In this practice, you will install the Remote Server Administration Tools on CL1.

## Instructions

### Desktop experience

Perform these steps on CL1.

1. Logon as **smart\Administrator**.
1. Open **Settings**.
1. In Settings, click **Apps**.
1. In Apps, click **Optional features**.
1. In Optional features, click the button **View features**.
1. In Add an optional feature, in the text field **Find an avaiable optional feature**, type **RSAT**.
1. Activate the check boxes of these tools:
    * RSAT: Active Directory Domain Services and Lightweight Directory Services Tools
    * RSAT: File Services tools
    * RSAT: Server Manager
1. Click **Next**.
1. Click **Install**.
1. If required, restart the computer.

### PowerShell

Perform these steps on CL1.

1. Logon as **smart\Administrator**.
1. In the context menu of **Start**, click **Windows Terminal (Admin)**.
1. Add the windows capabilities
    * RSAT: Active Directory Domain Services and Lightweight Directory Services Tools
    * RSAT: File Services tools
    * RSAT: Server Manager

    ````powershell
    Add-WindowsCapability -Online -Name 'Rsat.ActiveDirectory.DS-LDS.Tools*'
    Add-WindowsCapability -Online -Name 'Rsat.FileServices.Tools*'
    Add-WindowsCapability -Online -Name 'Rsat.ServerManager.Tools'
    ````

1. If required, restart the computer.

    ````powershell
    Restart-Computer
    ````
