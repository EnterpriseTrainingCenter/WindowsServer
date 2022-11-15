# Lab: Implementing modern authentication

## Required VMs

* VN1-SRV2
* VN1-SRV4
* VN1-SRV5
* VN1-SRV8
* VN1-SRV9
* PM-SRV2
* PM-SRV3
* PM-SRV4
* CL1

## Setup

* On CL1, sign in as **ad\Administrator**
* On PM-SRV2, sign in as **ad\Administrator**
* On PM-SRV3, sign in as **ad\Administrator**

## Introduction

Adatum introduces a new web application. The new web application will support modern authentication methods. You will implement the infrastructure to support the new web application. Moreover, Adatum wants to make Windows Admin Center accessible from the Internet, while maintaining a secure authentication.

## Exercises

1. [Deploy a web application](#exercise-1-deploy-a-web-application)
1. [Deploy Active Directory Federation Services (AD FS)](#exercise-2-deploy-active-directory-federation-services-ad-fs)
1. Publish the web application on the Internet using Web Application Proxy (WAP)
1. Publish Windows Admin Center on the Internet using WAP

## Exercise 1: Deploy a web application

1. [Install Internet Information Services](#task-1-install-internet-information-services) with support for Basic and Windows Authentication and ASP.NET 4.8 on PM-SRV2
1. [Install IIS Management Console](#task-2-install-iis-management-console) on CL1
1. [Configure remote administration for IIS Manager](#task-3-configure-remote-administration-for-iis-manager) on PM-SRV2
1. [Deploy the web application](#task-4-deploy-the-web-application) by copying the files from C:\LabResources\Authpage
<!--
1. [Request and export a wildcard certificate](#task-5-request-and-export-a-wildcard-certificate) for *.adatum.com
1. [Import the wildcard certificate](#task-6-import-the-wildcard-certificate) on PM-SRV2
1. [Configure the web application](#task-7-configure-the-web-application-for-https) for HTTPS
-->

### Task 1: Install Internet Information Services

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, on the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before you begin, click **Next >**.
1. On page Select installation type, ensure **Role-based or feature-base installation** is selected and click **Next >**.
1. On page Select destination server, click **PM-SRV2.ad.adatum.com** and click **Next >**.
1. On page Server Roles, activate **Web Server (IIS)** and click **Next >**.
1. On page Features, click **Next >**.
1. On page Web Server Role (IIS), click **Next >**.
1. On page Role Services, under **Web Server**, **Security**, activate **Basic Authentication** and **Windows Authentication**. Under **Application Development**, activate **ASP.NET 4.8**.
1. In Add features that are required for ASP.NET 4.8, click **Add Features**.
1. In **Add Roles and Features Wizard**, on page **Role Services**, under **Management Tools**, activate **Management Service**. Click **Next >**.
1. On page Confirmation, click **Install**.
1. On page Installation progress, click **Close**.

### Task 2: Install IIS Management Console

Perform this task on CL1.

1. Open **Settings**.
1. In Settings, click **Apps**.
1. In Apps, at the bottom, click **More Windows features**.
1. In Windows Features, expand **Internet Information Services**, **Web Management Tools**, activate **IIS Management Console** and click **OK**.
1. On Windows completed the requested changes, click **Close**.

### Task 3: Configure remote administration for IIS Manager

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

<!--
### Task 5: Request and export a wildcard certificate

Perform this task on CL1.

1. Open **Internet Information Services (IIS) Manager**.
1. In Internet Information Services (IIS) Manager, click **CL1 (AD\Administrator)**.
1. Under CL1 Home, double-click **Server Certificates**.
1. Under Server Certificates, in the right pane, click **Create Domain Certificate...**
1. In Create Certificate, on page Distinguished Name Properties, in **Common Name**, type **\*.adatum.com**. Fill the remaining fields with any meaningful values and click **Next**.
1. On page Online Certification Authority, under **Specify Online Certification Authority**, click **Select...**
1. In Select Certification Authority, click the certificate authority on computer **VN1-SRV2.ad.adatum.com** and click **OK**.
1. In Create Certificate, on page **Online Certification Authority**, under **Friendly name**, type **Wildcard adatum.com** and click **Finish**.
1. In **Internet Information Services (IIS) Manager**, under **Server Certificates**, click **Wildcard adatum.com** and click **Export...**
1. In Export Certificate, under **Export to**, type **\\\\vn1-srv6\\IT\\Wildcard adatum.com.pfx**. In **Password** and **Confirm password**, type a secure password and click **OK**. 

    Take a note of the password.

1. In **Internet Information Services (IIS) Manager**, under **Server Certificates**, click **Wildcard adatum.com** and click **Remove**
1. In Confirm Remove, click **Yes**.

### Task 6: Import the wildcard certificate

Perform this task on PM-SRV2.

1. In SConfig, enter **15**.
1. Read the password for the pfx file.

    ````powershell
    $password = Read-Host `
        -Prompt 'Enter the password for the pfx file' -AsSecureString
    ````

1. At the prompt Enter the password for the pfx file, enter the password you noted in the previous task.
1. Import the pfx file.

    ````powershell
    Import-PfxCertificate `
        -Password $password `
        -CertStoreLocation Cert:\LocalMachine\My\ `
        -FilePath '\\vn1-srv6\it\Wildcard adatum.com.pfx'
    ````

### Task 7: Configure the web application for HTTPS

Perform this task on CL1.

1. Open **Internet Information Services (IIS) Manager**.
1. In Internet Information Services (IIS) Manager, in the context-menu of **Start Page**, click **Connect to a Server...**
1. In Connect to Server, on page Specify Server Connection Details, under **Server name**, type **PM-SRV2** and click **Next**.
1. On page Provide Credentials, type the credentials of **ad\Administrator** and click **Next**.
1. In Server Certificate Alert, click **Connect**.
1. In **Connect to Server**, on page **Created a new connection successfully**, click **Finish**.
1. In **Internet Information Services (IIS) Manager**, expand **pm-srv2 (ad\Administrator)**, **Sites**, and click **Default Web Site**.
1. In the context-menu of **Default Web Site**, click **Edit Bindings...**
1. In Site Bindings, click **Add...**
1. In Add Site Binding, under **Type**, click **https**. Under **SSL certificate**, click **Wildcard adatum.com** and click **OK**.
1. In **Site Bindings**, click **Close**.
1. Use Microsoft Edge to navigate to <https://pm-srv2.ad.adatum.com>

    > You should see a page like in [figure 2].

-->

## Exercise 2: Deploy Active Directory Federation Services (AD FS)

<!--
1. Request a certificate for sts.adatum.com on VN1-SRV8 and VN1-SRV9
-->
1. Request and export a wildcard certificate for *.adatum.com
1. Install Active Directory Federation services on VN1-SRV8 and VN1-SRV9
1. Create the first federation server in a federation server farm
1. Add a federation server in a federation server farm
1. Add DNS A records for sts.adatum.com pointing to VN1-SRV8 and VN1-SRV9
1. Verify the functionality of AD FS
<!-- 
1. Add mappings for sts.adatum.com to the hosts file of the host

    Note: This simulates an external DNS server in the lab. In real world, you would add entries to you public DNS zone.

-->

<!--
### Task 1: Request a certificate

Perform this task on VN1-SRV8 and VN1-SRV9.

1. In Sconfig, enter **15**.
1. Request a certificate for the machine for the DNS name **sts.adatum.com** using the template **WebServer**.

    ````powershell
    $dnsName = 'sts.adatum.com'
    Get-Certificate `
        -Template 'WebServer' `
        -SubjectName "cn=$($dnsName[0])" `
        -DnsName $dnsName  `
        -CertStoreLocation Cert:\LocalMachine\My\
    ````
-->
### Task 1: Request and export a wildcard certificate

Perform this task on CL1.

1. Open **Internet Information Services (IIS) Manager**.
1. In Internet Information Services (IIS) Manager, click **CL1 (AD\Administrator)**.
1. Under CL1 Home, double-click **Server Certificates**.
1. Under Server Certificates, in the right pane, click **Create Domain Certificate...**
1. In Create Certificate, on page Distinguished Name Properties, in **Common Name**, type **\*.adatum.com**. Fill the remaining fields with any meaningful values and click **Next**.
1. On page Online Certification Authority, under **Specify Online Certification Authority**, click **Select...**
1. In Select Certification Authority, click the certificate authority on computer **VN1-SRV2.ad.adatum.com** and click **OK**.
1. In Create Certificate, on page **Online Certification Authority**, under **Friendly name**, type **Wildcard adatum.com** and click **Finish**.
1. In **Internet Information Services (IIS) Manager**, under **Server Certificates**, click **Wildcard adatum.com** and click **Export...**
1. In Export Certificate, under **Export to**, type **\\\\vn1-srv6\\IT\\Wildcard adatum.com.pfx**. In **Password** and **Confirm password**, type a secure password and click **OK**. 

    Take a note of the password.

1. In **Internet Information Services (IIS) Manager**, under **Server Certificates**, click **Wildcard adatum.com** and click **Remove**
1. In Confirm Remove, click **Yes**.

### Task 2: Install Active Directory Federation services

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

### Task 3: Create the first federation server in a federation server farm

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click *Notifications*, and under **Configuration required for Active Directory Federation Services at VN1-SRV8**, click **Configure the federation service on this server.**
1. In Active Directory Federation Services Configuration Wizard, on page Welcome, ensure **Create the first federation server in a federation server farm** is selected and click **Next >**.
1. On page Connect to AD DS, click **Change...**.
1. In Credentials for deployment operation, enter the credentials for **AD\Administrator**.
1. In **Active Directory Federation Services Configuration Wizard**, on page **Connect to AD DS**, click **Next >**.
1. On page Specify Service Properties, beside **SSL Certificate**, click **Import...**
1. In Open, navigate to **\\\\VN1-SRV6\\IT**, click **Wildcard adatum.com** and click **Open**.
1. In Enter certificate password, enter the password, you noted before.
1. In **Active Directory Federation Services Configuration Wizard**, on page **Specify Service Properties**, in **Federation Service name**, type **sts.adatum.com**. In **Federation Service Display Name**, type **Adatum Corporation** and click **Next >**.
1. On page Specify Service Account, ensure **Create a Group Managed Service Account** is selected and in **Account Name**, type **AdatumADFS**. Click **Next >**.
1. On page Specify Configuration Database, ensure **Create a database on this server using Windows Internal Database** is selected and click **Next >**.
1. On page Review Options, click **Next >**.
1. On page Pre-requisite Checks, click **Configure**.
1. On page Results, click **Close**.
1. In **Server Manager**, click **All Servers**.
1. Under All Servers, in the context-menu of **VN1-SRV8**, click **Restart Server**.
1. In message box Are you sure you want to restart these servers, click **OK**.

### Task 4: Add a federation server in a federation server farm

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click *Notifications*, and under **Configuration required for Active Directory Federation Services at VN1-SRV9**, click **Configure the federation service on this server.**
1. In Active Directory Federation Services Configuration Wizard, on page Welcome, click **Add a federation server to a federation server farm** and click **Next >**.
1. On page Connect to AD DS, click **Change...**.
1. In Credentials for deployment operation, enter the credentials for **AD\Administrator**.
1. In **Active Directory Federation Services Configuration Wizard**, on page **Connect to AD DS**, click **Next >**.
1. On page Specify Farm, ensure **Specify the primary federation server in an existing farm using Windows Internal Database** is selected. Beside **Primary Federation Server**, type **VN1-SRV8** and click **Next >**.
1. On page Specify Certificate, beside **SSL Certificate**, click **Import...**
1. In Open, navigate to **\\\\VN1-SRV6\\IT**, click **Wildcard adatum.com** and click **Open**.
1. In Enter certificate password, enter the password, you noted before.
1. In **Active Directory Federation Services Configuration Wizard**, on page **Specify Certificate**, click **Next >**.
1. On page Specify Service Account, click **Select...**
1. In Select User or Service Account, under **Enter the object name to select**, type **AdatumADFS** and click **OK**.
1. In **Active Directory Federation Services Configuration Wizard**, on page **Specify Service Account**, click **Next >**.
1. On page Review Options, click **Next >**.
1. On page Pre-requisite Checks, click **Configure**.
1. On page Results, click **Close**.
1. In **Server Manager**, click **All Servers**.
1. Under All Servers, in the context-menu of **VN1-SRV9**, click **Restart Server**.
1. In message box Are you sure you want to restart these servers, click **OK**.

### Task 5: Add DNS A records

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
1. On page Adatum Corporation, click **Sign in**.
1. On Sign in, type the credentials of any user, e.g., **Ada@adatum.com** and click **Sign in**.

    > You should receive **You are signed in**.

1. Click **Sign out**.

## Exercise 3: Deploy Web Applation Proxy

1. [Install the Remote Access Management Tools](#task-1-install-the-remote-access-management-tools) on CL1
1. [Install the Web Application Proxy role service](#task-2-install-the-web-application-proxy-role-service) on PM-SRV3
1. Import the certificate on the Web Application Proxy Server and disble TLS 1.3
1. [Configure the Web Application Proxy role service](#task-4-configure-the-web-application-proxy-role-service) on PM-SRV3

### Task 1: Install the Remote Access Management Tools

#### Desktop experience

Perform these steps on CL1.

1. Sign in as **.\Administrator**.
1. Open **Settings**.
1. In Settings, click **Apps**.
1. In Apps, click **Optional features**.
1. In Optional features, click the button **View features**.
1. In Add an optional feature, in the text field **Find an available optional feature**, type **RSAT**.
1. Activate the check box **RSAT: Remote Access Management Tools** and click **Next**.
1. Click **Install**.
1. If required, restart the computer.

#### PowerShell

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. In the context menu of **Start**, click **Terminal (Admin)**.
1. Add the windows capabilities
    * RSAT: Active Directory Domain Services and Lightweight Directory Services Tools
    * RSAT: File Services tools
    * RSAT: Server Manager

    ````powershell
    <# 
        Add-WindowsCapability does not support wildcards. Therefore, we use
        pipelines with the Get | Add pattern
    #>
    Get-WindowsCapability -Online -Name 'Rsat.RemoteAccess.Management.Tools*' |
    Add-WindowsCapability -Online
    # With the File Services tools, Server Manager is installed automatically
    ````

### Task 2: Install the Web Application Proxy role service

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
1. On page Confirmation, click **Install**.
1. On page Installation progress, click **Close**.

### Task 3: Import the certificate on the Web Application Proxy Server and disable TLS 1.3

Perform this task on CL1.

1. Run **Terminal**.
1. In Terminal, create a remote PowerShell session to **PM-SRV3**.

    ````Powershell
    $session = New-PSSession PM-SRV3
    ````

1. In the remote session, copy **\\\\VN1-SRV6\\IT\\Wildcard adatum.com.pfx** to C:\.

    ````powershell
    Copy-Item `
        -Path '\\VN1-SRV6\IT\Wildcard adatum.com.pfx' `
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

### Task 4: Configure the Web Application Proxy role service

<!-- #### Desktop experience -->

Perform this task on CL1.

1. Open **Remote Access Management**.
1. In Remote Access Management Console, in the right pane, click **Manage a Remote Server**.
1. In Manage a Remote Server, under **Server name**, type **PM-SRV3** and click **OK**.
1. In **Remote Access Management Console**, in the left pane, click **Web Application Proxy**.
1. In the middle pane, under **Configure Web Application Proxy**, click **Run the Web Application Proxy Configuration Wizard**.
1. In Web Application Proxy Configuration Wizard, on page Welcome, click **Next >**.
1. On page Federation Server, in **Federation service name**, type **sts.adatum.com**. Under **Enter the credentials of a local administrator account on the federation servers**, type the credentials of **ad\Administrator** and click **Next >**.
1. On page AD FS Proxy Certificate, under **Select a certificate to be used by the AD FS proxy**, click **\*.adatum.com** and click **Next >**.
1. On page **Confirmation**, click **Configure**.
1. On page Results, click **Close**.

#### PowerShell

Perform this task on CL1.

1. Run **Terminal**.
1. In Terminal, create a remote PowerShell session to **PM-SRV3**.

    ````Powershell
    Enter-PSSession PM-SRV3
    ````

1. Retrieve the certificate.

    ````powershell
    $certificate = Get-ChildItem -Path Cert:\LocalMachine\My | Where-Object { 
        $PSItem.Friendlyname -eq 'Wildcard adatum.com' 
    }

1. Install the Web Application Proxy using the imported certificate and the federation service name **sts.adatum.com**.

    ````powershell
    Install-WebApplicationProxy `
        -CertificateThumbprint $certificate.Thumbprint `
        -FederationServiceName sts.adatum.com
    ````

1. In Windows PowerShell credential request, enter the credentials of **AD\Administrator**.
1. Exit and remove the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

1. Restart the computer **PM-SRV3**.

    ````powershell
    Restart-Computer -ComputerName PM-SRV3 -WsmanAuthentication Default
    ````

## Exercise 4: Publish the web application on the Internet using Web Application Proxy (WAP)

## Exercise 5: Publish Windows Admin Center on the Internet using WAP

[figure 1]:/images/who-page_http_anonymous.jpeg
[figure 2]:/images/who-page_https_anonymous.jpeg
