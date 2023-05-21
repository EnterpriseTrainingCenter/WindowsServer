# Lab: Implementing Active Directory Federation Services

## Required VMs

* VN1-SRV2
* VN1-SRV4
* VN1-SRV5
* VN1-SRV8
* VN1-SRV9
* VN1-SRV10
* PM-SRV2
* PM-SRV3
* PM-SRV4
* CL1

## Setup

1. On **VN1-SRV2**, sign in as **ad\Administrator**.
1. In SConfig, enter **15**.
1. Execute these commands:

    ````powershell
    Add-KdsRootKey -EffectiveTime (Get-Date).AddHours(-10)
    C:\LabResources\Solutions\New-CertTemplateWebServerExportable.ps1
    C:\LabResources\Solutions\Set-CertTemplatePermissions.ps1 `
        -Template WebServerExportable `
        -ComputerName CL1
    ````

1. On CL1, sign in as **ad\Administrator**.
1. Open **Terminal** as Administrator.
1. In Terminal, execute this command:

    ````powershell
    C:\LabResources\Solutions\New-Shares.ps1
    ````

    Note: You can safely ignore the following warnings:

    ````text
    WARNING: Certificate cannot be requested!
    Please run Install-AdminCenter.ps1 on VN1-SRV4.
    Windows Admin Center was not installed.
    ````

    ````text
    WARNING: Junction cannot be created.
    Please run New-Volumes.ps1 on VN1-SRV10.
    ````

1. On CL3, sign in as **.\Administrator**.
1. On VN1-SRV8, sign in as **ad\Administrator**.
1. On PM-SRV2, sign in as **ad\Administrator**.
1. On PM-SRV3, sign in as **ad\Administrator**.

## Introduction

Adatum introduces a new web application. The new web application will support modern authentication methods. You will implement the infrastructure to support the new web application. Moreover, Adatum wants to make Windows Admin Center accessible from the Internet, while maintaining a secure authentication.

## Exercises

1. [Deploy a web application](#exercise-1-deploy-a-web-application)
1. [Deploy Active Directory Federation Services (AD FS)](#exercise-2-deploy-active-directory-federation-services-ad-fs)
1. [Deploy Web Application Proxy](#exercise-3-deploy-web-application-proxy)
1. [Publish the web application on the Internet using Web Application Proxy (WAP)](#exercise-4-publish-the-web-application-on-the-internet-using-web-application-proxy-wap)
1. [Publish Windows Admin Center on the Internet using WAP](#exercise-5-publish-windows-admin-center-on-the-internet-using-wap)

## Exercise 1: Deploy a web application

1. [Install Internet Information Services](#task-1-install-internet-information-services) with support for Basic and Windows Authentication and ASP.NET 4.8 on PM-SRV2
1. [Install IIS Management Console](#task-2-install-iis-management-console) on CL1
1. [Configure remote administration for IIS Manager](#task-3-configure-remote-administration-for-iis-manager) on PM-SRV2
1. [Deploy the web application](#task-4-deploy-the-web-application) by copying the files from C:\LabResources\Authpage

### Task 1: Install Internet Information Services´

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, on the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before you begin, click **Next >**.
1. On page Select installation type, ensure **Role-based or feature-base installation** is selected and click **Next >**.
1. On page Select destination server, click **PM-SRV2.ad.adatum.com** and click **Next >**.
1. On page Server Roles, activate **Web Server (IIS)** and click **Next >**.
1. On page Features, click **Next >**.
1. On page Web Server Role (IIS), click **Next >**.
1. On page Role Services, under **Web Server**, **Security**, activate **Windows Authentication**.
1. Expand **Application Development**, and activate **ASP.NET 4.8**.
1. In Add features that are required for ASP.NET 4.8, click **Add Features**.
1. In **Add Roles and Features Wizard**, on page **Role Services**, under **Management Tools**, activate **Management Service**. Click **Next >**.
1. On page Confirmation, click **Install**.
1. On page Installation progress, click **Close**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. On **PM-SRV2.ad.adatum.com**, install the features **Web Server** including **Windows Authentication**, **ASP.NET 4.8**, and the **Management Service**.

    ````powershell
    Install-WindowsFeature `
        -ComputerName PM-SRV2.ad.adatum.com `
        -Name Web-Server, Web-Windows-Auth, Web-Asp-Net45, Web-Mgmt-Service `
        -IncludeManagementTools `
        -Restart
    ````

### Task 2: Install IIS Management Console

#### Desktop experience

Perform this task on CL1.

1. Open **Settings**.
1. In Settings, click **Apps**.
1. In Apps, click **Optional features**.
1. In Optional features, at the bottom, click **More Windows features**.
1. In Windows Features, expand **Internet Information Services**, **Web Management Tools**, activate **IIS Management Console** and click **OK**.
1. On page Windows completed the requested changes, click **Close**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal** as Administrator.
1. Install the IIS Management Console.

    ````powershell
    Enable-WindowsOptionalFeature `
        -Online -FeatureName IIS-ManagementConsole -All

### Task 3: Configure remote administration for IIS Manager

#### Desktop experience

Perform this task on CL1.

1. Using Microsoft Edge, navigate to <https://www.microsoft.com/en-us/download/details.aspx?id=41177>.
1. On page Download IIS Manager for Remote Administration 1.2, click **Download**.
1. In Choose the download you want, activate **inetmgr_amd64_en-US.msi** and click **Next**.
1. In Downloads, click **Open file**.
1. In Internet Information Services (IIS) 7+ Manager Setup, on page Welcome to the Internet Information Services (IIS) 7+ Manager Setup Wizard, click **Next**.
1. On page End-User License Agreement, activate **I accept the terms in the License Agreement** and click **Next**.
1. On page Destination Folder, click **Next**.
1. On page Ready to install Internet Information Service (IIS) 7+ Manager, click **Install**.
1. On page Completed the Internet Information Service (IIS) 7+ Manager Setup Wizard, click **Finish**.
1. Open **Terminal**.
1. In Terminal, create a remote PowerShell session to **PM1-SRV2**.

    ````powershell
    Enter-PSSession PM-SRV2
    ````

1. Enable remote connections.

    ````powershell
    Set-ItemProperty `
        -Path HKLM:\SOFTWARE\Microsoft\WebManagement\Server\ `
        -Name EnableRemoteManagement `
        -Value 1
    ````

1. Configure the service **WMSvc** to start automatically

    ````powershell
    Set-Service -Name WMSvc -StartupType Automatic
    ````

1. Start the service **WMSvc**.

    ````powershell
    Start-Service -Name WMSvc
    ````

1. Exit the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

#### PowerShell

Perform this task on CL1.

1. Open **Terminal** as Administrator.
1. In Terminal, download **IIS Manager for Remote Administration 1.2**.

    ````powershell
    Start-BitsTransfer `
        -Destination Downloads `
        -Source `
            https://download.microsoft.com/download/2/4/3/24374C5F-95A3-41D5-B1DF-34D98FF610A3/inetmgr_amd64_en-US.msi
    ````

1. Install IIS Manager for Remote Administration 1.2.

    ````powershell
    msiexec.exe /i c:\Users\Administrator.AD\Downloads\inetmgr_amd64_en-US.msi /passive
    ````

1. Create a remote PowerShell session to **PM1-SRV2**.

    ````powershell
    Enter-PSSession PM-SRV2
    ````

1. Enable remote connections.

    ````powershell
    Set-ItemProperty `
        -Path HKLM:\SOFTWARE\Microsoft\WebManagement\Server\ `
        -Name EnableRemoteManagement `
        -Value 1
    ````

1. Configure the service **WMSvc** to start automatically

    ````powershell
    Set-Service -Name WMSvc -StartupType Automatic
    ````

1. Start the service **WMSvc**.

    ````powershell
    Start-Service -Name WMSvc
    ````

1. Exit the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

### Task 4: Deploy the web application

Perform this task on CL1.

1. Open **Terminal**.
1. In Terminal, create a remote PowerShell session to **PM1-SRV2**.

    ````powershell
    Enter-PSSession PM-SRV2
    ````

1. Remove all files from **C:\inetpub\wwwroot**.

    ````powershell
    Remove-Item -Path C:\inetpub\wwwroot\*.*
    ````

1. Copy all files from **C:\LabResources\Authpage** to **C:\inetpub\wwwroot**.

    ````powershell
    Copy-Item `
        -Path C:\LabResources\Authpage\*.* `
        -Destination C:\inetpub\wwwroot\
    ````

1. Exit the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

1. Use Microsoft Edge to navigate to <http://pm-srv2.ad.adatum.com>

    > You should see a page like in [figure 1].

### Task 5: Configure authentication

#### Desktop experience

Perform this task on CL1.

1. Open **Internet Information Services (IIS) Manager**.
1. In Internet Information Services (IIS) Manager, in the context-menu of **Start Page**, click **Connect to a Server...**
1. In Connect to Server wizard, on page Specify Server Connection Details, under **Server name**, type **PM-SRV2** and click **Next**.
1. On page Provide Credentials, type the credentials of **ad\administrator** and click **Next**.
1. In Server Certificate Alert, click **Connect**.
1. In **Connect to Server Wizard**, on page Created a new connection successfully, click **Finish**.
1. In Internet Information Services (IIS) Manager, expand **PM-SRV2 (ad\Administrator)**, **Sites**, and click **Default Web Site**.
1. Under **Default Web Site Home**, under **IIS**, double-click **Authentication**.
1. Under Authentication, in the context-menu of **Anonymous Authentication**, click **Disable**.
1. In the context-menu of **Windows Authentication**, click **Enable**.
1. Open **Microsoft Edge**.
1. In Microsoft Edge, navigate to <http://pm-srv2.ad.adatum.com>.
1. In Windows Security, enter the credentials of any user, e.g. **ad\Pam**.

    On the WHO Page, the Authentication Method should show Negotiate (KERBEROS).

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. In Terminal, create a remote PowerShell session to **PM1-SRV2**.

    ````powershell
    Enter-PSSession PM-SRV2
    ````

1. For Default Web Site, disable anonymous authentication.

    ````powershell
    $pSPath = 'IIS:\'
    $location = 'Default Web Site'
    $filter = '/system.webServer/security/authentication/'
    $name = 'Enabled'
    Set-WebConfigurationProperty `
        -PSPath $pSPath `
        -Location $location `
        -Filter "$filter/anonymousAuthentication" `
        -Name $name `
        -Value $false `
    ````

1. For Default Web Site, enable Windows authentication

    ````powershell
    Set-WebConfigurationProperty `
        -PSPath $pSPath `
        -Location $location `
        -Filter "$filter/windowsAuthentication" `
        -Name $name `
        -Value $true `
    ````

1. Exit from the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

1. Open Microsoft Edge and browse to **http://pm-srv2.ad.adatum.com**.

    ````powershell
    & 'C:\Program Files (x86)\Microsoft\Edge\Application\msedge.exe' http://pm-srv2.ad.adatum.com
    ````

1. In Windows Security, enter the credentials of any user, e.g. **ad\Pam**.

    On the WHO Page, the Authentication Method should show Negotiate (KERBEROS).

## Exercise 2: Deploy Active Directory Federation Services (AD FS)

1. [Request and export a wildcard certificate](#task-1-request-and-export-a-wildcard-certificate) for *.adatum.com
1. [Install Active Directory Federation services](#task-2-install-active-directory-federation-services) on VN1-SRV8 and VN1-SRV9
1. [Create the first federation server in a federation server farm](#task-3-create-the-first-federation-server-in-a-federation-server-farm) using VN1-SRV8 with a federation service name sts.adatum.com and a group managed service account
1. [Add a federation server to a federation server farm](#task-4-add-a-federation-server-to-a-federation-server-farm) using VN1-SRV9
1. [Add DNS A records](#task-5-add-dns-a-records) for sts.adatum.com on VN1-SRV5 pointing to VN1-SRV8 and VN1-SRV9
1. [Verify the functionality of AD FS](#task-6-verify-the-functionality-of-ad-fs)

### Task 1: Request and export a wildcard certificate

#### Desktop experience

Perform this task on CL1.

1. Open **mmc.exe**.
1. In Console1 - [Console Root], on the menu, click **File**, **Add/Remove Snap-in...**
1. In Add or Remove Snap-ins, under **Available snap-ins**, click **Certificate** and click **Add >**.
1. In Certificates snap-in, click **Computer account** and click **Next >**.
1. On page Select Computer, ensure **Local computer** is selected and click **Finish**.
1. In **Add or Remove Snap-ins**, click **OK**.
1. In **Console1 - [Console Root]**, expand **Certificates (Local Computer)**, **Personal**, and click **Certificates**. If Certificates is not available, click **Personal** instead.
1. In the context-menu of **Certificates** (or **Personal**), click **All Tasks**, **Request New Certificate...**
1. In Certificate Enrollment, on page Before You Begin, click **Next**.
1. On page Select Certificate Enrollment Policy, ensure **Active Directory Enrollment policy** is selected and click **Next**.
1. On page Request Certificates, activate the checkbox beside **Web Server exportable** and click the link **More information is required to enroll this certificate. Click here to configure settings.**
1. In Certificate Properties, under **Subject name**, under **Type**, click **Common name**. Under **Value**, type **\*.adatum.com** and click **Add >**. Under **Alternative name**, under **Type**, click **DNS**. Under **Value**, type **\*.adatum.com** and click **Add >**. Click **OK**.
1. In **Certificate Enrollment**, on page **Request Certificates**, click **Enroll**.

    If the certificate request fails with an access denied error message, on VN1-SRV2, restart the Certification Authority service.

    1. Open **Server Manager**.
    1. In Server manager, in the left pane, click **AD CS**.
    1. In Server manager > AD CS, under **SERVERS**, click **VN1-SRV2**.
    1. Under **SERVICES** (you might have to scroll down), in the context-menu of **CertSvc**, click **Restart Services**.

    Then, try to request the certificate again.

1. On page **Certificate Installation Results**, click **Finish**.
1. In **Console1 - [Console Root]**, expand **Certificates (Local Computer)**, **Personal**, and click **Certificates**.
1. In the context-menu of the certificate **\*.adatum.com**, click **All Tasks**, **Export...**
1. In the Certificate Export Wizard, on page Welcome to the Certificate Export Wizard, click **Next**.
1. On page Export Private Key, click **Yes, export the private key** and click **Next**.
1. On page Export File Format, ensure **Personal Information Exchange -PKCS #12 (.PFX)** is selected, the checkboxex **Include all certificates in the certification path if possible** and **Enable certificate privacy** are activated, and click **Next**.
1. On page Security, activate the checkbox **Password** and, below, type a secure password. Repeat the password under **Confirm password**, take a note and click **Next**.
1. On page File to Export, type or browse to **\\\\vn1-srv10\\IT\\Wildcard adatum.com.pfx** and click **Next**.
1. On page Completing the Certificate Export Wizard, click **Finish**.
1. In The export was successful, click **OK**.
1. In **Console1 - [Console Root]**, in the context-menu of the certificate **\*.adatum.com**, click **Delete**.
1. In the message box Certificates, click **Yes**.

You can close the console without saving it.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. In Terminal, request a wilcard certificate for **\*.adatum.com** using the template **WebServerExportable**.

    ````powershell
    $dnsName = '*.adatum.com'
    $certificate = (
        Get-Certificate `
            -Template 'WebServerExportable' `
            -SubjectName "CN=$dnsName" `
            -DnsName $dnsName `
            -CertStoreLocation 'Cert:\LocalMachine\My'
    ).Certificate
    ````

    If you receive an error message

    ````text
    The revocation function was unable to check revocation because the revocation server was offline. 0x80092013 (-2146885613 CRYPT_E_REVOCATION_OFFLINE)
    ````

    restart the **Active Directory Certificate Services** on **VN1-SRV2** and run the command again.

    ````powershell
    Invoke-Command -ComputerName VN1-SRV2 { Restart-Service -Name CertSvc }
    ````

1. Read a password for the PFX file into a variable.

    ````powershell
    $password = Read-Host -AsSecureString -Prompt 'Password for PFX file'
    ````

1. At the prompt Password for PFX file, enter a secure password and take a note.
1. Export the certificate including the private key to **\\\\vn1-srv10\\IT\\Wildcard adatum.com.pfx**.

    ````powershell
    $certificate | Export-PfxCertificate `
        -FilePath '\\vn1-srv10\IT\Wildcard adatum.com.pfx' -Password $password
    ````

1. Delete the certificate from CL1.

    ````powershell
    Remove-Item $certificate.PSPath
    ````

### Task 2: Install Active Directory Federation services

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, on the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before you begin, click **Next >**.
1. On page Select installation type, ensure **Role-based or feature-base installation** is selected and click **Next >**.
1. On page Select destination server, click **VN1-SRV8.ad.adatum.com** and click **Next >**.
1. On page Server Roles, activate **Active Directory Federation Services** and click **Next >**.
1. On page Features, click **Next >**.
1. On page AD FS, click **Next >**.
1. On page Confirmation, activate **Restart the destination server automatically, if required** and click **Install**.
1. On page Installation progress, click **Close**.

Repeat from step 2, but on step 5, click **VN1-SRV9.ad.adatum.com**.
#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. In Terminal, install **Active Directory Federation Services** including the management tools on **VN1-SRV8** and **VN1-SRV9**.

    ````powershell
    Invoke-Command -ComputerName VN1-SRV8, VN1-SRV9 {
        Install-WindowsFeature
            -Name ADFS-Federation `
            -IncludeManagementTools `
            -Restart 
    }
    ````

### Task 3: Create the first federation server in a federation server farm

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click *Notifications*, and under **Configuration required for Active Directory Federation Services at VN1-SRV8**, click **Configure the federation service on this server.**
1. In Active Directory Federation Services Configuration Wizard, on page Welcome, ensure **Create the first federation server in a federation server farm** is selected and click **Next >**.
1. On page Connect to AD DS, click **Change...**.
1. In Credentials for deployment operation, enter the credentials for **AD\Administrator**.
1. In **Active Directory Federation Services Configuration Wizard**, on page **Connect to AD DS**, click **Next >**.
1. On page Specify Service Properties, beside **SSL Certificate**, click **Import...**
1. In Open, navigate to **\\\\VN1-SRV10\\IT**, click **Wildcard adatum.com** and click **Open**.
1. In Enter certificate password, enter the password, you noted before.
1. In **Active Directory Federation Services Configuration Wizard**, on page **Specify Service Properties**, in **Federation Service name**, type **sts.adatum.com**. In **Federation Service Display Name**, type **Adatum Corporation** and click **Next >**.
1. On page Specify Service Account, ensure **Create a Group Managed Service Account** is selected and in **Account Name**, type **AdatumADFS**. Click **Next >**.
1. On page Specify Configuration Database, ensure **Create a database on this server using Windows Internal Database** is selected and click **Next >**.
1. On page Review Options, click **Next >**.
1. On page Pre-requisite Checks, click **Configure**.
1. On page Results, click **Close**.
1. In **Server Manager**, click **AD FS**.
1. Under AD FS, in the context-menu of **VN1-SRV8**, click **Restart Server**.
1. In message box Are you sure you want to restart these servers, click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. In Terminal, create a remote PowerShell session to **VN1-SRV8**.

    ````powershell
    $computerName = 'VN1-SRV8'
    $pSSession = New-PSSession -ComputerName $computerName
    ````

1. Copy the file **\\\\VN1-SRV10\\IT\\Wildcard adatum.com.pfx** to **c:\\** in the remote session.

    ````powershell
    Copy-Item `
        -Path '\\VN1-SRV10\IT\Wildcard adatum.com.pfx' `
        -ToSession $pSSession `
        -Destination c:\
    ````

1. Enter the remote session.

    ````powershell
    Enter-PSSession -Session $pSSession
    ````

1. Read a password for the PFX file into a variable.

    ````powershell
    $password = Read-Host -AsSecureString -Prompt 'Password for PFX file'
    ````

1. At the prompt Password for PFX file, enter the password of the PFX file.
1. Import the PFX file into the local machine's certificate store and save it in a variable.

    ````powershell
    $certificate = Import-PfxCertificate `
        -FilePath 'C:\Wildcard adatum.com.pfx'`
        -Password $password `
        -CertStoreLocation Cert:\LocalMachine\My\
    ````

1. Get the credential for performing configuration of ADFS.

    ````powershell
    $credential = Get-Credential
    ````

1. In Windows PowerShell credential request, enter the credentials of **ad\Administrator**

1. Install the AD FS farm with the federation service name **sts.adatum.com**, the federation display name **Adatum Corporation**, and the managed group service account **AD\AdatumADFS$**.

    ````powershell
    Install-AdfsFarm `
        -Credential $credential `
        -CertificateThumbprint $certificate.Thumbprint `
        -FederationServiceName 'sts.adatum.com' `
        -FederationServiceDisplayName 'Adatum Corporation' `
        -GroupServiceAccountIdentifier 'AD\AdatumADFS$'
    ````

1. Exit from the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

1. Remove the remote PowerShell session.

    ````powershell
    Remove-PSSession -Session $pSSession
    ````

1. Restart **VN1-SRV8**.

    ````powershell
    Restart-Computer `
        -ComputerName $computerName -WsmanAuthentication Default -Force
    ````

### Task 4: Add a federation server to a federation server farm

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click *Notifications*, and under **Configuration required for Active Directory Federation Services at VN1-SRV9**, click **Configure the federation service on this server.**
1. In Active Directory Federation Services Configuration Wizard, on page Welcome, click **Add a federation server to a federation server farm** and click **Next >**.
1. On page Connect to AD DS, click **Change...**.
1. In Credentials for deployment operation, enter the credentials for **AD\Administrator**.
1. In **Active Directory Federation Services Configuration Wizard**, on page **Connect to AD DS**, click **Next >**.
1. On page Specify Farm, ensure **Specify the primary federation server in an existing farm using Windows Internal Database** is selected. Beside **Primary Federation Server**, type **VN1-SRV8.ad.adatum.com** and click **Next >**.

    **Important**: Use the FQDN of the primary federation server.

1. On page Specify Certificate, beside **SSL Certificate**, click **Import...**
1. In Open, navigate to **\\\\VN1-SRV10\\IT**, click **Wildcard adatum.com** and click **Open**.
1. In Enter certificate password, enter the password, you noted before.
1. In **Active Directory Federation Services Configuration Wizard**, on page **Specify Certificate**, click **Next >**.
1. On page Specify Service Account, click **Select...**
1. In Select User or Service Account, under **Enter the object name to select**, type **AdatumADFS** and click **OK**.
1. In **Active Directory Federation Services Configuration Wizard**, on page **Specify Service Account**, click **Next >**.
1. On page Review Options, click **Next >**.
1. On page Pre-requisite Checks, click **Configure**.
1. On page Results, click **Close**.
1. In **Server Manager**, click **AD FS**.
1. Under AD FS, in the context-menu of **VN1-SRV9**, click **Restart Server**.
1. In message box Are you sure you want to restart these servers, click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. In Terminal, create a remote PowerShell session to **VN1-SRV9**.

    ````powershell
    $computerName = 'VN1-SRV9'
    $pSSession = New-PSSession -ComputerName $computerName
    ````

1. Copy the file **\\\\VN1-SRV10\\IT\\Wildcard adatum.com.pfx** to **c:\\** in the remote session.

    ````powershell
    Copy-Item `
        -Path '\\VN1-SRV10\IT\Wildcard adatum.com.pfx' `
        -ToSession $pSSession `
        -Destination c:\
    ````

1. Enter the remote session.

    ````powershell
    Enter-PSSession -Session $pSSession
    ````

1. Read a password for the PFX file into a variable.

    ````powershell
    $password = Read-Host -AsSecureString -Prompt 'Password for PFX file'
    ````

1. At the prompt Password for PFX file, enter the password of the PFX file.
1. Import the PFX file into the local machine's certificate store and save it in a variable.

    ````powershell
    $certificate = Import-PfxCertificate `
        -FilePath 'C:\Wildcard adatum.com.pfx'`
        -Password $password `
        -CertStoreLocation Cert:\LocalMachine\My\
    ````

1. Get the credential for performing configuration of ADFS.

    ````powershell
    $credential = Get-Credential
    ````

1. In Windows PowerShell credential request, enter the credentials of **ad\Administrator**

1. Install the AD FS farm with the federation service name **sts.adatum.com**, the federation display name **Adatum Corporation**, and the managed group service account **AD\AdatumADFS$**.

    ````powershell
    Add-AdfsFarmNode `
        -Credential $credential `
        -PrimaryComputerName VN1-SRV8.ad.adatum.com `
        -CertificateThumbprint $certificate.Thumbprint `
        -GroupServiceAccountIdentifier 'ad\AdatumADFS$'
    ````

1. Exit from the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

1. Remove the remote PowerShell session.

    ````powershell
    Remove-PSSession -Session $pSSession
    ````

1. Restart **VN1-SRV9**.

    ````powershell
    Restart-Computer `
        -ComputerName $computerName -WsmanAuthentication Default -Force
    ````

### Task 5: Add DNS A records

#### Desktop experience

Perform this task on CL1.

1. Open **DNS**.
1. In DNS, expand **VN1-SRV5** and **Forward Lookup Zones**.
1. In the context-menu of **Forward Lookup Zones**, click **New Zone...**
1. In the New Zone Wizard, on page Welcome to the New Zone Wizard, click **Next >**
1. On page Zone Type, ensure **Primary zone** is selected, **Store the zone in Active Directory** is sctivated, and click **Next >**.
1. On page Active Directory Zone Replication Scope, click **To all DNS servers running on domain controller in this forest: ad.adatum.com** and click **Next >**.
1. On page Zone Name, in **Zone name**, type **sts.adatum.com** and click **Next >**.
1. On page Dynamic Update, click **Do not allow dynamic updates** and click **Next >**.
1. On page Completing the New Zone Wizard, click **Finish**.
1. In **DNS**, click **sts.adatum.com**.
1. In the context-menu of **sts.adatum.com**, click **New Host (A or AAAA)...**
1. In New Host, under **IP address**, type **10.1.1.64** and click **Add Host** (leave **Name** blank).
1. In the message box the host record sts.adatum.com was successfully created, click **OK**.
1. In **New Host**, under **IP address**, type **10.1.1.72** and click **Add Host** (leave **Name** blank).
1. In the message box the host record sts.adatum.com was successfully created, click **OK**.
1. In **New Host**, click **Done**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. In Terminal, create a new DNS primary zone with the name **sts.adatum.com**, stored in Active Directory and replicated within the forest, with dynamic updates disabled on **VN1-SRV5**.

    ````powershell
    $computerName = 'VN1-SRV5'
    $zoneName = 'sts.adatum.com'
    Add-DnsServerPrimaryZone `
        -ComputerName $computerName `
        -Name $zoneName `
        -ReplicationScope Forest `
        -DynamicUpdate None
    ````

1. Add A records to the new zone with the name of the zone itself and the IPv4 addresses **10.1.1.64** and **10.1.1.72**.

    ````powershell
    Add-DnsServerResourceRecordA `
        -ComputerName $computerName `
        -ZoneName $zoneName `
        -Name '@' `
        -IPv4Address 10.1.1.64, 10.1.1.72
    ````

### Task 6: Verify the functionality of AD FS

Perform this task on CL1.

1. Run **Terminal**.
1. Open a remote PowerShell session to **VN1-SRV8**.

    ````powershell
    Enter-PSSession VN1-SRV8
    ````

1. For testing purposes, enable IDP initiated sign on.

    ````powershell
    Set-AdfsProperties -EnableIdPInitiatedSignonPage $true
    ````

1. Exit the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

1. Using Microsoft Edge, navigate to <https://sts.adatum.com/adfs/ls/idpinitiatedsignon.aspx>

    If the web page of AD FS appears with an error, wait 2 minutes and try again. The servers may not have been synchronized yet.

1. On page Adatum Corporation, click **Sign in**.
1. On Sign in, type the credentials of any user, e.g., **ad\Ida** and click **Sign in**.

    > You should receive **You are signed in**.

1. Click **Sign out**.

## Exercise 3: Deploy Web Application Proxy

1. [Install the Web Application Proxy role service](#task-1-install-the-web-application-proxy-role-service) on PM-SRV3
1. [Import the certificate on the Web Application Proxy Server and disable TLS 1.3](#task-2-import-the-wildcard-certificate-on-the-web-application-proxy-server-and-disable-tls-13) on PM-SRV3
1. [Configure the Web Application Proxy role service](#task-3-configure-the-web-application-proxy-role-service) on PM-SRV3

### Task 1: Install the Web Application Proxy role service

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, on the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before you begin, click **Next >**.
1. On page Select installation type, ensure **Role-based or feature-base installation** is selected and click **Next >**.
1. On page Select destination server, click **PM-SRV3.ad.adatum.com** and click **Next >**.
1. On page Server Roles, activate **Remote Access** and click **Next >**.
1. On page Features, click **Next >**.
1. On page Remote Access, click **Next >**.
1. On page Role Services, activate **Web Application Proxy**.
1. In Add features that are required for Web Application Proxy, click **Add Features**.
1. In **Add Roles and Features Wizard**, on page **Role Services**, click **Next >**.
1. On page Confirmation, activate **Restart the destination server automatically if required** and click **Install**.

    Wait for the feature installation to complete.

1. On page Installation progress, click **Close**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. In Terminal, install the windows feature Web Application Proxy on PM-SRV3, including management tools.

    ````powershell
    Install-WindowsFeature `
        -ComputerName PM-SRV3 `
        -Name Web-Application-Proxy `
        -IncludeManagementTools `
        -Restart
    ````

### Task 2: Import the wildcard certificate on the Web Application Proxy Server and disable TLS 1.3

Perform this task on CL1.

1. Run **Terminal**.
1. In Terminal, create a remote PowerShell session to **PM-SRV3**.

    ````Powershell
    $session = New-PSSession PM-SRV3
    ````

1. In the remote session, copy **\\\\VN1-SRV6\\IT\\Wildcard adatum.com.pfx** to C:\.

    ````powershell
    Copy-Item `
        -Path '\\VN1-SRV10\IT\Wildcard adatum.com.pfx' `
        -Destination C:\ `
        -ToSession $session
    ````

1. Enter the remote session.

    ````powershell
    Enter-PSSession $session
    ````

1. Read the password for the pfx file.

    ````powershell
    $password = Read-Host `
        -AsSecureString -Prompt 'Enter the password for the pfx file'
    ````

1. At the prompt Enter the password for the pfx file, enter the password of the exported certificate in the pfx file.
1. Import the pfx file **\\\\VN1-SRV6\\IT\\Wildcard adatum.com.pfx**.

    ````powershell
    $filePath = 'C:\Wildcard adatum.com.pfx'
    $certificate = Import-PfxCertificate `
        -Password $password `
        -CertStoreLocation Cert:\LocalMachine\My\ `
        -FilePath $filePath
    ````

1. Delete the pfx file.

    ````powershell
    Remove-Item -Path $filePath
    ````

1. Disable TLS 1.3.

    ````powershell
    $path = `
        'HKLM:\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.3\Client'
    New-Item $path -Force
    Set-ItemProperty -Path $path -Name DisabledByDefault -Value 1
    Set-ItemProperty -Path $path -Name Enabled -Value 0

1. Exit and remove the remote PowerShell session.

    ````powershell
    Exit-PSSession
    Remove-PSSession $session
    ````

### Task 3: Configure the Web Application Proxy role service

#### Desktop experience

Perform this task on PM-SRV3.

1. Open **Remote Access Management**.
1. In **Remote Access Management Console**, in the left pane, click **Web Application Proxy**.
1. In the middle pane, under **Configure Web Application Proxy**, click **Run the Web Application Proxy Configuration Wizard**.
1. In Web Application Proxy Configuration Wizard, on page Welcome, click **Next >**.
1. On page Federation Server, in **Federation service name**, type **sts.adatum.com**. Under **Enter the credentials of a local administrator account on the federation servers**, type the credentials of **ad\Administrator** and click **Next >**.
1. On page AD FS Proxy Certificate, under **Select a certificate to be used by the AD FS proxy**, click **\*.adatum.com** and click **Next >**.
1. On page **Confirmation**, click **Configure**.
1. On page Results, click **Close**.
1. Open **Server Manager**.
1. In Server Manager, click **Remote Access**.
1. Close **Remote Access Management Console**.

    Note: It is important to close Remote Access Management to discover the new configuration in the next task.

#### PowerShell

Perform this task on CL1.

1. Run **Terminal**.
1. In Terminal, create a remote PowerShell session to **PM-SRV3**.

    ````Powershell
    Enter-PSSession PM-SRV3
    ````

1. Retrieve a valid certificate with the subject CN=*.adatum.com. If there are more certificates with this subject, retrieve the longest lasting.

    ````powershell
    $now = Get-Date
    $certificate = Get-ChildItem Cert:\LocalMachine\My\ | Where-Object { 
        $PSItem.Subject -eq 'CN=*.adatum.com' `
        -and $PSItem.NotBefore -lt $now `
        -and $PSItem.NotAfter -gt $now
    } | 
    Sort-Object NotAfter -Descending | 
    Select-Object -First 1
    ````

1. Install the Web Application Proxy using the imported certificate and the federation service name **sts.adatum.com**.

    ````powershell
    Install-WebApplicationProxy `
        -CertificateThumbprint $certificate.Thumbprint `
        -FederationServiceName sts.adatum.com
    ````

1. In Windows PowerShell credential request, enter the credentials of **AD\Administrator**.
1. Exit the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

## Exercise 4: Publish the web application on the Internet using Web Application Proxy (WAP)

1. [Add a relying party trust](#task-1-add-a-relying-party-trust) to AD FS with the identifier http://app1.adatum.com
2. [Configure Kerberos constrained delegation](#task-2-configure-kerberos-constrained-delegation) to allow PM-SRV3 to receive service tickets for PM-SRV2 and add service principal names HTTP/PM-SRV3 and HTTP/PM-SRV3.ad.adatum.com to PM-SRV3
3. [Publish the web application](#task-3-publish-the-web-application) using WAP and allow HTTP to HTTPS redirection
4. [Verify the published web application](#task-4-verify-the-published-web-application) using CL3

### Task 1: Add a relying party trust

#### Desktop experience

Perform this task on VN1-SRV8.

1. Open **AD FS Management**.
1. In AD FS, in the context menu of **AD FS**, click **Add Relying Party Trust...**
1. In Add Relying Party Trust Wizard, on page Welcome, click **Non claims aware** and click **Start**.
1. On page Specify Display Name, type **App1** and click **Next >**.
1. On page Configure Identifiers, under **Relying party trust identifier**, type **http://app1.adatum.com** and click **Add**. Click **Next >**.
1. On page **Choose Access Control policy**, ensure **Permit everyone** is selected and click **Next >**.
1. On page Ready to Add Trust, click **Next >**.
1. On page Finish, click **Close**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Open a remote PowerShell session to **VN1-SRV8**.

    ````powershell
    Enter-PSSession -ComputerName VN1-SRV8
    ````

1. Add a non claims-aware relying party trust named **App1** with the identifiert **http://app1.adatum.com** and the access control policy **Permit everyone**.

    ````powershell
    Add-AdfsNonClaimsAwareRelyingPartyTrust `
        -Name App1 `
        -Identifier http://app1.adatum.com `
        -AccessControlPolicyName 'Permit everyone'
    ````

1. Exit from the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

### Task 2: Configure Kerberos constrained delegation

#### Desktop experience

Perform this task on CL1.

1. Open **Active Directory Users and Computers**.
1. In Active Directory Users and Computers, on the menu, click **View**, **Advanced Features**.
1. Click **Computers**.
1. Double-click **PM-SRV3**.
1. In PM-SRV3 Properties, click the tab **Delegation**.
1. On tab Delegation, click **Trust this computer for delegation to specified services only**.
1. Click **Add...**.
1. In Add Services, click **Users or Computer...**
1. In Select Users or Computers, under **Enter the object names to select**, type **PM-SRV2** and click **OK**.
1. In **Add Services**, under **Available services**, click **http** **PM-SRV2** and click **OK**.
1. In **PM-SRV3 Properties**, click the tab **Attribute Editor**.
1. On the tab Attribute Editor, under **Attributes**, click **servicePrincipalName** and click **Edit**.
1. In Multi-valued String Editor, under **Value to add**, type **HTTP/PM-SRV3** and click **Add**. Repeat this step for the value **HTTP/PM-SRV3.ad.adatum.com**. Click **OK**.
1. In **PM-SRV3 Properties**, click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. In Terminal, for **PM-SRV3** allow delegation of Kerberos tickets to **HTTP/PM-SRV2**. And add the service principal names **HTTP/PM-SRV3** and **HTTP/PM-SRV3.ad.adatum.com**.

    ````powershell
    Set-ADComputer -Identity PM-SRV3 -Add @{
        'msDS-AllowedToDelegateTo' = 'HTTP/PM-SRV2'
        'servicePrincipalName' = @(
            'HTTP/PM-SRV3', 'HTTP/PM-SRV3.ad.adatum.com'
        )
    }
    ````

### Task 3: Publish the web application

#### Desktop experience

Perform this task on PM-SRV3.

1. Open **Remote Access Management**.
1. In Remote Access Management Console, click **Web Application Proxy**.
1. In the pane **Tasks**, click **Publish**.
1. In Publish New Application Wizard, on page Welcome, click **Next >**.
1. On page Preauthentication, ensure **Active Directory Federation Service (AD FS)** is selected, and click **Next >**.
1. On page Supported Clients, ensure **Web and MSOFBA** is selected and click **Next >**.
1. On page Relying Party, click **App1** and click **Next >**.
1. On page Publishing Settings, under Name, type **App1**. Under **External URL**, type **https://app1.adatum.com**. Under **External certificate**, click **\*.adatum.com**. Activate **Enable HTTP to HTTPS redirection**. Under Backend server url, type **http://pm-srv2.ad.adatum.com**. Under Backend server SPN, type **HTTP/pm-srv2.ad.adatum.com**. Click **Next >**.
1. On page Confirmation, click **Publish**.
1. On page Results, click **Close**.
1. Open **Windows Defender Firewall with Advanced Security**.
1. In Windows Defender Firewall with Advanced Security, click **Inbound Rules**.
1. In the context-menu of **Inbound Rules**, click **New Rule...**
1. In the New Inbound Rule Wizard, on page Rule Type, click **Port** and click **Next >**.
1. On page Protocol and Ports, ensure **TCP** is selected. Ensure **Specific local ports** is selected and, beside, type **80**. Click **Next >**.
1. On page Action, ensure **Allow the connection** is selected and click **Next >**.
1. On page Profile, ensure **Domain**, **Private**, and **Public** are selected and click **Next >**.
1. On page Name, under **Name**, type **AD FS HTTP Services (TCP-In)** and click **Finish**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Open a remote PowerShell session to **PM-SRV3**.

    ````powershell
    Enter-PSSession -ComputerName PM-SRV3
    ````

1. Retrieve a valid certificate with the subject CN=*.adatum.com. If there are more certificates with this subject, retrieve the longest lasting.

    ````powershell
    $now = Get-Date
    $certificate = Get-ChildItem Cert:\LocalMachine\My\ | Where-Object { 
        $PSItem.Subject -eq 'CN=*.adatum.com' `
        -and $PSItem.NotBefore -lt $now `
        -and $PSItem.NotAfter -gt $now
    } | 
    Sort-Object NotAfter -Descending | 
    Select-Object -First 1
    ````

1. Publish **App1** with pre-authentication using **ADFS**, the external URL **https://app1.adatum.com**, the backend server URL **http://pm-srv2.ad.adatum.com**, and the backend server SPN **HTTP/pm-srv2.ad.adatum.com**. Enable HTTP redirection.

    ````powershell
    Add-WebApplicationProxyApplication `
        -Name App1 `
        -ExternalPreauthentication ADFS `
        -ADFSRelyingPartyName App1 `
        -ExternalUrl https://app1.adatum.com `
        -ExternalCertificateThumbprint $certificate.Thumbprint `
        -BackendServerUrl http://pm-srv2.ad.adatum.com `
        -BackendServerAuthenticationSPN HTTP/pm-srv2.ad.adatum.com `
        -EnableHTTPRedirect
    ````

1. Exit from the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

### Task 4: Verify the published web application

Perform this task on CL3.

1. Open **File Explorer**.
1. In File Explorer, navigate to **C:\\Windows\\System32\\drivers\\etc**
1. In etc, double-click **hosts**.
1. In Select an app to open 'hosts', click **Editor** and click **Just once**.
1. In hosts, at the end of the file, add the following lines.

    ````text
    10.1.200.24 app1.adatum.com
    10.1.200.24 sts.adatum.com
    ````

1. Close **hosts** and save the file.

    Note: In real world, these entries should be created in a public DNS zone.

1. Open **Microsoft Edge**.
1. In Microsoft Edge, navigate to <http://app1.adatum.com>.
1. In Adatum Corporation, sign in as any user, e.g. **ad\Ida**

    You should get redirected to https://app1.adatum.com and you should see the WHO Page with the Authentication Method Negotiate (KERBEROS).

## Exercise 5: Publish Windows Admin Center on the Internet using WAP

Use the instructions from the previous exercise to publish Windows Admin Center. Use the parameters from the table below.

| Label                          |                                   |
|--------------------------------|-----------------------------------|
| Relying party trust identifier | https://admincenter.ad.adatum.com |
| External URL                   | https://admincenter.adatum.com    |
| Backend server URL             | https://admincenter.ad.adatum.com |
| Backend server SPN             | HTTP/VN1-SRV4.ad.adatum.com       |

[figure 1]:/images/who-page_http_anonymous.jpeg
