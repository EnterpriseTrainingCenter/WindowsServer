# Practice: Manage local users

## Required VMs

* VN1-SRV1
* VN1-SRV5
* CL1
* CL2

## Task

On CL1, CL2, and VN1-SRV5, create a local user with the name LocalAdmin. Use different tools on both machines.

## Instructions

Perform these steps on CL1.

1. Login as ad\Administrator.
1. On the desktop, double-click **Basic Administration**.
1. In Basic Administration, click **Computer Management**.
1. Make sure, **Computer Management** is connected to **(Local)**
1. Expand **Computer Management**, **System Tools**,  **Local Users and Groups**.
1. In the context-menu of **Users**, click **New User...**.
1. In New User, enter this data:

    **User name:** LocalAdmin

    **Full name:** Local Administrator

    **Description:** Account for administering the computer

1. In **Password** and **Confirm password** enter a secure password.
1. Deactivate the checkbox **User must change password at next logon** and click **Create**.
1. Click **Close**.
1. Click **Users** and verify that **LocalAdmin** was created.
1. Run **Terminal** as Administrator.
1. Open a remote PowerShell session to CL2.

    ````powershell
    Enter-PSSession CL2
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

1. Exit the remote PowerShell session

    ````powershell
    Exit-PSSession
    ````

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **VN1-SRV5.ad.adatum.com**.
1. Connected to VN1-SRV5.ad.adatum.com, under **Tools**, click **Local users & groups**.
1. In Local users and groups, on the tab **Users**, click **New user**.
1. In the pane Add new user, enter this data:

    **Name**: LocalAdmin

    **Full name**: Local Administrator

    **Description**: Account for administering the computer

1. In **Password** and **Confirm password** enter a secure password.
1. Click **Submit**.
1. In **Local users and groups**, verify the new account was created.

If time allows, try to logon with the new account on the computers.
