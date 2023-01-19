# Practice: Manage Services using Windows Admin Center

## Required VMs

* VN1-SRV1
* VN1-SRV4
* VN1-SRV5
* CL1

## Task

On CL1, stop the W32Time service on VN1-SRV5 and disable it. Restart VN1-SRV5 and verify the changes. Then set W32Time to start automatically and start it again.

## Instructions

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **VN1-SRV5.ad.adatum.com**.
1. Connected to VN1-SRV5.ad.adatum.com, under **Tools**, click **Services**.
1. Under Services, click **W32Time**.
1. Click **Stop**. Click **Yes**.
1. Click **Settings**.
1. In W32Time Settings, in **Set Startup Mode**, select **Disabled**.
1. Click **Save**.
1. Click **Close**.
1. Under Tools, click **Overview**.
1. In Overview, click **Restart** and click **Yes**. Wait for about 30 seconds.
1. In Windows Admin Center, click **VN1-SRV5.ad.adatum.com**.
1. Connected to VN1-SRV5.ad.adatum.com, under **Tools**, click **Services**.
1. Verify, that the status of **W32Time** is **Stopped** and the **Start** button is disabled. Click **Settings**
1. In W32Time Settings, in **Set Startup Mode**, select **Automatic**.
1. Click **Save**.
1. Click **Close**.
1. Click **Start**.
