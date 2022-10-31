# Lab: File server resource management

## Required VMs

VN1-SRV1
VN1-SRV6
CL1

## Setup

1. On CL1 sign in as **ad\Administrator**.
1. On CL2 sign in as **ad\Administrator**.

## Introduction

## Exercises

1. [Enable support for claims](#exercise-1-enable-support-for-claims)
1. [Manage access based on user properties](#exercise-2-manage-access-based-on-user-properties)
1. Manage access based on file resource properties
1. Manage access using a central access policy

## Exercise 1: Enable support for claims

1. [Enable claims support for domain controllers](#task-1-enable-claims-support-for-domain-controllers)
1. [Enable claims support for clients](#task-2-enable-claims-support-for-clients)
1. [Update group policies](#task-3-update-group-policies) on CL1 and CL2

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
        -Type String `
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

### Task 3: Update group policies

Perform this task on CL1 and CL2.

1. Open **Terminal**.
1. Invoke a group policy update.

    ````powershell
    gpupdate.exe
    ````

## Exercise 2: Manage access based on user properties

1. [Create a new claim type](#task-1-create-a-new-claim-type) department for users
1. [Create a new share](#task-2-create-a-new-share) Research on VN1-SRV6 and grant users of the same despartment modify permissions
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
1. On page **Share Location**, under **Servers** click **VN1-SRV6**.
1. Under **Share location**, click **D:** and click **Next >**.
1. On page **Share Name**, in **Share name**, type **Research** and click **Next >**.
1. On page **Other Settings**, activate the checkboxes **Enable access-based enumeration** and click **Next >**.
1. On page **Permissions**, click **Customize permissions...**.
1. In Advanced Security Settings for Research, on tab **Permissions**, click **Disable inheritance**.
1. In the dialog **What would you like to do with the current inherited permissioons?**, click **Convert inherited permissions into explicit permissions on this object.**
1. Under **Permission entries**, click the first entry with the **Principal** **Users (VN1-SRV6\\Users)** and click **Remove**. Repeat this step for all entries except for those with Principal **Administrators**, **SYSTEM**, and **CREATOR OWNER**.
1. Click **Add**.
1. In Permission Entry for Research, click **Select a principal**.
1. In Select User, Computer, Service Account, or Group, click **Locations...**
1. In Locations, click **VN1-SRV6.ad.adatum.com** and click **OK**.
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
1. In **Advanced Security Settings for Research**, click **Add**.
1. In Permission Entry for ..., click **Select a principal**.

    > The permission entries should be equal to the table below.

    | Type  | Principal                                 | Type  | Access         | Condition                           | Inherited from                    | Applies to                        |
    |-------|-------------------------------------------|-------|----------------|-------------------------------------|-----------------------------------|-----------------------------------|
    | Allow | Administrators (VN1-SRV6\\Administrators) | Allow | Full Control   |                                     | None                              | This folder, subfolders and files |
    | Allow | SYSTEM                                    | Allow | Full Control   |                                     | None                              | This folder, subfolders and files |
    | Allow | CREATOR OWNER                             | Allow | Full Control   |                                     | None                              | Subfolders and files only         |
    | Allow | Users (VN1-SRV6\\Users)                   | Allow | Modify         | (User.department Equals "Research") | None                              | This folder, subfolders and files |

1. In **Advanced Security Settings for Research**, click the tab **Share**.
1. In the tab Share, click **Add**.
1. In Permission Entry for Research, click **Select a principal**.
1. In Select User, Computer, Service Account, or Group, click **Locations...**
1. In Locations, click **VN1-SRV6.ad.adatum.com** and click **OK**.
1. In **Select User, Computer, Service Account, or Group**, in **Enter the object name to select**, type **Users** and click **OK**.
1. In **Permission entry for Research**, under **Permissions**, activate **Change** and click **OK**.
1. In **Advanced Security Settings for ...**, click **Add**.
1. In Permission entry for ..., click **Select a principal**.
1. In **Select User, Computer, Service Account, or Group**, in **Enter the object name to select**, type **Administrators** and click **OK**.
1. In **Permission entry for ...**, under **Permissions**, activate the checkbox **Full Control** and click **OK**.
1. In **Advanced Security Settings for ...**, click the permission entry with the **Principal** **Everyone** and click **Remove**.

    > The permission entries should be equal to the table below.

    | Principal                                 | Type  | Access         |
    |-------------------------------------------|-------|----------------|
    | Users (VN1-SRV6\\Users)                   | Allow | Change         |
    | Administrators (VN1-SRV6\\Administrators) | Allow | Full Control   |

1. Click **OK**.
1. On page **Permissions**, click **Next >**.
1. On page **Confirm selections**, click **Create**.
1. On page **Results**, click **Close**.

### Task 3: Verify access to the share

Perform this task on CL2.

1. Sign out.
1. Sign in as **ad\Claire**.
1. Using **File Explorer**, navigate to \\\\VN1-SRV6\\Research.

    > Claire should have access to the share.

1. Create a file in the share.
1. Sign out.
1. Sign in as **ad\Milton**.
1. Using File Explorer, navigate to \\\\VN1-SRV6\\Research.

    > Milton should not be able to access the share.

1. Sign out.

## Exercise 3: Manage access based on file resource properties

1. Add the claim type title for users
1. Enable the resource property Confidentiality
1. Set the property Confidentiality on file to High
1. Modify permissions for the share Finance to allow access only for users with the job title Data Protection Manager.
1. Verify access to the share Finance

## Exercise 4: Manage access using a central access policy

1. Enable the claim type physicalDeliveryOffice for users.
1. Create an access rule to deny users with the claim physicalDeliveryOffice any access to resources with high confidentiality.
1. Create a central access policy and add the access rule
1. Create a group policy object and apply it to the organizational unit file servers. Add the cental access policy to the group policy object.
1. Apply the cental access policy to the Marketing share
1. Verify access to the Marketing share

[figure 1]:/images/Permission-Entry-for-Research.png