# Practice: Install Roles using Server Manager

## Required VMs

* VN1-SRV1
* VN1-SRV10
* CL1

## Task

On CL1, use Server Manager to install the File Server role on VN1-SRV10.

## Instructions

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. Open **Server Manager**.
1. In Server manager, in the left pane, click **All Servers**.
1. In All Servers, in the context-menu of **VN1-SRV10**, click **Add Roles and Features**.
1. In the Add Rules and Features Wizard, on the page **Before You Begin**, click **Next >**.
1. On the page **Installation Type**, ensure **Role-based or feature-based installation** is selected and click **Next >**.
1. On the page **Server Selection**, ensure **VN1-SRV10.ad.adatum.com** is selected and click **Next >**.
1. On the page **Server Role**, expand **File and Storage Service** and expand **File and iSCSI Services**. Activate the checkbox next to **File Server** and click **Next >**.
1. On the page **Features**, click **Next >**.
1. On the page **Confirmation**, verify your selection and click **Install**.
1. On the page **Results**, wait for the installation to succeed, then click **Close**.
