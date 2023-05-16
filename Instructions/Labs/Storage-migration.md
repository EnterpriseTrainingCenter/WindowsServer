# Lab: Storage migration

## Required VMs

* VN1-SRV1
* VN1-SRV4
* VN1-SRV5
* VN1-SRV8
* VN1-SRV10
* CL1
* CL2

## Setup

1. On **CL1**, sign in as **ad\Administrator**.
1. On **CL2**, sign in as **ad\Administrator**.

## Introduction

Adatum wants to migrate shares to a different server to reduce the number of servers.

## Exercises

1. [Migrating shares](#exercise-1-migrating-shares)
1. [Verify the migration](#exercise-2-verify-the-migration)

## Exercise 1: Migrating shares

1. [Install Storage Migration Service on the orchestration server](#task-1-install-storage-migration-service-on-the-orchestration-server) VN1-SRV8
1. [Create a migration job](#task-2-create-a-migration-job)
1. [Inventory server](#task-3-inventory-server) VN1-SRV10
1. [Transfer data](#task-4-transfer-data) to VN1-SRV5
1. [Cut over to the new server](#task-5-cut-over-to-the-new-server)

### Task 1: Install Storage Migration Service on the orchestration server

Perform this task on CL1.

1. Open **Microsoft Edge** and navigate to <https://admincenter>.
1. In Windows Admin Center, click **vn1-srv8.ad.adatum.com**.
1. Connected to vn1-srv8.ad.adatum.com, under **Tools**, click **Storage Migration Service**.
1. Under Storage Migration Service, click **Install**.

    Wait for the setup to finish. This should take less than a minute.

1. In Migrate storage in three steps, activate **Don't show this again** and click **Close**.

### Task 2: Create a migration job

Perform this task on CL1.

1. Open **Microsoft Edge** and navigate to <https://admincenter>.
1. In Windows Admin Center, click **vn1-srv8.ad.adatum.com**.
1. Connected to vn1-srv8.ad.adatum.com, under **Tools**, click **Storage Migration Service**.
1. In **Windows Admin Center**, under **Storage Migration Service**, click **+ New Job**.
1. In the New job pane, under **Job name**, type **MigrationJob1**. Under **Source devices**, ensure **Windows servers and clusters** is selected and click **OK**.

### Task 3: Inventory server

Perform this task on CL1.

If you proceeded from the previous task, you may skip to step 5.

1. Open **Microsoft Edge** and navigate to <https://admincenter>.
1. In Windows Admin Center, click **vn1-srv8.ad.adatum.com**.
1. Connected to vn1-srv8.ad.adatum.com, under **Tools**, click **Storage Migration Service**.
1. Under Storage Migration Service, click **MigrationJob1**.
1. Under 1 Inventory servers, on page Check the prerequisites, click **Next**.
1. On page Enter credentials for the devices you want to migration, type the credentials of **ad\Administrator** and click **Next**.

    On page Install required tools, wait for the installation to finish. This takes less than a minute.

1. Click **Next**.
1. On page Add and scan devices, click **Add a device**.
1. In pane Add source device, in **Name**, type **VN1-SRV10** and click **Add**
1. On page **Add and scan device**, click **Start scan**.

    Wait for the scan to finish. This takes less than a minute. After the scan has finished, you may explore the details in the bottom pane.

1. Click **Next**.

### Task 4: Transfer data

Peform this task on CL1.

If you proceeded from the previous task, you may skip to step 5.

1. Open **Microsoft Edge** and navigate to <https://admincenter>.
1. In Windows Admin Center, click **vn1-srv8.ad.adatum.com**.
1. Connected to vn1-srv8.ad.adatum.com, under **Tools**, click **Storage Migration Service**.
1. Under Storage Migration Service, click **MigrationJob1**.
1. Under 2 Transfer data, on page **Enter credentials for the destination devices**, type the credentials of **ad\Administrator** and click **Next**.
1. On page vn1-srv10.ad.adatum.com, ensure **Use an existing server or VM** is selected. Under **Destination device**, type **VN1-SRV5** and click **Scan**. Under **Map each source volume to a destination volume**, in line with **Source volume** D:, in column **Destination volume**, click **E:**. Click **Next**.
1. On page Adjust settings, under **Validation method (checksum) for transmitted files**, click **CRC64**. Review other settings and click **Next**.

    On page Install required features, wait for the installation to finish. This takes less than a minute.

1. Click **Next**.
1. On page Validate devices, click **Validate**.

    Wait for the validation to finish. The column **Validation** should show **Pass**.

1. Click **Next**.
1. On page Start the transfer, click **Start transfer**.

    Wait for the transfer to finish. This will take less than a minute. You may explore th details in the bottom pane.

1. Click **Next**.

### Task 5: Cut over to the new server

Peform this task on CL1.

If you proceeded from the previous task, you may skip to step 5.

1. Open **Microsoft Edge** and navigate to <https://admincenter>.
1. In Windows Admin Center, click **vn1-srv8.ad.adatum.com**.
1. Connected to vn1-srv8.ad.adatum.com, under **Tools**, click **Storage Migration Service**.
1. Under Storage Migration Service, click **MigrationJob1**.
1. Under 3 Cut over to the new servers, on page **Enter credentials**, under **Enter credentials for the source devices** and **Enter credentials for the destination devices**, ensure **Stored credentials** is selected and click **Next**.
1. On page vn1-srv10.ad.adatum.com, in column **Destination network adapters**, for each source network adapter select the destination network adapter with the same name. Under **VNet1**, under **IP address**, type **10.1.1.208**. Under **Subnet**, type **255.255.255.0**, under **Gateway**, type **10.1.1.1**. Under **SAN1** and **SAN2**, activate **Use DHCP**. Click **Next**.

    Note: There is not DHCP server on the subnets of SAN1 and SAN2. However, these network adapter are not needed for the lab. Therefore, to simplify the task, the option to use DHCP is activated.

1. On page Adjust settings, under **Enter AD credentials**, ensure **Stored credentials** is selected and click **Next**.
1. On page Validate devices, click **Validate**.

    Wait for the validation to finish.  The column **Validation** should show **Pass**.

1. Click **Next**.
1. On page Cut over to the new servers, click **Start cutover**.

    Wait for the cutover to finish. The cutover will take a few minutes. You may observe the source and destination computers to reboot two times.

1. Click **Finish**.

## Exercise 2: Verify the migration

1. [Verify changes on the source server](#task-1-verify-changes-on-the-source-server) VN1-SRV10 and shut down the computer

    > What is the computer name of former VN1-SRV10?

    > Which IP address does VNet1 have?

    > Which IP address do SAN1 and SAN2 have?

1. [Verify the changes on the destination server](#task-2-verify-changes-on-the-destination-server) VN1-SRV5

    > What is the computer name of former VN1-SRV5?

    > What are the IP addresses of the network adapters?

1. [Verify client access to the shares](#task-3-verify-client-access-to-the-shares)

    > What are the permissions on the shares?

    > What is the content of the shares?

### Task 1: Verify changes on the source server

Perform this task on VN1-SRV10.

1. Sign in as **ad\Administrator**.
1. Open **Server Manager**.
1. In Server Manager, click **Local Server**.

    > The computer name will be a random name starting with WIN-.
    
    > The IP address of VNet1 should be the IP address you defined in the previous exercise.
    
    > SAN1 and SAN2 should be configured as DHCP clients.

1. Shut down the computer.

### Task 2: Verify changes on the destination server

Perform this task on VN1-SRV5.

1. Sign in as **ad\Administrator**.

    SConfig will start. The computer name will be VN1-SRV10.

1. Enter **8**.

    The IP addresses of the network adapters will be 10.1.1.80, 10.1.128.80, 10.1.144.80 and 10.1.160.40.

1. Press ENTER.
1. Enter **12**.
1. Enter **y**.

### Task 3: Verify client access to the shares

Perform this task on CL1.

1. In **File Explorer**, navigate to **\\\\VN1-SRV10**.
1. In the context menu of **Finance**, click **Properties**.
1. In Finance properties, click the tab **Security**.

    > Beyond entries for CREATOR OWNER, SYSTEM, and Administrators, Finance Read should have Read & execute, and Finance Modify should have Modify permissions.

1. Click **Cancel**
1. Double-click **Finance**.

    > The content of Finance should be the same as throughout other labs.

If time permits, repeat from step 2 for the other shares.
