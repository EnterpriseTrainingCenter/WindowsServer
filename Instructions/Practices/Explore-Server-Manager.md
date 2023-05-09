# Practice: Explore Server Manager

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

## Setup

If you skipped previous practices or labs, on CL1, run ````C:\Labs\Solutions\Install-RemoteServerAdministrationTools.ps1````.

## Task

On CL1, in Server Manager, add all Servers with a name starting with VN or PM and explore Server Manager.

## Instructions

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. Open **Server Manager**.
1. In the dialog **Try managing server with Windows Admin Center**, activate **Don't show this message again** and close it.
1. On the menu, click **Manage**, **Add Servers**.
1. In Add Servers, on tab **Active Directory**, in **Name (CN)**, type **VN** and click **Find Now**.
1. Select all Servers found, and click the arrow button to move them to the list box **Selected**.
1. In **Name (CN)**, type **PM** and click **Find Now**.
1. Select all Servers found, and click the arrow button to move them to the list box **Selected**.
1. Click **OK**.
1. Click **Tools** and explore the menu items.
1. In the left pane, click **All Servers**.
1. Explore the differences in the context menu of various servers, especially of **VN1-SRV1** and others.
1. Take some time to, explore the other options in the left pane.
