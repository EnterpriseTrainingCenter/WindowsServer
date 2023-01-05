# Practice: Configure DHCP server options

## Required VMs

* VN1-SRV1
* VN1-SRV6
* VN1-SRV7
* VN2-SRV2
* CL1

## Task

On VN1-SRV6, VN1-SRV7, and VN2-SRV2 set the IPv4 server options DNS Server to 10.1.1.8 and DNS domain to ad.adatum.com.

## Instructions

### Desktop experience

Perform this task on CL1.

1. Open **DHCP**.
1. In DHCP, in the context-menu of **DHCP**, click **Add Server...**
1. In Add Server, under **This server**, type **VN1-SRV6** and click **OK**.
1. In **DHCP**, expand  **vn1-srv6.ad.adatum.com**, **IPv4** and click **Server Options**.
1. In the context-menu of **Server Options**, click **Configure Options...**
1. In Server Options, under **Available Options**, activate **006 DNS Servers**.
1. Under **IP address**, type **10.1.1.8** and click **Add**.
1. Under **Available Options**, activate **015 DNS Domain Name**.
1. Under **String value**, type **ad.adatum.com**.
1. Click **OK**.

Repeat from step 2 for **VN1-SRV7** and **VN2-SRV2**.

### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. On **VN1-SRV6**, **VN1-SRV7**, and **VN2-SRV2** set the IPv4 server options DNS Server to **10.1.1.8** and DNS domain to **ad.adatum.com**.

    ````powershell
    'VN1-SRV6', 'VN1-SRV7', 'VN2-SRV2' | ForEach-Object {
        Set-DhcpServerv4OptionValue `
            -ComputerName $PSItem -DnsServer 10.1.1.8 -DnsDomain ad.adatum.com
    }
    ````
