# Practice: Add DHCP reservations

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
* CL1

## Task

For all servers in VNet1, create reservations on VN1-SRV6.

## Instructions

Perform this task on CL1.

1. Open **Terminal**.
1. For all computers starting with **VN1-SRV*** get the MAC addresses and IP addresses of all net adapters on VNet1.

    ````powershell
    $computerName = (Get-ADComputer -Filter 'Name -like "VN1-SRV*"').DNSHostName
    
    $vNet1ServerAddresses = Invoke-Command -ComputerName $computerName -ScriptBlock { 
        Get-NetAdapter | ForEach-Object { 
            # Save macAddress and interfaceAlias for later use
            $macAddress = $PSItem.MacAddress
            $interfaceAlias = $PSItem.InterfaceAlias
            

            Get-NetIPAddress -InterfaceIndex $PSItem.InterfaceIndex |
            Where-Object { $PSItem.IPAddress -like '10.1.1.*' } | 
            Select-Object `
                @{ 
                    Name = 'InterfaceAlias'
                    Expression = { $interfaceAlias } 
                }, `
                @{
                    Name = 'ClientId'
                    Expression = { $macAddress } 
                }, `
                IPAddress
        } 
    } |
    Select-Object `
        @{ 
            Name = 'Name'
            Expression = { $PSItem.PSComputerName } 
        },
        InterfaceAlias,
        ClientId,
        IPAddress |
    Sort-Object Name, IPAddress

    $vNet1ServerAddresses |
    Format-Table Name, InterfaceAlias, ClientId, IPAddress
    ````

    You receive a table of computer names, MAC addresses, and IP addresses, similar to the table below.

    ````shell
    Name                    InterfaceAlias ClientId          IPAddress
    ----                    -------------- --------          ---------
    VN1-SRV1.ad.adatum.com  Ethernet       00-15-5D-00-01-08 10.1.1.8
    VN1-SRV10.ad.adatum.com VNet1          00-15-5D-00-01-50 10.1.1.80
    VN1-SRV2.ad.adatum.com  Ethernet       00-15-5D-00-01-10 10.1.1.16
    VN1-SRV3.ad.adatum.com  Ethernet       00-15-5D-00-01-18 10.1.1.24
    VN1-SRV4.ad.adatum.com  VNet1          00-15-5D-00-01-20 10.1.1.32
    VN1-SRV5.ad.adatum.com  VNet1          00-15-5D-00-01-28 10.1.1.40
    VN1-SRV6.ad.adatum.com  VNet1          00-15-5D-00-01-30 10.1.1.48
    VN1-SRV7.ad.adatum.com  VNet1          00-15-5D-00-01-38 10.1.1.56
    VN1-SRV8.ad.adatum.com  VNet1          00-15-5D-00-01-40 10.1.1.64
    VN1-SRV9.ad.adatum.com  VNet1          00-15-5D-00-01-48 10.1.1.72
    ````

    Leave **Terminal** open.

For the rest of the practice, choose your favorite tool.

### Desktop experience

1. Open **DHCP**.
1. In DHCP, in the context-menu of **DHCP**, click **Add Server...**
1. In Add Server, under **This server**, type **VN1-SRV6** and click **OK**.
1. In **DHCP**, expand  **vn1-srv6.ad.adatum.com**, **IPv4**., **Scope [10.1.1.0] VNet1**, and click **Reservations**.

Perform these steps for each line of the table in Terminal.

1. In the context-menu of Reservations, click **New Reservation...**
1. In New Reservation, in **Reservation name**, type the value of **PSComputerName**. In **IP address**, type the value of **IPAddress**. In **MAC address**, type the value of **MacAddress**. Click **Add**.

### Windows Admin Center

1. Open **Microsoft Edge**
1. In Microsoft Edge, navigate to <https://admincenter>
1. In Windows Admin Center, click **vn1-srv6.ad.adatum.com**.
1. Connected to vn1-srv6.ad.adatum.com, under **Tools**, click **DHCP**.
1. In **DHCP**, click **VNet1 [10.1.1.0]**.

Perform these steps for each line of the table in Terminal.

1. In VNet1 [10.1.1.0], under **Address reservations**, click **New reservation**.
1. In the panel Create a new reservation, under **Reservation name**, type the value of **PSComputerName**. Under **IP address**, type the value of **IPAddress**. In **MAC address**, type the value of **MacAddress**. Click **Create**.

### PowerShell

1. In **Terminal**, on **VN1-SRV6** in scope **10.1.1.0**, create the reservations from the variable created earlier.

    ````powershell
    $computerName = 'vn1-srv6'
    $scopeId = '10.1.1.0'
    $vNet1ServerAddresses |
    Add-DhcpServerv4Reservation -ComputerName $computerName -ScopeId $scopeId
    ````

    Note: If you have created reservations previously, you may receive error messages. You can safely ignore them.

1. Verify the reservations.

    ````powershell
    Get-DhcpServerv4Reservation -ComputerName $computerName -ScopeId $scopeId
    ````

    You should receive an output similar to the table below.

    ````shell
    IPAddress            ScopeId              ClientId             Name                 Type                 Description
    ---------            -------              --------             ----                 ----                 -----------
    10.1.1.8             10.1.1.0             00-15-5d-00-01-08    VN1-SRV1.ad.adatu... Both
    10.1.1.16            10.1.1.0             00-15-5d-00-01-10    VN1-SRV2.ad.adatu... Both
    10.1.1.32            10.1.1.0             00-15-5d-00-01-20    VN1-SRV4.ad.adatu... Both
    10.1.1.40            10.1.1.0             00-15-5d-00-01-28    VN1-SRV5.ad.adatu... Both
    10.1.1.64            10.1.1.0             00-15-5d-00-01-40    VN1-SRV8.ad.adatu... Both
    10.1.1.56            10.1.1.0             00-15-5d-00-01-38    VN1-SRV7.ad.adatu... Both
    10.1.1.80            10.1.1.0             00-15-5d-00-01-50    VN1-SRV10.ad.adat... Both
    10.1.1.24            10.1.1.0             00-15-5d-00-01-18    VN1-SRV3.ad.adatu... Both
    10.1.1.48            10.1.1.0             00-15-5d-00-01-30    VN1-SRV6.ad.adatu... Both
    10.1.1.72            10.1.1.0             00-15-5d-00-01-48    VN1-SRV9.ad.adatu... Both
    ````
