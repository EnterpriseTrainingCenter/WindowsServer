# Practice: Configure a guest operating system

## Required VMs

* VN1-SRV1
* PM-SRV1
* CL1

## Task

In the virtual machine PM-SRV20, set the administrator password, set the IP address to 10.1.200.160/26, the default gateway to 10.1.200.1 and the DNS client server to 10.1.1.8. Join the virtual machine to the domain ad.adatum.com and rename the guest operating system computer.

## Instructions

Perform this task on CL1.

1. Sign in as **ad\Administrator**.
1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, under Virtual machines, in the context-menu of **PM-SRV20**, click **Connect...**.
1. In C:\\Windows\\system32\\LogonUI.exe, at the prompt The user's password must be changed before signing in, select **OK** and press ENTER.
1. In New password and Confirm password, enter a secure password and take a note.
1. At Your password has been changed, press ENTER.
1. In SConfig, enter **8**.
1. In Network settings, enter **1**.
1. In Network adapter settings, enter **1**.
1. At the prompt Select (D)HCP or (S)tatic IP address (Blank=Cancel), enter **S**.
1. Copy the IP-Address **10.1.200.160** from this web page into the clipboard.
1. In **PM-SRV20 on PM-SRV1 - Virtual Machine Connection**, at the prompt **Enter static IP address (Blank=Cancel)**, press CTRL+V.

    > Can you paste the the IP address?

1. In the menu, click **Clipboard**, **Type clipboard text**.
1. Ensure, the IP address **10.1.200.160** was typed and press ENTER.
1. At the prompt Enter subnet mask (Blank=255.255.255.0), press ENTER.
1. At the prompt Enter default gateway (Blank=Cancel), enter **10.1.200.1**. You can use the clipboard again.
1. Under a number of success messages, press ENTER.
1. In SConfig, enter **8**.
1. In Network settings, enter **1**.
1. In Network adapter settings, enter **2**.
1. At the prompt Enter new preferred DNS server (Blank=Cancel), enter **10.1.1.8**.
1. At the prompt Enter alternate DNS server, press ENTER.
1. Under Sucessfully assigned DNS server(s), press ENTER.
1. In SConfig, enter **1**.
1. At the prompt Join (D)omain or (W)orkgroup? (Blank=Cancel), enter **D**.
1. At the prompt Name of domain to join (Blank=Cancel), enter **ad.adatum.com**.
1. At the prompt Specify an authorized domain\user (Blank=Cancel), enter **ad\Administrator**.
1. At the prompt Password for ad\Administrator, enter the password of **ad\Administrator**.
1. At the prompt Do you want to change the computer name before restarting? (Y)es or (N)o, enter **Y**.
1. At the prompt Enter new computer name (Blank=Canel), enter **PM-SRV20**.
1. At the prompt Password for ad\Administrator, enter the password of **ad\Administrator**.
1. At the prompt Restart now? (Y)es or (N)o, enter **Y**.
