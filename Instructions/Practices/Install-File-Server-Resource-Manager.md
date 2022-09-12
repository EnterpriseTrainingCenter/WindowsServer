# Practice: Install File Server Resource Manager and Tools

## Required VMs

* VN1-DC1
* VN1-FS1
* CL1

## Task

On VN1-FS1, install File Server Resource Manager, configure it for remote management and verify the remote management connection.

## Instructions

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. Open **Windows Terminal** as Administrator.
1. Creaate a variable for the computer, where File Server Resource Manager should be configured.

    ````powershell
    $computerName = 'VN1-FS1'
    ````

1. Install the File Server Resource Manager on VN1-FS1.

    ````powershell
    Add-WindowsFeature -Name FS-Resource-Manager -ComputerName $computername
    ````

1. Enable remote management of FSRM through the firewall of VN1-FS1.

    ````powershell
    Invoke-Command -Computername $computerName -ScriptBlock {
        <#
            '@FirewallAPI.dll,-53631' is the group 
            Remote File Server Server Resource Manager Management
        #>
        Set-NetFirewallRule `
            -Group '@FirewallAPI.dll,-53631' `
            -Enabled True `
            -Profile Domain
    }

1. Open **File Server Resource Manager**.
1. In File Server Resource Manager, in the left pane, in the context menu of **File Server Resource Manager**, click **Connect to Another Computer...**
1. In Connect to Another Computer, click **Another computer**, type **VN1-FS1**, and click **OK**.

    > You should be able to connect to FSRM on VN1-FS1. Take a minute to explore the nodes in **File Server Resource Manager**.
