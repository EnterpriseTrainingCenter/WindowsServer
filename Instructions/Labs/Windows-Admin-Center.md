# Lab: Windows Admin Center

## Required VMs

* VN1-SRV1
* VN1-SRV2
* VN1-SRV3
* VN1-SRV4
* VN1-SRV5
* VN1-SRV6
* VN1-SRV7
* VN1-SRV8
* VN1-SRV9
* VN1-SRV10
* VN2-SRV1
* VN2-SRV2
* VN3-SRV1
* PM-SRV1
* PM-SRV2
* PM-SRV3
* PM-SRV4
* CL1
* CL2

## Setup

1. On **CL1**, sign in as **ad\Administrator**.
1. Open **Terminal**.
1. Set permissions on the Web Server certificate template.

    ````powershell
    C:\LabResources\Solutions\Set-CertTemplatePermissions.ps1 `
        -Template 'WebServer' -ComputerName 'VN1-SRV4'
    ````

1. On **CL2**, sign in as **ad\Ida**.
1. On **VN1-SRV4**, sign in as **ad\Administrator**.
1. In SConfig, enter **15**.

You need sign-in credentials for an active Azure Subscription in which you have the permissions to create resources. Moreover, you need to have the Global Administrator role in Azure Active Directory. Ask your instructor for help, if you are unsure.

## Introduction

## Exercises

1. [Installing Windows Admin Center](#exercise-1-installing-windows-admin-center)
1. [Configuring Windows Admin Center](#exercise-2-configuring-windows-admin-center)
1. Securing Windows Admin Center

## Exercise 1: Installing Windows Admin Center

1. [Install the Remote Server Administration DNS Server Tools](#task-1-install-the-remote-server-administration-dns-server-tools) on CL1
1. [Download Windows Admin Center](#task-2-download-windows-admin-center)
1. [Request a certificate and install Windows Admin Center](#task-3-request-a-certificate-and-install-windows-admin-center) on VN1-SRV4 using the web server template
1. [Add a DNS host record for Windows Admin Center](#task-4-add-a-dns-host-record-for-windows-admin-center)
1. [Add URL with the FQDN of Windows Admin Center to intranet zone and verify Windows Admin Center loads](#task-5-add-url-with-the-fqdn-of-windows-admin-center-to-intranet-zone-and-verify-windows-admin-center-loads)

    > Are there any connections added by default?

### Task 1: Install the Remote Server Administration DNS Server Tools

#### Desktop experience

Perform these steps on CL1.

1. Sign in as **ad\Administrator**.
1. Open **Settings**.
1. In Settings, click **Apps**.
1. In Apps, click **Optional features**.
1. In Optional features, click the button **View features**.
1. In Add an optional feature, in the text field **Find an available optional feature**, type **RSAT**.
1. Activate the check box beside **RSAT: DNS Server Tools**.
1. Click **Next**.
1. Click **Install**.
1. If required, restart the computer.

You do not need to wait for the completion of the installation

#### PowerShell

Perform these steps on CL1.

1. In the context menu of **Start**, click **Terminal (Admin)**.
1. Add the windows capabilities **RSAT: Server DNS Server tools**.

    ````powershell
    Get-WindowsCapability -Online -Name 'Rsat.Dns.Tools*' |
    Add-WindowsCapability -Online
    ````

You do not need to wait for the completion of the installation.

### Task 2: Download Windows Admin Center

#### Desktop experience

Perform these steps on CL1.

1. Open Microsoft Edge and navigate to <https://www.microsoft.com/en-us/windows-server/windows-admin-center>.
1. On page Windows Admin Center | Microsoft, click **Download Windows Admin Center**.

    Wait for the download to finish.

1. Open **Terminal**.
1. Copy the setup file to **C:\LabResources** on **VN1-SRV4**.

    ````powershell
    $pSSession = New-PSSession -ComputerName VN1-SRV4
    Copy-Item `
        -Path ~\Downloads\WindowsAdminCenter*.msi `
        -Destination C:\LabResources\WindowsAdminCenter.msi `
        -ToSession $pSSession
    Remove-PSSession -Session $pSSession
    ````

#### PowerShell

Perform these steps on CL1.

1. Open **Terminal**.
1. On **VN1-SRV4**, download Windows Admin Center from **https://aka.ms/WACDownload** and save it as **C:\LabResource\WindowsAdminCenter.msi**.

    ````powershell
    Invoke-Command -ComputerName VN1-SRV4 -ScriptBlock {
        Start-BitsTransfer `
            -Source 'https://aka.ms/WACDownload' `
            -Destination 'C:\LabResources\WindowsAdminCenter.msi'
    }
    ````

### Task 3: Request a certificate and install Windows Admin Center

Perform this task on VN1-SRV4.

1. Store the hostname and FQDN of VN1-SRV4 in variables.

    ````powershell
    $hostName = 'admincenter'
    $fQDN = "$hostName.ad.adatum.com"
    ````

1. Request a certificate for **VN1-SRV4** using the template **WebServer**. Make sure, the certificate is valid for the FQDN as well as the hostname only.

    ````powershell
    $certificate = (
        Get-Certificate `
            -Template 'WebServer' `
            -SubjectName "CN=$fQDN" `
            -DnsName $hostName, $fQDN `
            -CertStoreLocation 'Cert:\LocalMachine\My'
        ).Certificate
    ````

1. Install Windows Admin Center using the requested certificate's thumbprint.

    ````powershell
    msiexec.exe /i C:\LabResources\WindowsAdminCenter.msi /qb+ /L*v 'C:\WAC-Install.log' CHK_REDIRECT_PORT_80=1 SME_PORT=443 SSL_CERTIFICATE_OPTION=installed SME_THUMBPRINT=$($certificate.Thumbprint)
    ````

    Wait for the installation to complete. This should take about a minute.

1. In the message box Windows Admin Center Setup completed successfully, click **OK**.
1. Restart the computer.

    ````powershell
    Restart-Computer
    ````

### Task 4: Add a DNS host record for Windows Admin Center

#### Desktop Experience

Perform this task on CL1.

1. Open **DNS**.
1. In Connet to DNS Server, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.
1. Expand **vn1-srv1.ad.adatum.com**, **Forward Lookup Zones**, and click **ad.adatum.com**.
1. In the context-menu of **adatum.com**, click **New Host (A or AAAA)...**
1. In New Host, under **Name (uses parent domain name if blank)**, type **admincenter**. Under IP address, type **10.1.1.32**. Click **Add Host**.
1. In the message box The host record admincenter.ad.adatum.com was successfully created, click **OK**.
1. In **New Host**, click **Done**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**
1. On **VN1-SRV1**, in zone **ad.adatum.com**, create an A record with the name **admincenter** and the IPv4 address **10.1.1.48**.

    ````powershell
    Add-DnsServerResourceRecordA `
        -ComputerName VN1-SRV1 `
        -ZoneName ad.adatum.com `
        -Name admincenter `
        -IPv4Address 10.1.1.32
    ````

### Task 5: Add URL with the FQDN of Windows Admin Center to intranet zone and verify Windows Admin Center loads

Perform this task on CL1.

1. Click **Start**, and type **Internet Options**.
1. Click **Internet Options**.
1. In Internet Properties, click the tab **Security**.
1. On tab Security, click **Local intranet**.
1. Click the button **Sites**.
1. In Local intranet, click the button **Advanced**.
1. In Local intranet, under **Add this website to the zone**, enter **https://admincenter.ad.adatum.com**, click **Add** and click **Close**.
1. In **Local intranet**, click **OK**.
1. In **Internet Properties**, click **OK**.
1. Open **Microsoft Edge**.
1. In Microsoft Edge, navigate to **https://admincenter.ad.adatum.com**.

    Windows Admin Center should load.

    > The gateway **VN1-SRV4** is already added as connection.

## Exercise 2: Configuring Windows Admin Center

1. [Create shared connections from a CSV file](#task-1-create-shared-connections-from-a-csv-file) to all servers and clients in Active Directory

    > Why are you prompted for credentials when clicking on one of the imported connections?

1. [Configure single sign-on](#task-2-configure-single-sign-on) to all computers registered with Windows Admin Center
1. [Manage extensions](#task-3-manage-extensions): ensure automatic update is enabled and install the Active Directory extension
1. [Register Windows Admin Center with Azure](#task-4-register-windows-admin-center-with-azure)
1. [Validate access by users](#task-5-validate-access-by-users)

    > Can Ida access Windows Admin Center and see all shared connections?

    > Can Ida get administrative access to a server through Windows Admin Center?

### Task 1: Create shared connections from a CSV file

Perform this task on CL1.

1. Open **Terminal**.
1. Create a CSV file with all server computers.

    ````powershell
    $path = '~\Documents\computers.csv'
    Get-ADComputer -Filter "Name -like 'VN*-*' -or Name -like 'PM-*'" |
    Select-Object `
        @{ name = 'name'; expression = { $PSItem.DNSHostName } }, `
        @{
            name = 'type'
            expression = { 'msft.sme.connection-type.server' }
        }, `
        @{ name = 'tags'; expression = {} }, `
        @{ name = 'groupId'; expression = { 'global' } } |
    Export-Csv -Path $path -NoTypeInformation -Force
    ````

1. Append all client computers to the CSV file.

    ````powershell
    Get-ADComputer -Filter "Name -like 'CL*'" |
    Select-Object `
        @{ name = 'name'; expression = { $PSItem.DNSHostName } }, `
        @{ 
            name = 'type'
            expression = { 'msft.sme.connection-type.windows-client' }
        }, `
        @{ name = 'tags'; expression = {} }, `
        @{ name = 'groupId'; expression = { 'global' } } |
    Export-Csv -Path $path -Append
    ````

    If you want, you may take a look at the CSV file in your Documents folder.

1. Create a remote PowerShell session to **VN1-SRV4**.

    ````powershell
    $pSSession = New-PSSession -ComputerName VN1-SRV4
    ````

1. Copy the Windows Admin Center Connection Tools module to the client.

    ````powershell
    Copy-Item `
        -FromSession $pSSession `
        -Path `
            "$env:ProgramFiles\Windows Admin Center\PowerShell\Modules\ConnectionTools\" `
        -Destination '~\Documents\WindowsPowerShell\Modules' `
        -Container `
        -Recurse
    ````

1. Import connections from the CSV file.

    ````powershell
    Import-Connection -GatewayEndpoint admincenter.ad.adatum.com -fileName $path
    ````

1. Remove the remote PowerShell session.

    ````Powershell
    Remove-PSSession $pSSession
    ````

1. Open **Microsoft Edge** and navigate to <https://admincenter.ad.adatum.com>.

    You should see connections to all servers and clients in Active Directory.

1. Click *Settings*.
1. In Settings, click **Shared Connections**.

    All imported connections are shared connections and are visible for all users.

1. Click **Windows Admin Center** to return to the connections page.
1. Click **vn1-srv1.ad.adatum.com**.

    > The pane Specify your credentials opens, because single sign-on with Kerberos constrained delegation is not configured yet.

1. Either enter the credentials of **ad\Administrator** and click **Continue** or click **Cancel**.

### Task 2: Configure single sign-on

Perform this task on CL1.

1. Open **Terminal**.
1. Export all Windows Admin Center connections to a CSV file.

    ````powershell
    $fileName = '~\Documents\WAC-connections.csv'
    Export-Connection `
        -GatewayEndpoint admincenter.ad.adatum.com -fileName $fileName
    ````

1. Import the CSV file.

    ````powershell
    $wACConnections = Import-Csv -Path $fileName
    ````

    You may want to take a look at the contents of the variable ````$wACConnections````.

1. Get the computer account of the Windows Admin Center gateway and save it in a variable.

    ````powershell
    $wACGatewayComputer = Get-ADComputer -Identity 'VN1-SRV4$'
    ````

1. Allow the computers from the CSV to be delegated to the Windows Admin Center gateway.

    ````powershell
    $wACConnections.name | ForEach-Object { 
        Get-ADComputer -Filter "DNSHostName -eq '$PSItem'" |
        Set-ADComputer -PrincipalsAllowedToDelegateToAccount $wACGatewayComputer
    }
    ````

1. Open **Microsoft Edge** and navigate to <https://admincenter.ad.adatum.com>.
1. Click **vn1-srv1.ad.adatum.com**.

    You should be connected without having to enter additional credentials.

### Task 3: Manage extensions

Perform this task on CL1.

1. Open **Microsoft Edge** and navigate to <https://admincenter.ad.adatum.com>.
1. Click *Settings*.
1. In Settings, click **Extensions**.
1. In Extensions, ensure the switch **Automatically update extension** is enabled.
1. On tab Available Extensions, **Active Directory** and click **Install**.

    Wait for Windows Admin Center to reload.

You may repeat the last step for other extensions at your choice.

### Task 4: Register Windows Admin Center with Azure

Perform this task on CL1.

1. Open **Microsoft Edge** and navigate to <https://admincenter>.
1. In Windows Admin Center, click *Settings*.
1. In Settings, ensure **Account** is selected.
1. In Account, click **Register with Azure**.
1. Under Register with Azure, click **Register**.
1. In the pane Get started with Azure in Windows Admin Center, under **Select an Azure cloud**, ensure **Azure Global** is selected.
1. Under **Copy this code**, click **Copy** and click **Enter the code**.

    In Microsoft Edge, a new tab opens.

1. Under **Enter code**, paste the copied code and click **Next**.
1. Sign in to Microsoft Azure.
1. Under **Are you trying to sign in to Windows Admin Center**, click **Continue**.
1. If you see the message You have signed in to the Windows Admin Center application on your device. You may now close this window, close the tab.
1. In **Windows Admin Center**, on the pane **Get started with Azure in Windows Admin Center**, under **Azure Active Directory (tenant) ID**, ensure the correct ID is selected.

    If you are unsure about the correct ID, perform these steps:

    1. Open a new tab.
    1. Navigate to <https://portal.azure.com> and sign in if necessary.
    1. In the search box at the top, type **Azure Active Directory** and click **Azure Active Directory**.
    1. In Azure Active Directory, on the page **Overview**, take a note of **Tenant ID**.

1. Under **Azure Active Directory application**, ensure **Create new** is selected and click **Connect**.

    Wait for the message **Now connected to Azure AD** to appear.

1. Click **Sign in**.
1. In the dialog Permissions requested, click **Accept**.

### Task 5: Validate access by users

Perform this task on CL2.

1. Open **Microsoft Edge** and navigate to <https://admincenter>.

    > Pia can access Windows Admin Center and sees all connections.

1. Click **vn1-srv1.ad.adatum.com**.

    > The pane Specify your credentials opens, because Ida does not have administrative permissions on this server.

## Exercise 3: Securing Windows Admin Center

1. Restrict access
1. Verify access restrictions
1. Configure role-based access control
1. Verify role-based access control

