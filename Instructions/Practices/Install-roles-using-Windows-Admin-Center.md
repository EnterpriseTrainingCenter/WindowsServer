# Practice: Install Roles using Windows Admin Center

## Required VMs

* VN1-SRV1
* PM-SRV1
* VN1-SRV3
* CL1

## Task

On CL1, use Windows Admin Center to install the Certification Authority Web Enrollment role service on VN1-SRV3.

## Instructions

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **VN1-SRV3.ad.adatum.com**.
1. Connected to VN1-SRV3.ad.adatum.com, under **Tools**, click **Roles & features**.
1. In Roles and features, expand **Active Directory Certificate Service**.
1. Activate the checkbox beside  **Certification Authority Web Enrollment** and click **Install**.
1. In the pane Install Role and Features, activate the checkbox **Reboot the server automatically, if required** and click **Yes**.
1. After a few minutes, a notification **Install Roles and Features** appears. If you missed the notification, a small number appears beside the icon *Notifications* (in form of a bell) at the top-right of Windows Admin Center. Click the icon *Notification* and the notification **Install Roles and Features**.
1. In the pane **Notification details**, click **Close**.
1. In Roles and features, verify the **State** of **Certification Authority Web Enrollment** is **Installed**.
1. In Roles and features, verify that the **State** of **Web Server (IIS)** is **16 of 43 installed**.
1. Expand **Web Server (IIS)** and explore the installed role services.
1. Under Web Server (IIS), expand **Application Development**, activate the checkbox beside **ASP**, and click **Uninstall**.
1. In the pane **Uninstall Roles and Features**, notice that **Certification Authority Web Enrollment** would be removed together with ASP, because there is a dependency. Click **No**.
