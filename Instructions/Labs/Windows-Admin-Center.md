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

1. On **CL2**, sign in as **ad\Pia**.
1. On **VN1-SRV4**, sign in as **ad\Administrator**.
1. In SConfig, enter **15**.

You need sign-in credentials for an active Azure Subscription in which you have the permissions to create resources. Moreover, you need to have the Global Administrator role in Azure Active Directory. Ask your instructor for help, if you are unsure.

## Introduction

Adatum wants to use Windows Admin Center for server management. After installing Windows Admin Center, Adatum wants to add all computers efficiently. Moreover, Adatum wants to integrate Windows Admin Center with Azure. Furthermore, Adatum wants to restrict access to Windows Admin Center and wants some users in IT to manage servers through Windows Admin Center without granting adding them to the local Administrators group.

## Exercises

1. [Installing Windows Admin Center](#exercise-1-installing-windows-admin-center)
1. [Configuring Windows Admin Center](#exercise-2-configuring-windows-admin-center)
1. [Securing Windows Admin Center](#exercise-3-securing-windows-admin-center)

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

    > Can Pia access Windows Admin Center and see all shared connections?

    > Can Pia get administrative access to a server through Windows Admin Center?

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

    > The pane Specify your credentials opens, because Pia does not have administrative permissions on this server.

## Exercise 3: Securing Windows Admin Center

1. [Create groups in Active Directory](#task-1-create-groups-in-active-directory): Create an organizational unit named Entitling groups and, within that organizational unit, the domain-local groups Windows Admin Center users and Windows Admin Center administrators.
1. [Restrict access to Windows Admin Center](#task-2-restrict-access-to-windows-admin-center) with local Administrators and the domain group Windows Admin Center administrators as Gateway administratory and the domain group Windows Admin Center users as Gateway users only
1. [Verify access by unauthorized user](#task-3-verify-access-by-unauthorized-user)

    > Can Pia access Windows Admin Center?

1. [Verify access by authorized user](#task-4-verify-access-by-authorized-user)

    > Can Ida access Widnows Admin Center?

    > Can Ida connect to VN1-SRV5 using Windows Admin Center?

1. [Apply role-based access control](#task-5-apply-role-based-access-control) to server VN1-SRV5 allowing members of the domain group IT to administer the server
1. [Verify role-based access control](#task-6-verify-role-based-access-control)

    > Can Ida connect to VN1-SRV5 using Windows Admin Center?

    > Can Ida stop and start services on VN1-SRV5 using Windows Admin Center?

    > Can Ida stop and start services on VN1-SRV5 using PowerShell?

### Task 1: Create groups in Active Directory

#### Active Directory Users and Computer

Perform this task on CL1.

1. From the desktop, open **Active Directory Users and Computers**.
1. In Active Directory Users and Computer, expand **ad.adatum.com**.
1. In the context-menu of **ad.adatum.com**, click **New**, **Organizational Unit**.
1. In New Object - Organizational Unit, in **Name**, enter **Entitling groups** and click **OK**.
1. In Active Directory Users and Computers, in the left pane, click **Entitling Groups**.
1. In the context-menu of Entitling Groups, click **New**, **Group**.
1. In New Object - Group, in **Group name**, type **Windows Admin Center users**. **Group scope** should be **Domain local** and **Group type** should be **Security**. Click **OK**.
1. In the context-menu of Entitling Groups, click **New**, **Group**.
1. In New Object - Group, in **Group name**, type **Windows Admin Center administrators**. **Group scope** should be **Domain local** and **Group type** should be **Security**. Click **OK**.
1. Double-click the group **Windows Admin Center users**.
1. In Windows Admin Center users Properties, click the tab **Members**.
1. On the tab Members, click **Add...**.
1. In Select Groups, Contacts, Computers, Service Accounts, or Groups, in **Enter the object names to select**, type **IT** and click **OK**.
1. In Windows Admin Center users Properties, on the tab **Members**, click **OK**.

#### Active Directory Administrative Center

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In the context-menu of **ad (local)**, click **New**, **Organizational Unit**.
1. In Create Organizational Unit, in **Name**, type **Entitling groups** and click **OK**.
1. In Active Directory Administrative Center, double-click **Entitling groups**.
1. In the pane **Tasks**, click **New**, **Group**.
1. In Create Group, in **Group name**, type **Windows Admin Center users**.
1. Under **Group scope**, click **Domain local**.
1. On the left, click **Members**.
1. Under Members, click **Add...**.
1. In Select Groups, Contacts, Computers, Service Accounts, or Groups, in **Enter the object names to select**, type **IT** and click **OK**.
1. In Create Group: Windows Admin Center users, click **OK**.
1. In Active Directory Administrative Center, under Entitling Groups, in the pane **Tasks**, click **New**, **Group**.
1. In Create Group, in **Group name**, type **Windows Admin Center administrators**. Under **Group scope**, click **Domain local**. Click **OK**.

#### Windows Admin Center

Perform this task on CL1.

1. Using Microsoft edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **VN1-SRV1.ad.adatum.com**.
1. Connected to VN1-SRV1.ad.adatum.com, under Tools, click **Active Directory**.
1. Under Active Directory Domain Services, click the tab **Browse**.
1. Click **DC=ad, DC=adatum, DC=com**.
1. In the right pane, click **Create**, **OU**.
1. In the pane Add Organizational Unit, in **Name**, enter **Entitling groups** and click **Create**.
1. In the tree pane, click **Entitling Groups**.
1. In the right pane, click **Create**, **Group**.
1. In the pane Add Group, in **Name**, type **Windows Admin Center users**.
1. Under **Group Scope**, ensure **Domain local** is selected.
1. In **Sam Account Name**, type **Windows Admin Center users**.
1. Right to **Create in**, click **Change...**.
1. In Select Path, click **Entitling Groups**.
1. Click **Select**.
1. Click **Create**.
1. In the right pane, left to the search box, click the icon *Refresh*.
1. Click the group **Windows Admin Center users**.
1. Click **Properties**.
1. In Group properties: Windows Admin Center users, on the left, click **Membership**.
1. Click **Add**.
1. In the pane Add Group Membership, under **User SamAccountname**, type **IT** and click **Add**.
1. Click **Save**.
1. Click **Close**.
1. In the right pane, click **Create**, **Group**.
1. In the pane Add Group, in **Name**, type **Windows Admin Center administrators**.
1. Under **Group Scope**, ensure **Domain local** is selected.
1. In **Sam Account Name**, type **Windows Admin Center users**.
1. Right to **Create in**, click **Change...**.
1. In Select Path, click **Entitling Groups**.
1. Click **Select**.
1. Click **Create**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Create the organizational unit **Entitling groups** at the domain level.

    ````powershell
    $aDorganizationalUnit = New-ADOrganizationalUnit `
        -Path 'DC=ad, DC=adatum, DC=com' `
        -Name 'Entitling groups' `
        -PassThru
    ````

1. In the new organizational unit, create the group **Windows Admin Center users** and add the group **IT** as member.

    ````powershell
    New-ADGroup `
        -Name 'Windows Admin Center users' `
        -Path $aDorganizationalUnit.DistinguishedName `
        -GroupScope DomainLocal `
        -PassThru | 
    Add-ADGroupMember -Members 'IT'
    ````

1. In the new organizational unit, create the group **Windows Admin Center administrators**.

    ````powershell
    New-ADGroup `
        -Name 'Windows Admin Center administrators' `
        -Path $aDorganizationalUnit.DistinguishedName `
        -GroupScope DomainLocal
    ````

### Task 2: Restrict access to Windows Admin Center

Perform this task on CL1.

1. Using Microsoft edge, navigate to <https://admincenter>.
1. Click *Settings*.
1. In Settings, click **Access**.
1. Under Gateway Access, under **Allowed Groups**, click **Add**.
1. In the pane Add an allowed group, under **Name**, type **ad\Windows Admin Center users**. Under **Role**, ensure **Gateway users** is selected. Under **Type**, ensure **Gateway users security group** is selected. Click **Save**.

    Note: You can ignore the error message regarding the invalid group name format.

1. Under Gateway Access, under **Allowed Groups**, click **Add**.
1. In the pane Add an allowed group, under **Name**, type **ad\Windows Admin Center administrators**. Under **Role**, click **Gateway administrators**. Under **Type**, ensure **Gateway users security group** is selected. Click **Save**.
1. Under Gateway Access, under **Allowed Groups** click **BUILTIN\Users** and click **Delete**.
1. In the message box Delete, click **Yes**.

### Task 3: Verify access by unauthorized user

Perform this task on CL2.

1. Open **Microsoft Edge** and navigate to <https://admincenter>.

    > Pia cannot access Windows Admin Center anymore.

1. Sign out.

### Task 4: Verify access by authorized user

Perform this task on CL2.

1. Sign in as **ad\Ida**.
1. Open **Microsoft Edge** and navigate to <https://admincenter>.

    > Ida can access Windows Admin Center and sees all connections.

1. Click **vn1-srv5.ad.adatum.com**.

    > The pane Specify your credentials opens, because Ida does not have administrative permissions on this server.

### Task 5: Apply role-based access control

Perform this task on CL1.

1. Open **Microsoft Edge** and navigate to <https://admincenter>.
1. In Windows Admin Center, click **vn1-srv5.ad.adatum.com**.
1. Connected to vn1-srv5.ad.adatum.com, under **Tools**, click **Firewall**.
1. Under Firewall, click **File and Printer Sharing (SMB-in)** and click **Enable**.
1. Under **Tools**, click **Settings**.

1. In Settings, click **Role-based Access control**.
1. Under Role-based access control, click **Apply**.
1. In message box Restart the WinRM service, click **Yes**.

    Wait a few minutes, refresh the page, until the status of **Role-based access control** changes to **Applied**. It can take up to 10 minutes until this happens.

1. Under **Tools, click **Local users & groups**.
1. Under Local users and groups, click the tab **Groups**.
1. On the tab Groups, click **Windows Admin Center Administrators**.
1. In the bottom pane, Details - Windows Admin Center Administrators, click **Add user**.
1. In Add a user to the Windows Admin Center Administrators group, under **Username**, type **ad\IT** and click **Submit**.

### Task 6: Verify role-based access control

Perform this task on CL2.

1. Open **Microsoft Edge** and navigate to <https://admincenter>.
1. Click **vn1-srv5.ad.adatum.com**.

    > Ida can connect to the server, because she is member of the Windows Admin Center administrators group on that server.

    Note: If a Specify your credentials pane appears at any time, enter the credentials of **ad\Ida**.

1. Under **Tools**, click **Services**.
1. Under Services, click **W32Time** and click **Stop**.

    > Ida can stop the service.

1. Click **Start**.
