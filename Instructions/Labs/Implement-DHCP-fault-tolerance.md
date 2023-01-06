# Lab: Implement DHCP fault-tolerance

## Required VMs

* VN1-SRV1
* VN1-SRV6
* VN1-SRV7
* VN2-SRV1
* VN2-SRV2
* CL1
* CL2

## Setup

On CL1, CL2 and VN2-SRV1, sign in as **ad\Administrator**.

## Introduction

Because most of Adatum's computers, including servers, now rely on DHCP, you need to provide a highly available service. Moreover, Adatum needs too implement DHCP on VNet2.

Because you are not allowed to configure an additional DHCP server on VNet2 and communication between VNet1 and VNet2 is unreliable, DHCP failover cannot be used. Therefore, you need split the scope for VNet2 and rely on DHCP relay to provide fault-tolerance.

For VNet1, you already configured an additional DHCP server. You must configure DHCP failover to provice fault-tolerance for this subnet.

## Exercises

1. [Implementing DHCP on a subnet](#exercise-1-implementing-dhcp-on-a-subnet)
1. [Implementing DHCP relay](#exercise-2-implement-dhcp-relay)
1. [Implementing DHCP failover](#exercise-3-implement-dhcp-failover)

## Exercise 1: Implementing DHCP on a subnet

1. [Add a DHCP scope](#task-1-add-a-dhcp-scope) for VNet2
1. [Add DHCP reservations](#task-2-add-dhcp-reservations) for VNet2
1. [Authorize DHCP server and activate scope](#task-3-authorize-dhcp-server-and-activate-scope) for VNet2
1. [Verify DHCP functionality](#task-4-verify-dhcp-functionality) on VNet2

### Task 1: Add a DHCP scope

Perform this task on CL1.

1. Open **DHCP**.
1. In DHCP, in the context-menu of **DHCP**, click **Add Server...**
1. In Add Server, under **This server**, type **VN2-SRV2"** and click **OK**.
1. In **DHCP**, expand  **vn2-srv2.ad.adatum.com**, and click **IPv4**.
1. In the context-menu of **IPv4**, click **New Scope...**
1. In the New Scope Wizard, on page Welcome to the New Scope Wizard, click **Next >**.
1. On page Scope Name, in **Name**, type **VNet2** and click **Next >**.
1. On page IP Address Range, in **Start IP address**, type **10.1.2.2**. In **End IP address**, type **10.1.2.254**. In **Length**, type **24**. Click **Next >**

    > The remaining IP addresses of the subnet will be used in another scope on another DHCP server to implement fault-tolerance later.

1. On page Add Exclusions and Delay, click **Next >**.
1. On page Lease Duration, in **Days**, type **0**, in **Hours**, type **2**, and click **Next >**.
1. On page Configure DHCP options, ensure **Yes, I want to configure these options now** is selected, and click **Next >**.
1. On page Router (Default Gateway), under **IP address**, type **10.1.2.1**, click **Add** and click **Next >**.
1. On page Domain Name and DNS Servers, In **Parent domain**, delete the text (leave it blank). Under **IP address**, click **10.1.1.8** and click **Remove**. Click **Next >**.
1. On page WINS Servers, click **Next >**.
1. On page Activate Scope, click **No, I will activate this scope later** and click **Next >**.
1. On Completing the New Scope Wizard, click **Finish**.

### Task 2: Add DHCP reservations

Perform this task on CL1.

1. Open **Terminal**.
1. For all computers starting with **VN2-SRV*** get the MAC addresses and IP addresses of all net adapters on VNet1.

    ````powershell
    $computerName = (Get-ADComputer -Filter 'Name -like "VN2-SRV*"').DNSHostName
    
    $vNet2ServerAddresses = Invoke-Command -ComputerName $computerName -ScriptBlock { 
        Get-NetAdapter | ForEach-Object { 
            # Save macAddress and interfaceAlias for later use
            $macAddress = $PSItem.MacAddress
            $interfaceAlias = $PSItem.InterfaceAlias
            

            Get-NetIPAddress -InterfaceIndex $PSItem.InterfaceIndex |
            Where-Object { $PSItem.IPAddress -like '10.1.2.*' } | 
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
        IPAddress

    Sort-Object Name, IPAddress

    $vNet2ServerAddresses |
    Format-Table Name, InterfaceAlias, ClientId, IPAddress
    ````

    You receive a table of computer names, MAC addresses, and IP addresses, similar to the table below.

    ````shell
    Name                   InterfaceAlias ClientId          IPAddress
    ----                   -------------- --------          ---------
    VN2-SRV1.ad.adatum.com Ethernet       00-15-5D-00-02-08 10.1.2.8
    VN2-SRV2.ad.adatum.com Ethernet       00-15-5D-00-02-10 10.1.2.16
    ````

    Leave **Terminal** open.

For the rest of the practice, choose your favorite tool.

#### Desktop experience

1. Open **DHCP**.
1. In DHCP, in the context-menu of **DHCP**, click **Add Server...**
1. In Add Server, under **This server**, type **VN2-SRV2** and click **OK**.
1. In **DHCP**, expand  **vn2-srv2.ad.adatum.com**, **IPv4**., **Scope [10.1.2.0] VNet2**, and click **Reservations**.

Perform these steps for each line of the table in Terminal.

1. In the context-menu of Reservations, click **New Reservation...**
1. In New Reservation, in **Reservation name**, type the value of **PSComputerName**. In **IP address**, type the value of **IPAddress**. In **MAC address**, type the value of **MacAddress**. Click **Add**.

#### Windows Admin Center

1. Open **Microsoft Edge**
1. In Microsoft Edge, navigate to <https://admincenter>
1. In Windows Admin Center, click **vn2-srv2.ad.adatum.com**.
1. Connected to vn2-srv2.ad.adatum.com, under **Tools**, click **DHCP**.
1. In **DHCP**, click **VNet2 [10.1.2.0]**.

Perform these steps for each line of the table in Terminal.

1. In Vnet [10.1.2.0], under **Address reservations**, click **New reservation**.
1. In the panel Create a new reservation, under **Reservation name**, type the value of **PSComputerName**. Under **IP address**, type the value of **IPAddress**. In **MAC address**, type the value of **MacAddress**. Click **Create**.

#### PowerShell

1. In **Terminal**, on **VN2-SRV2** in scope **10.1.2.0**, create the reservations from the variable created earlier.

    ````powershell
    $computerName = 'vn2-srv2'
    $scopeId = '10.1.2.0'
    $vNet2ServerAddresses |
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
    10.1.2.16            10.1.2.0             00-15-5d-00-02-10    VN2-SRV2.ad.adatu... Both
    10.1.2.8             10.1.2.0             00-15-5d-00-02-08    VN2-SRV1.ad.adatu... Both
    ````

### Task 3: Authorize DHCP server and activate scope

#### Desktop experience

Perform this task on CL1.

1. Open **DHCP**.
1. In DHCP, in the context-menu of **DHCP**, click **Add Server...**
1. In Add Server, under **This server**, type **VN2-SRV2** and click **OK**.
1. In **DHCP**, expand **vn2-srv2.ad.adatum.com**, **IPv4**., **Scope [10.1.2.0] VNet2**.
1. In the context-menu of **vn2-srv2.ad.adatum.com**, click **Authorize**.
1. In the context-menu of **DHCP**, click **Manage authorized servers...**

    In Manage Authorized Servers, verify that vn2-srv2.ad.adatum.com is listed.

1. Click **Close**.
1. In **DHCP**, in the context-menu of **Scope [10.1.2.0] VNet2**, click **Activate**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Add **vn2-srv2.ad.adatum.com** to the list of autorized DHCP server services in Active Directory.

    ````powershell
    Add-DhcpServerInDC -DnsName vn2-srv2.ad.adatum.com
    ````

1. Verify the autorization of vn2-srv2.ad.adatum.com.

    ````powershell
    Get-DhcpServerInDC
    ````

    This should list vn2-srv2.ad.adatum.com.

1. Activate the scope **10.1.2.0** on **VN2-SRV2**.

    ````powershell
    $computerName = 'VN2-SRV2'
    $scopeId = '10.1.2.0'
    Set-DhcpServerv4Scope `
        -ComputerName $computerName -ScopeId $scopeId -State Active
    ````

1. Verify the state of the scope.

    ````powershell
    Get-DhcpServerv4Scope -ComputerName $computerName -ScopeId $scopeId
    ````

    The scope schould be listed with a State of Active.

### Task 4: Verify DHCP functionality

#### PowerShell

Perform this task on CL2.

1. Configure network adapters connected to VNet2 to use DHCP.

    ````powershell
    $interfaceIndex = (
            Get-NetIPAddress -AddressFamily IPv4 |
            Where-Object { $PSItem.IPAddress -like '10.1.2.*' }
        ).InterfaceIndex
        
    Get-NetRoute -InterfaceIndex $interfaceIndex |
    Remove-NetRoute -Confirm:$false
        
    Set-NetIPInterface -InterfaceIndex $interfaceIndex -Dhcp Enabled
        
    Set-DnsClientServerAddress `
        -InterfaceIndex $interfaceIndex -ResetServerAddresses
    ````

1. Verify the IP configuration.

    ````powershell
    Get-NetIPConfiguration
    ````

    At least one network adapter should haven an IP address in the 10.1.2.0 subnet, the IPv4DefaultGateway of 10.1.2.1 and DNSServer of 10.1.1.8.

    If the command fails, try again after a few minutes. If it still fails, execute

    ````shell
    ipconfig.exe /renew
    ````

#### Desktop experience

Perform this task on CL2.

1. Sign in as **ad\Administrator**.
1. Open **Settings**.
1. In Settings, click **Network & internet**.
1. In Network & internet, click **Ethernet**.
1. In Ethernet, right to **IP assignment**, click **Edit**.
1. In Edit IP settings, in the drop-down at the top, click **Automatic (DHCP)** and click **Save**.

    In **Ethernet**, verify that the computer has received an IP address in the 10.1.2.0 subnet (such as 10.1.2.2) and IPv4 DNS servers of 10.1.1.8.

## Exercise 2: Implement DHCP relay

1. Split DHCP scope for VNet2 and configure 20 % of the available range on VN1-SRV6

    > Why do you not configure a delay for the backup DHCP scope?

1. [Install the Routing and Remote Access role](#task-2-install-the-routing-and-remote-access-role) on VN2-SRV1
1. [Configure DHCP relay](#task-3-configure-dhcp-relay) for VNet2 on VN2-SRV1

    Note: In real-world, this functionality typically is configured on the router. For the purpose of this lab, you will configure it on a Windows server.

1. [Verify fault-tolerance for DHCP](#task-4-verify-fault-tolerance-for-dhcp) on VNet2

### Task 1: Split DHCP scope

Perform this task on CL1.

1. Open **DHCP**.
1. In DHCP, in the context-menu of **DHCP**, click **Add Server...**
1. In Add Server, under **This server**, type **VN2-SRV2"** and click **OK**.
1. In **DHCP**, expand  **vn2-srv2.ad.adatum.com**, **IPv4**, and click **Scope [10.1.2.0] VNet2**.
1. In the context menu of **Scope [10.1.2.0] VNet2**, click **Advanced...**, **Split Scope**.
1. In Dhcp Split-Scope Configuration Wizard, on page DHCP Split-Scope, click **Next >**.
1. On page Additional DHCP Server, click **Add Server**.
1. In Add Server, click **This authorized DHCP server**. Click **vn1-srv6.ad.adatum.com** and click **OK**.
1. In **Dhcp Split-Scope Configuration Wizard**, on page **Additional DHCP Server**, click **Next >**.
1. On page Percentage of Split, beside **Percentage of IPv4 Addresses service**, under **Host DHCP Server**, ensure **80** is filled in, and, under **Added DHCP server**, **20** is filled in, and click **Next >**.

    Under Following is the Exclusion IPv4 Address Range, the Start IPv4 Address on the Host DHCP server should be 10.1.2.204. The End IPv4 Address under Added DHCP Server should be 10.1.2.203.

1. On page Delay in DHCP Offer, leave the value at **0**.

    > The delay will be accomplished by the DHCP relay agent.

1. On page Summary of Split-Scope Configuration, click **Finish**.
1. Click **Close**.
1. In DHCP, in the context-menu of **DHCP**, click **Add Server...**
1. In Add Server, under **This server**, type **VN1-SRV6"** and click **OK**.
1. In **DHCP**, expand  **vn1-srv6.ad.adatum.com**, **IPv4**, and click **Scope [10.1.2.0] VNet2**.
1. In the context menu of **Scope [10.1.2.0] VNet2**, click **Activate**.

### Task 2: Install the Routing and Remote Access role

Perform this task on VN2-SRV1.

1. In **Server Manager**, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on the page **Before You Begin**, click **Next >**.
1. On the page Installation Type, ensure **Role-based or feature-base installation** is selected and click **Next >**.
1. On the page Server Selection, ensure **VN2-SRV1.ad.adatum.com** is selected, and click **Next >**.
1. On the page Server Roles, activate the checkbox next to **Remote Access** and click **Next >**.
1. On the page Features, click **Next >**.
1. On the page Remote Access, click **Next >**.
1. On the page **Role Services**, activate **Routing**.
1. In Add feature that are required for Routing?, click **Add Features**.
1. In the **Add Rules and Features Wizard**, on the page **Role Services**, click **Next >**.
1. On the page **Web Server Role (IIS)**, click **Next >**.
1. On the page **Role Services**, click **Next >**.
1. On the page Confirmation, verify your selection, activate **Restart the destination server automatically if required**, and click **Install**.

    Wait for the installation to finish.

1. On the page **Results**, click **Close**.

If the server restarts, sign in again as **ad\Administrator**.

### Task 3: Configure DHCP relay

Perform this task on VN2-SRV1.

1. Open **Routing and Remote Access**.
1. In Routing and Remote Access, in the context-menu of **VN2-SRV1 (local)**, click **Configure and Enable Routing and Remote Access**.
1. In the Routing and Remote Access Server Setup Wizard, on page Welcome to the Routing and Remote Access Server Setup Wizard, click **Next >**.
1. On page Configuration, click **Secure connection between two private networks** and click **Next >**.
1. On page Demand-Dial Connections, click **No** and click **Next >**.
1. On page Completing the Routing and Remote Access Server Setup Wizard, click **Finish**.
1. In **Routing and Remote Access**, expand **VN2-SRV1 (local)**, **IPv4**, and click **General**.
1. Under **IPv4**, in the context-menu of **General**, click **New Routing Protocol...**
1. In New Routing Protocol, click **DHCP Relay Agent** and click **OK**.
1. In **Routing and Remote Access**, in the context-menu of **DHCP Relay Agent**, click **Properties**.
1. In DHCP Relay Agent Properties, under **Server address**, type **10.1.1.48** and click **Add**. Click **OK**.
1. In **Routing and Remote Access**, in the context-menu of **DHCP Relay Agent**, click **New Interface...**
1. In New Interface for DHCP Relay Agent, click **Ethernet** and click **OK**.
1. In DHCP Relay Properties - Ethernet Properties, ensure **Relay DHCP packets** is activated. Leave the default values in **Hop-count threshold**, and the **Boot threshold (seconds)** (in both cases **4**) and click **OK**.

    > If there is no DHCP server on the subnet, you should set the boot threshold to the smallest possible value.

### Task 4: Verify fault-tolerance for DHCP

Perform this task on CL1.

1. Open **DHCP**.
1. In DHCP, in the context-menu of **DHCP**, click **Add Server...**
1. In Add Server, under **This server**, type **VN1-SRV6"** and click **OK**.
1. In **DHCP**, in the context-menu of **DHCP**, click **Add Server...**
1. In Add Server, under **This server**, type **VN2-SRV2"** and click **OK**.
1. Open **Terminal**.
1. Release and renew the IP configuration on CL2.

    ````powershell
    Invoke-Command -ComputerName CL2 -ScriptBlock{
        ipconfig.exe /release
        ipconfig.exe /renew
    }
    ````

1. Switch to **DHCP**.
1. In DHCP, expand **vn2-srv2.ad.adatum.com**, **IPv4**, **Scope [10.1.2.0] VNet2**, and click **Address Leases**.
1. In the context-menu of **Address Leases**, click **Refresh**.

    You should see a lease with the Name CL2.ad.adatum.com with a Lease Expiration of approximately 2 hours from now.

1. In DHCP, expand **vn1-srv6 ad.adatum.com**, **IPv4**, **Scope [10.1.2.0] VNet2**, and click **Address Leases**.
1. In the context-menu of **Address Leases**, click **Refresh**.

    You should not see any active leases.

    You might want to repeat from step 7 several times to verify that VN2-SRV2 always leases the IP addresses for VNet2.

1. In the context-menu of **vn2-srv2.ad.adatum.com**, click **All Tasks**, **Stop**.
1. Switch to **Terminal**.
1. Release and renew the IP configuration on CL2.

    ````powershell
    Invoke-Command -ComputerName CL2 -ScriptBlock{
        ipconfig.exe /release
        ipconfig.exe /renew
    }
    ````

1. Switch to **DHCP**.
1. In DHCP, under **vn1-srv6.ad.adatum.com**, **IPv4**, **Scope [10.1.2.0] VNet2**, click **Address Leases**.
1. In the context-menu of **Address Leases**, click **Refresh**.

    You should see a lease with the Name CL2.ad.adatum.com with a Lease Expiration of approximately 2 hours from now.

1. In the context-menu of **vn2-srv2.ad.adatum.com**, click **All Tasks**, **Start**.

## Exercise 3: Implement DHCP failover

1. [Authorize DHCP server](#task-1-authorize-dhcp-server) VN1-SRV7
1. [Configure failover](#task-2-configure-failover) for VNet1 from VN1-SRV6 to VN1-SRV7 in load balance mode with default settings
1. [Validate DHCP failover](#task-3-validate-dhcp-failover)

    > What is the lease time for the client if all DHCP servers are working?

    > What is the lease time for the client, if the original DHCP server of the lease fails?

### Task 1: Authorize DHCP server

Perform this task on CL1.

1. Open **DHCP**.
1. In DHCP, in the context-menu of **DHCP**, click **Manage authorized servers...**
1. In Manage Authorized Servers, click **Authorize...**
1. In Authorize DHCP Server, under **Name or IP address**, type **vn1-srv7.ad.adatum.com** and click **OK**.
1. In Confirm Authorization, verify the **IP address** is **10.1.1.56** and click **OK**.
1. In **Manage Authorized Servers**, click **Close**.

### Task 2: Configure failover

Perform this task on CL1.

1. Open **DHCP**.
1. In DHCP, in the context-menu of **DHCP**, click **Add Server...**
1. In Add Server, click **This authorized DHCP server**. Click **vn1-srv6.ad.adatum.com** and click **OK**.
1. In **DHCP**, expand **vn1-srv6.ad.adatum.com**, **IPv4**, and click **Scope [10.1.1.0] VNet1**.
1. In the context menu of **Scope [10.1.1.0] VNet1**, click **Configure Failover...**
1. In the Configure Failover wizard, on page Introduction to DHCP Failover, click **Next >**.
1. On page Specify the partner server to use for failover, beside **Partner Server**, click **Add Server**.
1. In Add Server, click **This authorized DHCP server**. Click **vn1-srv7.ad.adatum.com** and click **OK**.
1. In the **Configure Failover** wizard, on page **Specify the partner server to use for failover**, cick **Next >**.
1. On page Create a new failover relationship, configure the settings according to the table below and click **Next >**.

    | Label                         | Value                                         |
    | ------------------------------|-----------------------------------------------|
    | Maximum Client Lead Time      | **1** hours **0** minutes                     |
    | Mode                          | Load balance                                  |
    | Local Balance Percentage      | Local Server **50 %** Partner Server **50 %** |
    | State Switchover Interval     | Decativated                                   |
    | Enable Message Authentication | Activated                                     |
    | Shared Secret                 | Type a secure secret                          |

1. On the last page, click **Finish**.
1. In Configure Failover, verify everything is successful and click **Close**.

### Task 3: Validate DHCP failover

Perform this task on CL1.

1. Open **Terminal**.
1. Renew the IP address.

    ````powershell
    ipconfig.exe /renew
    ````

1. Review the IP configuration.

    ````powershell
    ipconfig.exe /all
    ````

    > Note the difference between Lease Obtained and Lease Expires. This should be approximately 2 hours.

    Take a note of the DHCP Server IP address. Refer to the table below to identify the DHCP server where CL1 obtained the lease from.

    | IP address | Server name |
    |------------|-------------|
    | 10.1.1.48  | VN1-SRV6    |
    | 10.1.1.56  | VN1-SRV7    |

1. Open **DHCP**.
1. In DHCP, in the context-menu of **DHCP**, click **Add Server...**
1. In Add Server, click **This authorized DHCP server**. Click **vn1-srv6.ad.adatum.com**, hold down STRG and click **vn1-srv7-ad.adatum.com**. Click **OK**.
1. In **DHCP**, expand **vn1-srv6.ad.adatum.com**, **IPv4**, **Scope [10.1.1.0] VNet1** and click **Address Leases**.

    You should see the lease for CL1.ad.adatum.com.

1. Expand **vn1-srv7.ad.adatum.com**, **IPv4**, **Scope [10.1.1.0] VNet1** and click **Address Leases**.

    You should see the lease for CL1.ad.adatum.com with the same data as in the previous step.

1. In the context menu of the DHCP server, CL1 obtained the lease from, click **All Tasks**, **Stop**.
1. Switch to **Terminal**.
1. Renew the IP address.

    ````powershell
    ipconfig.exe /renew
    ````

1. Review the IP configuration.

    ````powershell
    ipconfig.exe /all
    ````

    > Note the difference between Lease Obtained and Lease Expires. This should be approximately 1 hour, because of the maximum client lead time.

1. Switch to **DHCP**.
1. In the context menu of the DHCP server stopped, click **All Tasks**, **Start**.
