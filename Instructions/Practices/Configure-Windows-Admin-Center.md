# Practice: Configure Windows Admin Center

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

## Task

On CL1, add the URL https://admincenter.ad.adatum.com to the Local intranet zone. In Windows Admin Center, add connections to all servers starting with VN and PM.

## Instructions

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
