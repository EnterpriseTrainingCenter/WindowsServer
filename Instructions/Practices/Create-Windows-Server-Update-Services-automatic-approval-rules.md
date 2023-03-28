# Practice: Create Windows Server Update Services automatic approval rules

## Required VMs

* VN1-SRV1
* VN1-SRV5
* CL1

## Task

In Update Services on VN1-SRV5, create automatic approval rules:

* Definition updates should be approved immediately.
* Critical updates should be approved after 2 days.
* For the Insider group, all updates should be approved immediately.
* For the Target group, all other updates should be approved after 7 days.
* For the Standard group, all other updates should be approved after 14 days.

Start the synchronization manually.

## Instructions

Perform this task on CL1.

1. Sign in as **ad\Administrator**.
1. Open **Windows Server Update Services**.
1. In Update Services, expand **VN1-SRV5** and click **Options**.

    If you do not see **VN1-SRV5**, perform these steps:

    1. In Update Services, in the context-menu of **Update Services**, click **Connect to Server...**
    1. In Connect to Server, beside **Server name**, type **VN1-SRV5** and click **Connect**.

1. Under Options, click **Automatic Approvals**.
1. In Automatic Approvals, click **New Rule...**
1. In Add Rule, under **Step 1: Select properties**, activate **When an update is in a specific classification**.
1. Under **Step 2: Edit the properties (click an underlined value)**, click **any classification**.
1. In Choose Update Classifications, deactivate **All Classifications** and activate **Definition Updates**. Click **OK**.
1. In **Add Rule**, under **Step 3: Specify a name**, type **Definition Updates** and click **OK**.
1. In **Automatic Approvals**, click **New Rule...**
1. In Add Rule, under **Step 1: Select properties**, activate **When an update is in a specific classification** and **Set a deadline for the approval**.
1. Under **Step 2: Edit the properties (click an underlined value)**, click **any classification**.
1. In Choose Update Classifications, deactivate **All Classifications** and activate **Critical Updates**. Click **OK**.
1. In **Add Rule**, under **Step 2: Edit the properties (click an underlined value)**, click **all computers**.
1. In Choose Computer Groups, deactivate **Insider** and click **OK**.
1. In **Add Rule**, under **Step 2: Edit the properties (click an underlined value)**, click **7 days after the approval at 03:00**.
1. In Choose Deadline, beside **Days**, type **2** and click **OK**.
1. In **Add Rule**, under **Step 3: Specify a name**, type **Critical Updates** and click **OK**.
1. In **Automatic Approvals**, click **New Rule...**
1. Under **Step 2: Edit the properties (click an underlined value)**, click **all computers**.
1. In Choose Computer Groups, deactivate **All Computers** and activate **Insider**. Click **OK**.
1. In **Add Rule**, under **Step 3: Specify a name**, type **Insider Updates** and click **OK**.
1. In **Automatic Approvals**, click **New Rule...**
1. In Add Rule, under **Step 1: Select properties**, activate **When an update is in a specific classification** and **Set a deadline for the approval**.
1. Under **Step 2: Edit the properties (click an underlined value)**, click **any classification**.
1. In Choose Update Classifications, deactivate **Critical Updates** and **Definition Updates**. Click **OK**.
1. In **Add Rule**, under **Step 2: Edit the properties (click an underlined value)**, click **all computers**.
1. In Choose Computer Groups, deactivate **All Computers** and activate **Target**. Click **OK**.
1. In **Add Rule**, under **Step 3: Specify a name**, type **Target updates** and click **OK**.
1. In **Automatic Approvals**, click **New Rule...**
1. In Add Rule, under **Step 1: Select properties**, activate **When an update is in a specific classification** and **Set a deadline for the approval**.
1. Under **Step 2: Edit the properties (click an underlined value)**, click **any classification**.
1. In Choose Update Classifications, deactivate **Critical Updates** and **Definition Updates**. Click **OK**.
1. In **Add Rule**, under **Step 2: Edit the properties (click an underlined value)**, click **all computers**.
1. In Choose Computer Groups, deactivate **Insider** and **Target**. Click **OK**.
1. In **Add Rule**, under **Step 2: Edit the properties (click an underlined value)**, click **14 days after the approval at 03:00**.
1. In Choose Deadline, beside **Days**, type **14**. and click **OK**.
1. In **Add Rule**, under **Step 3: Specify a name**, type **Standard updates** and click **OK**.
1. In **Automatic Approvals**, click **OK**.
1. In **Update Services**, click **Synchronizations**.
1. In the context-menu of **Synchronizations**, click **Synchronize Now**.

The first synchronization may run for a few hours.
