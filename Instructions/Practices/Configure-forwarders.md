# Practice: Configure forwarders

## Required VMs

* VN1-SRV1
* VN1-SRV5
* VN2-SRV1
* PM-SRV1
* CL1

## Task

On VN1-SRV5, VN2-SRV1, and PM-SRV1, add the DNS servers 8.8.8.8 and 8.8.4.4 as forwarders and add vn1-srv1.ad.adatum.com as conditional forwarder for ad.adatum.com.

> Does the query performance for internet names improve after you added the forwarders?

> Can you resolve ad.adatum.com from the DNS servers?

## Instructions

Perform this task on CL1.

1. Sign in as **ad\Administrator**.
1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn1-srv5.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn1-srv5.ad.adatum.com**.
1. In the right pane, double-click **Forwarders**.
1. In vn1-srv5.ad.adatum.com Properties, on tab Forwarders, click **Edit...**
1. In Edit Forwards, in **\<Click here to add an IP Address or DNS name\>**, enter **8.8.8.8**.
1. In **\<Click here to add an IP Address or DNS name\>**, enter **8.8.4.4**.
1. Click **OK**.
1. In **vn1-srv5.ad.adatum.com Properties**, on tab **Forwarders**, click **OK**.
1. In **DNS Manager**, expand **vn1-srv5.ad.adatum.com** and click **Conditional Forwarders**.
1. In the context-menu of **Conditional Forwarders**, click **New Conditional Forwarder...**
1. In New Conditional Forwarder, under **DNS Domain**, type **ad.adatum.com**.
1. Under IP addresses of the master servers, click **\<Click here to add an IP Address or DNS name\>** and enter **vn1-srv1.ad.adatum.com**.
1. Click the faulty entry **No such host is known.** and click **Delete**.

    You can safely ignore the remaining error under **Validated**. It appears, because we have not added a reverse lookup zone yet. Moreover, we have not configured a valid IPv6 address for the DNS server.

1. Click **OK**.
1. Open **Terminal**.
1. Clear the DNS client cache and the DNS server cache of **vn1-srv5.ad.adatum.com**.

    ````powershell
    $server = 'vn1-srv5.ad.adatum.com'
    Clear-DnsClientCache
    Clear-DnsServerCache -ComputerName $server -Force
    ````

1. Resolve the name **microsoft.com** on the server **vn1-srv5.ad.adatum.com**.

    ````powershell
    Resolve-DnsName -Name microsoft.com -Server $server
    ````

    > The response should appear very fast.

1. Resolve the name **ad.adatum.com** on the server **vn1-srv5.ad.adatum.com**.

    ````powershell
    Resolve-DnsName -Name ad.adatum.com -Server $server
    ````

    > The query should be resolved.

Repeat the task for **vn2-srv1.ad.adatum.com** and **pm-srv1.ad.adatum.com**. To add additional servers to DNS Manager, in the context menu of **DNS**, click **Connect to DNS Server...** and follow the instructions from step 2.
