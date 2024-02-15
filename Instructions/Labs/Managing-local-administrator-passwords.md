# Lab: Managing local administrator passwords

## Required VMs

* VN1-SRV1
* CL1
* CL2

## Setup

1. On **CL1**, sign in as **ad\Administrator**.
1. On **CL2**, sign in as **Administrator**.

## Exercises

1. [Implement Local Administrator Password Solution](#exercise-1-implement-local-administrator-password-solution)
1. [Use Local Administrator Password Solution](#exercise-2-use-local-administrator-password-solution)

## Exercise 1: Implement Local Administrator Password Solution

1. [Create organization units and move all servers and clients](#task-1-create-organizational-unit-and-move-all-servers-and-client) except for VN1-SRV1 into the OUs
1. [Download LAPS and install the management tools](#task-2-download-laps-and-install-the-management-tools) on CL1
1. [Configure LAPS for all servers and clients](#task-3-configure-laps-for-all-servers-and-clients) except for VN1-SRV1

### Task 1: Create organizational unit and move all servers and client

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **ad (local)**.
1. In Active Directory Administrative Center, double-click **ad (local)**.
1. Under **ad (local)**, in the context-menu of **Devices**, click **New**, **Organizational Unit**.
1. In Create Organizational Unit, beside **Name**, type **Servers** and click **OK**.
1. Under **ad (local)**, in the context-menu of **Devices**, click **New**, **Organizational Unit**.
1. In Create Organizational Unit, beside **Name**, type **Clients** and click **OK**.
1. In **Active Directory Administrative Center**, click **ad (local)**.
1. Under **ad (local)**, double-click **Computers**.
1. Under **Computers**, ensure the list is sorted by **Name**. If not, click on the column header **Name**.
1. Click the first computer starting with **CL**, hold down SHIFT, and click the last computer starting with **CL**.
1. In the context-menu of one computer starting with **CL**, click **Move...**
1. In Move, in the middle pane, click **Devices**, and, in the right pane, click **Clients**. Click **OK**.
1. In **Active Directory Administrative Center**, under **Computers**, click the first computer starting with **PM**, hold down SHIFT, and click the last computer starting with **VN**.
1. In the context-menu of one selected computer, click **Move...**
1. In Move, in the middle pane, click **Devices**, and, in the right pane, click **Servers**. Click **OK**.

### Task 2: Download LAPS and install the management tools

Perform this task on CL1.

1. Open **Microsoft Edge**.
1. In Microsoft Edge, navigate to <https://www.microsoft.com/en-us/download/details.aspx?id=46899>.
1. On page Download Local Administrator Password Solution (LAPS) from Official Microsoft Download Center, click **Download**.
1. Under Choose the download you want, activate the checkbox next to **LAPS_x64.msi**.
1. Open the downloaded file.
1. In Local Administrator Password Solution Setup, on page Welcome to the Local Administrator Password Solution Setup Wizard, click **Next**.
1. On page End-User License Agreement, activate **I accept the terms in the License Agreement** and click **Next**.
1. On page Custom Setup, click the icon left to **AdmPwd GPO Extension** and click **Entire feature will be unavailable**. Click the icon left to **Management Tools** and click **Entire feature will be installed on local hard drive**. Click **Next**.
1. On page Ready to install Local Administrator Password Solution, click **Install**.
1. On page Completed the Local Administrator Password Solution Setup Wizard, click **Finish**.

### Task 3: Configure LAPS for all servers and clients

Perform this task on CL1.

1. In File Explorer, copy **LAPS_x64.msi** from **Downloads** to **\\\\ad.adatum.com\\NETLOGON**.

    *Note:* In real world, you can copy the file to any share. However, the computer accounts must have read permissions. Copying the file to the NETLOGON share, replicates it to all domain controllers and ensures, the file is highly available.

1. Open **Terminal**.
1. In Terminal, extend the Active Directory schema.

    ````powershell
    Update-AdmPwdADSchema
    ````

1. Grant devices write permission on ms-MCS-AdmPwdExpirationTime and ms-MCS-AdmPwd.

    ````powershell
    Set-AdmPwdComputerSelfPermission -OrgUnit Devices
    ````

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Forest: ad.adatum.com**, **Domains**, **ad.adatum.com**, and click **Group Policy Objects**.
1. In the context-menu of **Group Policy Objects**, click **New**.
1. In New GPO, under **Name**, type **Custom Computer LAPS**.
1. In the context-menu of **Custom Computer LAPS**, click **Edit**.
1. In Group Policy Management Editor, expand **Computer Configuration**, **Policies**, **Software Settings**, and click **Software installation**.
1. In the context-menu of Software installation, click **New**, **Package...**
1. In Open, open **\\\\ad.adatum.com\\NETLOGON\\LAPS_x64.msi**.
1. In Deploy Software, ensure **Assigned** is selected and click **OK**.
1. In **Group Policy Management Editor**, expand **Administrative Templates** and click **LAPS**.
1. Under LAPS, double-click **Enable local admin password management**.
1. In Enable local admin password management, cick **Enabled** and click **OK**.
1. In **Group Policy Management Editor**, under **LAPS**, double-click **Password Settings**.
1. In Password Settings, click **Enabled**. Under **Password Complexity**, ensure **Large Letters + small letters + number + specials** is selected. Beside **Password Length**, ensure **14** is filled in. Beside **Password Age (Days)**, ensure **30** is filled in. Click **OK**.
1. In **Group Policy Management Eidtor**, under **LAPS**, double-click **Do not allow password expiration time longer than required by policy**.
1. In Do not allow password expiration time longer than required by policy, click **Enabled** and click **OK**.
1. Close **Group Policy Management Editor**.
1. In **Group Policy Management**, in the context-menu of **Devices**, click **Link an Existing GPO...**
1. In Select GPO, click **Custom Computer LAPS** and click **OK**.

## Exercise 2: Use Local Administrator Password Solution

1. [Ensure installation and configuration of LAPS](#task-1-ensure-installation-and-configuration-of-laps) on CL2
1. [Retrieve local administrator password](#task-2-retrieve-local-administrator-password) for CL2
1. [Sign in using the local administrator](#task-3-sign-in-using-the-local-administrator) on CL2
1. [Reset the local administrator password](#task-4-reset-the-local-administrator-password) for CL2
1. [Verify password reset](#task-5-verify-password-reset) on CL2

### Task 1: Ensure installation and configuration of LAPS

Perform this task on CL2.

1. Open **Terminal** or **Windows PowerShell**.
1. In Terminal, force the update of group policies.

    ````powershell
    gpupdate.exe /force
    ````

1. At the prompt OK to restart, enter **y**.
1. In You're about to be signed out, click **Close**.

    Wait for CL2 to restart.

1. Sign in as **Administrator**.
1. Open **Terminal** or **Windows PowerShell**.
1. In Terminal, update group policies.

    ````powershell
    gpupdate.exe
    ````

### Task 2: Retrieve local administrator password

Perform this task on CL1.

1. Open **LAPS UI**.
1. In LAPS UI, under **Computer name**, type **CL2** and click **Search**.

    Under **Password**, take a note of the password or copy it to the clipboard. You will need it in the next task.

### Task 3: Sign in using the local administrator

Perform this task on CL2.

1. Sign in as **Administrator** using the password, you noted in the previous task.

### Task 4: Reset the local administrator password

Perform this task on CL1.

1. Open **LAPS UI**.
1. In LAPS UI, under **Computer name**, type **CL2** and click **Search**.
1. Click **Set**.

### Task 5: Verify password reset

Perform this task on CL2.

1. Open **Terminal** or **Windows PowerShell**.
1. In Terminal, update group policies.

    ````powershell
    gpupdate.exe
    ````

1. Sign out.
1. Sign in as **Administrator** using the password, you noted in the previous task.

    This will fail, because the password was reset and is not valid anymore.

If time allows, repeat this exercise on one of the servers, e.g. VN1-SRV8.