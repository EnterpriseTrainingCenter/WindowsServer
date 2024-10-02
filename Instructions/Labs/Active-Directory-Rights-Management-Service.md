# Lab: Active Directory Rights Management Service

## Required VMs

* VN1-SRV1
* VN1-SRV3
* VN1-SRV4
* VN1-SRV6
* VN2-SRV1
* VN2-SRV2
* CL1

## Setup

1. On **CL1**, sign in as **ad\\Administrator**.
1. Run **Terminal** as Administrator.
1. Execute the following commands:

    ````powershell
    C:\LabResources\Solutions\Set-CertTemplatePermissions.ps1 `
        -Template WebServer `
        -ComputerName 'VN2-SRV1', 'VN2-SRV2' `
        -Verbose
    
    Get-ADUser -Filter * | ForEach-Object { 
        $PSItem | 
        Set-ADUser -EmailAddress "$($PSItem.SamAccountName)@adatum.com" 
    }

    Get-ADGroup -Filter * | ForEach-Object { 
        $PSItem | Set-ADGroup -Replace @{ mail="$($PSItem.Name)@adatum.com" } 
    }
    ````

1. On **VN1-SRV1**, sign in as **ad\\Administrator**.

## Introduction

To prevent leaks of confidential documents from your organization, you configure an Active Directory Rights Management Cluster and verify its operation.

## Exercises

1. [Create an Active Directory Rights Management Cluster](#exercise-1-create-an-active-directory-rights-management-cluster)
1. [Configure the Active Directory Rights Management Cluster](#exercise-2-configure-active-directory-rights-management)
1. [Validate Active Directory Rights Management](#exercise-3-validate-active-directory-rights-management)

## Exercise 1: Create an Active Directory Rights Management Cluster

1. [Create a service account](#task-1-create-a-service-account)
1. [Create DNS A records](#task-2-create-dns-a-records) for **rms.ad.adatum.com** pointing to **VN2-SRV1** and **VN2-SRV2** and for **rmsdb.ad.adatum.com** pointing to **rmsdb.ad.adatum.com**.
1. [Configure a group policy for Internet options to assign all https sites in the domain to the Intranet zone](#task-3-configure-a-group-policy-for-internet-options-to-assign-all-https-sites-in-the-domain-to-the-intranet-zone)
1. [Install Active Directory Rights Managment Server Role](#task-4-install-active-directory-rights-managment-server-role) on **VN2-SRV1** and **VN2-SRV1**
1. [Request a web server certificate](#task-5-request-a-web-server-certificate) for **rms.ad.adatum.com** on **VN2-SRV1**
1. [Create the RMS cluster](#task-6-create-the-rms-cluster) on **VN2-SRV1** using **rmsdb.ad.adatum.com** as database server and **https://rms.ad.adatum.com** as cluster URL
1. [Request a web server certificate](#task-7-request-a-web-server-certificate) for **rms.ad.adatum.com** on **VN2-SRV2**
1. [Expand the RMS cluster](#task-8-expand-the-rms-cluster) to **VN2-SRV2**.

### Task 1: Create a service account

#### Desktop Experience

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **ad (local)**
1. In the context-menu of **ad (local)**, click **New**, **Organizational Unit**.
1. In Create Organizational Unit, in **Name**, type **Service Accounts** and click **OK**.
1. In **Active Directory Administrative Center**, in the middle pane, double-click **Service Accounts**.
1. In the context-menu of an empty space in the middle pane, click **New**, **User**.
1. In Create User, in **Full name**, type **Active Directory Rights Management Service Account**. 
1. In **User UPN logon**, type **SvcRMS**. 
1. In **Password** and **Confirm password**, type a secure password.
1. Click **Other password options**.
1. Activate the checkbox **Password never expires**.
1. Click **OK**.

#### Windows Admin Center

Perform these steps on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click *Settings*.
1. In Settings, click **Extensions**.
1. In Extensions, on tab **Available extensions**, click **Active Directory** and click **Install**. Wait for Windows Admin Center page to refresh.
1. On the top-left, click **Windows Admin Center** to return to the connections page.
1. On the tab **Add one**, in **Server name**, enter **VN1-SRV1**.
1. After VN1-SRV1 was found, click **Add**.
1. Click **VN1-SRV1.ad.adatum.com**.
1. Connected to VN1-SRV1.ad.adatum.com, under Tools, click **Active Directory**.
1. In Active Directory Domain Service, click **Create**, **OU**.
1. In the panel Add Organizational Unit, under **Name**, type **Service Accounts**. In **Create in**, ensure **DC=ad, DC=adatum, DC=com** is selected and click **Create**.
1. Click **Create**, **User**.
1. In the panel Add User, under **Name**, type **Active Directory Rights Management Service Account**.
1. Under **Sam Account Name**, type **SvcRMS**.
1. Under **Password**, type a secure password.
1. Click **Change...**.
1. Click **Service Accounts** and click **Select**.
1. Click **Create**.
1. In **Search Active Directory**, type **Active Directory Rights** click **Search**.
1. Click the user **Active Directory Rights Management Service Account**.
1. Click **Properties**.
1. In User properties: SvcRMS, under **User UPN logon**, type **svcrms@ad.adatum.com**.
1. Activate the checkbox **Password never expires**.
1. Click **Save**.
1. Click **Close**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Create an organization unit for **Service Accounts**.

    ````powershell
    $organizationalUnit = New-ADOrganizationalUnit `
        -Path 'DC=ad, DC=adatum, DC=com' `
        -Name 'Service Accounts' `
        -PassThru
    ````

1. Create a service account for Active Directory Rights Management.

    ````powershell
    $user = New-ADUser `
        -Path $organizationalUnit.DistinguishedName `
        -Name 'Active Directory Rights Management Service Account' `
        -UserPrincipalName 'SvcRMS@ad.adatum.com' `
        -SamAccountName SvcRMS `
        -PasswordNeverExpires $true `
        -PassThru
    ````

1. Set the password for the service account.

    ````powershell
    $user | Set-ADAccountPassword -Reset
    ````

    Beside **Password** and **Repeat Password**, enter a secure password.

1. Set the service account to not require password change at logon and enable the account.

    ````powershell
    $user | Set-ADUser -ChangePasswordAtLogon $false
    $user | Enable-ADAccount
    ````

### Task 2: Create DNS A records

#### Desktop Experience

Perform this task on CL1.

1. Open **DNS**.
1. In the dialog **Connect to DNS Server**, click **The following computer**, type **VN1-SRV1**, and click **OK**.
1. In **DNS Manager**, click **VN1-SRV1**.
1. Expand **VN1-SRV1**, **Forward Lookup Zones**.
1. Click **ad.adatum.com**.
1. In the context-menu of **ad.adatum.com**, click **New Host (A or AAAA)...**
1. In New Host, under **Name**, type **rms**, under IP address, type **10.1.2.8** and click **Add Host**.
1. In the dialog **The host record rms.ad.adatum.com was successfully created.**, click **OK**.
1. In **New Host**, under **Name**, type **rms**, under IP address, type **10.1.2.16** and click **Add Host**.
1. In the dialog **The host record rms.ad.adatum.com was successfully created.**, click **OK**.
1. In **New Host**, under **Name**, type **rmsdb**, under IP address, type **10.1.1.24** and click **Add Host**.
1. In **New Host**, click **Done**.

#### Windows Admin Center

Perform these steps on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click *Settings*.
1. In Settings, click **Extensions**.
1. In Extensions, on tab **Available extensions**, click **DNS (Preview)** and click **Install**. Wait for Windows Admin Center page to refresh.
1. On the top-left, click **Windows Admin Center** to return to the connections page.
1. On the tab **Add one**, in **Server name**, enter **VN1-SRV1**.
1. After VN1-SRV1 was found, click **Add**.
1. Click **VN1-SRV1.ad.adatum.com**.
1. Connected to VN1-SRV1.ad.adatum.com, under Tools, click **DNS**.
1. If a message **The PowerShell tools (RSAT) are not installed** is displayed, click **Install** and wait for the installation to complete.
1. In DNS, click **ad.adatum.com**.
1. In the bottom panel, cick **Create a new DNS record**.
1. In the panel Create a new DNS record, under **DNS record type**, ensure **Host (A)** is selected.
1. Under **Record name (uses FQDN if blank)**, type **rms**.
1. Under **IP address**, type **10.1.2.8**.
1. Click **Create**.
1. Under **Record name (uses FQDN if blank)**, type **rms**.
1. Under **IP address**, type **10.1.2.16**.
1. Click **Create**.
1. Click **Cancel**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Create a new CIM session to VN1-SRV1.

    ````powershell
    $session = New-CimSession VN1-SRV1
    ````

1. On VN1-SRV1, add an A record with the name **rmsdb** pointing to **10.1.1.24**.

    ````powershell
    Add-DnsServerResourceRecordA `
        -Name rmsdb `
        -ZoneName ad.adatum.com `
        -IPv4Address 10.1.1.24 `
        -Session $session 
    ````

1. On VN1-SRV1, add A records with the name **rms** pointing to **10.1.2.8** and **10.1.2.16**.

    ````powershell
    '10.1.2.8', '10.1.2.16' | ForEach-Object { 
        Add-DnsServerResourceRecordA `
            -Name rms `
            -ZoneName ad.adatum.com `
            -IPv4Address $PSItem `
            -Session $session 
        }
    ````

1. Remove the session

    ````powershell
    Remove-CimSession $session
    ````

### Task 3: Configure a group policy for Internet options to assign all https sites in the domain to the Intranet zone

#### Desktop Experience

Perform this task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Forest: ad.adatum.com**, **Domains**, **ad.adatum.com**.
1. In the context-menu of **ad.adatum.com**, click **Create a GPO in this domain, and Link it here...**
1. In New GPO, under **Name**, type **Custom User Internet Settings**.
1. In the context-menu of **Custom User Internet Settings**, click **Edit**.
1. In Group Policy Management Editor, expand **User Configuration**, **Policies**, **Administrative Templates**, **Windows Components,** **Internet Explorer**, **Internet Control Panel**, and click **Security Page**.
1. In the right pane, double-click **Site to Zone Assignment List**.
1. In Site to Zone Assignment List, cick **Enabled** and, under **Options**, click **Show...**.
1. In Show Contents, in the column **Value name**, type **https://*.ad.adatum.com**, in the column **Value**, type **1**, and click **OK**.
1. In **Site to Zone Assignment List**, click **OK**.
1. Close **Group Policy Management Editor**.

#### PowerShell

Perform this task on CL1.

1. Run **Terminal** as Administrator.
1. Create a new group policy object.

    ````powershell
    $gpo = New-GPO -Name 'Custom User Internet Settings'
    ````

1. Configure **https://*.ad.adatum.com** to be in the **Intranet** zone.

    ````powershell
    $key = `
        'HKCU\Software\Policies\Microsoft\Windows\CurrentVersion\Internet Settings'
    $gpo | Set-GPRegistryValue `
        -Key $key `
        -ValueName 'ListBox_Support_ZoneMapKey' `
        -Type DWord `
        -Value 1
    $gpo | Set-GPRegistryValue `
        -Key "$key\ZoneMapKey" `
        -ValueName 'https://*.ad.adatum.com' `
        -Type String `
        -Value 1
    $gpo | Set-GPRegistryValue `
        -Key "$key\ZoneMap\Domains\adatum.com\*.ad" `
        -ValueName '' `
        -Type DWord `
        -Value 1
    ````

1. Link the group policy object to the domain.

    ````powershell
    $gpo | New-GPLink -Target 'dc=ad, dc=adatum, dc=com'
    ````

### Task 4: Install Active Directory Rights Managment Server Role

#### Desktop Experience

Perform this task on CL1.

1. Open Server Manager.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on the page **Before You Begin**, click **Next >**.
1. On the page **Installation Type**, ensure **Role-based or feature-based installation** is selected and click **Next >**.
1. On the page **Server Selection**, click **VN2-SRV1.ad.adatum.com** and click **Next >**.
1. On the page **Server Roles**, activate the checkbox next to **Active Directory Rights Management Services**
1. In the dialog **Add Roles and Features Wizard**, click **Add Features**.
1. On the page click **Next >**.
1. On the page **Features**, click **Next >**.
1. On the page **AD RMS**, click **Next >**.
1. On the page **Role Services**, click **Next >**.
1. On the page **Web Server Role (IIS)**, click **Next >**.
1. On the page **Role Services**, click **Next >**.
1. On the page **Confirmation**, verify your selection and click **Install**.
1. On the page **Results**, wait for the installation to succeed, then click **Close**.

Repeat the steps of this task to install the role on **VN2-SRV2**.

#### Windows Admin Center

Perform these steps on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, on the connections page, click **Add**.
1. In the panel **Add or create resources**, under **Server**, click **Add**.
1. On the tab **Add one**, in **Server name**, enter **VN2-SRV1**.
1. After VN2-SRV1 was found, click **Add**.
1. Click **VN2-SRV1**.
1. Connected to VN2-SRV1.ad.adatum.com, under **Tools**, click **Roles & features**.
1. In Roles and features, expand **Active Directory Rights Management Services**.
1. Activate the checkbox beside **Active Directory Rights Management Server**.
1. Expand **Web Server (IIS)**, **Management Tools**.
1. Activate the checkbox beside **IIS Management Console** and click **Install**.
1. In the pane Install Role and Features, activate the checkbox **Reboot the server automatically, if required** and click **Yes**.
1. After a few minutes, a notification **Install Roles and Features** appears. If you missed the notification, a small number appears beside the icon *Notifications* (in form of a bell) at the top-right of Windows Admin Center. Click the icon *Notification* and the notification **Install Roles and Features**.
1. In the pane **Notification details**, click **Close**.
1. In Roles and features, verify the **State** of **Active Directory Rights Management Server** is **Installed**.

Repeat the steps of this task to install the role on **VN2-SRV2**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install Active Directory Rights Management Server on VN2-SRV1 and VN2-SRV2.

    ````powershell
    Invoke-Command -ComputerName VN2-SRV1, VN2-SRV2 -ScriptBlock {
        Install-WindowsFeature `
            -Name ADRMS-Server, Web-Mgmt-Console `
            -IncludeManagementTools `
            -Restart 
    }
    ````

### Task 5: Request a web server certificate

#### Desktop Experience

Perform this task on VN2-SRV1.

1. Run **gpupdate.exe**.
1. Run **certlm.msc**.
1. In certlm - [Certificates - Local Computer], expand **Personal** and click **Certificates**.
1. In the context-menu of Certificates, click **All Tasks**, **Request New Certificate...**.
1. In Certificate Enrollment, on page **Before You begin**, click **Next**.
1. On page **Select Certificate Enrollment Policy**, ensure **Active Directory Enrollment Policy** is selected and click **Next**.
1. On page **Request Certificates**, activate the checkbox **Web Server** and click the link **More information is required to enroll for this certificate. Click here to configure settings.**.
1. In Certificate Properties, under **Subject name**, in the drop-down **Type**, click **Common name**.
1. Under **Subject name**, under **Value**, type **rms.ad.adatum.com** and click the top **Add >**.
1. Under **Alternative name**, in the drop-down **Type**, click **DNS**.
1. Under **Alternative name**, under **Value**, type **rms.ad.adatum.com** and click the bottom **Add >**.
1. Repeat the previous step for the values **rms**, **VN2-SRV1.ad.adatum.com**, and **VN2-SRV1**.
1. Click **OK**.
1. On page **Request Certificates**, click **Enroll**.
1. On page **Certificate Installation Results**, click **Finish**.

#### PowerShell

Perform this task on VN2-SRV1.

1. Run **Windows PowerShell** as Administrator.
1. Update the group policies.

    ````powershell
    gpupdate.exe
    ````

1. Request a certificate for **rms.ad.adatum.com**.

    ````powershell
    $dnsName = @(
        'rms.ad.adatum.com'
        'rms'
        'VN2-SRV1.ad.adatum.com'
        'VN2-SRV1'
    )
    Get-Certificate `
            -Template WebServer `
            -SubjectName "cn=$($dnsName[0])" `
            -DnsName $dnsName `
            -CertStoreLocation 'Cert:\LocalMachine\My'
    ````

### Task 6: Create the RMS cluster

#### Desktop Experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, at the top-right, click *Notifications* (the flag icon with the yellow exclamation mark).
1. Under **Configuration required for Active Directory Rights Management Services at VN2-SRV1**, click **Perform additional configuration.**
1. In AD RMS Configuration: VN2-SRV1.ad.adatum.com, on page **AD RMS**, click **Specify...**
1. In the dialog Type User Account Name and Password, enter the credentials for **AD\Administrator**.
1. On page **AD RMS**, click **Next >**.
1. On page **AD RMS Cluster**, click **Next >**.
1. On page **Configuration Database**, ensure **Specify a database server and a database instance** is selected. Under **Server**, type **rmsdb.ad.adatum.com** and click **List**.
1. Click the drop-down under **Database Instance**, click **DefaultInstance** and click **Next >**.
1. On page **Service Account**, click **Select...**
1. In **User name**, type **SvcRMS@ad.adatum.com**, type the **Password** of that account and click **OK**.
1. On page **Service Account**, click **Next >**.
1. On page **Cryptographic Mode**, ensure **Cryptographic Mode 2 (RSA 2048-bit keys/SHA-256 hashes)** is selected and click **Next >**.
1. On page **Cluster Key Storage**, ensure **Use AD RMS centrally managed key storage** is selected and click **Next >**.
1. On page **Cluster Key Password**, type a long and secure password in **Password** and **Confirm Password**. Take a note of the password.
1. On page **Cluster Web Site**, click **Next >**.
1. On page **Cluster Address**, ensure **Use an SSL-encrypted connection (https://)** is selected. Under Fully-Qualified Domain Name, type **rms.ad.adatum.com**.
1. On page **Server certificate**, click the certificate **Issued To** **rms.ad.adatum.com** and click **Next >**.
1. On page **Licensor Certificate**, in **Name**, type **Adatum-RMS-Server-Licensor-Certificate** and click **Next >**.
1. On page **SCP Registration**, ensure **Register the SCP now** is selected and click **Next >**.
1. On page **Confirmation**, click **Install**.
1. On page **Results**, click **Close**.

#### PowerShell

Perform this task on VN2-SRV1.

1. Run **Windows PowerShell (Administrator)**.
1. Import the module **ADRMS** and create a new PowerShell drive with the provider **ADRMSInstall**, the name **RC** and the root **RootCluster**.

    ````powershell
    Import-Module ADRMS
    New-PSDrive -PSProvider ADRMSInstall -Name RC -Root RootCluster
    ````

1. Configure the AD RMS server to use **rmsdb.ad.adatum.com** as database server.

    ````powershell
    Set-ItemProperty `
        –Path RC:\ClusterDatabase `
        -Name ServerName `
        -Value rmsdb.ad.adatum.com
    ````

1. Set the credentials for the service account.

    ````powershell
    $adrmssvc = Get-Credential
    Set-ItemProperty –Path RC:\ -Name ServiceAccount -Value $adrmssvc
    ````

    In **Windows PowerShell credential request**, enter the credentials for **ad\svcrms**.

1. Configure the cryptographic mode.

    ````powershell
    Set-ItemProperty `
            -Path RC:\CryptoSupport `
            -Name SupportCryptoMode2 `
            -Value $true
    ````

1. Configure the cluster key storage.

    ````powershell
    Set-ItemProperty `
        -Path RC:\ClusterKey `
        -Name UseCentrallyManaged `
        -Value $true
    ````

1. Assign the cluster key password to your AD RMS installation.

    ````powershell
    $password = Read-Host -AsSecureString -Prompt 'Cluster Key Password'
    Set-ItemProperty -Path RC:\ClusterKey -Name CentrallyManagedPassword -Value $password
    ````

    When prompted for the **Cluser Key Password**, enter a long and secure password. Take a note of the password.

1. Select **Default Web Site** as the cluster web site.

    ````powershell
    Set-ItemProperty `
        -Path RC:\ClusterWebSite `
        -Name WebSiteName `
        -Value 'Default Web Site'
    ````

1. Set the AD RMS cluster address to **https://rms.ad.adatum.com**.

    ````powershell
    Set-ItemProperty `
        -Path RC:\ `
        -Name ClusterURL `
        -Value 'https://rms.ad.adatum.com'
    ````

1. Set the cluster certificate to the longest lasting certificate with a subject equal to **CN=rms.ad.adatum.com**

    ````powershell
    $now = Get-Date
    $certificate = `
        Get-ChildItem 'Cert:\LocalMachine\My' | 
        Where-Object { 
            $PSItem.Subject -eq 'CN=rms.ad.adatum.com' `
            -and $now -gt $PSItem.NotBefore `
            -and $now -lt $PSItem.NotAfter `
        } | 
        Sort-Object NotAfter -Descending |
        Select-Object -First 1

    Set-ItemProperty `
        -Path RC:\SSLCertificateSupport `
        -Name SSLCertificateOption `
        -Value Existing

    Set-ItemProperty `
        -Path RC:\SSLCertificateSupport `
        -Name Thumbprint `
        -Value $certificate.Thumbprint
    ````

1. Set the Server Licensor Certificate name for your AD RMS installation.

    ````powershell
    Set-ItemProperty `
        -Path RC:\ `
        -Name SLCName `
        -Value 'Adatum-RMS-Server-Licensor-Certificate'
    ````

1. Register the SCP connection point

    ````powershell
    Set-ItemProperty -Path RC:\ -Name RegisterSCP -Value $true
    ````

1. Install the AD RMS root cluster using the settings provided.

    ````powershell
    Install-ADRMS -Path RC:\ -Force
    ````

### Task 7: Request a web server certificate

#### Desktop Experience

Perform this task on VN2-SRV2.

1. Run **gpupdate.exe**.
1. Run **certlm.msc**.
1. In certlm - [Certificates - Local Computer], expand **Personal** and click **Certificates**.
1. In the context-menu of Certificates, click **All Tasks**, **Request New Certificate...**.
1. In Certificate Enrollment, on page **Before You begin**, click **Next**.
1. On page **Select Certificate Enrollment Policy**, ensure **Active Directory Enrollment Policy** is selected and click **Next**.
1. On page **Request Certificates**, activate the checkbox **Web Server** and click the link **More information is required to enroll for this certificate. Click here to configure settings.**.
1. In Certificate Properties, under **Subject name**, in the drop-down **Type**, click **Common name**.
1. Under **Subject name**, under **Value**, type **rms.ad.adatum.com** and click the top **Add >**.
1. Under **Alternative name**, in the drop-down **Type**, click **DNS**.
1. Under **Alternative name**, under **Value**, type **rms.ad.adatum.com** and click the bottom **Add >**.
1. Repeat the previous step for the values **rms**, **VN2-SRV2.ad.adatum.com**, and **VN2-SRV2**.
1. Click **OK**.
1. On page **Request Certificates**, click **Enroll**.
1. On page **Certificate Installation Results**, click **Finish**.

#### PowerShell

Perform this task on VN2-SRV2.

1. Run **Windows PowerShell** as Administrator.
1. Update the group policies.

    ````powershell
    gpupdate.exe
    ````

1. Request a certificate for **rms.ad.adatum.com**.

    ````powershell
    $dnsName = @(
        'rms.ad.adatum.com'
        'rms'
        'VN2-SRV2.ad.adatum.com'
        'VN2-SRV2'
    )
    Get-Certificate `
            -Template WebServer `
            -SubjectName "cn=$($dnsName[0])" `
            -DnsName $dnsName `
            -CertStoreLocation 'Cert:\LocalMachine\My'
    ````

### Task 8: Expand the RMS cluster

#### Desktop Experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, at the top-right, click *Notifications* (the flag icon with the yellow exclamation mark).
1. Under **Configuration required for Active Directory Rights Management Services at VN2-SRV2**, click **Perform additional configuration.**
1. In AD RMS Configuration: VN2-SRV2.ad.adatum.com, on page **AD RMS**, click **Specify...**
1. In the dialog Type User Account Name and Password, enter the credentials for **AD\Administrator**.
1. On page **AD RMS**, click **Next >**.
1. On page **AD RMS Cluster**, ensure **Join an existing AD RMS Cluster** is selected, and click **Next >**.
1. On page **Configuration Database**, under **Server**, type **rmsdb.ad.adatum.com** and click **List**.
1. Click the drop-down under **Database Instance**, click **DefaultInstance**, in the drop-down under **Configuration Database Name**, click **DRMS_Config_rms_ad_adatum_com_80**, and click **Next >**.
1. On page **Database Information**, in **Password** and **Confirm Password**, type the cluster key password, you noted before and click **Next >**.
1. On page **Service Account**, click **Select...**
1. In **User name**, type **SvcRMS@ad.adatum.com**, type the **Password** of that account and click **OK**.
1. On page **Service Account**, click **Next >**.
1. On page **Cluster Web Site**, click **Next >**.
1. On page **Confirmation**, click **Install**.
1. On page **Results**, click **Close**.

#### PowerShell

1. Run **Windows PowerShell (Administrator)**.
1. Import the module **ADRM** and create a new PowerShell drive with the provider **ADRMSInstall**, the name **RC** and the root **RootCluster**.

    ````powershell
    Import-Module ADRMS
    New-PSDrive -PSProvider ADRMSInstall -Name RC -Root JoinCluster
    ````

1. Set the credentials for the service account.

    ````powershell
    $adrmssvc = Get-Credential
    Set-ItemProperty –Path RC:\ -Name ServiceAccount -Value $adrmssvc
    ````

    In **Windows PowerShell credential request**, enter the credentials for **ad\svcrms**.

1. Configure the AD RMS server to use **rmsdb.ad.adatum.com** as database server and **DRMS_Config_rms_ad_adatum_com_443** as database.

    ````powershell
    Set-ItemProperty –Path RC:\ClusterDatabase -Name ServerName -Value rmsdb.ad.adatum.com
    Set-ItemProperty `
        –Path RC:\ClusterDatabase `
        -Name DatabaseName `
        -Value DRMS_Config_rms_ad_adatum_com_443
    ````

1. Securely store the cluster key password string in a variable.

    ````powershell
    $password = Read-Host -AsSecureString -Prompt "Cluster Key Password"
    ````

    When prompted for the **Cluser Key Password**, enter the password you took note of before.

1. Assign the cluster key password to your AD RMS installation.

    ````powershell
    Set-ItemProperty `
        -Path RC:\ClusterKey `
        -Name CentrallyManagedPassword `
        -Value $password
    ````

1. Install the AD RMS root cluster using the settings provided.

    ````powershell
    Install-ADRMS -Path RC:\ -Force
    ````

1. Get the longest lasting certificate with a subject eqal to **CN=rms.ad.adatum.com**

    ````powershell
    $now = Get-Date
    $certificate = `
        Get-ChildItem 'Cert:\LocalMachine\My' | 
        Where-Object { 
            $PSItem.Subject -eq 'CN=rms.ad.adatum.com' `
            -and $now -gt $PSItem.NotBefore `
            -and $now -lt $PSItem.NotAfter `
        } | 
        Sort-Object NotAfter -Descending |
        Select-Object -First 1
    ````

1. Bind the certificate to the **Default Web Site**.

    ````powershell
    $binding = Get-WebBinding -Name 'Default Web Site' -Protocol https -Port 443
    $binding.AddSslCertificate($certificate.GetCertHashString(), 'my')
    ````

## Exercise 2: Configure Active Directory Rights Management

1. [Configure the extranet URLs](#task-1-configure-the-extranet-urls) to use **rms.adatum.com** as FQDN.
1. [Backup Server Licensor Certificate](#task-2-backup-the-server-licensor-certificate)
1. [Create a rights policy template](#task-3-create-a-rights-policy-template) for the locale **en-us** with the display name **Reasearch**, granting **research@adatum.com** the rights **Edit**, **Reply**, and **ReplyAll**. The use license should expire after 7 days.

### Task 1: Configure the extranet URLs

Note: You should configure your extranet URL at the time of installation, even if it will not be initially deployed. If external access is enabled after documents are AD RMS protected you must remove the protection, remove the DRM folder on the client computers, configure extranet access, and then protect the documents again.

#### Desktop Experience

Perform this task on VN2-SRV1.

1. Sign out.
1. Sign in as **ad\Administrator**.
1. Open **Active Directory Rights Management Services**.
1. In Active Directory Rights Management Services, in the context-menu of **vn2-srv1 (Local)**, click **Properties**.
1. In vn2-srv1 (Local) Properties, click the tab **Cluster URLs**.
1. On the tab Cluster URLs, activate the checkbox **Extranet URLs**.
1. Beside **Licensing**, in the drop-down, click **https://** and in the text field, type **rms.adatum.com**.
1. Beside **Certification**, in the drop-down, click **https://** and in the text field, type **rms.adatum.com**.
1. Click **OK**.

#### PowerShell

Perform this task on VN2-SRV1.

1. Sign out.

    ````powershell
    logoff
    ````

1. Sign in as **ad\Administrator**.
1. Run **Windows PowerShell** as Administrator.
1. Import the **ADRMSAdmin** module and create a PowerShell Drive using the **AdRMSAdmin** provider and **https://vn2-srv1** as root.

    ````powershell
    Import-Module AdRmsAdmin
    New-PSDrive -PSProvider AdRmsAdmin -Name RMS -Root https://vn2-srv1
    ````

1. Set the extranet certification URL and the extranet licensing URL to the FQDN **rms.adatum.com**.

    ````powershell
    Set-ItemProperty `
        -Path RMS:\ `
        -Name ExtranetCertificationUrl `
        -Value https://rms.adatum.com/_wmcs/certification/certification.asmx
    Set-ItemProperty `
        -Path RMS:\ `
        -Name ExtranetLicensingUrl `
        -Value https://rms.adatum.com/_wmcs/licensing
    ````

### Task 2: Backup the Server Licensor Certificate

#### Desktop Experience

Perform this task on VN2-SRV1.

1. Open **Active Directory Rights Management Services**.
1. In Active Directory Rights Management Services, expand **vn2-srv1 (Local)**, **Trust Policies** and click **Trusted Publishing Domain**.
1. In Trusted Publishing Domains, in the context menu of **Adatum-RMS-Server-Licensor-Certificate**, click **Export Trusted Publishing Domain...**.
1. In Export Trusted Publishing Domain, click **Save As...**
1. In Export Trusted Publishing Domain File As..., save the file as **\\\\VN1-SRV6\\IT\\Adatum-RMS-Server-Licensor-Certificate**.
1. In **Export Trusted Publishing Domain**, in **Password** and **Confirm Password**, enter a secure password.
1. Click **Finish**.

#### PowerShell

Perform this task on VN2-SRV1.

1. Run **Windows PowerShell** as Administrator.
1. Import the **ADRMSAdmin** module and create a PowerShell Drive using the **ADRMSAdmin** provider and **https://vn2-srv1** as root.

    ````powershell
    Import-Module AdRmsAdmin
    New-PSDrive -PSProvider AdRmsAdmin -Name RMS -Root https://vn2-srv1
    ````

1. Export the Trusted Publishing Domain.

    ````powershell
    $tpd = Get-ChildItem RMS:\TrustPolicy\TrustedPublishingDomain\ |
        Where-Object { $PSItem.DisplayName -eq 'Adatum-RMS-Server-Licensor-Certificate' }
    Export-RmsTPD `
        -Path "RMS:\TrustPolicy\TrustedPublishingDomain\$($tpd.Id)" `
        -SavedFile '\\vn1-srv6\IT\Adatum-RMS-Server-Licensor-Certificate.xml'
    ````

    At the prompt **Password** and **Please type in a confirmed password**, enter a secure password.

### Task 3: Create a rights policy template

#### Desktop Experience

Perform this task on VN2-SRV1.

1. Open **Active Directory Rights Management Services**.
1. In Active Directory Rights Management Services, expand **vn2-srv1 (Local)** and click **Rights Policy Templates**.
1. In Distributed Rights Policy Templates, click **Create distributed rights policy template**.
1. In Create Distributed Rights Policy Template, on page **Add Template Identification Information**, click **Add**.
1. In Add New Template Identification Information, in **Language**, ensure **English (United States)** is selected. In **Name**, type **Research**. In Description type **Grants access to members of Research** and click **Add**.
1. On page **Add Template Identification Information**, click **Next >**.
1. On page **Add User Rights**, click **Add**.
1. In Add User or Group, click **Browse...**.
1. In Select User or Group, under **Enter the object name to select**, type **Research** and click **OK**.
1. In **Add User or Group**, click **OK**
1. On page **Add User Rights**, activate the checkboxes **Edit**, **Reply** and **Reply All**.
1. Click **Next >**.
1. On page **Specify Expiration Policy**, activate the checkbox under **Use license expiration**, type **7**, and click **Next >**.
1. On page **Specify Extended Policy**, click **Next >**.
1. On page **Specify Revocation Policy**, click **Finish**.

#### PowerShell

Perform this task on VN2-SRV1.

1. Run **Windows PowerShell** as Administrator.
1. Import the **ADRMSAdmin** module and create a PowerShell Drive using the **AdRMSAdmin** provider and **https://vn2-srv1** as root.

    ````powershell
    Import-Module AdRmsAdmin
    New-PSDrive -PSProvider AdRmsAdmin -Name RMS -Root https://vn2-srv1
    ````

1. Create a rights policy template for the locale **en-us** with the display name **Reasearch**, granting **research@adatum.com** the rights **Edit**, **Reply**, and **ReplyAll**. The use license should expire after 7 days.

    ````powershell
    New-Item `
        -Path RMS:\RightsPolicyTemplate\ `
        -LocaleName 'en-us' `
        -DisplayName 'Research' `
        -Description 'Grants access to members of Research' `
        -UserGroup 'research@adatum.com' `
        -Right ('Edit', 'Reply', 'ReplyAll') `
        -UseLicenseExpiredInDays 7
    ````

## Exercise 3: Validate Active Directory Rights Management

1. [Protect a document](#task-1-protect-a-document) as **Ilga**
1. [Verify the access by an authorized user](#task-2-verify-the-access-by-an-authorized-user) **Max**

    > Can Max open the document?

    > Can Max copy content from the document to the clipboard?

    > Can Max print the document?

1. [Verify unauthorized users cannot access the document](#task-3-verify-an-unauthorized-user-cannot-access-the-document) as **Lara** and **Administrator**

    > Can the Lara or Administrator open the document?

    > Can the Lara copy the file?

1. [Enable super users](#task-4-enable-super-users) and configure **IT** as super users
1. [Verify the access by super users](#task-5-verify-the-access-by-super-users), use **Lara**

    > Can Lara remove the restrictions?

1. [Disable super users](#task-6-disable-super-users)

### Task 1: Protect a document

Perform this task on CL2.

1. Sign in as **ad\Ilga**.
1. Open **Word**.
1. In Word, click **Blank document**.
1. In the document, type **=lorem()** and press ENTER.
1. Click **File**, **Info**, **Protect Document**, **Restrict Access**, **Research**.
1. Click **Save**, **Browse**.
1. Save the document as **C:\Users\Public\Documents\Research results**.
1. Sign out.

### Task 2: Verify the access by an authorized user

Perform this task on CL2.

1. Sign in as **ad\Max**.
1. In **File Explorer**, navigate to **C:\Users\Public\Documents**.
1. Open **Research results**.

    > The document should be displayed.

    > The clipboard commands should be disabled.

    > The **File**, **Print** command shuld be disabled.

1. Make some changes to the document and save it.

    > You should be able to change the document.

1. Sign out.

### Task 3: Verify an unauthorized user cannot access the document

Perform this task on CL2.

1. Sign in as **ad\Lara**.
1. In **File Explorer**, navigate to **C:\Users\Public\Documents**.
1. Open **Research results**.

    > You should not be able to open the document.

1. Copy **Research results** to **Documents**.

    > You should still be able to copy the file.

1. Sign out.
1. Sign in as **ad\Administrator**.
1. In **File Explorer**, navigate to **C:\Users\Public\Documents**.
1. Open **Research results**.

    > You should not be able to open the document.

1. Sign out.

### Task 4: Enable super users

#### Desktop Experience

Perform this task on VN2-SRV1.

1. Open **Active Directory Rights Management Services**.
1. In Active Directory Rights Management Services, expand **vn2-srv1 (Local)**, **Security Policies**, and click **Super Users**.
1. In the context-menu of **Super Users**, click **Enable Super Users**.
1. In Super Users, click **Change super user group**.
1. In Super Users, click **Browse...**.
1. In Select Group, under **Enter the object name to select**, type **IT** and click **OK**.
1. In **Multiple Names Found**, click **IT** and click **OK**.
1. In **Super Users**, click **OK**.

#### PowerShell

Perform this task on VN2-SRV1.

1. Run **Windows PowerShell** as Administrator.
1. Import the **ADRMSAdmin** module and create a PowerShell Drive using the **AdRMSAdmin** provider and **https://vn2-srv1** as root.

    ````powershell
    Import-Module AdRmsAdmin
    New-PSDrive -PSProvider AdRmsAdmin -Name RMS -Root https://vn2-srv1
    ````

1. Enable super users.

    ````powershell
    $path = 'RMS:\SecurityPolicy\SuperUser'
    Set-ItemProperty -Path $path -Name 'IsEnabled' -Value $true
    ````

1. Configure **IT@adatum.com** as super users group.

    ````powershell
    Set-ItemProperty -Path $path -Name SuperUserGroup -Value 'IT@adatum.com'
    ````

### Task 5: Verify the access by super users

Perform this task on CL2.

1. Sign in as **ad\Lara**.

    Note: Lara is a member of the IT group.

1. In **File Explorer**, navigate to **C:\Users\Public\Documents**.
1. Open **Research results**.

    > You should not be able to open the document.

1. Click **File**, **Info**, **Protect Document**, **Restrict Access**, **Unrestricted Access**

    > This would remove the restrictions from the document. In this task, we will cancel the operation.

1. In the message box **Are you sure you want to remove permission?**, click **No**.

1. Sign out.

If time permits, verify that **ad\Administrator** still does not have access to the document.

### Task 6: Disable super users

#### Desktop Experience

Perform this task on VN2-SRV1.

1. Open **Active Directory Rights Management Services**.
1. In Active Directory Rights Management Services, expand **vn2-srv1 (Local)**, **Security Policies**, and click **Super Users**.
1. In the context-menu of **Super Users**, click **Disable Super Users**.

#### PowerShell

Perform this task on VN2-SRV1.

1. Run **Windows PowerShell** as Administrator.
1. Import the **ADRMSAdmin** module and create a PowerShell Drive using the **AdRMSAdmin** provider and **https://vn2-srv1** as root.

    ````powershell
    Import-Module AdRmsAdmin
    New-PSDrive -PSProvider AdRmsAdmin -Name RMS -Root https://vn2-srv1
    ````

1. Disable super users.

    ````powershell
    Set-ItemProperty `
        -Path 'RMS:\SecurityPolicy\SuperUser' `
        -Name 'IsEnabled' `
        -Value $false
    ````

