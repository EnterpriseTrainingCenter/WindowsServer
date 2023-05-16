# Practice: Create Windows Server Update Services computer groups

## Required VMs

* VN1-SRV1
* VN1-SRV5
* CL1

## Task

In Update Services on VN1-SRV5, create computer groups

* Insider
* Target
* Standard

## Instructions

1. On **CL1**, sign in as **ad\administrator**.
1. Open **Windows Server Update Services**.
1. In Update Services, expand **VN1-SRV5**, **Computers**, and click **All Computers**.

    If you do not see **VN1-SRV5**, perform these steps:

    1. In Update Services, in the context-menu of **Update Services**, click **Connect to Server...**
    1. In Connect to Server, beside **Server name**, type **VN1-SRV5** and click **Connect**.

1. In the context-menu of **All Computers**, click **Add Computer group...**
1. In Add Computer Group, beside **Name**, type **Insider** and click **Add**.

    Repeat the last two steps to create the computer groups:

    * Target
    * Standard
