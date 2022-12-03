# Practice: NIC teaming

## Required VMs

* VN1-SRV1
* PM-SRV3
* CL1

## Task

On PM-SRV3, configure the network adapters in a team and assign it the IP address 10.1.200.26.

## Instructions

Perform this task on the host.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click the name of your computer.
1. Under Virtual Machines, in the context menu of **PM-SRV3**, click **Settings...**
1. In Settings for PM-SRV3, expand the first **Network Adapter** and click **Advanced Features**.
1. Under **NIC Teaming** (you might have to scroll down), activate **Enable this network adapter to be part of a team in guest operating system**.
1. Expand the first **Network Adapter** and click **Advanced Features**.
1. Under **NIC Teaming** (you might have to scroll down), activate **Enable this network adapter to be part of a team in guest operating system** and click **OK**.

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click **All Servers**.
1. Under All Servers, in the context-menu of **PM-SRV3**, click **Configure NIC Teaming**.
1. In NIC Teaming, under **TEAMS**, click **TASKS**, **New Team**.
1. In NIC Teaming, under **Team name**, type **Perimeter**. Under **Member adapters**, activate **VNet1-1**. Click **Additional Properties**. Review the additional properties and click **OK**.

    > Leave add least one adapter out of the team to not loose the remote connection to the server.

1. Open **Terminal**.
1. Open a CIM session to **PM-SRV3**.

    ````powershell
    $cimSession = New-CimSession PM-SRV3
    ````

1. Assign IP address **10.1.200.26/24** and default gateway **10.1.200.1** to **Perimeter**.

    ````powershell
    $interfaceAlias = 'Perimeter'
    New-NetIPAddress `
        -InterfaceAlias $interfaceAlias `
        -IPAddress 10.1.200.26 `
        -PrefixLength 24 `
        -DefaultGateway 10.1.200.1 `
        -AddressFamily IPv4 `
        -CimSession $cimSession
    ````

1. Set the DNS server address **10.1.1.8** for **Perimeter**.

    ````powershell
    Set-DnsClientServerAddress `
        -InterfaceAlias $interfaceAlias `
        -ServerAddresses 10.1.1.8 `
        -CimSession $cimSession
    ````

1. Remove the CIM session.

    ````powershell
    Remove-CimSession $cimSession
    ````

1. Switch to **NIC Teaming**.
1. In NIC Teaming, under **ADAPTERS AND INTERFACES**, on tab **Network Adapters**, under **Available to be added to a team (1)**, in the context-menu of **Vnet1-0**, click **Add to Team "Perimeter"**.

    The configuration can take a little while, because NIC Teaming might try to access the remote server through its forder IP address. You can speed up the display of results by closing NIC Teaming and Server Manager and re-opening them.

Note: Because NIC teaming in virtual machines is only supported with external virtual switches, you will not see any gains in performance or fault-tolerance. The purpose of this practice is to demonstrate the configuration of NIC teaming in general.