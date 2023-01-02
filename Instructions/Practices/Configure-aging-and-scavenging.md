# Practice: Configure aging and scavenging

## Required VMs

* VN1-SRV1
* CL1

## Task

Enable aging and scavenging for all Active Directory integrated zones and enable automatic scavenging of stale records at an interval of 7 days.

> When will a stale record be removed at the earliest and the latest?

## Instructions

Perform this task on CL1.

1. Sign in as **ad\Administrator**.
1. Open **DNS**.
1. If the dialog **Connect to DNS Server** does not appear, in DNS Manager, in the context-menu of **DNS**, click **Connect to DNS server...**.
1. In Connect to DNS Server, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.
1. In **DNS-Manager**, click **vn1-srv1.ad.adatum.com**.
1. In the context-menu of **vn1-srv1.ad.adatum.com**, click **Set Aging/Scavenging for All Zones...**

    Note: It can take a few seconds until the context-menu appears.

1. In Server Aging/Scavenging Properties, activate **Scavenge stale resource records** and click **OK**.

    Note that the No-refresh interval and the Refresh interval are set to 7 days.

    > A stale record will be removed after 14 days at the earliest and 21 days at the latest.

1. In Server Aging/Scavenging Confirmation, activate **Apply these settings to the existing Active Directory-integrated zones** and click **OK**.

1. In **DNS Manager**, expand **vn1-srv1.ad.adatum.com**, **Forward Lookup Zones** and click **ad.adatum.com**.
1. In the context-menu of **ad.adatum.com**, click **Properties**.

    Note, that in ad.adatum.com Properties, on tab General, Dynamic updates are set to Secure only.

1. Click **Aging...**

    Note that Scavenge stale resource records is activated.

1. Click **Cancel**.
1. In **ad.adatum.com Properties**, click **Cancel**.
1. In **DNS-Manager**, in the context-menu of **vn1-srv1.ad.adatum.com**, click **Properties**.
1. In **vn1-srv1.ad.adatum.com Properties**, click the tab **Advanced**.
1. On tab Advanced, activate **Enable automatic scavenging of stale records** and click **OK**.
