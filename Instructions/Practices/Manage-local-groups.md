# Practice: Manage local groups

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

On CL1, CL2, and VN1-SRV5, add LocalAdmin to the Administrators group. Use a different tool on each machine.

## Instructions

### Desktop experience

Perform these steps on CL1.

1. Login as **ad\Administrator**.
1. On the desktop, double-click **Basic Administration**.
1. In Basic Administration, click **Computer Management**.
1. In Basic Administration, in the context-menu of **Computer Management**, click **Connect to another computer...**
1. In Select Computer, ensure **Another computer** is selected and type either **CL1**, **CL2** or **VN1-SRV10**. Click **OK**.
1. In **Basic Administration**, expand **Computer Management**, **System Tools**,  **Local Users and Groups**, and click **Groups**.
1. Double-click the group **Administrators**.
1. In Administrators Properties, click **Add...**.
1. In Select Users, Computers Service Accounts, or Groups, click **Locations...**.
1. In Locations, click **CL1**, **CL2**, or **VN1-SRV10** and click **OK**.
1. In **Select Users, Computers Service Accounts**, in **Enter the object names to select**, type **LocalAdmin** and click **OK**.
1. In **Administators Properties**, click **OK**.

### PowerShell

Perform these steps on CL1.

1. Login as **ad\Administrator**.
1. Run **Terminal** as Administrator.
1. Open a remote PowerShell session to **CL2** or **VN1-SRV10**. For CL1, you can skip this step.

    ````powershell
    Enter-PSSession CL2 # or
    ````

1. Add **LocalAdmin** to the **Administrators** group.

    ````powershell
    Add-LocalGroupMember -Group Administrators -Member LocalAdmin
    ````

1. Verify the members of the group **Administrators**.

    ````powershell
    Get-LocalGroupMember -Group Administrators
    ````

    **.\Administrator** or, **.\LocalAdmin**, and **ad\Domain Admins** should be members.

1. Exit the remote PowerShell session. If you did not open a remote PowerShell session, skip this step.

    ````powershell
    Exit-PSSession
    ````

### Windows Admin Center

Perform these steps on CL1.

1. Sign in as **ad\Administrator**.
1. Open **Microsoft Edge**.
1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **CL1.ad.adatum.com**, **CL2.ad.adatum.com**, or **VN1-SRV10.ad.adatum.com**.
1. Connected to CL1.ad.adatum.com, CL2.ad.adatum.com, or VN1-SRV10.ad.adatum.com, under **Tools**, click **Local users & groups**.
1. In Local users and groups, click the tab **Groups**.
1. On the tab Groups, click the group **Administrators**.
1. At the bottom, under **Details - Administrators**, click **Add user**.
1. In the pane Add a user to the Administrators group, in **Username**, type **LocalAdmin** and click **Submit**.
1. Under **Details - Administrators**, verify LocalAdmin is a member.
