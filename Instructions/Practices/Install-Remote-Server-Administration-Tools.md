# Practice: Install Remote Server Administration Tools

## Required VMs

* VN1-DC1
* CL1

## Task

Install the Remote Server Administration Tools for Active Directory Domain Services, File Services, and the Server Manager on CL1.

## Instructions

### Desktop experience

Perform these steps on CL1.

1. Sign in as **ad\Administrator**.
1. Open **Settings**.
1. In Settings, click **Apps**.
1. In Apps, click **Optional features**.
1. In Optional features, click the button **View features**.
1. In Add an optional feature, in the text field **Find an available optional feature**, type **RSAT**.
1. Activate the check boxes of these tools:
    * RSAT: Active Directory Domain Services and Lightweight Directory Services Tools
    * RSAT: File Services tools
    * RSAT: Server Manager
1. Click **Next**.
1. Click **Install**.
1. If required, restart the computer and Sign in as **ad\Administrator** again.
1. In **Settings**, **Apps** > **Optional feautre**, click **More windows features** (scroll to the bottom).
1. In Windows features, expand **Hyper-V**, activate the checkbox **Hyper-V Management Tools**, and click **OK**.
1. If required, restart the computer.

### PowerShell

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. In the context menu of **Start**, click **Windows Terminal (Admin)**.
1. Add the windows capabilities
    * RSAT: Active Directory Domain Services and Lightweight Directory Services Tools
    * RSAT: File Services tools
    * RSAT: Server Manager

    ````powershell
    <# 
        Add-WindowsCapability does not support wildcards. Therefore, we use
        pipelines with the Get | Add pattern
    #>
    Get-WindowsCapability -Online -Name 'Rsat.ActiveDirectory.DS-LDS.Tools*' |
    Add-WindowsCapability -Online
    Get-WindowsCapability -Online -Name 'Rsat.FileServices.Tools*' | 
    Add-WindowsCapability -Online
    # With the File Services tools, Server Manager is installed automatically
    ````

1. Add the windows feature **Hyper-V Management Tools.**

    ````powershell
    Enable-WindowsOptionalFeature `
        -Online `
        -FeatureName Microsoft-Hyper-V-Tools-All `
        -All
    ````

1. If required, restart the computer.

    ````powershell
    Restart-Computer
    ````
