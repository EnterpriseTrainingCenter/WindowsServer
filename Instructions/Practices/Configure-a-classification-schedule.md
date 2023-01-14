# Practice: Configure a classification schedule

## Required VMs

* VN1-SRV1
* VN1-SRV10
* CL1

## Task

Configure the automatic classification to run every Friday at 18:00 for a maximum of 60 hours and allow continuous classification.

## Instructions

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. Open **File Server Resource Manager**.
1. In File Server Resource Manager, in the left pane, in the context menu of **File Server Resource Manager**, click **Connect to Another Computer...**
1. In Connect to Another Computer, click **Another computer**, type **VN1-SRV10**, and click **OK**.
1. In the context-menu of **File Server Resource Manager**, click **Configure Options...**
1. In File Server Resource Manager Options, click the tab **Automatic Classification**.
1. On tab Automatic Classification, activate the checkbox **Enable fixed schedule**.
1. In **Run at**, type **18:00:00**.
1. Ensure, **Weekly** is selected and activate the checkbox **Friday**.
1. Activate the checkbox **Limit (in hours)** and type **60**.
1. Activate the checkbox **Allow continuous classification for new files**.
1. Click **OK**.
