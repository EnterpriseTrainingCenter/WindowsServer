# Practice: Manage local users

## Required VMs

* VN1-SRV1
* VN1-SRV4
* VN1-SRV5
* CL1
* CL2

## Setup

If you skipped the practice [Create a custom Microsoft Management Console](Create-a-custom-Microsoft-Management-Console.md), on CL1, run ````C:\LabResources\Solutions\New-CustomMMC.ps1````.

If you skipped the lab [Explore Windows Admin Center](../Labs/Explore-Windows-Admin-Center.md), on VN1-SRV4, run ````C:\LabResources\Solutions\Add-WACServers.ps1````.


## Task

On CL1, CL2, and VN1-SRV10, create a local user with the name LocalAdmin. Use different tools on the machines.

## Instructions

### Desktop experience

Perform these steps on CL1.

1. Sign in as **ad\Administrator**.
1. On the desktop, double-click **Basic Administration**.
1. In Basic Administration, click **Computer Management**.
1. In Basic Administration, in the context-menu of **Computer Management**, click **Connect to another computer...**
1. In Select Computer, ensure **Another computer** is selected and type either **CL1**, **CL2** or **VN1-SRV10**. Click **OK**.
1. In **Basic Administration**, expand **Computer Management**, **System Tools**,  **Local Users and Groups**, and click **Users**.
1. In the context-menu of **Users**, click **New User...**.
1. In New User, enter this data:

    **User name:** LocalAdmin

    **Full name:** Local Administrator

    **Description:** Account for administering the computer

1. In **Password** and **Confirm password** enter a secure password.
1. Deactivate the checkbox **User must change password at next logon** and click **Create**.
1. Click **Close**.

Repeat from step 4 for CL2 and VN1-SRV10.

### PowerShell

Perform these steps on CL1.

1. Sign in as **ad\Administrator**.
1. Run **Terminal** as Administrator.
1. Open a remote PowerShell session to CL2 or VN1-SRV10. If you want to create a user on CL1, you can skip this step.

    ````powershell
    Enter-PSSession CL2 # or VN1-SRV10
    ````

1. Store a secure password in a variable as secure string.

    ````powershell
    $password = Read-Host -Prompt 'Enter password' -AsSecureString
    ````

    Enter a secure password.

1. Create a user with the same data as in the previous step.

    ````powershell
    New-LocalUser `
        -Name 'LocalAdmin' `
        -FullName 'Local Administrator' `
        -Description 'Account for administering the computer' `
        -Password $password
    ````

1. Verify the creation of the user.

    ````powershell
    Get-LocalUser -Name LocalAdmin
    ````

    Data about the new user should be returned.

1. Exit the remote PowerShell session. If you did not open a remote PowerShell session, skip this step.

    ````powershell
    Exit-PSSession
    ````

### Windows Admin Center

Perform these steps on CL1.

1. Sign in as **ad\Administrator**.
1. Open **Microsoft Edge**.
1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **CL1.ad.adatum.com**, **CL2.ad.adatum.com** or **VN1-SRV10.ad.adatum.com**.
1. Connected to CL1.ad.adatum.com, CL2.ad.adatum.com or VN1-SRV10.ad.adatum.com, under **Tools**, click **Local users & groups**.
1. In Local users and groups, on the tab **Users**, click **New user**.
1. In the pane Add new user, enter this data:

    **Name**: LocalAdmin

    **Full name**: Local Administrator

    **Description**: Account for administering the computer

1. In **Password** and **Confirm password** enter a secure password.
1. Click **Submit**.
1. In **Local users and groups**, verify the new account was created.

If time allows, try to logon with the new account on the computers.
