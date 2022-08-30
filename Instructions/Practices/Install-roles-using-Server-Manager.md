# Practice: Install Roles using Server Manager

## Required VMs

* VN1-DC1
* VN1-FS1
* CL1

## Task

On CL1, use Server Manager to install the File Server role on VN1-FS1.

## Instructions

Perform these steps on CL1.

1. Logon as **smart\Administrator**.
1. Start Server Manager.
1. In the left pane, click **All Server**.
1. In the context-menu of **VN1-FS1**, click **Add Roles and Features**.
1. In the Add Rules and Features Wizard, on the page **Before You Begin**, click **Next >**.
1. On the page **Installation Type**, ensure **Role-based or feature-base installation** is selected and click **Next >**.
1. On the page **Server Selection**, ensure **VN1-FS1.smart.etc** is selected and click **Next >**.
1. On the page **Server Role**, expand **File and Storage Service** and expand **File and iSCSI Services**. Activate the checkbox next to **File Server** and click **Next >**.
1. On the page **Features**, click **Next >**.
1. On the page **Confirmation**, verify your selection and click **Install**.
1. On the page **Results**, wait for the installation to succeed, then click **Close**.