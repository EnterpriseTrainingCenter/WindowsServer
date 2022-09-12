# Practice: Configure e-mail notifications in FSRM

## Required VMs

* VN1-DC1
* VN1-FS1
* CL1

Note: In the lab environment no real SMTP server is installed. Therefore, e-mail notification will not work. This practice demonstrates the configuration and avoids warning messages in later practices and labs.

## Task

Configure the e-mail notification settings of FSRM to use mail.adatum.com as SMTP server. The from address should be fsrm@adatum.com and the default administrator recipients should be fsm@adatum.com.

## Instructions

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. Open **File Server Resource Manager**.
1. In File Server Resource Manager, in the left pane, in the context menu of **File Server Resource Manager**, click **Connect to Another Computer...**
1. In Connect to Another Computer, click **Another computer**, type **VN1-FS1**, and click **OK**.
1. In the context-menu of **File Server Resource Manager**, click **Configure Options...**
1. In File Server Resource Manager Options, on tab Email Notifications, enter the values below and click **OK**.

    | Label                            | Value               |
    |----------------------------------|---------------------|
    | SMTP server name or IP address   | **mail.adatum.com** |
    | Default administrator recipients | **fsm@adatum.com**  |
    | Default "From" e-mail address    | **fsrm@adatum.com** |

    > In a real-world environment, you would click **Send Test E-mail** to verify the settings. In the lab environment, this would fail. Therefore, do not click the button.
