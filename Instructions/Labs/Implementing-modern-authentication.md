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

1. On CL1, sign in as **ad\Administrator**.
1. On PM-SRV2, sign in as **ad\Administrator**.
1. In SConfig, enter **15**.
1. Set the permissions on the **WebServer** certificate to allow **PM-SRV2**.

    ````powershell
    C:\LabResources\Solutions\Set-CertTemplatePermissions.ps1 `
        -Template WebServer -ComputerName PM-SRV2
    ````

## Introduction

Adatum introduces a new web application. The new web application will support modern authentication methods. You will implement the infrastructure to support the new web application. Moreover, Adatum wants to make Windows Admin Center accessible from the Internet, while maintaining a secure authentication.

## Exercises

1. [Deploy a web application](#exercise-1-deploy-a-web-application)
1. Deploy Active Directory Federation Services (AD FS)
1. Configure the web appliation to use AD FS
1. Publish the web application on the Internet using Web Application Proxy (WAP)
1. Publish Windows Admin Center on the Internet using WAP

## Exercise 1: Deploy a web application

1. [Install Internet Information Services](#task-1-install-internet-information-services) with support for Basic and Windows Authentication and ASP.NET 4.8 on PM-SRV2
1. [Install IIS Management Console](#task-2-install-iis-management-console) on CL1
1. [Configure remote administration for IIS Manager](#task-3-configure-remote-administration-for-iis-manager) on PM-SRV2
1. [Deploy the web application](#task-4-deploy-the-web-application) by copying the files from C:\LabResources\Authpage
1. [Request a certificate for the web application](#task-5-request-a-certificate-for-the-web-application) with the DNS names pm-srv2.ad.adatum.com and pm-srv2
1. [Configure the web application](#task-6-configure-the-web-application-for-https) for HTTPS

### Task 1: Install Internet Information Services

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, on the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before you begin, click **Next >**.
1. On page Select installation type, ensure **Role-based or feature-base installation** is selected and click **Next >**.
1. On page Select destination server, click PM-SRV2.ad.adatum.com and click **Next >**.
1. On page Server Roles, activate **Web Server (IIS)** and click **Next >**.
1. On page Features, click **Next >**.
1. On page Web Server Role (IIS), click **Next >**.
1. On page Role Services, under **Web Server**, **Security**, activate **Basic Authentication** and **Windows Authentication**. Under **Application Development**, activate **ASP.NET 4.8**.
1. In Add features that are required for ASP.NET 4.8, click **Add Features**.
1. In **Add Roles and Features Wizard**, on page **Role Services**, under **Management Tools**, activate <!--**IIS management Console** and -->**Management Service**. Click **Next >**.
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

### Task 5: Request a certificate for the web application

Perform this task on PM-SRV2.

1. In SConfig, enter **15**.
1. Request a certificate for the machine for the DNS names **pm-srv2.ad.adatum.com** and **pm-srv2** using the template **WebServer**.

    ````powershell
    $dnsName = 'pm-srv2.ad.adatum.com', 'pm-srv2'
    Get-Certificate `
        -Template 'WebServer' `
        -SubjectName "cn=$($dnsName[0])" `
        -DnsName $dnsName  `
        -CertStoreLocation Cert:\LocalMachine\My\
    ````

    Note: If you see an error message regarding certificate revocation list, restart VN1-SRV2.

### Task 6: Configure the web application for HTTPS

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
1. In Add Site Binding, under **Type**, click **https**. Under **SSL certificate**, click **pm-srv2.ad.adatum.com** and click **OK**.
1. In **Site Bindings**, click **Close**.
1. Use Microsoft Edge to navigate to <https://pm-srv2.ad.adatum.com>

    > You should see a page like in [figure 2].

[figure 1]:/images/who-page_http_anonymous.jpeg
[figure 2]:/images/who-page_https_anonymous.jpeg
