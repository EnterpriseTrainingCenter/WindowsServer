# Lab: Audit file server events

## Required VMs

* VN1-SRV1
* VN1-SRV2
* CL1

## Setup

1. On **CL1**, sign in as **ad\\Administrator**.
2. On **CL2**, sign in as **ad\\Pia**.

## Introduction


## Exercises

1. [Audit access to a folder](#exercise-1-audit-access-to-a-folder)
1. [Use a a global audit policy](#exercise-2-use-a-a-global-audit-policy)

## Exercise 1: Audit access to a folder

1. [Install the Group Policy Management Console](#task-1-install-the-group-policy-management-console) on CL1
1. [Create a group policy object](#task-2-create-a-group-policy-object)
1. [Edit the audit policy in the group policy object](#task-3-edit-the-audit-policy-in-the-group-policy-object)
1. [Apply the group policy object to file servers](#task-4-apply-the-group-policy-object-to-file-servers) by creating an organizational unit, linking the group policy object to the OU and moving VN1-SRV2 into the OU.
1. [Enable auditing of read access](#task-5-enable-auditing-of-read-access) on the D:\Shares\Finance
1. [Create auditing events](#task-6-create-auditing-events) by accessing files in the Finance share
1. [View auditing events](#task-7-view-auditing-events) on VN1-SRV2

    > Which event IDs are associated with file access?
    > Can you identify the file, Pia accessed?

### Task 1: Install the Group Policy Management Console

#### Desktop experience

Perform these steps on CL1.

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
1. If required, restart the computer.

#### PowerShell

Perform these steps on CL1.

1. Run **Windows Terminal** as Administrator.
1. Add the windows capability RSAT: Group Policy Management Tools

    ````powershell
    Get-WindowsCapability -Online -Name 'RSAT.GroupPolicy.Management.Tools*' |
    Add-WindowsCapability -Online    
    ````

1. If required, restart the computer.

    ````powershell
    Restart-Computer
    ````

### Task 2: Create a group policy object

#### Desktop experience

Perform these steps on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Forest: ad.adatum.com**, **Domains**, **ad.adatum.com**, and click **Group Policy Objects**.
1. In the context-menu of **Group Policy Objects**, click **New**.
1. In New GPO, under **Name**, type **Custom Computer Audit Object Access** and click **OK**.

#### PowerShell

Perform these steps on CL1.

1. Open **Windows Terminal**.
1. Create a new GPO with the name **Custom Computer Audit**.

    ````powershell
    New-GPO -Name 'Custom Computer Audit Object Access'
    ````

### Task 3: Edit the audit policy in the group policy object

Perform this Task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Forest: ad.adatum.com**, **Domains**, **ad.adatum.com**, and click **Group Policy Objects**.
1. In the context-menu of **Custom Computer Audit Object Access**, click **Edit**.
1. In Group Policy Management Editor, under **Computer Configuration**, expand **Policies**, **Windows Settings**, **Security Settings**, **Local Policies** and click **Audit Policy**.
1. Double-Click **Audit object access**.
1. In Audit object access Properties, activate the checkboxes **Define these policy settings**, **Success**, and **Failure**, and click **OK**.
1. In **Group Policy Object Management Editor**, double-Click **Audit policy change**.
1. In Audit policy change Properties, activate the checkboxes **Define these policy settings** and  **Success**, and click **OK**.
1. Close **Group Policy Management Editor**.

### Task 4: Apply the group policy object to file servers

#### Desktop experience

Perform this Task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Forest: ad.adatum.com**, **Domains**, **ad.adatum.com**.
1. In the context-menu of **ad.adatum.com**, click **New Organizational Unit**.
1. In New Organizational Unit, in **Name**, type **Devices** and click **OK**.
1. In the context-menu of **Devices**, click **New Organizational Unit**.
1. In New Organizational Unit, in **Name**, type **Devices** and click **OK**.
1. Expand **Devices**.
1. In the context-menu of **Servers**, click **New Organizational Unit**.
1. In New Organizational Unit, in **Name**, type **File Servers** and click **OK**.
1. Expand **Servers**.
1. In the context-menu of **File Servers**, click **Link an Existing GPO...**
1. In Select GPO, click **Custom Computer Audit Object Access** and click **OK**.
1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, in the left pane, click **ad (local)**.
1. In the middle pane, double-click **Computers**.
1. In Active Directory Administrative Center > ad (local) > Computers, in the context-menu of **VN1-SRV2**, click **Move...**
1. In Move, in the middle column, click **Devices**.
1. In the right column, click **Servers**.
1. In the right column, click **File Servers** and click **OK**.
1. Switch to **Group Policy Management**.
1. In the context-menu of **File Servers**, click **Group Policy Update...**
1. In Force Group Policy update, verify that **1 computer** is affected, and click **Yes**.
1. In **Remote Group Policy update results**, verify that the update on **VN1-SRV2.ad.adatum.com** **Succeeded** and click **Close**.

#### PowerShell

Perform these steps on CL1.

1. Open **Windows Terminal**.
1. Create a new organizational unit with the name **Devices** in the domain.

    ````powershell
    $organizationalUnit = New-ADOrganizationalUnit `
        -Name 'Devices' `
        -Path 'dc=ad,dc=adatum,dc=com' `
        -PassThru
    ````

1. In the organization unit **Devices**, create another organizational unit with the name **Servers**.

    ````powershell
    $organizationalUnit = New-ADOrganizationalUnit `
        -Name 'Servers' `
        -Path $organizationalUnit.DistinguishedName
        -PassThru
    ````

1. In the organization unit **Servers**, create another organizational unit with the name **File Servers**.

    ````powershell
    $organizationalUnit = New-ADOrganizationalUnit `
        -Name 'File Servers' `
        -Path $organizationalUnit.DistinguishedName
        -PassThru
    ````

1. Link the GPO **Custom Computer Audit Object Access** to the organizational unit **File Servers**.

    ````powershell
    New-GPLink `
        -Name 'Custom Computer Audit Object Access' `
        -Target $organizationalUnit.DistinguishedName
    ````

1. Move **VN1-SRV2** to the organizational unit **File Servers**.

    ````powershell
    Get-ADComputer 'VN1-SRV2' | 
    Move-ADObject -TargetPath $organizationalUnit.DistinguishedName
    ````

1. Update group policy settings on **VN1-SRV2**.

    ````powershell
    Invoke-GPUpdate -Computer 'VN1-SRV2' -Force
    ````

### Task 5: Enable auditing of read access

#### Desktop experience

Perform this task on CL1.

1. In **File Explorer**, navigate to **\\\\VN1-SRV2**.
1. In the context-menu of **Finance**, click **Properties**.
1. In Finance (\\\\VN1-SRV2) Properties, click the tab **Security**.
1. On the tab Security, click **Advanced**.
1. In Advanced Security Settings for Finance (\\\\VN1-SRV2), click the tab **Auditing**.
1. On the tab Auditing, click **Add**.
1. In Auditing Entry for Finance (\\\\VN1-SRV2), click the link **Select a principal**.
1. In Select User, Computer, Service Account, or Group, under **Enter the object name to select**, type **Everyone** and click **OK**.
1. In **Auditing Entry for Finance (\\\\VN1-SRV2)**, in **Type**, ensure **Success** is selected.
1. In **Applies to**, ensure **This folder, subfolder and files** is selected.
1. Under **Basic permissions**, ensure **Read & execute**, **List folder contents**, and **Read** are activated and click **OK**.
1. In **Advanced Security Settings for Finance (\\\\VN1-SRV2)**, click  **OK**.
1. In **Finance (\\\\VN1-SRV2) Properties**, click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Open a remote PowerShell session to **VN1-SRV2**.

    ````powershell
    Enter-PSSession VN1-SRV2
    ````

1. On **D:\\Shares\\Finance**, disable inheritance for the audit rules, removing all existing rules.

    ````powershell
    $path = 'D:\Shares\Finance'
    $acl = Get-Acl -Path $path -Audit
    $acl.SetAuditRuleProtection($true, $false)
    ````

1. Enable auditing or read and execute for everyone.

    ````powershell
    # Typical flags for audit rules

    $inheritanceFlags = 'ContainerInherit, ObjectInherit'
    $propagationFlags = 'None'
    $auditFlags = 'Success'

    # Create the Read rule

    $auditRule = New-Object `
        -TypeName `
            System.Security.AccessControl.FileSystemAuditRule `
        -ArgumentList `
            'Everyone', `
            'ReadAndExecute', `
            $inheritanceFlags, `
            $propagationFlags, `
            $auditFlags
    
    $acl.AddAuditRule($auditRule)

1. Write back the acl.

    ````powershell
    $acl | Set-Acl -Path $path
    ````

### Task 6: Create auditing events

Perform this task on CL2.

1. In **File Explorer**, navigate to **\\\\VN1-SRV2\\Finance**.
1. Open a file.

### Task 7: View auditing events

#### Desktop experience

Perform this task on CL1.

1. Open **Event Viewer**.
1. In Event Viewer, in the left pane, in the context-menu of **Event Viewer (local)**, click **Connect to Another computer...**.
1. In Select Computer, click **Another computer**, type **VN1-SRV2**, and click **OK**.
1. Expand **Windows Logs** and click **Security**.
1. In the context-menu of **Security**, click **Filter Current Log...**
1. In Filter Current log, in **\<All Event IDs\>**, type **4663, 4656** and click **OK**.
1. In **Event Viewer**, browse through the events.

    > Can you identify the file, Pia accessed?

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Retrieve all entries from the security event log on VN1-SRV2 and store them in a variable.

    ````powershell
    $log = Get-EventLog -LogName Security -ComputerName VN1-SRV2
    ````

1. Filter the retrieved events to find events containing **D:\\Shares\\Finance**.

    ````powershell
    $log | Where-Object { $PSItem.Message -like '*D:\Shares\Finance*' }
    ````

    > You will see events with the IDs 4663 and 4656.

1. View events with the ID 4663 as list to see details of the events.

    ````powershell
    $log | Where-Object { $PSItem.InstanceID -eq 4663 } | Format-List
    ````

    > Can you identify the file, Pia accessed?

## Exercise 2: Use a a global audit policy

1. [Edit the group policy object](#task-1-edit-the-group-policy-object) and enable auditing of deletes for all files and folders
2. [Create auditing events](#task-2-create-auditing-events) by deleting a file in the Finance share
3. [View auditing events](#task-3-view-auditing-events)

    > Can you identify the file, Pia deleted?

### Task 1: Edit the group policy object

Perform this task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Forest: ad.adatum.com**, **Domains**, **ad.adatum.com**, and click **Group Policy Objects**.
1. In the context-menu of **Custom Computer Audit Object Access**, click **Edit**.
1. In Group Policy Management Editor, under **Computer Configuration**, expand **Policies**, **Windows Settings**, **Security Settings**, **Local Policies** and click **Security Options**.
1. In the right pane, double-click **Audit: Force audit policy subcategory settings (Windows Vista or later) to override audit policy category settings**.
1. In Audit: Force audit policy subcategory settings (Windows Vista or later) to override audit policy category settings Properties, active the checkbox **Define this policy setting**, click **Enabled** and click **OK**.
1. In **Group Policy Management Editor**, under **Computer Configuration**, expand **Policies**, **Windows Settings**, **Security Settings**, In the left pane, under **Security Settings**, expand **Advanced Audit Policy Configuration**, **Audit Policies** and click **Object Access**.
1. In the right pane, double-click **Audit File System**.
1. In Audit File System Properties, activate the checkboxes **Configure the following audit events**, **Success**, and **Failure**, and click **OK**.
1. In **Group Policy Management Editor**, under **Audit Policies**, click **Global Object Access Auditing**.
1. In the right pane, double-click **File system**.
1. In File system Properties, activate the checkbox **Define this policy setting** and click **Configure**.
1. In Advanced Security Settings for Global File SACL, click **Add**.
1. In Auditing Entry for Global File SACL, click the link **Select a principal**.
1. In Select User, Computer Service Account, or Group, under **Enter the object name to select**, type **EveryOne** and click **OK**.
1. In **In Auditing Entry for Global File SACL**, in **Type**, ensure **Success** is selected.
1. Under Permissions, deactivate all checkboxes except **Delete subfolders and files** and **Delete**.
1. Click **OK**.
1. In **Advanced Security Settings for Global File SACL**, click **OK**.
1. In **File system Properties**, click **OK**.
1. Close **Group Policy Management Editor**.
1. In **Group Policy Management**, expand **Devices** and **Servers**.
1. In the context-menu of **File Servers**, click **Group Policy Update...**
1. In Force Group Policy update, verify that **1 computer** is affected, and click **Yes**.
1. In **Remote Group Policy update results**, verify that the update on **VN1-SRV2.ad.adatum.com** **Succeeded** and click **Close**.

### Task 2: Create auditing events

Perform this task on CL2.

1. In **File Explorer**, navigate to **\\\\VN1-SRV2\\Finance**.
1. Delete a file.

### Task 3: View auditing events

#### Desktop experience

Perform this task on CL1.

1. Open **Event Viewer**.
1. In Event Viewer, in the left pane, in the context-menu of **Event Viewer (local)**, click **Connect to Another computer...**.
1. In Select Computer, click **Another computer**, type **VN1-SRV2**, and click **OK**.
1. Expand **Windows Logs** and click **Security**.
1. In the context-menu of **Security**, click **Filter Current Log...**
1. In Filter Current log, in **\<All Event IDs\>**, type **4663** and click **OK**.
1. In **Event Viewer**, browse through the events.

    > Can you identify the file, Pia deleted?

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Retrieve all entries from the security event log on VN1-SRV2 and store them in a variable.

    ````powershell
    $log Get-EventLog `
        -LogName Security `
        -Newest 20 `
        -InstanceId 4663 `
        -ComputerName VN1-SRV2
    ````

1. View events as list to see details of the events.

    ````powershell
    $log | Format-List
    ````

    > Can you identify the file, Pia deleted?
