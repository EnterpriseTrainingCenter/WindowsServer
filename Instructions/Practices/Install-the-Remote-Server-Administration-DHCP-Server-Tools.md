# Practice: Install the Remote Server Administration DHCP Server Tools

## Required VMs

* VN1-SRV1
* CL1

## Task

On CL1, install the Remote Server Administration DHCP Server Tools.

## Instructions

### Desktop experience

Perform these steps on CL1.

1. Sign in as **ad\Administrator**.
1. Open **Settings**.
1. In Settings, click **Apps**.
1. In Apps, click **Optional features**.
1. In Optional features, click the button **View features**.
1. In Add an optional feature, in the text field **Find an available optional feature**, type **RSAT**.
1. Activate the check box beside **RSAT: DHCP Server Tools**.
1. Click **Next**.
1. Click **Install**.
1. If required, restart the computer.

You do not need to wait for the completion of the installation

### PowerShell

Perform these steps on CL1.

1. In the context menu of **Start**, click **Terminal (Admin)**.
1. Add the windows capabilities **RSAT: Server DHCP Server tools**.

    ````powershell
    Get-WindowsCapability -Online -Name 'Rsat.DHCP.Tools*' |
    Add-WindowsCapability -Online
    ````

You do not need to wait for the completion of the installation
