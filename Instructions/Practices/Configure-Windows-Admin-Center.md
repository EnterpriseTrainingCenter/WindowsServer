# Practice: Configure Windows Admin Center

## Required VMs

* VN1-SRV1
* VN1-SRV4
* CL1

## Task

On CL1, add the URL https://admincenter.ad.adatum.com to the Local intranet zone. In Windows Admin Center, add connections to all servers starting with VN and PM, and to all clients starting with CL. Furthermore, install the Active Directory extension.

## Instructions

### Desktop experience/Windows Admin Center

Perform these steps on CL1.

1. Sign in as **ad\Administrator**.
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
1. In Windows Admin Center, on the connections page, click **Add**.
1. In the panel **Add or create resources**, under **Server**, click **Add**.
1. In the panel, click the tab **Search Active Directory**.
1. On tab Search Active Directory, enter **VN\*** and click **Search**.
1. To the left of the column header **Name** activate the checkbox to select all servers starting with VN and click **Add**.
1. In Windows Admin Center, on the connections page, click **Add**.
1. In the panel **Add or create resources**, under **Server**, click **Add**.
1. In the panel, click the tab **Search Active Directory**.
1. On tab Search Active Directory, enter **PM\*** and click **Search**.
1. To the left of the column header **Name** activate the checkbox to select all servers starting with PM and click **Add**.
1. In Windows Admin Center, on the connections page, click **Add**.
1. In the panel **Add or create resources**, under **Windows PCs**, click **Add**.
1. In the panel, click the tab **Search Active Directory**.
1. On tab Search Active Directory, enter **CL\*** and click **Search**.
1. To the left of the column header **Name** activate the checkbox to select all computers starting with CL and click **Add**.
1. In Windows Admin Center, click the icon **Settings**.
1. In Settings, click **Extensions**.
1. On tab Available extensions, click **Active Directory** and click **Install**.

### PowerShell

Perform these steps on CL1.

1. Sign in as **ad\Administrator**.
1. Open **Terminal**.
1. In Terminal, add https://admincenter.ad.adatum.com to the Intranet Zone.

    ````powershell
    $path = `
        'HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet Settings\ZoneMap\Domains\admincenter.ad.adatum.com'
    New-Item -Path $path -Force
    New-ItemProperty `
        -Path $path -Name 'https' -Value 1 -PropertyType DWORD -Force
    ````

1. Create a CSV file with all server computers.

    ```powershell
    $path = '~\Documents\computers.csv'
    Get-ADComputer -Filter { Name -like 'VN*-*' -or Name -like 'PM-*' } |
    Select-Object `
        @{ name = 'name'; expression = { $PSItem.DNSHostName } }, `
        @{
            name = 'type'
            expression = { 'msft.sme.connection-type.server' }
        }, `
        @{ name = 'tags'; expression = {} }, `
        @{ name = 'groupId'; expression = { } } |
    Export-Csv -Path $path -NoTypeInformation -Force

    ```

1. Append all client computers to the CSV file.

    ```powershell
    Get-ADComputer -Filter { Name -like 'CL*' } |
    Select-Object `
        @{ name = 'name'; expression = { $PSItem.DNSHostName } }, `
        @{ 
            name = 'type'
            expression = { 'msft.sme.connection-type.windows-client' }
        }, `
        @{ name = 'tags'; expression = {} }, `
        @{ name = 'groupId'; expression = { } } |
    Export-Csv -Path $path -Append

    ```

1. Copy the Windows Admin Center modules to the client.

    ```powershell
    $pSSession = New-PSSession -ComputerName VN1-SRV4
    Copy-Item `
        -FromSession $pSSession `
        -Path `
            "$env:ProgramFiles\Windows Admin Center\PowerShell\Modules\*\" `
        -Destination '~\Documents\WindowsPowerShell\' `
        -Container `
        -Recurse `
        -Force
    Remove-PSSession $pSSession
    ```

1. Import connections.

    ```powershell
    Import-Connection `
        -GatewayEndpoint https://admincenter.ad.adatum.com `
        -fileName $path
    ```

1. Open **Microsoft Edge**.
1. In Microsoft Edge, navigate to **https://admincenter.ad.adatum.com**.
1. In Windows Admin Center, click the icon **Settings**.
1. In Settings, click **Extensions**.
1. On tab Available extensions, click **Active Directory** and click **Install**.
