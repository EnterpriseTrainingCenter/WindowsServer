# Practice: Manage local users

## Required VMs

* VN1-SRV1
* PM-SRV1
* VN1-SRV5
* CL1
* CL2

## Task

On CL1, CL2, and VN1-SRV5, add LocalAdmin to the Administrators group. Use a different tool on each machine.

## Instructions

Perform these steps on CL1.

1. Login as ad\Administrator.
1. On the desktop, double-click **Basic Administration**.
1. In Basic Administration, click **Computer Management**.
1. Make sure, **Computer Management** is connected to **(Local)**
1. Expand **Computer Management**, **System Tools**,  **Local Users and Groups**, and click **Groups**.
1. Double-click the group **Administrators**.
1. In Administrators Properties, click **Add...**.
1. In Select Users, Computers Service Accounts, or Groups, click **Locations...**.
1. In Locations, click **CL1** and click **OK**.
1. In **Select Users, Computers Service Accounts**, in **Enter the object names to select**, type **LocalAdmin** and click **OK**.
1. In **Administators Properties**, click **OK**.
1. Run **Windows Terminal** as Administrator.
1. Open a remote PowerShell session to CL2.

    ````powershell
    Enter-PSSession CL2
    ````

1. Add **LocalAdmin** to the **Administrators** group.

    ````powershell
    Add-LocalGroupMember -Group Administrators -Member LocalAdmin
    ````

1. Verify the members of the group **Administrators**.

    ````powershell
    Get-LocalGroupMember -Group Administrators
    ````

    **CL2\Administrator**, **CL2\LocalAdmin**, and **ad\Domain Admins** should be members.

1. Exit the remote PowerShell session

    ````powershell
    Exit-PSSession
    ````

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **VN1-SRV5.ad.adatum.com**.
1. Connected to VN1-SRV5.ad.adatum.com, under **Tools**, click **Local users & groups**.
1. In Local users and groups, click the tab **Groups**.
1. On the tab Groups, click the group **Administrators**.
1. At the bottom, under **Details - Administrators**, click **Add user**.
1. In the pane Add a user to the Administrators group, in **Username**, type **LocalAdmin** and click **Submit**.
1. Under **Details - Administrators**, verify LocalAdmin is a member.
