# Lab: Manage file sharing

## Required VMs

* VN1-SRV1
* VN1-SRV4
* VN1-SRV10
* CL1
* CL2

## Setup

On **CL1**, logon as **ad\Administrator**.

On **VN1-SRV10**, logon as **ad\Administrator**.

If you skipped the lab [Manage local storage](../Labs/Manage-local-storage.md), on VN1-SRV10, run ````C:\LabResources\Solutions\New-Volumes.ps1````.

If you skipped the lab [Explore Windows Admin Center](../Labs/Explore-Windows-Admin-Center.md), on VN1-SRV4, run ````C:\LabResources\Solutions\Add-WACServers.ps1````.

## Introduction

You want to create shares for Finance, IT, and Marketing and configure permissions on the shares. You want to explore offline files. Finance dever should never be cached on a client, but Marketing data should automatically be cached. Finally, you want to ensure, that minor user errors like accidental deletes or modifications of files can be restored by the users in self-service.

## Exercises

1. [Manage file shares and permissions](#exercise-1-manage-file-shares-and-permissions)
1. [Explore offline files](#exercise-2-explore-offline-files)
1. [Explore Volume Shadow Copies](#exercise-3-configure-volume-shadow-copies)

## Exercise 1: Manage file shares and permissions

1. [Create an organizational unit](#task-1-create-an-organization-unit) in the domain with the name Entitling groups
1. [Create domain local groups and add members](#task-2-create-domain-local-groups-and-add-members) in the organizational unit according to the table below.

    | Group            | Members         |
    |------------------|-----------------|
    | Finance Read     |                 |
    | Finance Modify   | Managers        |
    | IT Read          | Managers        |
    | IT Modify        | IT              |
    | Marketing Read   | Sales, Managers |
    | Marketing Modify | Marketing       |

1. [Create shares and configure file system and share permissions](#task-3-create-shares-and-configure-file-system-and-share-permissions) according to the table below. Leave the permissions for Administrators, CREATOR OWNER and SYSTEM.

    | Share name | Modify             | Read             |
    |------------|--------------------|------------------|
    | Finance    | Finance Modify     | Finance Read     |
    | IT         | IT Modify          | IT Read          |
    | Marketing  | Marketing Modify   | Marketing Read   |

1. [Copy the contents of the folders with the respective names](#task-4-copy-the-contents-of-the-folders-with-the-respective-names) from c:\Sample Documents to the file shares
1. [Verify access to the file shares](#task-5-verify-access-to-the-file-shares) using the test users from the table below

    | User | Finance permissions | IT permissions | Marketing permissions |
    |------|---------------------|----------------|-----------------------|
    | Bill |                     |                | Modify                |
    | Lara |                     | Modify         |                       |
    | Pia  | Modify              | Read           | Read                  |

### Task 1: Create an organization unit

#### Active Directory Users and Computer

Perform this task on CL1.

1. From the desktop, open **Basic Administration**.
1. In Basic Ádministration, expand **Active Directory Users and Computer**, **ad.adatum.com**.
1. In the context-menu of **ad.adatum.com**, click **New**, **Organizational Unit**.
1. In New Object - Organizational Unit, in **Name**, type **Entitling groups** and click **OK**.

#### Active Directory Administrative Center

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In the context-menu of **ad (local)**, click **New**, **Organizational Unit**.
1. In Create Organizational Unit, in **Name**, type **Entitling groups** and click **OK**.

#### Windows Admin Center

Perform this task on CL1.

1. Using Microsoft edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **VN1-SRV1.ad.adatum.com**.
1. Connected to VN1-SRV1.ad.adatum.com, under Tools, click **Active Directory**.
1. Under Active Directory Domain Services, click the tab **Browse**.
1. Click **DC=ad, DC=adatum, DC=com**.
1. In the right pane, click **Create**, **OU**.
1. In the pane Add Organizational Unit, in **Name**, type **Entitling groups** and click **Create**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Create the organizational unit **Entitling groups** at the domain level.

    ````powershell
    New-ADOrganizationalUnit `
        -Path 'DC=ad, DC=adatum, DC=com' `
        -Name 'Entitling groups'
    ````

### Task 2: Create domain local groups and add members

Create domain local groups according to the table above in the organizational unit **Entitling groups**. Use the different administration tools for this task. Below, generic instructions for each tool are provided.

#### Active Directory Users and Computers

Perform this task on CL1.

1. From the desktop, open **Basic Administration**.
1. In Basic Administration, expand **Active Directory Users and Computer**, **ad.adatum.com**.
1. Click **Entitling Groups**.
1. In the context-menu of Entitling Groups, click **New**, **Group**.
1. In New Object - Group, in **Group name**, type the name of the group. **Group scope** should be **Domain local** and **Group type** should be **Security**. Click **OK**.
1. Double-click the new group.
1. In ... Properties, click the tab **Members**.
1. On the tab Members, click **Add...**.
1. In Select Groups, Contacts, Computers, Service Accounts, or Groups, in **Enter the object names to select**, type the name of the members. You can type multiple names separted by semicolon.
1. Click **OK**.
1. On the tab **Members**, click **OK**.

#### Active Directory Administrative Center

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. Click **ad (local)**.
1. Double-click **Entitling groups**.
1. In the pane **Tasks**, click **New**, **Group**.
1. In Create Group, in **Group name**, type the name of the new group.
1. Under **Group scope**, click **Domain local**.
1. On the left, click **Members**.
1. Under Members, click **Add...**.
1. In Select Groups, Contacts, Computers, Service Accounts, or Groups, in **Enter the object names to select**, type the name of the user. You can type multiple users separted by semicolon.
1. Click **OK**.
1. Click **OK**.

#### Windows Admin Center

Perform this task on CL1.

1. Using Microsoft edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **VN1-SRV1.ad.adatum.com**.
1. Connected to VN1-SRV1.ad.adatum.com, under Tools, click **Active Directory**.
1. Under Active Directory Domain Services, click the tab **Browse**.
1. In the tree pane, click **Entitling Groups**.
1. In the right pane, click **Create**, **Group**.
1. In the pane Add Group, in **Name**, type the name of the new group.
1. Under **Group Scope**, ensure **Domain local** is selected.
1. In **Sam Account Name**, type the name of the new group again.
1. Right to **Create in**, click **Change...**.
1. In Select Path, click **Entitling Groups**.
1. Click **Select**.
1. Click **Create**.
1. In the right pane, left to the search box, click the icon *Refresh*.
1. Click the new group.
1. Click **Properties**.
1. In Group properties: ..., on the left, click **Membership**.
1. Click **Add**.
1. In the pane Add Group Membership, under **User SamAccountname**, type the name of the member and click **Add**.
1. Repeat the last two steps for additional members.
1. Click **Save**.
1. Click **Close**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Define the location for the new group.

    ````powershell
    $path = 'OU=Entitling Groups, DC=ad, DC=adatum, DC=com'
    ````

1. Define the parameters for the new group.

    ````powershell
    # Fill in the data in quotes

    $groupName = ''
    <# 
        you can provide multiple member names separated by comma, 
        e.g., 'Sales', 'Managers'
    #>
    $members = ''
    ````

1. Create the group and add the members.

    ````powershell
    New-ADGroup -Name $groupName -Path $path -GroupScope DomainLocal -PassThru |
    Add-ADGroupMember -Members $members
    ````

### Task 3: Create shares and configure file system and share permissions

Create the shares on VN1-SRV10 and configure the permissions according to the table above. Leave the permissions for Administrators, CREATOR OWNER and SYSTEM. Use different administation tools for this task. Below, generic instructions for each tool are provided.

#### Server Manager

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the left pane, click **File and Storage Services**.
1. In File and Storage Services > Servers, on the left, click **Shares**.
1. In Server Manager > File and Storage Services > Shares, under **SHARES**, click **Tasks**, **New Share**.
1. In the New Share Wizard, on page **Select Profile**, ensure **SMB Share - Quick** is selected and click **Next >**.
1. On page **Share Location**, under **Servers** click **VN1-SRV10**.
1. Under **Share location**, click **D:** and click **Next >**.
1. On page **Share Name**, in **Share name**, type the share name and click **Next >**.
1. On page **Other Settings**, activate the checkboxes **Enable access-based enumeration** and click **Next >**.
1. On page **Permissions**, click **Customize permissions...**.
1. In Advanced Security Settings for ..., on tab **Permissions**, click **Disable inheritance**.
1. In the dialog **What would you like to do with the current inherited permissioons?**, click **Convert inherited permissions into explicit permissions on this object.**
1. Under **Permission entries**, click the first entry with the **Principal** **Users (VN1-SRV10\\Users)** and click **Remove**. Repeat this step for all entries except for those with Principal **Administrators**, **SYSTEM**, and **CREATOR OWNER**.
1. Click **Add**.
1. In Permission Entry for ..., click **Select a principal**.
1. In Select User, Computer, Service Account, or Group, in **Enter the object name to select**, type the name of the group to grant read permissions (e.g. **Finance Read)** and click **OK**.
1. In **Permission entry for ...**, under **Basic permissions**, ensure **Read & execute** is activated and click **OK**.
1. In **Advanced Security Settings for ...**, click **Add**.
1. In Permission Entry for ..., click **Select a principal**.
1. In Select User, Computer, Service Account, or Group, in **Enter the object name to select**, type the name of the group to grant modify permissions (e.g. **Finance Modify)** and click **OK**.
1. In **Permission entry for ...**, under **Basic permissions**, activate the checkbox **Modify** and click **OK**.

    > The permission entries should be equal to the table below.

    | Type  | Principal                                | Access         | Inherited from | Applies to                        |
    |-------|------------------------------------------|----------------|----------------|-----------------------------------|
    | Allow | Administrators (VN1-SRV10\\Administrators) | Full Control   | None           | This folder, subfolders and files |
    | Allow | SYSTEM                                   | Full Control   | None           | This folder, subfolders and files |
    | Allow | CREATOR OWNER                            | Full Control   | None           | Subfolders and files only         |
    | Allow | Finance Read (ad\\Finance Read)       | Read & execute | None           | This folder, subfolders and files |
    | Allow | Finance Modify (ad\\Finance Modify)   | Modify         | None           | This folder, subfolders and files |

1. In **Advanced Security Settings for ...**, click the tab **Share**.
1. In the tab Share, click **Add**.
1. In Permission entry for ..., click **Select a principal**.
1. In Select User, Computer, Service Account, or Group, in **Enter the object name to select**, type the name of the group to grant read permissions (e.g. **Finance Read)** and click **OK**.
1. In **Permission entry for ...**, under **Permissions**, ensure **Read** is activated and click **OK**.
1. In **Advanced Security Settings for ...**, click **Add**.
1. In Select User, Computer, Service Account, or Group, in **Enter the object name to select**, type the name of the group to grant modify permissions (e.g. **Finance Modify)** and click **OK**.
1. In **Permission entry for ...**, under **Permissions**, activate the checkbox **Change** and click **OK**.
1. In **Advanced Security Settings for ...**, click **Add**.
1. In Permission entry for ..., click **Select a principal**.
1. In Select User, Computer, Service Account, or Group, click **Locations...**.
1. In Locations, click **VN1-SRV10.ad.adatum.com** and click **OK**.
1. In **Select User, Computer, Service Account, or Group**, in **Enter the object name to select**, type **Administrators** and click **OK**.
1. In **Permission entry for ...**, under **Permissions**, activate the checkbox **Full Control** and click **OK**.
1. In **Advanced Security Settings for ...**, click the permission entry with the **Principal** **Everyone** and click **Remove**.

    > The permission entries should be equal to the table below.

    | Type  | Principal                                | Access         |
    |-------|------------------------------------------|----------------|
    | Allow | Finance Read (ad\\Finance Read)       | Read           |
    | Allow | Finance Modify (ad\\Finance Modify)   | Change         |
    | Allow | Administrators (VN1-SRV10\\Administrators) | Full Control   |

1. Click **OK**.
1. On page **Permissions**, click **Next >**.
1. On page **Confirm selections**, click **Create**.
1. On page **Results**, click **Close**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Open a remote PowerShell session to **VN1-SRV10**.

    ````powershell
    Enter-PSSession VN1-SRV10
    ````

1. Create the folder.

    ````powershell
    # TODO: Fill the share name between the quotes, e.g., 'Finance'
    $shareName = ''
    $path = "D:\Shares\$shareName"
    New-Item $path -ItemType Directory
    ````

1. On the new folder, disable inheritance, preserve existing access rules.

    ````powershell
    $acl = Get-Acl $path
    $acl.SetAccessRuleProtection($true, $true)
    ````

1. Remove all permission entries except for those for principals **Administrators**, **SYSTEM**, and **CREATOR OWNER**.

    ````powershell
    $acl.Access | 
    Where-Object { 
        $PSItem.IdentityReference -notin @(
            'BUILTIN\Administrators', 
            'NT AUTHORITY\SYSTEM', 
            'CREATOR OWNER'
        ) 
    } | 
    ForEach-Object { $acl.RemoveAccessRule($PSItem) }
    `````

1. Grant the permissions to the groups.

    ````powershell
    # Typical flags for access rules

    $inheritanceFlags = 'ContainerInherit, ObjectInherit'
    $propagationFlags = 'None'
    $type = 'Allow'

    # Create the Read rule

    $accessRule = New-Object `
        -TypeName `
            System.Security.AccessControl.FileSystemAccessRule `
        -ArgumentList `
            "ad\$shareName Read", `
            'ReadAndExecute', `
            $inheritanceFlags, `
            $propagationFlags, `
            $type
    
    $acl.AddAccessRule($accessRule)

    # Create the Modify rule

    $accessRule = New-Object `
        -TypeName `
            System.Security.AccessControl.FileSystemAccessRule `
        -ArgumentList `
            "ad\$shareName Modify", `
            "Modify", `
            $inheritanceFlags, `
            $propagationFlags, `
            $type
    
    $acl.AddAccessRule($accessRule)
    ````

1. Write back the permissions

    ````powershell
    $acl | Set-Acl -Path $path
    ````

1. Create the share.

    ````powershell
    New-SmbShare `
        -Name $sharename `
        -Path $path `
        -FullAccess 'BUILTIN\Administrators' `
        -ReadAccess "ad\$shareName Read" `
        -ChangeAccess "ad\$shareName Modify" `
        -FolderEnumerationMode AccessBased `
        -EncryptData $true
    ````

### Task 4: Copy the contents of the folders with the respective names

#### Desktop Experience

Perform this task on CL1.

1. Open **File Explorer**.
1. In File Explorer, copy the contents of the respective folder in \\\\VN1-SRV10\\c$\LabResources\\Sample Documents to the share with the same name on VN1-SRV10.

#### Windows Admin Center

Perform this task on CL1.

1. Using Microsoft edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **VN1-SRV10.ad.adatum.com**.
1. Connected to VN1-SRV10.ad.adatum.com, under Tools, click **File & file sharing**.
1. In the tab Files, navigate to **C:\LabResources\Sample Documents** and click the respective folder name.
1. In the right pane, activate the checkbox left to the column name **Name** to select all files and folders.
1. Click **Copy**. If you do not see Copy, click the ellipsis (**...**).
1. Navigate to **D:\Shares** and click the respective folder name.
1. Click **Paste**. If you do not see Paste, click the ellipsis (**...**).

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Open a remote PowerShell session to **VN1-SRV10**.

    ````powershell
    Enter-PSSession VN1-SRV10
    ````

1. On VN1-SRV10, copy the contents of the respective folder in **C:\\LabResources\\Sample Documents** to **D:\\Shares\\...**.

    ````powershell
    # TODO: Fill the share name between the quotes, e.g., 'Finance'
    $shareName = ''
    Copy-Item `
        -Path "C:\LabResources\Sample Documents\$shareName\*" `
        -Destination "D:\Shares\$shareName" `
        -Recurse
    ````

### Task 5: Verify access to the file shares

Perform this task on CL2.

Repeat this task for every user from the table above.

1. Sign in with a user from the table above.
1. Using **File Explorer**, navigate to **\\\\VN1-SRV10**.
1. Try to access the shares and write down the results.
1. In each share, try to create a file and write down the results.
1. Compare the results with the table above.
1. Sign out.

## Exercise 2: Explore offline files

1. [Configure shares for offline access](#task-1-configure-shares-for-offline-access) according to the table below.

    | Share name | Offline Settings                                                                                 | Caching mode |
    |------------|--------------------------------------------------------------------------------------------------|--------------|
    | Finance    | No files or programs from the shared folder are available offline                                | None         |
    | IT         | Only the files and programs that users specify are avaiable offline                              | Manual       |
    | Marketing  | All files and program that users open from the shared folder are automatically available offline | Documents    |

1. [Make files available offline](#task-2-make-files-available-offline) on CL2. In the **IT** share, make **Step-by-Step Guides** available offline. In the **Marketing** share, open a **Building Partnerships.pptx**.

    > In the **Finance** share, can you make a file available offline?

1. [Take CL2 offline](#task-3-take-computer-offline).
1. [Verify offline access](#task-4-verify-offline-access).

    > Which folders and files are available in  \\\\VN1-SRV10\\IT?

    > Which folders and files are available in  \\\\VN1-SRV10\\Finance?

    > Which folders and files are available in  \\\\VN1-SRV10\\Marketing?

1. [Bring CL2 online](#task-5-bring-computer-online).
1. [Verify online access](#task-6-verify-online-access).

    > Can you open \\\\VN1-SRV10\\Marketing\\Building Partnerships.pptx?

### Task 1: Configure shares for offline access

Configure the Offline Settings of the shares on VN1-SRV10 according to the table above. Use different administation tools for this task. Below, generic instructions for each tool are provided.

#### Desktop experience

Perform this task on CL1.

1. On the desktop, open **Basic Administration**.
1. In Basic Administration, click **Computer Management (local)**.
1. From the context-menu of Computer Management (local), click **Connect to another computer...**.
1. In Select Computer, in **Another computer**, type **VN1-SRV10** and click **OK**.
1. Expand **Computer Management (VN1-SRV10)**, **System Tools**, **Shared Folders**, and click **Shares**.
1. From the context-menu of the respective share, click **Properties**.
1. In ... Properties, click **Offline Settings...**.
1. In the tab Sharing, click **Advanced Sharing...**.
1. In Offline Settings, click the required option and click **OK**.
1. In **... Properties**, click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Open a remote PowerShell session to **VN1-SRV10**.

    ````powershell
    Enter-PSSession VN1-SRV10
    ````

1. Configure the caching mode of each share.

    ````powershell
    <# 
        TODO: Fill the share name and the caching mode from the table between 
        the quotes
    #>
    $shareName = ''
    $cachingMode = ''

    Set-SmbShare -Name $shareName -CachingMode $cachingMode
    ````

### Task 2: Make files available offline

Perform this task on CL2.

1. Sign in as **ad\Pia**.
1. Using **File Explorer**, navigate to \\\\VN1-SRV10\\IT.
1. In the context menu of **Step-by-Step Guides**, click **Show more options**, **Always available offline**.
1. Navigate to \\\\VN1-SRV10\\Finance.
1. In the context menu of any document, click **Show more options**

    > The command **Always available offline** is not available.

1. Navigate to \\\\VN1-SRV10\\Marketing.
1. Open **Marketing With Partners.pptx**.
1. If **Sign in to set up Office** appears, close the dialog.
1. If **Your privacy matters** appears, click **Close**.
1. If **Default File Types appears**, click **Office Open XML formats** and click **OK**.
1. Close **Marketing With Partners**.

Stay signed in for the upcoming tasks.

### Task 3: Take computer offline

Perform these steps on the host.

1. Run **Windows PowerShell** as Administrator.
1. In Windows PowerShell, execute

    ````powershell
    Get-VMNetworkAdapter -VMName WIN-CL2 | 
    Disconnect-VMNetworkAdapter
    ````

### Task 4: Verify offline access

Perform these tasks on CL2.

1. Using **File Explorer**, navigate to \\\\VN1-SRV10\\IT.

    > Step-by-Step Guides and all files in the folder are available offline.

1. Navigate to \\\\VN1-SRV10\\Finance.

    > You receive an error message telling you that Windows cannot access the share.

1. Using **File Explorer**, navigate to \\\\VN1-SRV10\\Marketing.

    > All files are visible.

1. Open **Marketing With Partners.pptx**.

    > You should be able to open the file.

1. Close **Marketing With Partners**.

1. In **File Explorer**, open **Building Partnerships.pptx**.

    > PowerPoint will launch. However, you receive an error message, telling you that the file cannot be opened.

1. In the message box **Microsoft PowerPoint**, click **OK**.
1. If **Sign in to set up Office** appears, close the dialog.
1. Close **PowerPoint**.

### Task 5: Bring computer online

Perform this task on the host.

1. Run **Windows PowerShell** as Administrator.
1. In Windows PowerShell, execute

    ````powershell
    Get-VMNetworkAdapter -VMName WIN-CL2 | 
    Connect-VMNetworkAdapter -SwitchName VNet1
    ````

### Task 6: Verify online access

Perform this task on CL2.

1. Using **File Explorer**, navigate to \\\\VN1-SRV10\\Marketing.
1. Open **Building Partnerships.pptx**.
1. If **Sign in to set up Office** appears, close the dialog.

    > The file should appear.

1. Close **Building Partnerships**.

## Exercise 3: Configure Volume Shadow Copies

1. [Configure Volume Shadow Copies](#task-1-configure-volume-shadow-copies) on D: of VN1-SRV10
1. [Delete and modify files on a VSS protected share](#task-2-delete-and-modify-files-on-a-vss-protected-share)
1. [Undo the changes using a Volume Shadow Copy](#task-3-undo-the-changes-using-a-volume-shadow-copy)

### Task 1: Configure Volume Shadow Copies

Perform this task on VN1-SRV10.

> Note: The configuration interface for Volume Shadow Copies is available on NTFS volumes only, although ReFS supports Volume Shadow Copies.

1. Open **File Explorer**.
1. In File Explorer, navigate to **This PC**.
1. In the context menu of **Local Disk (C:)**, click **Properties**.
1. In Local Disk (D:) Properties, click the tab **Shadow Copies**.
1. In the tab Shadow Copies, under **Select a volume**, click **D:\\**.
1. Click **Settings**.

    > You should not click **Enable**, because this configures Shadow Copies with default settings.

1. In Settings, click **Schedule**.
1. In D:\, ensure **1: At 07:00 every Mo, ...** is selected. Under **Schedule Task**, click **Daily**.
1. At the top drop down, click **2: At 12:00 every Mo, ...**. Under **Schedule Task**, click **Daily**.
1. Click **OK**.
1. In **Settings**, click **OK**.
1. In **Local Disk (C:) Properties**, on tab Shadow Copies, ensure **D:\** is still selected.
1. Click **Create now**.
1. Click **OK**.

### Task 2: Delete and modify files on a VSS protected share

Perform this task on CL2.

1. Sign in as **ad\Pia**.
1. Using **File Explorer**, navigate to **\\\\VN1-SRV10\\Finance**.
1. In Finance, delete the file **3 Month Snapshot.xls**.
1. Open **6 Month Snapshot.xlsx**.
1. In 6 Month Snapshot, click **Sheet1**.
1. On Sheet1, delete the first column with the header **Argumentum**.
1. Close **6 Month Snapshot**.
1. In the message box **Want to save your changes to 6 Monath Snapshot.xlsx**, click **Save**.

### Task 3: Undo the changes using a Volume Shadow Copy

Perform this task on CL2.

1. Sign in as **ad\Pia**.
1. Using **File Explorer**, navigate to **\\\\VN1-SRV10\\Finance**.
1. Open **6 Month Snapshot.xlsx**.
1. Verify, the column **Argumentum** is missing.
1. Close **6 Month Snapshot**.
1. In **File Explorer**, in the context menu of **6 Month Snapshot.xlsx**, click **Properties**.
1. In 6 Month Snapshot Properties, click the tab **Previous Versions**.
1. On the tab Previous Versions, click **Open**.
1. In 6 Month Snapshot, click **Sheet1**.

    > Verify the column Argumentum exists.

1. Close **6 Month Snapshot**.
1. In the message box **Want to save your changes to 6 Monath Snapshot.xlsx**, click **Don't Save**.
1. In **6 Month Snapshot Properties**, on the tab Previous Versions, ensure the previous version, you just opened, is still selected. Click **Restore**.
1. In the message box **Are you sure, you want to restore the previous version of "6 Month Snapshot"" from..."**, click **Restore**.
1. In the message box **The file has been successfully restored to the previous version.**, click **OK**.
1. In **6 Month Snapshot Properties**, click **OK**.
1. In **File Explorer**, in Finance, Open **6 Month Snapshot.xlsx**.
1. In 6 Month Snapshot, click **Sheet1**.

    > Verify the column Argumentum exists.

1. Close **6 Month Snapshot**.
1. In the message box **Want to save your changes to 6 Monath Snapshot.xlsx**, click **Don't Save**.
1. In **File Explorer**, in the context menu of **Finance** (click an empty area in Finance), click **Properties**.
1. In Finance (\\\\VN1-SRV10) Properties, click the tab **Previous Versions**.
1. On the tab Previous Versions, click **Open**. A new File Explorer window opens.
1. From the new File Explorer window, copy **3 Month Snapshot.xlsx** to the original **Finance** window.
