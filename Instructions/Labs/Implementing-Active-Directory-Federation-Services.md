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

* On CL1, sign in as **ad\Administrator**.
* On CL3, sign in as **.\Administrator**.
* On VN1-SRV8, sign in as **ad\Administrator**.
* On PM-SRV2, sign in as **ad\Administrator**
* On PM-SRV3, sign in as **ad\Administrator**

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
1. On page Role Services, under **Web Server**, **Security**, activate **Basic Authentication** and **Windows Authentication**.
1. Expand **Application Development**, and activate **ASP.NET 4.8**.
1. In Add features that are required for ASP.NET 4.8, click **Add Features**.
1. In **Add Roles and Features Wizard**, on page **Role Services**, under **Management Tools**, activate **Management Service**. Click **Next >**.
1. On page Confirmation, click **Install**.
1. On page Installation progress, click **Close**.

### Task 2: Install IIS Management Console

Perform this task on CL1.

1. Open **Settings**.
1. In Settings, click **Apps**.
1. In Apps, click **Optional features**.
1. In Optional features, at the bottom, click **More Windows features**.
1. In Windows Features, expand **Internet Information Services**, **Web Management Tools**, activate **IIS Management Console** and click **OK**.
1. On page Windows completed the requested changes, click **Close**.

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

### Task 5: Configure authentication

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
1. In Windows Security, enter the credentials of any user, e.g. **ad\Ada**.

    On the WHO Page, the Authentication Method should show Negotiate (KERBEROS).

## Exercise 2: Deploy Active Directory Federation Services (AD FS)

1. [Request and export a wildcard certificate](#task-1-request-and-export-a-wildcard-certificate) for *.adatum.com
1. [Install Active Directory Federation services](#task-2-install-active-directory-federation-services) on VN1-SRV8 and VN1-SRV9
1. [Create the first federation server in a federation server farm](#task-3-create-the-first-federation-server-in-a-federation-server-farm) using VN1-SRV8 with a federation service name sts.adatum.com and a group managed service account
1. [Add a federation server in a federation server farm](#task-4-add-a-federation-server-in-a-federation-server-farm) using VN1-SRV9
1. [Add DNS A records](#task-5-add-dns-a-records) for sts.adatum.com on VN1-SRV5 pointing to VN1-SRV8 and VN1-SRV9
1. [Verify the functionality of AD FS](#task-6-verify-the-functionality-of-ad-fs)

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

    If the certificate request fails with an access denied error message, leave the Create Certificate wizard open. On VN1-SRV2restart the Certification Authority service:

    1. Open **Server Manager**.
    1. In Server manager, in the left pane, click **AD CS**.
    1. In Server manager > AD CS, under **SERVERS**, click **VN1-SRV2**.
    1. Under **SERVICES** (you might have to scroll down), in the context-menu of **CertSvc**, click **Restart Services**.

    Then, switch back to the Create Certificate wizard, and click **Finish** again.

1. In **Internet Information Services (IIS) Manager**, under **Server Certificates**, click **Wildcard adatum.com** and click **Export...**
1. In Export Certificate, under **Export to**, type **\\\\vn1-srv10\\IT\\Wildcard adatum.com.pfx**. In **Password** and **Confirm password**, type a secure password and click **OK**.

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

### Task 4: Add a federation server in a federation server farm

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

## Exercise 3: Deploy Web Application Proxy

1. [Install the Web Application Proxy role service](#task-1-install-the-web-application-proxy-role-service) on PM-SRV3
1. [Import the certificate on the Web Application Proxy Server and disable TLS 1.3](#task-2-import-the-wildcard-certificate-on-the-web-application-proxy-server-and-disable-tls-13) on PM-SRV3
1. [Configure the Web Application Proxy role service](#task-3-configure-the-web-application-proxy-role-service) on PM-SRV3

### Task 1: Install the Web Application Proxy role service

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

    Note: It is importnt to close Remote Access Management to discover the new configuration in the next task.

#### PowerShell

Perform this task on CL1.

1. Run **Terminal**.
1. In Terminal, create a remote PowerShell session to **PM-SRV3**.

    ````Powershell
    Enter-PSSession PM-SRV3
    ````

1. Retrieve the certificate.

    ````powershell
    $certificate = Get-ChildItem -Path Cert:\LocalMachine\My | 
    Where-Object { $PSItem.Friendlyname -eq 'Wildcard adatum.com' }

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

## Exercise 4: Publish the web application on the Internet using Web Application Proxy (WAP)

1. [Add a relying party trust](#task-1-add-a-relying-party-trust) to AD FS with the identifier http://app1.adatum.com
2. [Configure Kerberos constrained delegation](#task-2-configure-kerberos-constrained-delegation) to allow PM-SRV3 to receive service tickets for PM-SRV2 and add service principal names HTTP/PM-SRV3 and HTTP/PM-SRV3.ad.adatum.com to PM-SRV3
3. [Publish the web application](#task-3-publish-the-web-application) using WAP and allow HTTP to HTTPS redirection
4. [Verify the published web application](#task-4-verify-the-published-web-application) using CL3

### Task 1: Add a relying party trust

Perform this task on VN1-SRV8.

1. Open **AD FS Management**.
1. In AD FS, in the context menu of **AD FS**, click **Add Relying Party Trust...**
1. In Add Relying Party Trust Wizard, on page Welcome, click **Non claims aware** and click **Start**.
1. On page Specify Display Name, type **App1** and click **Next >**.
1. On page Configure Identifiers, under **Relying party trust identifier**, type **http://app1.adatum.com** and click **Add**. Click **Next >**.
1. On page **Choose Access Control policy**, ensure **Permit everyone** is selected and click **Next >**.
1. On page Ready to Add Trust, click **Next >**.
1. On page Finish, click **Close**.

### Task 2: Configure Kerberos constrained delegation

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

### Task 3: Publish the web application

Perform this task on PM-SRV3.

1. Open **Remote Access Management**.
1. In Remote Access Management Console, click **Web Application Proxy**.
1. In the pane **Tasks**, click **Publish**.
1. In Publish New Application Wizard, on page Welcome, click **Next >**.
1. On page Preauthentication, ensure **Active Directory Federation Service (AD FS)** is selected, and click **Next >**.
1. On page Supported Clients, ensure **Web and MSOFBA** is selected and click **Next >**.
1. On page Relying Party, click **App1** and click **Next >**.
1. On page Publishing Settings, under Name, type **App 1**. Under **External URL**, type **https://app1.adatum.com**. Under **External certificate**, click **\*.adatum.com**. Activate **Enable HTTP to HTTPS redirection**. Under Backend server url, type **http://pm-srv2.ad.adatum.com**. Under Backend server SPN, type **HTTP/pm-srv2.ad.adatum.com**. Click **Next >**.
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
1. In Adatum Corporation, sign in as any user, e.g. **Ada@ad.adatum.com**

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
