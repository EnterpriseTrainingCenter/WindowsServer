# Practice: Update root hints

## Required VMs

* VN1-SRV1
* VN2-SRV1
* CL1

## Task

On VN2-SRV1, delete all root hints. Verify the effect of a DNS server without forwarders or root hints. Restore the current root hints from another DNS server

> Can you resolve DNS names from the internet after you removed all DNS root hints?

## Instructions

Perform this task on CL1.

1. Sign in as **ad\Administrator**.
1. Open **DNS**.
1. In Connect to DNS Server, click **The following computer**, type **VN2-SRV1.ad.adatum.com** below and click **OK**.
1. In DNS Manager, click **VN2-SRV1.ad.adatum.com**.
1. In the right pane, double-click **Root Hints**.
1. In VN2-SRV1.ad.adatum.com Properties, on tab Root Hints, click **Remove** multiple times until the list under **Name servers** is empty. Then, click **OK**.
1. In the message box You are about to remove the last root hint. If the DNS server is not authoritative for the root zone and is not configured for exclusive forwarding, the DNS server may not be able to resolve queries for names not stored on the local DNS server. Do you want to continue?, click **Yes**.
1. In **DNS Manager**, in the context-menu of **VN2-SRV1.ad.adatum.com**, click **Clear Cache**.

    Note: It may take a few seconds until the context menu appears.

1. Open **Terminal**.
1. Resolve the name **microsoft.com** using the server **VN2-SRV1.ad.adatum.com**.

    ````powershell
    Resolve-DnsName -Name microsoft.com -Server VN2-SRV1.ad.adatum.com
    ````

    > You will receive an error message, because the server cannot resolve names from the internet anymore.

1. Switch to **DNS Manager**.
1. In the right pane, double-click **Root Hints**.
1. In VN2-SRV1.ad.adatum.com Properties, on tab Root Hints, click **Copy from Server**.
1. In Server to Copy From, under **IP address or DNS name**, type **8.8.8.8** and click **OK**.
1. In **VN2-SRV1.ad.adatum.com Properties**, on tab **Root Hints**, under **Name servers**, click the first name server with an **IP Address** of **Unknown** (most likely **a.root-servers.net**) and click **Edit...**
1. In Edit Name Server Record, click **Resolve** and click **OK**.

    The name server should have an IP address now.

    Repeat the last 2 steps for all name servers without IP addresses.

1. In **VN2-SRV1.ad.adatum.com Properties**, on tab **Root Hints**, click **OK**.
1. Switch to **Terminal**.
1. Resolve the name **microsoft.com** using the server **VN2-SRV1.ad.adatum.com** again.

    ````powershell
    Resolve-DnsName -Name microsoft.com -Server VN2-SRV1.ad.adatum.com
    ````

    > The query should resolve again.
