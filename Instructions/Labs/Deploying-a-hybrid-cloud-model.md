# Lab: Deploying a hybrid cloud model

## Required VMs

* VN1-SRV5
* VN2-SRV1
* VN1-SRV7
* VN1-SRV7
* PM-SRV1
* CL1
* CL2

## Setup

* On **CL1**, logon as **ad\Administrator**.
* On **VN2-SRV1**, logon as **ad\Administrator**.

## Introduction

Adatum wants to move various services into the cloud. As a first step, Adatum wants to synchronize users and groups into Azure Active Directory. To simplify sign in and device management, Adatum wants the client computers to use hybrid Azure AD join.

## Exercises

1. [Create and configure an Azure Active Directory tenant](#exercise-1-create-and-configure-an-azure-active-directory-tenant)
1. [Synchronize Active Directory with Azure Active Directory](#exercise-2-synchronize-active-directory-users-and-groups-with-azure-active-directory)
<!-- 1. Manage Active Directory in a hybrid cloud -->
1. [Configure hybrid Azure AD domain join](#exercise-3-configure-hybrid-azure-ad-domain-join)

## Exercise 1: Create and configure an Azure Active Directory tenant

1. [Create an Azure Active Directory tenant](#task-1-create-an-azure-active-directory-tenant)
1. [Create a global admin](#task-2-create-a-global-admin)

### Task 1: Create an Azure Active Directory tenant

Perform this task on CL1 or the host.

1. Using **Microsoft Edge**, navigate to <https://portal.azure.com> and sign in.
1. In Microsoft Azure, at the top-left, click **Create a resource**.
1. In Create a resource, click **Identity** and under **Azure Active Directory**, click **Create**.
1. In Create a tenant, on tab Basics, beside **Select a tenant type**, ensure **Azure Active Directory** is selected and click **Next: Configuration >**.
1. On tab Configuration, in **Organization name**, type **Adatum Corporation**. In **Initial domain name**, type **Adatum** followed by some random number. In **Country/Region**, select the country of your current location, e.g., **Austria**. Take a note of the full domain name, which will be your typed domain name, followed by onmicrosoft.com.

    Important: Make sure, a green check mark appears rights to **Initial domain name**. If the message **Already in use by another directory** appears under the field, append some other number to the domain name.

1. Click **Review + Create** or **Next: Review and Create >**.
1. On tab Review + Create, ensure **Validation passed** appears at the top. Click **Create**.
1. If a pane **Help us prove you're not a robot** appears, solve the Captcha and click **Submit**.

### Task 2: Create a global admin

Perform this task on CL1 or the host.

1. Using **Microsoft Edge**, navigate to <https://portal.azure.com> and sign in.
1. In Microsoft Azure, in the left pane, click **All services**.
1. In All services, click  **Identity**.
1. Under **Identity management**, click **Azure Active Directory**.
1. In Azure Active Directory, at the top of the right pane, click **Manage tenants**.
1. In Manage tenants, activate the checkbox beside **Adatum Corporation** and, at the top, click **Switch**.

    Note: If Adatum Corporation does not appear, wait a few minutes and click **Refresh**.

1. In the Adatum Corporation, in the left pane, click **Users**.

    If the user interface appears in a different language than English, perform these steps to switch to English:

    1. At the top right, click the icon *Settings*.

        Note: The following steps refer to the English version, you have to find the options in the current language by yourself. Ask your instructor, if you need help.

    1. In Portal settings, click **Language + Region**.
    1. In Language + region, in **Language**, click **English**. In *Regional format*, click a format of your preference and click **Apply**.
    1. In Change language, click **OK**.
    1. At the top-left, click the hamburger menu and click **All Services**

        Continue with step 3 of the main task.

1. In users, click **New user**, **Create new user**.
1. In New user, beside **Select template**, ensure **Create user** is selected. Under **Identity**, beside **User name**, type your firstname followed by a dot and your last name, e.g., Gregory.Salgado. Beside **Name**, type your first and last name. In First name and last name, do the same.

    Take a note of the full user name, including the domain name. It will look something like Gregory.Salgado@adatum382602.onmicrosoft.com.

1. Under **Group and roles**, beside **Roles**, click **User**.
1. In the pane Directory roles, active **Global administrator** and click **Select**.
1. Under Password, activate **Show Password** and take a note of the password.
1. Click **Create**.
1. In **Users**, click **Refresh**.

    You should see the new user.

## Exercise 2: Synchronize Active Directory users and groups with Azure Active Directory

1. [Add the Azure Active Directory domain as UPN to users](#task-1-add-the-azure-active-directory-domain-as-upn-to-users) in organization units IT, Research, and Sales
1. [Enable Active Directory Recycle Bin](#task-2-enable-active-directory-recycle-bin)
1. [Download Azure AD Connect](#task-3-download-azure-ad-connect)
1. [Install and configure Azure AD Connect](#task-4-install-and-configure-azure-ad-connect) to synchronize the organizational units IT, Research, and Sales with the sign-in option password hash sync
1. [Verify the synchronization](#task-5-verify-the-synchronizaton)

### Task 1: Add the Azure Active Directory domain as UPN to users

Perform this task on CL1.

1. Open **Active Directory Domains and Trusts**.
1. In Active Directory Domains and Trusts, in the context-menu of **Active Directory Domains and Trusts**, click **Properties**.
1. In Active Directory Domains and Trusts, on tab UPN Suffixes, under **Alternative UPN suffixes**, type the domain name of your Azure Active Directory instance, click **Add** and click **OK**.
1. Open **Terminal**.
1. In Terminal, change the UPN name of users in IT, Research, and Sales to the Azure Active Directory domain suffix.

    ````powershell
    $suffix = 'adatum.onmicrosoft.com' # Replace with your domain name
    @('IT', 'Research', 'Sales') | ForEach-Object { 
        Get-ADUser `
            -SearchBase "ou=$PSItem, dc=ad, dc=adatum, dc=com" `
            -Filter * | 
        ForEach-Object { 
            $PSItem | Set-ADUser `
                -UserPrincipalName (
                    $PSItem.UserPrincipalName -replace `
                        '^(.*)@(.*)$', "`$1@$suffix"
                )
        }
    }
    ````

### Task 2: Enable Active Directory Recycle Bin

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **ad (local)**.
1. In the context-menu of **ad (local)**, click **Enable Recycle Bin ...**.

    If the command is greyed out, the Recycle Bin has already been enabled. Skip to the next task.

1. In Enable Recycle bin Confirmation, click **OK**.
1. In Please refresh AD Administrative Center now, click **OK**.
1. In **Active Directory Administrative Center**, click *Refresh*.

### Task 3: Download Azure AD Connect

Perform this task on VN2-SRV1.

1. In **Server Manager**, click **Local Server**.
1. Under Local Server, beside **IE Enhanced Security Configuration**, click **On**.
1. In Internet Explorer Enhanced Security Configuration, under **Administrators**, click **Off** and click **OK**.
1. Using **Microsoft Edge**, navigate to <https://aad.portal.azure.com>.
1. In Microsoft Azure, sign in with the account, you created earlier in this lab. You will have to change the password.
1. In Azure Active Directory admin center, in the left pane, click **Azure Active Directory**.
1. Under Adatum Corporation, in the left pane, under **Manage**, click **Azure AD Connect**.
1. In Azure AD Connect, under Azure AD connect sync, click **Download Azure AD Connect**.
1. On page Download Microsoft Azure Active Directory Connect, click **Download**.

### Task 4: Install and configure Azure AD Connect

Perform this task on VN2-SRV1.

1. In **Downloads**, open **AzureADConnect.msi**.
1. In Microsoft Azure Active Directory Connect, on page Welcome to Azure AD Connect, activate **I agree to the license terms and privacy notice** and click **Continue**.
1. On page Express Settings, click **Customize**.
1. On page Install required components, click **Install**.

    The installation will take a minute or two.

1. On page User Sign-in, ensure **Password Hash Synchronization** is selected and click **Next**.
1. On page Connect to Azure AD, enter the credentials of the global administrator.
1. On page Connect your directories, under **DIRECTORY TYPE**, ensure **Active Directory** is selected. Under **FOREST**, ensure **ad.adatum.com** is selected and click **Add Directory**.
1. In AD forest account, under **Select account option**, ensure **Create new AD account** is selected. Under **ENTERPRISE ADMIN USERNAME**, type the credentials of **ad\Administrator** and click **OK**.
1. In **Microsoft Azure Active Directory Connect**, on page **Connect your directories**, click **Next**.
1. On Azure AD sign-in configuration, under **USER PRINCIPAL NAME**, ensure **userPrincipalName** is selected. Activate **Continue without matching all UPN suffixes to verified domains** and click **Next**.
1. On page Domain and OU filtering, click **Sync selected domains and OUs**. Deactivate **ad.adatum.com**. Expand **ad.adatum.com**. Activate **IT**, **Research**, and **Sales**. Click **Next**.
1. On page Uniquely identifying your users, click **Next**.
1. On page Filter users and devices, ensure **Synchronize all users and devices** is selected and click **Next**.
1. On page Optional features, click **Next**.
1. On page Ready to configure, click **Install**.

    The configuration will take a minute or two.

1. When you see **Configuration complete**, click **Exit**.

### Task 5: Verify the synchronizaton

Perform this task on VN2-SRV1.

1. Open **Event Viewer**.
1. In Event Viewer, expand **Windows Logs** and click **Application**. Review the events with the source Directory Synchronization.
1. Using **Microsoft Edge**, navigate to <https://aad.portal.azure.com>.
1. In Microsoft Azure, sign in with the global administrator account.
1. In Azure Active Directory admin center, click **Users**.

    You should see users from your Active Directory.

## Exercise 3: Configure hybrid Azure AD domain join

1. [Move client computers into a synchronized organizational unit](#task-1-move-client-computers-into-a-synchronized-organizational-unit)
1. [Configure Azure AD Connect for Hybrid Azure AD join](#task-2-configure-azure-ad-connect-for-hybrid-azure-ad-join)
1. [Verify Hybrid Azure AD join](#task-3-verify-hybrid-azure-ad-join)

### Task 1: Move client computers into a synchronized organizational unit

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **Global Search**.
1. Under Global Search, in **Search**, type **CL** and click **Search**.
1. Click **CL1**, hold down CTRL and click **CL2**.
1. In the context-menu of **CL1**, click **Move...**
1. In Move, click **IT** and click **OK**.

### Task 2: Configure Azure AD Connect for Hybrid Azure AD join

Perform this task on VN2-SRV1.

1. On the desktop, open **Azure AD Connect**.
1. In Microsoft Azure Active Directory Connect, on page Welcome to Azure AD Connect, click **Configure**.
1. On page Additional tasks, click **Configure device options** and click **Next**.
1. On page Overview, click **Next**.
1. On page Connect to Azure AD, enter the credentials of the global administrator.
1. On page Device options, ensure **Configure Hybrid Azure AD join** is selected, and click **Next**.
1. On page Device operating systems, activate **Windows 10 or later domain-joined devices**, and click **Next**.
1. On page SCP configuration, activate **ad.adatum.com**. Under **Authentication service**, click **Azure Active Directory** and click **Add**.
1. In Enterprise Admin Credentials, enter the credentials of **ad\Administrator**.
1. In **Microsoft Azure Active Directory Connect**, on page **SCP configuration**, under **Enterprise Admin**, ensure **ad\Administrator** is displayed and click **Next**.
1. On page Configure, click **Configure**.
1. When you see **Configuration complete**, click **Exit**.
1. Open **Windows PowerShell**.
1. Start a sync cycle.

    ````powershell
    Start-ADSyncSyncCycle -PolicyType Delta
    ````

1. Using **Microsoft Edge**, navigate to <https://aad.portal.azure.com>.
1. In Microsoft Azure, sign in with the account, you created earlier in this lab. You will have to change the password.
1. In Azure Active Directory admin center, in the left pane, click **Azure Active Directory**.
1. Under Adatum Corporation, in the left pane, under **Manage**, click **Devices**.
1. In Devices, click **All devices**.

    You should see **CL2**. If you do not see the computer, wait for 2 minutes and click **Refresh**.

### Task 3: Verify Hybrid Azure AD join

Perform this task on CL2.

1. Click *Power*, **Restart**.
1. Sign in with **ad\abbi**.
1. Open **Terminal** or **Windows PowerShell**.
1. Check the status of the hybrid Azure AD join.

    ````powershell
    dsregcmd.exe /status
    ````

    Under **Device State**, **AzureADJoined** should be **Yes**.

1. Sign out.
1. Sign in with **ad\abbi**.
1. Open **Settings**.
1. In Settings, click **Accounts**.
1. In Accounts, click **Email & accounts**

    Under Accounts used by other apps, the Azure AD account of Abbi should be displayed.

1. Expand Abbi's Azure AD account.

    Note: You cannot remove this account.

1. Click **Manage**.
1. In Microsoft Edge, in **Sync your profile**, click **Sync**.

    The page **My account** opens withot further sign in.

1. Navigate to <https://myapps.comicrosoft.com>

    You should see various apps without further sign in.
