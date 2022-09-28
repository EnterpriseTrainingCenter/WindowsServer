# Lab: Manage servers remotely using Microsoft Management Console

## Required VMs

* VN1-SRV1
* PM-SRV1
* VN1-SRV2
* CL1

## Setup

On **CL1**, logon as **ad\Administrator**.
On **VN1-SRV2**, logon as **ad\Administrator**.

## Introduction

You want to manage servers remotely using Microsoft Management Console snap-ins. You try to connect to a server with the Computer Management snap-in, but you discover the server cannot be managed. Configure the Windows Defender Firewall to allow remote management of a server using the Computer Management snap-in.

## Exercises

1. [Evaluate remote administration using Microsoft Management Console and PowerShell](#exercise-1-evaluate-remote-administration-using-microsoft-management-console-and-powershell)
1. [Configure Windows Defender Firewall rules for remote administration](#exercise-2-configure-windows-defender-firewall-rules-for-remote-administration)
1. [Verify the ability of remote administration using Computer Management](#exercise-3-verify-the-ability-of-remote-administration-using-computer-management)

## Exercise 1: Evaluate remote administration using Microsoft Management Console and PowerShell

[Use the custom console Basic Administration to open Event Viewer and Disk Management on VN1-SRV2](#task-use-the-custom-console-basic-administration-to-open-event-viewer-and-disk-management-on-VN1-SRV2)

> Are you able to view the events and disks?

### Task: Use the custom console Basic Administration to open Event Viewer and Disk Management on VN1-SRV2

Perform this task on CL1.

1. On the desktop, double-click **Basic Administration**.
1. In Basic Administration, in the context-menu of **Computer Management (local)**, click **Connect to another computer...**
1. In Select Computer, Another computer, type **VN1-SRV2** and click **OK**.
1. In Basic Administration, expand **Computer Management (VN1-SRV2)**.
1. Expand **System Tools**.

    > You will receive an error message telling you to enable these firewall rules:
    * COM+ Network Access (DCOM-In)
    * All rules in the Remote Event Log Management group

1. Expand **Storage** and click **Disk Management**.

    > You will receive an error message.

1. Close **Basic Administration**.

## Exercise 2: Configure Windows Defender Firewall rules for remote administration

1. [Enable firewall rules to allow for remote event log and volume management](#task-1-enable-firewall-rules-to-allow-for-remote-event-log-and-volume-management) on VN1-SRV2
1. [Enable firewall rules to allow remote volume management](#task-2-enable-firewall-rules-to-allow-remote-volume-management) on CL1

### Task 1: Enable firewall rules to allow for remote event log and volume management

#### Desktop experience

Perform this task on VN1-SRV2.

1. Open **Windows Defender Firewall with Advanced Security**.
1. In Windows Defender Firewall with Advanced Security, click **Inbound Rules**.
1. Double-click the rule **COM+ Remote Administration (DCOM-In)**.
1. In COM+ Network Access (DCOM-In) Properties, on tab **General**, activate the checkbox **Enabled**.
1. Click the tab **Advanced**.
1. On tab Advanced, deactive the checkboxes **Private** and **Public**. Click **OK**.
1. Repeat the steps above for the 3 rules in the group **Remove Event Log Management**.
1. Repeat the steps above the the 3 rules in the group **Remote Volume Management**

#### Windows Admin Center

Perform this task on CL1.

1. In Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **VN1-SRV2.ad.adatum.com**.
1. Connected to VN1-SRV2.ad.adatum.com, under Tools, click **Firewall**.
1. Under Firewall, click the tab **Incoming rules**.
1. In the search box, enter **COM+ Network Access**.
1. Click the rule **COM+ Remote Administration (DCOM-In)** and click **Settings**.
1. In Firewall Rule COM+ Remote Administration (DCOM-In) Settings, in **General**, set **Enable Firewall Rule** to **Yes**. Under **Profiles** deactivate the checkboxes **Private** and **Public**. Click **Save**.
1. Click **Close**.
1. In the search box, enter **Remote Event Log**. 3 rules will be found.
1. Repeat the steps above to enable each rule for the Domain profile.
1. In the search box, enter **Remote Volume Management**. 3 rules will be found.
1. Repeat the steps above to enable each rule for the Domain profile.

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Start an interactive PowerShell remote session with VN1-SRV2.

    ````powershell
    Enter-PSSession VN1-SRV2
    ````

1. Find the firewall rule **COM+ Network Access**.

    ````powershell
    Get-NetFirewallRule -DisplayName 'COM+ Network Access*'
    ````

1. Enable the firewall rule **COM+ Network Access (DCOM-In)** for the domain profile only.

    ````powershell
    Set-NetFirewallRule `
        -Name ComPlusRemoteAdministration-DCOM-In `
        -Enabled True `
        -Profile Domain
    ````

1. Enable all rules in the group **Remote Event Log Management** for the domain profile only.

    ````powershell
    Get-NetFirewallRule -DisplayGroup 'Remote Event Log Management' |
    Set-NetFirewallRule -Enabled True -Profile Domain
    ````

1. Enable all rules in the group **Remote Volume Management** for the domain profile only.

    ````powershell
    Get-NetFirewallRule -DisplayGroup 'Remote Volume Management' |
    Set-NetFirewallRule -Enabled True -Profile Domain
    ````

1. Exit the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

Note: You could perform all the commands in just one command.

````powershell
Invoke-Command -ComputerName VN1-SRV2 -ScriptBlock {
    Set-NetFirewallRule `
        -Name ComPlusRemoteAdministration-DCOM-In `
        -Enabled True `
        -Profile Domain
    # Remote Event Log Management
    Get-NetFirewallRule -Group '@FirewallAPI.dll,-29252' |
    Set-NetFirewallRule -Enabled True -Profile Domain
    # Remote Volume Management
    Get-NetFirewallRule -Group '@FirewallAPI.dll,-34501' |
    Set-NetFirewallRule -Enabled True -Profile Domain
}
````

### Task 2: Enable firewall rules to allow remote volume management

#### Desktop experience

Perform this task on CL1.

1. Open **Windows Defender Firewall with Advanced Security**.
1. In Windows Defender Firewall with Advanced Security, click **Inbound Rules**.
1. Click the rule **Remote Volume Management - Virtual  Disk Service (RPC)** with the profile **Domain**.
1. Hold down the CTRL key and click the rules

    * **Remote Volume Management - Virtual Disk Service Loader (RPC)** with the profile **Domain**
    * **Remote Volume Management (RPC-EPMAP)** with the profile **Domain**

1. In the pane **Actions**, under **Selected Item**, click **Enable Rule**.
1. Double-click the rule **COM+ Network Access (DCOM-In)**.
1. In COM+ Network Access (DCOM-In) Properties, on tab **General**, activate the checkbox **Enabled**.
1. Click the tab **Advanced**.
1. On tab Advanced, deactive the checkboxes **Private** and **Public**. Click **OK**.
1. Repeat the steps above for the 3 rules in the group **Remove Event Log Management**.

#### PowerShell

1. Open **Windows Terminal** as Administrator.
1. Find all rules in the group **Remote Volume Management** in the profile **Domain** and enable them.

    ````powershell
    Get-NetFirewallRule -DisplayGroup 'Remote Volume Management' | 
    Where-Object { $PSItem.Profile -eq 'Domain' } | 
    Set-NetFirewallRule -Enabled True
    ````

## Exercise 3: Verify the ability of remote administration using Computer Management

[Connect Computer Management to VN1-SRV2 and open Event Viewer and Disk Management](#task-connect-computer-management-to-VN1-SRV2-and-open-event-viewer-and-disk-management)

> Are you able to view events and the disks?

### Task: Connect Computer Management to VN1-SRV2 and open Event Viewer and Disk Management

Perform this task on CL1.

1. On the desktop, double-click **Basic Administration**.
1. In Basic Administration, in the context-menu of **Computer Management (local)**, click **Connect to another computer...**
1. In Select Computer, Another computer, type **VN1-SRV2** and click **OK**.
1. In Basic Administration, expand **Computer Management (VN1-SRV2)**.
1. Expand **System Tools**, **Event Viewer**, **Windows Logs**, **System**.

    > You should see events from the System log.

1. Expand **Storage** and click **Disk Management**.

    > You should see the disk configuration.
