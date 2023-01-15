# Lab: File server resource management

## Required VMs

VN1-SRV1
VN1-SRV10
CL1

## Setup

1. On CL1, sign in as **ad\Administrator**.
1. On CL2, sign in as **ad\Administrator**.
1. On VN1-SRV10, sign in as **ad\Administrator**.

## Introduction

Adatum wants to use dynamic access control to streamline permissions and fulfill GDPR requirements. First, you have to enable the support for claims for the Kerberos protocol. Then you will grant permissions to files in a share based on user and device properties. Furthermore, to demonstrate the dynamic nature, you will grant permissions based on file resource properties, which could be assigned using File Server Resource Manager. Finally, you will grant permissions based on a Central Access Policy, which can be applied to any share on any server.

## Known issues

[Exercise 3: Manage access based on device properties does not work as expected](<https://github.com/EnterpriseTrainingCenter/WindowsServer/issues/191>)

## Exercises

1. [Enable support for claims](#exercise-1-enable-support-for-claims)
1. [Manage access based on user properties](#exercise-2-manage-access-based-on-user-properties)
1. [Manage access based on device properties](#exercise-3-manage-access-based-on-device-properties)
1. [Manage access based on file resource properties](#exercise-4-manage-access-based-on-file-resource-properties)
1. [Manage access using a Central Access Policy](#exercise-5-manage-access-using-a-central-access-policy)

## Exercise 1: Enable support for claims

1. [Enable claims support for domain controllers](#task-1-enable-claims-support-for-domain-controllers)
1. [Enable claims support for clients](#task-2-enable-claims-support-for-clients)

### Task 1: Enable claims support for domain controllers

#### Desktop experience

Perform this task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand Forest: **ad.adatum.com**, **Domains**, **ad.adatum.com**, and click **Domain Controllers**.
1. In the context-menu of **Domain Controllers**, click **Create a GPO in this domain, and link it here...**
1. In New GPO, under **Name**, type **Custom Computer claims support kdc enable** and click **OK**.
1. In the context-menu of **Custom Computer claims support kdc enable**, click **Edit**.
1. In Group Policy Management Editor, under  **Computer Configuration**, expand **Policies**, **Administrative Templates**, **System**, and click **KDC**.
1. In the right pane, double-click **KDC support for claims, compound authentication and Kerberos armoring**.
1. In KDC support for claims, compound authentication and Kerberos armoring, click **Enabled** and ensure that, under **Claims, compound authenticatin for Dynamic Access Control and Kerberos armoring options**, **Supported** is selected, then click **OK**.
1. Close **Group Policy management Editor**.
1. In **Group Policy Management**, in the context-menu of **Domain Controllers**, click **Group Policy Update...**.
1. In Force Group Policy update, click **Yes**.
1. In Remote Group Policy update results, ensure, under **Succeeded**, **VN1-SRV1.ad.adatum.com** is listed and click **Close**.

#### PowerShell

Perform this task on CL1.

1. Run **Terminal** as Administrator.
1. Create a new group policy object.

    ````powershell
    $gpo = New-GPO -Name 'Custom Computer claims support kdc enable'
    ````

1. Configure KDC support for claims, compound authentication, and Kerberos as supported.

    ````powershell
    $key = `
        'HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System\KDC\Parameters'
    $gpo | Set-GPRegistryValue `
        -Key $key `
        -ValueName 'EnableCbacAndArmor' `
        -Type DWord `
        -Value 1
    $gpo | Set-GPRegistryValue `
        -Key $key `
        -ValueName 'CbacAndArmorLevel' `
        -Type DWord `
        -Value 1
    ````

1. Link the group policy object to the organizational unit Domain Controllers.

    ````powershell
    $gpo | New-GPLink -Target 'ou=Domain Controllers, dc=ad, dc=adatum, dc=com'
    ````

1. Invoke a group policy update all domain controllers.

    ````powershell
    Get-ADComputer `
        -Filter * `
        -SearchBase 'ou=Domain Controllers, dc=ad, dc=adatum, dc=com' | 
    Select-object -ExpandProperty DNSHostName | 
    Invoke-GPUpdate
    ````

### Task 2: Enable claims support for clients

#### Desktop experience

Perform this task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand Forest: **ad.adatum.com**, **Domains**, **ad.adatum.com**.
1. In the context-menu of **ad.adatum.com**, click **Create a GPO in this domain, and link it here...**
1. In New GPO, under **Name**, type **Custom Computer claims support clients enable** and click **OK**.
1. In the context-menu of **Custom Computer claims support clients enable**, click **Edit**.
1. In Group Policy Management Editor, under  **Computer Configuration**, expand **Policies**, **Administrative Templates**, **System**, and click **Kerberos**.
1. In the right pane, double-click **Kerberos client support for claims, compound authentication and Kerberos armoring**.
1. In Kerberos client support for claims, compound authentication and Kerberos armoring, click **Enabled** and click **OK**.
1. Close **Group Policy management Editor**.

#### PowerShell

Perform this task on CL1.

1. Run **Terminal** as Administrator.
1. Create a new group policy object.

    ````powershell
    $gpo = New-GPO -Name 'Custom Computer claims support clients enable'
    ````

1. Enable Kerberos client support for claims, compound authentication and Kerberos armoring.

    ````powershell
    $key = `
        'HKLM\Software\Microsoft\Windows\CurrentVersion\Policies\System\Kerberos\Parameters'
    $gpo | Set-GPRegistryValue `
        -Key $key `
        -ValueName 'EnableCbacAndArmor' `
        -Type DWord `
        -Value 1
    ````

1. Link the group policy object to the domain.

    ````powershell
    $gpo | New-GPLink -Target 'dc=ad, dc=adatum, dc=com'
    ````

## Exercise 2: Manage access based on user properties

1. [Create a new claim type](#task-1-create-a-new-claim-type) department for users
1. [Create a new share](#task-2-create-a-new-share) Research on VN1-SRV10 and grant users of the department Research modify permissions
1. [Verify access to the share](#task-3-verify-access-to-the-share) Research

    > Does a user of the department Research have access to the Research share?

    > Does a user of a department other than Research have access to the Research share?

### Task 1: Create a new claim type

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **Dynamic Access Control**.
1. In Dynamic Access Control, double-click **Claim Types**.
1. In Claim Types, in the context-menu of the empty area, click **New**, **Claim Type**.
1. In Create Claim Type, under **Source Attribute**, click **department**.
1. Under **Suggested Value**, click **The following values are suggested**.
1. Click **Add...**.
1. In Add suggested value, in **Value** and in **Display name**, type **Finance**, and click **OK**. Repeat this step for

    * IT
    * Marketing
    * Research

1. In **Create Claim Type: department**, click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Create a new claim type from the source attribute **Department** with the suggested values Finance, IT, Marketing, and **Research**.

    ````powershell
    New-ADClaimType `
        -DisplayName 'department' `
        -SourceAttribute `
            'CN=Department, CN=Schema, CN=Configuration, DC=ad, DC=adatum, DC=com' `
        -ProtectedFromAccidentalDeletion $true `
        -SuggestedValues (
            'Finance', 'IT', 'Marketing', 'Research' | Foreach-Object {
                New-Object `
                    -TypeName `
                        Microsoft.ActiveDirectory.Management.ADSuggestedValueEntry `
                    -ArgumentList $PSItem, $PSItem, ''
        })
    ````

### Task 2: Create a new share

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the left pane, click **File and Storage Services**.
1. In File and Storage Services > Servers, on the left, click **Shares**.
1. In Server Manager > File and Storage Services > Shares, under **SHARES**, click **Tasks**, **New Share**.
1. In the New Share Wizard, on page **Select Profile**, ensure **SMB Share - Quick** is selected and click **Next >**.
1. Under **Share location**, click **D:** and click **Next >**.
1. On page Share Name, in **Share name**, type **Research** and click **Next >**.
1. On page Other Settings, activate the checkboxes **Enable access-based enumeration** and **Encrypt data access**. Click **Next >**.
1. On page **Permissions**, click **Customize permissions...**.
<!-- 1. Advanced Security Settings for Research, click the tab **Central Policy**.
1. On tab Central Policy, beside **Central Policy**, click **Change**. In the drop-down, click **Research Policy**. Beside **Applies to** ensure **This folder, subfolders and files** is selected. Click **OK**. -->
1. In Advanced Security Settings for Research, on tab **Permissions**, click **Disable inheritance**.
1. In the dialog **What would you like to do with the current inherited permissioons?**, click **Convert inherited permissions into explicit permissions on this object.**
1. Under **Permission entries**, click the first entry with the **Principal** **Users (VN1-SRV10\\Users)** and click **Remove**. Repeat this step for all entries except for those with Principal **Administrators**, **SYSTEM**, and **CREATOR OWNER**.
1. Click **Add**.
1. In Permission Entry for Research, click **Select a principal**.
1. In Select User, Computer, Service Account, or Group, click **Locations...**
1. In Locations, click **VN1-SRV10.ad.adatum.com** and click **OK**.
1. In **Select User, Computer, Service Account, or Group**, in **Enter the object name to select**, type **Users** and click **OK**.
1. In **Permission entry for Research**, under **Basic permissions**, activate **Modify**.
1. Click **Add a condition**.
1. In the drop-downs, from left to right, click these entries ([figure 1]):

    * User
    * department
    * Equals
    * Value
    * Research

1. Click **OK**.
1. In **Advanced Security Settings for Research**, click the tab **Share**.
1. On the tab Share, click **Add**.
1. In Permission Entry for Research, click **Select a principal**.
1. In Select User, Computer, Service Account, or Group, click **Locations...**
1. In Locations, click **VN1-SRV10.ad.adatum.com** and click **OK**.
1. In **Select User, Computer, Service Account, or Group**, in **Enter the object name to select**, type **Users** and click **OK**.
1. In **Permission entry for Research**, under **Permissions**, activate **Change** and click **OK**.
1. In **Advanced Security Settings for ...**, on the tab Share, click **Add**.
1. In Permission entry for Research, click **Select a principal**.
1. In **Select User, Computer, Service Account, or Group**, in **Enter the object name to select**, type **Administrators** and click **OK**.
1. In **Permission entry for Research**, under **Permissions**, activate the checkbox **Full Control** and click **OK**.
1. In **Advanced Security Settings for Research**, click the permission entry with the **Principal** **Everyone** and click **Remove**.

    > The permission entries should be equal to the table below.

    | Principal                                  | Type  | Access         |
    |--------------------------------------------|-------|----------------|
    | Users (VN1-SRV10\\Users)                   | Allow | Change         |
    | Administrators (VN1-SRV10\\Administrators) | Allow | Full Control   |

1. Click **OK**.
1. In **New Share Wizard**, on page **Permissions**, click **Next >**.
1. On page Confirm selections, click **Create**.
1. On page Results, click **Close**.

### Task 3: Verify access to the share

Perform this task on CL2.

1. Open **Terminal**.
1. Invoke a group policy update.

    ````powershell
    gpupdate.exe
    ````

1. Sign out.
1. Sign in as **ad\Claire**.
1. Using **File Explorer**, navigate to \\\\VN1-SRV10\\Research.

    > Claire should have access to the share.

1. Create a file in the share.
1. Sign out.
1. Sign in as **ad\Milton**.
1. Using File Explorer, navigate to \\\\VN1-SRV10\\Research.

    > Milton should not be able to access the share.

1. Sign out.

## Exercise 3: Manage access based on device properties

1. [Modify claim type](#task-1-modify-claim-type) department to apply to computers as well as users and set the property department of CL2 to Research.
1. [Modify the share permissions](#task-2-modify-the-share-permissions) of Research so that only users from the department Research on computers from the department Research have Modify permissions
1. [Verify access to the share from authorized computer](#task-3-verify-access-to-the-share-from-authorized-computer)

    > Can a user from the department Research using a computer from the same department access the share?

1. [Verify access to the share from unauthorized computer](#task-4-verify-access-to-the-share-from-unauthorized-computer)

    > Can a user from the department Research using a computer from a different department access the share?

### Task 1: Modify claim type

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **Dynamic Access Control**.
1. In Dynamic Access Control, double-click **Claim Types**.
1. In Claim Types, double-click **department**.
1. In department, under **Claims of this type can be issued to the following classes**, activate the checkbox **Computer** and click **OK**.
1. In **Active Directory Administrative Center**, click **Global Search**.
1. In Global Search, in **Search**, type **CL2** and click **Search**.
1. Double-click the computer **CL2**.
1. In CL2, click the page **Extensions**.
1. Under Extensions, click the tab **Attribute Editor**.
1. On tab Attribute Editor, click **department** and click **Edit**.
1. In String Attribute Editor, under **Value**, type **Research** and click **OK**.
1. In **CL2**, click **OK**.
1. Open **Terminal**
1. Restart **CL2**.

    ````powershell
    Restart-Computer -ComputerName CL2 -Protocol WSMan -Force
    ````

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Set the claim type **department** to apply to the classes **User** and **Computer**.

    ````powershell
    Get-ADClaimType -Identity department | 
    Set-ADClaimType -AppliesToClasses User, Computer
    ````

1. For the computer **CL2**, set the property **department** to **Research**.

    ````powershell
    Set-ADComputer -Identity CL2 -Add @{ department = 'Research' }
    ````

1. Restart **CL2**.

    ````powershell
    Restart-Computer -ComputerName CL2 -Protocol WSMan -Force
    ````

### Task 2: Modify the share permissions

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the left pane, click **File and Storage Services**.
1. In File and Storage Services > Servers, on the left, click **Shares**.
1. In Server Manager > File and Storage Services > Shares, in the context-menu of **Research**, click **Properties**.
1. In Research Properties, click the page **Permissions**.
1. Under **Permissions**, click **Customize permissions...**.
1. Click **Users (VN1-SRV10\\Users)** and click **Edit**.
1. In Permission Entry for Research, in the row of the existing condition, click the drop-down **RESEARCH**, and click **Research** again.

    Note: Due to a bug in the user interface, you have to select the value again when modifying the permissions.

1. Click **Add a condition** and ensure, that in the drop-down between the two condition rows, **And** is selected.
1. For the new row, in the drop-downs, from left to right, click these entries ([figure 2]):

    * Device
    * department
    * Equals
    * Value
    * Research

1. Click **OK**.
1. In **Advanced Security Settings for Research**, click **OK**.
1. In **Research Properties**, click **OK**.

### Task 3: Verify access to the share from authorized computer

Perform this task on CL2.

1. Sign in as **ad\Claire**.
1. Using **File Explorer**, navigate to \\\\VN1-SRV10\\Research.

    > Claire should have access to the share.

1. Create a file in the share.
1. Sign out.

### Task 4: Verify access to the share from unauthorized computer

Perform this task on CL1.

1. Restart the computer.
1. Sign in as **ad\Claire**.
1. Using **File Explorer**, navigate to \\\\VN1-SRV10\\Research.

    > Claire should not have access to the share.

1. Sign out.

## Exercise 4: Manage access based on file resource properties

1. [Create a new claim type for users](#task-1-create-a-new-claim-type-for-users) based on the property title and  for Harry Lawrence and Elise Hughes, set the property title to Data Protection Manager
1. [Enable resource property](#task-2-enable-resource-property) Confidentiality
1. [Modify resource property](#task-3-modify-resource-property) Confidentiality on file Payroll.xlsx in the Finance share and Upcoming Campaign.pptx in the Marketing Share to High

    Note: In real world, you would not set the classification manually, but rather use the classification management feature of File Server Resource Management.

1. [Modify permissions](#task-4-modify-permissions) for the share Finance to allow access only for users with the job title Data Protection Manager.
1. [Verify access to a confidential file](#task-5-verify-access-to-a-confidential-file)

    > Is Harry able to open the file Payroll.xlsx?

    > Is Pia (another user in the Managers group) able to open the file Payroll.xlsx?

### Task 1: Create a new claim type for users

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **Dynamic Access Control**.
1. In Dynamic Access Control, double-click **Claim Types**.
1. In Claim Types, in the context-menu of the empty area, click **New**, **Claim Type**.
1. In Create Claim Type, under **Source Attribute**, click **department** and click **OK**.
1. In **Active Directory Administrative Center**, click **ad (local)**.
1. In ad (local), double-click **Managers**.
1. In Managers, double-click **Harry Lawrence**.
1. In Harry Lawrence, under **Organization**, in **Job title**, type **Data Protection Manager** and click **OK**.
1. In **Active Directory Administrative Center**, click **ad (local)**.
1. In ad (local), double-click **Marketing**.
1. In Managers, double-click **Elise Hughes**.
1. In Elise Hughes, under **Organization**, in **Job title**, type **Data Protection Manager** and click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Create a new claim type from the source attribute **Department** with the suggested values Finance, IT, Marketing, and **Research**.

    ````powershell
    New-ADClaimType `
        -DisplayName 'title' `
        -SourceAttribute `
            'CN=Title, CN=Schema, CN=Configuration, DC=ad, DC=adatum, DC=com' `
        -ProtectedFromAccidentalDeletion $true
    ````

1. For user **Harry** and **Elise**, set the property title to **Data Protection Manager**.

    ````powershell
    'Harry', 'Elise' | ForEach-Object { 
        Set-ADUser -Identity $PSItem -Title 'Data Protection Manager'
    }
    ````

### Task 2: Enable resource property

#### Desktop Experience

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **Dynamic Access Control**.
1. In Dynamic Access Control, double-click **Resource Properties**.
1. In Resource Properties, in the context-menu of **Confidentiality**, click **Enable**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Enable the resource property **Confidentiality_MS**.

    ````powershell
    Set-ADResourceProperty -Identity 'Confidentiality_MS'-Enabled $true
    ````

### Task 3: Modify resource property

Perform this task on VN1-SRV10.

1. In **File Explorer**, navigate to **D:\\Shares\\Finance**.
1. In Finance, in the context-menu of **Payroll.xlsx**, click **Properties**.
1. In Payroll.xlsx Properties, click the tab **Classification**.
1. On tab Classification, click **Confidentiality**, and, under **Value**, click **High**. Click **OK**.

Repeat the task for the file **D:\\Shares\\Marketing\\Upcoming Campaign.pptx**.

### Task 4: Modify permissions

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the left pane, click **File and Storage Services**.
1. In File and Storage Services > Servers, on the left, click **Shares**.
1. In Server Manager > File and Storage Services > Shares, in the context-menu of **Finance**, click **Properties**.
1. In Research Properties, click the page **Permissions**.
1. Under **Permissions**, click **Customize permissions...**.
1. Click **Finance Modify (AD\\Finance Modify)** and click **Edit**.
1. In Permission Entry for Finance, click **Add a condition**.
1. For the new row, in the drop-downs, from left to right, click these entries:

    * Resource
    * Confidentiality
    * Greater than
    * Value
    * Moderate

1. Click **Add a condition** and ensure that, in the drop-down between the rows, **And** is selected.
1. For the new row, in the drop-downs, from left to right, click or type these entries:

    * User
    * title
    * Equals
    * Value
    * Data Protection Manager

1. Click **Add a condition** and, in the drop-down between the rows, select **Or**.
1. For the new row, in the drop-downs, from left to right, click these entries:

    * Resource
    * Confidentiality
    * Less than or equal to
    * Value
    * Moderate

1. Click **Add a condition** and, in the drop-down between the rows, select **Or**.
1. For the new row, in the drop-downs, from left to right, click these entries:

    * Resource
    * Confidentiality
    * Not exists

    Now, the permission entry should look like in [figure 3].

1. Click **OK**.

    You may repeat from step 7 for the entry **Finance Modify (AD\\Finance Modify)**, but it is not necessary for this lab.

1. In **Advanced Security Settings for Finance**, click **OK**.
1. In **Finance Properties**, click **OK**.

### Task 5: Verify access to a confidential file

Perform this task on CL2.

1. Sign in as **ad\Harry**.
1. Using **File Explorer**, navigate to **\\\\VN1-SRV10\\Finance**.
1. Open the file **Payroll**.

    > Harry should be able to open the file.

1. Open any other file in the share.

    > Harry should be able to open any file.

1. Sign out.
1. Sign in as **ad\Pia**.
1. Using **File Explorer**, navigate to **\\\\VN1-SRV10\\Finance**.

    > Pia does not see the file Payroll.

1. Open any other file in the share.

    > Pia should be able to open any other file.

1. Sign out.

## Exercise 5: Manage access using a central access policy

1. [Create a Central Access Policy](#task-1-create-a-central-access-policy) with a Central Access Rule granting modify permissions to users with the title Data Protection Manager on files with the property Confidentiality set to High or greater
1. [Apply Central Access Policy](#task-2-apply-central-access-policy) to all computer in the domain
1. [Use Central Access Policy](#task-3-use-central-access-policy) on the Marketing share
1. [Verify access to confidential file](#task-4-verify-access-to-confidential-file) Upcoming Campaign in the Marketing share with the users Elise and Teddy

    > Does Elise have access to the file?

    > Does Teddy have access to the file?

### Task 1: Create a Central Access Policy

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, cick **Dynamic Access Control**.
1. In Dynamic Access Control, double-click **Central Access Rules**.
1. In Central Access Rules, in the **Tasks** pane, click **New**, **Central Access Rule**.
1. In Create Central Access Rule, under **General**, in **Name**, type **Allow Modify on Confidentiality High for DPM**.
1. Under **Target Resources**, click **Edit...**
1. In Central Access Rule, click **Add a condition**.
1. For the new row, in the drop-downs, from left to right, click these entries:

    * Resource
    * Confidentiality
    * Greater than or equal to
    * Value
    * High

1. Under **Permissions**, click **Use following permissions as current permissions** and click **Edit...**
1. In Advanced Security Settings for Permissions, click **Add**.
1. In Permission Entry for Research, click **Select a principal**.
1. In **Select User, Computer, Service Account, or Group**, in **Enter the object name to select**, type **Authenticated Users** and click **OK**.
1. In **Advanced Security Settings for Permissions**, under **Basic permissions**, activate **Modify**.
1. Click **Add a condition**.
1. In the drop-downs, from left to right, click these entries:

    * User
    * title
    * Equals
    * Value
    * Data Protection Manager

1. Click **OK**.

    > In **Advanced Security Settings for Permissions**, the entries should be equal to the table below.

    | Type  | Principal                            | Type  | Access         | Condition                                     | Inherited from |
    |-------|--------------------------------------|-------|----------------|-----------------------------------------------|----------------|
    | Allow | OWNER RIGHTS                         | Allow | Full Control   |                                               | None           |
    | Allow | Administrators (CL1\\Administrators) | Allow | Full Control   |                                               | None           |
    | Allow | SYSTEM                               | Allow | Full Control   |                                               | None           |
    | Allow | Authenticated Users                  | Allow | Modify         | (User.title Equals "Data Protection Manager") | None           |

1. Click **OK**.
1. In **Create Central Access Rule: Allow Modify on Confidentiality High for DPM**, click **OK**.
1. In **Active Directory Administrative Center**, click **Dynamic Access Control**.
1. In Dynamic Access Control, double-click **Central Access Policies**.
1. In Central Access Policies, in the **Tasks** pane, click **New**, **Central Access Policy**.
1. In Create Central Access Policy, under **General**, in **Name**, type **GDPR Policy**.
1. Under Member Central Access Rules, cick **Add...**
1. In Add Central Access Rules, click **Allow Modify on Confidentiality High for DPM**, click **\>\>**, and click **OK**.
1. In **Create Central Access Policy: GDPR Policy**, click **OK**.

### Task 2: Apply Central Access Policy

Perform this task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Forest: ad.adatum.com**, **Domains**, **ad.adatum.com** and click **ad.adatum.com**
1. In the context-menu of **ad.adatum.com**, click **Create a GPO in this domain and Link it here...**.
1. In New GPO, in **Name**, type **Custom Computer Central Access Policies** and click **OK**.
1. In **Group Policy Management**, in the context-menu of **Custom Computer Central Access Policies**, click **Edit**.
1. In Group Policy Management Editor, expand **Computer Configuration**, **Policies**, **Windows Settings**, **Security Settings**, **File System**, and click **Central Access Policy**.
1. In the context-menu of **Central Access Policy**, click **Manage Central Access Policies...**
1. In Central Access Policies Configuration, click **GDPr Policy**, click **Add \>**, and click **OK**.
1. Open **Terminal**.
1. Invoke a group policy update on **VN1-SRV10**.

    ````powershell
    Invoke-GPUpdate -Computer VN1-SRV10
    ````

### Task 3: Use Central Access Policy

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the left pane, click **File and Storage Services**.
1. In File and Storage Services > Servers, on the left, click **Shares**.
1. In Server Manager > File and Storage Services > Shares, in the context-menu of **Marketing**, click **Properties**.
1. In Research Properties, click the page **Permissions**.
1. In Advanced Security Settings for Marketing, click the tab **Central Policy**.

    If you do not see the tab **Central Policy**, wait for a few minutes, or follow these steps:

    1. Open **Terminal**.
    1. Restart **VN1-SRV10**.

        ````powershell
        Restart-Computer VN1-SRV10 -Force
        ````

1. On tab Central Policy, beside **Central Policy**, click **Change**. In the drop-down, click **GDPR Policy**. Beside **Applies to**, click **Files only**. Click **OK**.
1. In **Marekting Properties**, click **OK**.

    A red banner **Expression evaluated to false** appears on top of the dialog.

1. Click **Cancel**.

### Task 4: Verify access to confidential file

Perform this task on CL2.

1. Sign in as **ad\Elise**.
1. Using **File Explorer**, navigate to **\\\\VN1-SRV10\\Marketing**.
1. Open the file **Upcoming Campaign**.

    > Elise should be able to open the file.

1. Open any other file in the share.

    > Elise should be able to open any file.

1. Sign out.
1. Sign in as **ad\Teddy**.
1. Using **File Explorer**, navigate to **\\\\VN1-SRV10\\Marketing**.

    > Teddy does not see the file Upcoming Campaign.

1. Open any other file in the share.

    > Teddy should be able to open any other file.

1. Sign out.

[figure 1]:/images/Permission-entry-for-research.png
[figure 2]:/images/Permission-entry-for-research-with-device-claim.png
[figure 3]:/images/Permission-entry-for-finance.png