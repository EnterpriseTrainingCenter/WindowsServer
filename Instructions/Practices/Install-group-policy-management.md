# Practice: Install Group Policy Management

## Required VMs

* VN1-SRV1
* CL1

## Task

On CL1, install Group Policy Management.

## Instructions

### Desktop experience

Perform these steps on CL1.

1. Sign in as **.\Administrator** or **ad\Administrator**.
1. Open **Settings**.
1. In Settings, click **Apps**.
1. In Apps, click **Optional features**.
1. In Optional features, click the button **View features**.
1. In Add an optional feature, in the text field **Find an available optional feature**, type **RSAT**.
1. Activate the checkbox beside **RSAT: Group Policy Management Tools**.
1. Click **Next**.
1. Click **Install**.
1. If required, restart the computer and Sign in as **ad\Administrator** again.
1. In **Settings**, **Apps** > **Optional feautre**, click **More windows features** (scroll to the bottom).
1. In Windows features, expand **Hyper-V**, activate the checkbox **Hyper-V Management Tools**, and click **OK**.
1. If required, restart the computer, otherwise sign out.

### PowerShell

Perform these steps on CL1.

1. Sign in as **.\Administrator** or **ad\Administrator**.
1. Run **Terminal** as Administrator.
1. Add the windows capability RSAT: Group Policy Management Tools

    ````powershell
    Get-WindowsCapability -Online -Name 'RSAT.GroupPolicy.Management.Tools*' |
    Add-WindowsCapability -Online    
    ````

1. If required, restart the computer, otherwise sign out.

    ````powershell
    Restart-Computer
    ````
