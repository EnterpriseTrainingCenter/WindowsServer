# Practice: Verify DHCP functionality

## Required VMs

* VN1-SRV1
* VN1-SRV2
* VN1-SRV3
* VN1-SRV4
* VN1-SRV5
* VN1-SRV6
* VN1-SRV8
* VN1-SRV9
* VN1-SRV10
* CL1

## Task

On all servers on VNet1, except for VN1-SRV6, and CL1, change the IP assignment mode of the network adapter connected to VNet1 to use DHCP.

## Instructions

### PowerShell

Perform this task on CL1.

1. On VN1-SRV1, configure network adapters connected to VNet1 to use DHCP.

    ````powershell
    $computerName = 'vn1-srv1.ad.adatum.com'
    Invoke-Command -ComputerName $computerName -ScriptBlock {
        $interfaceIndex = (
                Get-NetIPAddress -AddressFamily IPv4 |
                Where-Object { $PSItem.IPAddress -like '10.1.1.*' }
            ).InterfaceIndex
        
        Get-NetRoute -InterfaceIndex $interfaceIndex |
        Remove-NetRoute -Confirm:$false
        
        Set-NetIPInterface -InterfaceIndex $interfaceIndex -Dhcp Enabled
        
        Set-DnsClientServerAddress `
            -InterfaceIndex $interfaceIndex -ResetServerAddresses
    }
    ````

1. Verify the IP configuration.

    ````powershell
    Get-NetIPConfiguration -ComputerName $computerName
    ````

    At least one network adapter should haven an IP address in the 10.1.1.0 subnet, the IPv4DefaultGateway of 10.1.1.1 and DNSServer of 10.1.1.8.

    If the command fails, try again after a few minutes. If it still fails, sign in to the server and execute

    ````shell
    ipconfig.exe /renew
    ````

Repeat these steps for VN1-SRV2, VN1-SRV3, VN1-SRV4, VN1-SRV5, VN1-SRV8, VN1-SRV9,VN1-SRV10, and CL1. To save time, you may define $computerName as an array, like

````powershell
$computerName = @(
        'VN1-SRV1'
        'VN1-SRV2'
        'VN1-SRV3'
        'VN1-SRV4'
        'VN1-SRV5'
        'VN1-SRV8'
        'VN1-SRV9'
        'VN1-SRV10'
        'CL1'
    )
````

If you used an array for ````$computerName````, you must change verification command like this:

````powershell
$computerName | ForEach-Object { Get-NetIPConfiguration -ComputerName $PSItem }
````

### SConfig

Perform this task on VN1-SRV1, VN1-SRV2, VN1-SRV3, VN1-SRV4, and VN1-SRV5.

1. Sign in as **ad\Administrator**.
1. On **VN1-SRV1** only: Start **SConfig**.

    ````shell
    sconfig
    ````

1. In SConfig, enter **8**.
1. In Network settings, enter the **Index#** of a network adapter with an **IP address** starting with **10.1.1**.
1. In Network Adapter Settings, enter **1**.
1. Enter **D**.
1. On **VN1-SRV2**, **VN1-SRV3**, **VN1-SRV4**, **VN1-SRV5**, and **VN1-SRV7**: Under a number auf success messages, press ENTER.
1. Enter **4**.

    If the server has more than one network adapter connected to VNet1, repeat from step 3.

1. In **SConfig**, enter **15**.
1. Verify the IP configuration.

    ````shell
    ipconfig.exe /all
    ````

    The network adapters should have received the IP address you added as reservation. The DNS Servers should be 10.1.1.8 and the Default Gateway should be 10.1.1.1. If the IP address is an APIPA address, try to execute

    ````shell
    ipconfig.exe /renew
    ````

1. Sign out.

    ````shell
    logoff.exe
    ````

### Desktop experience (Windows Server 2022 Desktop Experience)

Perform this task on VN1-SRV8, VN1-SRV9, and VN1-SRV10.

1. Sign in as **.\Administrator**.
1. In **Server Manager**, click **Local Server**.

    Under Local Server, under **PROPERTIES**, take a note of all network adapters with an IP address starting with **10.1.1**.

1. Click any IP address.
1. In Network Connections, in the context-menu of the network adapter, you took note of before, click **Properties**.
1. In network adapter's Properties, click **Internet Protocol Version 4 (TCP/IPv4)** and click **Properties**.
1. In Internet Protocol Version 4 (TCP/IPv4) Properties, click **Obtain an IP address automatically** and **Obtain DNS server address automatically**. Click **OK**.
1. In network adapter's **Properties**, click **Close**.
1. Double-click the ntwork adpater you just configured.
1. In network adapter's status, click **Details...**

    The network adapters should have received the IP address you added as reservation. The DNS Servers should be 10.1.1.8 and the Default Gateway should be 10.1.1.1. If the IP address is an APIPA address, try to execute

    ````shell
    ipconfig.exe /renew
    ````

    For other network adapters connected to VNet1, repeat from step 4.

1. Sign out.

### Desktop experience (Windows 11)

Perform this task on CL1.

1. Sign in as **ad\Administrator**.
1. Open **Settings**.
1. In Settings, click **Network & internet**.
1. In Network & internet, click **Ethernet**.
1. In Ethernet, right to **IP assignment**, click **Edit**.
1. In Edit IP settings, in the drop-down at the top, click **Automatic (DHCP)** and click **Save**.

    In **Ethernet**, verify that the computer has received an IP address in the 10.1.1.0 subnet (such as 10.1.1.2) and IPv4 DNS servers of 10.1.1.8.

### Windows Admin Center

Perform this task on CL1.

1. Open **Microsoft Edge**
1. In Microsoft Edge, navigate to <https://admincenter>
1. In Windows Admin Center, click **vn1-srv1.ad.adatum.com**.
1. Connected to vn1-srv1.ad.adatum.com, under **Tools**, click **Networks**.
1. Under Networks, click any network with an **IPv4 Address** starting with **10.1.1** and click **Settings**
1. Under IPv4, click **Obtain an IP address automatically** and **Obtain DNS server address automatically**. Click **Save**.
1. In the message box Update IPv4 settings for ..., click **Yes**.
1. In the top-left corner, click **Windows Admin Center**.
1. In Windows Admin Center, click **vn1-srv1.ad.adatum.com** again.

    If the connection fails, try again after a few minutes. If it still fails, sign in to the server and execute

    ````shell
    ipconfig.exe /renew
    ````

1. Connected to vn1-srv1.ad.adatum.com, under **Tools**, click Networks.
1. Under Networks, click the network you just configured.

    In the bottom pane, verify that IPv4 DHCP is set to Yes, the computer has obtained the IP address you added as reservation, the IPv4 Default Gateway is 10.1.1.1 and the IPv4 DNS Servers is 10.1.1.8.

1. In the top-left corner, click **Windows Admin Center**.

Repeat from step 3 for VN1-SRV2, VN1-SRV3, VN1-SRV4, VN1-SRV5, VN1-SRV8, VN1-SRV9,VN1-SRV10, and CL1.

To add CL1 to the Windows Admin Center, on the connections page, click **Add**. In the pane Add or create resources, under **Windows PCs**, click **Add**. In **Computer name**, type **CL1** and click **Add**.
