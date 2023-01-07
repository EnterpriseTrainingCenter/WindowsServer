# Lab: Implementing IP address management

## Required VMs

* VN1-SRV1
* VN1-SRV4
* VN1-SRV6
* VN1-SRV7
* VN1-SRV8
* VN2-SRV1
* VN2-SRV2
* PM-SRV1
* PM-SRV2
* CL1

## Setup

On **CL1**, sign in as **ad\Administrator**.

## Known issues and workarounds

<https://github.com/EnterpriseTrainingCenter/WindowsServer/issues/181>
<https://github.com/EnterpriseTrainingCenter/WindowsServer/issues/182>

## Introduction

Because of the growing complexity of managing IP addresses and host names, you must implement IP address management for Adatum. You manage IP address blocks and ranges. You manage DHCP and DNS with the new solution. You allocate and track IP addresses with IPAM.

## Exercises

1. [Configuring the IP Address Management server](#exercise-1-configuring-the-ip-address-management-server)
1. [Mananaging IP address blocks, ranges, and subnets](#exercise-2-mananaging-ip-address-blocks-ranges-and-subnets)
1. [Managing DHCP and DNS servers using IPAM](#exercise-3-managing-dhcp-and-dns-servers-using-ipam)
1. [Managing IP addresses](#exercise-4-managing-ip-addresses)
1. [Tracking IP addresses](#exercise-5-tracking-ip-addresses)

## Exercise 1: Configuring the IP Address Management server

1. [Install the IP Address Management (IPAM) Client](#task-1-install-the-ip-address-management-ipam-client) on CL1
1. [Install the IP Address Management (IPAM) feature](#task-2-install-the-ip-address-management-ipam-feature) on VN1-SRV8
1. [Create the database for IPAM](#task-3-create-the-database-for-ipam) on VN1-SRV3 and grant VN1-SRV8 the necessary permissions
1. [Provision the IP Address Management (IPAM) server](#task-4-provision-the-ip-address-management-ipam-server)
1. [Discover servers](#task-5-discover-servers)

### Task 1: Install the IP Address Management (IPAM) Client

#### Desktop experience

Perform this task on CL1.

1. Open **Settings**.
1. In Settings, click **Apps**.
1. In Apps, click **Optional features**.
1. In Optional features, click the button **View features**.
1. In Add an optional feature, in the text field **Find an available optional feature**, type **RSAT**.
1. Activate the check box beside **RSAT: IP Address Management (IPAM) Client**.
1. Click **Next**.
1. Click **Install**.
1. If required, restart the computer.

You do not need to wait for the completion of the installation.

#### PowerShell

Perform these steps on CL1.

1. In the context menu of **Start**, click **Terminal (Admin)**.
1. Add the windows capabilities **RSAT: IP Address Management (IPAM) Client**.

    ````powershell
    Get-WindowsCapability -Online -Name 'Rsat.IPAM.Client.Tools*' |
    Add-WindowsCapability -Online
    ````

You do not need to wait for the completion of the installation.

### Task 2: Install the IP Address Management (IPAM) feature

Perform this task on CL1.

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on the page **Before You Begin**, click **Next >**.
1. On the page Installation Type, ensure **Role-based or feature-based installation** is selected and click **Next >**.
1. On the page Server Selection, click **VN1-SRV8.ad.adatum.com** and click **Next >**.
1. On the page Server Roles, click **Next >**.
1. On the page Features, activate **IP Address Management (IPAM) Server**.
1. In Add features that are required for IP Address Management (IPAM) Server, click **Add Features**.
1. In the **Add Rules and Features Wizard**, on the page **Features** click **Next >**.
1. On the page DHCP Server, click **Next >**.
1. On the page Confirmation, verify your selection, activate **Restart the destination server automatically if required**, and click **Install**.
1. On the page **Results**, click **Close**.

#### Windows Admin Center

Perform this task on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, on the connections page, click **vn1-srv8.ad.adatum.com**.
1. Connected to vn1-srv5.ad.adatum.com, under **Tools**, click **Roles & features**.
1. In Roles and features, activate the checkbox beside **IP Address Management (IPAM) Server** and click **Install**.
1. In the pane Install Role and Features, activate the checkbox **Reboot the server automatically, if required** and click **Yes**.

After a few minutes, a notification **Install Roles and Features** appears. If you missed the notification, a small number appears beside the icon *Notifications* (in form of a bell) at the top-right of Windows Admin Center.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install **IP Address Management (IPAM) Server** on VN1-SRV8 and VN1-SRV9.

    ````powershell
    Install-WindowsFeature `
        -ComputerName VN1-SRV8 -Name IPAM -IncludeManagementTools -Restart 
    ````

### Task 3: Create the database for IPAM

Perform this task on CL1.

1. Open **Microsoft SQL Server Management Studio 18**.
1. In Connect to Server, in **Server name**, type **vn1-srv3.ad.adatum.com** and click **Connect**.
1. In **Microsoft SQL Server Management Studio 18 (Administrator)**, expand **Databases**.
1. In the context-menu of **Databases**, click **New Database...**
1. In New Database, in **Database name**, type **IPAM-VN1-SRV8** and click **OK**.
1. In **Microsoft SQL Server Management Studio 18 (Administrator)**, expand **Security**, **Logins**.
1. In the context-menu of **Logins**, click **New Login...**.
1. In Login - New, in **Login name**, type **ad\vn1-srv8$**.
1. Click the page **User Mapping**.
1. On page User Mapping, under **Users mapped to this login**, activate **IPAM-VN1-SRV8**. Under **Database role memberhip for: IPAM-VN1-SRV8**, activate **db_owner**.
1. Click **OK**.

### Task 4: Provision the IP Address Management (IPAM) server

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click **IPAM**.
1. In Server Manager > IPAM > OVERVIEW, under **IPAM SERVER TASKS**, click **Connect to IPAM server**.
1. In Connect to an IPAM server, click **VN1-SRV8.ad.adatum.com** and click **OK**.
1. In **Server Manager**, click **Provision the IPAM server**.
1. In Provision IPAM, on page Before you begin, click **Next >**.
1. On page Configure database, click **Microsoft SQL Server**. In **Server name**, type **vn1-srv3.ad.adatum.com**. In **Database name**, type **IPAM-VN1-SRV8**. Click **Next >**.
1. On page Database credentials, ensure **IPAM server credentials** is selected, and click **Next >**.
1. On page Select provisioning method, ensure **Group Policy Based** is selected. In **GPO name prefix**, type **IPAM-VN1-SRV8**. Click **Next >**.
1. On page Confirm the Settings, click **Apply**.
1. On page Completion, click **Close**.
1. In **Server Manager**, click **All Servers**.
1. In All Servers, under **SERVERS**, in the context-menu of **VN1-SRV8**, click **Restart Server**.
1. In Are you sure you want to restart these servers, click **OK**.

### Task 5: Discover servers

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click **IPAM**.
1. In Server Manager > IPAM > OVERVIEW, under **IPAM SERVER TASKS**, click **Configure server discovery**.
1. In Configure Server Discovery, under **Select the forest**, click **Get forests**.
1. Close **Configure Server Discovery**.

    In Server Manager > IPAM > OVERVIEW, wait until a yellow banner stating **Discovered servers are based on: .... Please refresh to update the view** apears.

1. Click **Configure server discovery**.
1. In Configure Server Discovery, under **Select domains to discover**, click **Add**.
1. Click **OK**.
1. Open **Terminal**
1. Provision the group poolicy objects for IPAM.

    ````powershell
    Invoke-IpamGpoProvisioning `
        -Domain ad.adatum.com `
        -GpoPrefixName IPAM-VN1-SRV8 `
        -IpamServerFqdn vn1-srv8.ad.adatum.com
    ````

1. Switch to **Server Manager**.
1. In Server Manager > IPAM > OVERVIEW, under **IPAM SERVER TASKS**, click **Start server discovery**.

    Wait until the discovery finishes. This will take a few minutes.

1. Click **SERVER INVENTORY**.

    You should see VN1-SRV1, VN1-SRV6, VN1-SRV7, VN2-SRV1, VN2-SRV2, PM-SRV1, and PM-SRV2 with a **Recommended Action** of **Set Manageability Status**.

1. Click on the column header **Recommended Action** to sort the server list.
1. Click the first server with a **Recommended Action** of **Set Manageability Status**, hold down SHIFT and click the last server with a **Recommended Action** of **Set Manageability Status**.
1. In the context menu of one of the selected servers, click **Edit Server...**
1. In Add or Edit Server, in **Manageability Status**, click **Managed** and click **OK**.

    The **Recommended Action** changes to **Unblock IPAM Access**.

1. Switch to **Terminal**.
1. Refresh group policies on VN1-SRV1, VN1-SRV6, VN1-SRV7, VN2-SRV1, VN2-SRV2, PM-SRV1, and PM-SRV2.

    ````powershell
    Invoke-Command `
        -ComputerName @(
            'VN1-SRV1'
            'VN1-SRV6'
            'VN1-SRV7'
            'VN2-SRV1'
            'VN2-SRV2'
            'PM-SRV1'
            'PM-SRV2'
        ) `
        -ScriptBlock {
            gpupdate.exe
        }

1. Switch to **Server Manager**.
1. With servers with a **Recommended Action** of **Unblock IPAM Access** still selected, in the context-menu of one of the selected server, click **Refresh Server Access Status**.

    Wait for the task to complete.

1. Click the icon *Refresh (IPv4)*.
1. Click **TASKS**, **Retrieve All Server Data**.

    Wait for the task to complete.

## Exercise 2: Mananaging IP address blocks, ranges, and subnets

1. [Add IP address blocks](#task-1-add-ip-address-blocks) for the networks 10.0.0.0/8 and 85.13.142.192/28
1. [Add IP address ranges](#task-2-add-ip-address-ranges) for the network 10.1.200.254 with the gateway 10.1.200.1,  the DNS server 10.1.1.8, and the connection-specific DNS suffix of ad.adatum.com, and the network 85.13.142.192/28 with the gateway 85.13.142.193.
1. [Manage IP address subnets](#task-3-manage-ip-address-subnets) by naming the subnets VNet1, VNet2, Perimeter, and External

### Task 1: Add IP address blocks

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click **IPAM**.
1. In Server Manager > IPAM > OVERVIEW, in the left pane, under **IP ADDRESS SPACE**, click **IP Address Blocks**.
1. In Server Manager > IPAM > IP ADDRESS SPACE > IP Address Blocks > IPv4, in  **Current view**, click **IP Address Blocks**.
1. Click **TASKS**, **Add IP Address Block...**
1. In Add or Edit IPv4 Address Block, in **Network ID**, type **10.0.0.0**. In **Prefix length**, click **8**. Click **OK**.
1. In **Server Manager > IPAM > IP ADDRESS SPACE > IP Address Blocks > IPv4**, click **TASKS**, **Add IP Address Block...**
1. In Add or Edit IPv4 Address Block, in **Network ID**, type **85.13.142.192**. In **Prefix length**, click **28**. In **Regional internet registry (RIR)**, click **RIPE**. Click **OK**.
1. In **Server Manager > IPAM > IP ADDRESS SPACE > IP Address Blocks > IPv4**.

### Task 2: Add IP address ranges

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click **IPAM**.
1. In Server Manager > IPAM > OVERVIEW, in the left pane, under **IP ADDRESS SPACE**, click **IP Address Range Groups**.
1. In Server Manager > IPAM > IP ADDRESS SPACE > IP Address Blocks > IPv4, click **TASKS**, **Add IP Address Range...**
1. In Add IPv4 Address Range, in **Network ID**, type **10.1.200.0**. In **Prefix length (21 - 32)**, type **24**. Activate **Automatically create IP address subnet**.
1. In **Start IP address**, type **10.1.200.2**. In End IP address, type **10.1.200.254**.
1. In **Managed by service**, ensure **IPAM** is selected. In **Service instance**, ensure **Localhost** is selected. In **Assignment type**, ensure **Static** is selected. In **Assignment date**, select the current date.
1. Under **WINS and DNS Servers**, under **DNS servers in order of priority**, type **10.1.1.8** and click **Add**. Under **Connection-specific DNS suffix**, type **ad.adatum.com**.
1. Under **Gateway IP Address and Metric**, in **Gateway**, type **10.1.200.1** and click **Add**.
1. Click **OK**.
1. In **Server Manager > IPAM > IP ADDRESS SPACE > IP Address Blocks > IPv4**, click **TASKS**, **Add IP Address Range...**
1. In Add IPv4 Address Range, in **Network ID**, type **85.13.142.192**. In **Prefix length (26 - 32)**, type **28**. Activate **Automatically create IP address subnet**.
1. In **Start IP address**, type **85.13.142.193**. In End IP address, type **85.13.142.206**.
1. In **Managed by service**, ensure **IPAM** is selected. In **Service instance**, ensure **Localhost** is selected. In **Assignment type**, ensure **Static** is selected. In **Assignment date**, select the current date.
1. Under **Gateway IP Address and Metric**, in **Gateway**, type **85.13.142.193** and click **Add**.
1. Under **Reservations and VIPs**, in **Reservation**, type **85.13.142.193** and click **Add**.
1. Click **OK**.

### Task 3: Manage IP address subnets

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click **IPAM**.
1. In Server Manager > IPAM > OVERVIEW, in the left pane, under **IP ADDRESS SPACE**, click **IP Address Blocks**.
1. In Server Manager > IPAM > IP ADDRESS SPACE > IP Address Block > IPv4, in **Current View**, click **IP Address Subnets**.
1. In the context-menu of Network **10.1.1.0/24**, click **Edit IP Address Subnet...**
1. In Edit IP Address Subnet, in Name, type **VNet1** and click **OK**.
1. In **Server Manager > IPAM > IP ADDRESS SPACE > IP Address Block > IPv4**, in the context-menu of Network **10.1.2.0/24**, click **Edit IP Address Subnet...**
1. In Edit IP Address Subnet, in Name, type **VNet2** and click **OK**.
1. In **Server Manager > IPAM > IP ADDRESS SPACE > IP Address Block > IPv4**, in the context-menu of Network **10.1.200.0/24**, click **Edit IP Address Subnet...**
1. In Edit IP Address Subnet, in Name, type **Perimeter** and click **OK**.
1. In **Server Manager > IPAM > IP ADDRESS SPACE > IP Address Block > IPv4**, in the context-menu of Network **85.13.141.192/28**, click **Edit IP Address Subnet...**
1. In Edit IP Address Subnet, in Name, type **Perimeter** and click **OK**.

## Exercise 3: Managing DHCP and DNS servers using IPAM

1. [Create a DHCP scope using IPAM](#task-1-create-a-dhcp-scope-using-ipam) for VNet3 on VN1-SRV6
1. [Create a DNS zone using IPAM](#task-2-create-a-dns-zone-using-ipam) on PM-SRV1 with the name contoso.com
1. [Create a DNS resource record using IPAM](#task-3-create-a-dns-resource-record-using-ipam) in the zone contoso.com with the type MX pointing to smtp.contoso.com

### Task 1: Create a DHCP scope using IPAM

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click **IPAM**.
1. In Server Manager > IPAM > OVERVIEW, in the left pane, under **MONITOR AND MANAGE**, click **DNS and DHCP Servers**.
1. In Server Manager > IPAM > MONITOR AND MANAGE > DNS and DHCP Servers > IPv4, in the context-menu of **vn1-srv6.ad.adatum.com**, click **Create DHCP Scope...**.
1. In Create Scope, under **GENERAL PROPERTIES**, in **Scope name**, type **VNet3**. In **Start IP address**, type **10.1.3.2**. In **End IP address**, type **10.1.3.254**. In **Subnet mask**, type **255.255.255.0**. Set the **Lease duration for DHCP clients** to 2 hours.
1. Under **DHCP Scope Options**, click **New**.
1. Under New Configuration, in **Option**, click **003 Router**. Under **IP Address**, double-click the first line and type 10.1.3.1. Click **Add Configuration**.
1. Click **OK**.
1. In **Server Manager > IPAM > MONITOR AND MANAGE > DNS and DHCP Servers > IPv4**, in the left pane, click **IP Address Range Groups**.
1. Click the icon *Refresh (IPv4)*.

    You should see a new IP Address Range for the network 10.1.3.0/24. In the bottom pane, you might want to investigate its properties.

1. Open **DHCP**.
1. In DHCP, in the context-menu of **DHCP**, click **Add Server...**
1. In Add Server, under **This server**, type **VN1-SRV6"** and click **OK**.
1. In **DHCP**, expand **vn1-srv6.ad.adatum.com**, **IPv4**.

    You should see the new **Scope [10.1.3.0] VNet3**.

1. Expand **Scope [10.1.3.0] VNet3** and click **Scope Options**.

    You should see the option for the router.

### Task 2: Create a DNS zone using IPAM

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click **IPAM**.
1. In Server Manager > IPAM > OVERVIEW, in the left pane, under **MONITOR AND MANAGE**, click **DNS and DHCP Servers**.
1. In Server Manager > IPAM > MONITOR AND MANAGE > DNS and DHCP Servers > IPv4, in the context-menu of **pm-srv1.ad.adatum.com**, click **Create DNS Zone...**.
1. In Create DNS Zone, under **General Properties**, in **Zone catgory**, ensure **Forward Lookup zone** is selected. In **Zone type**, ensure **Primary zone** is selected. In **Zone name**, type **contoso.com**.
1. Under **Advanced Properties**, in **Store the zone in**, click **Zone file**. In **Dynamic update**, click **Do not allow dynamic updates**.
1. Click **OK**.

### Task 3: Create a DNS resource record using IPAM

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click **IPAM**.
1. In Server Manager > IPAM > OVERVIEW, in the left pane, under **MONITOR AND MANAGE**, click **DNS Zones**.
1. In Server Manager > IPAM > MONITOR AND MANAGE > DNS Zones > Forward Lookup, in the context-menu of **contoso.com**, click **Add DNS resource record...**
1. In Add Resource Record, click **New**.
1. Under **New resource record**, in **Resource record type**, click **MX**. In **Fully qualified domain name (FQDN) of mail server**, type **smtp.contoso.com**.
1. Click **Add resource record**.
1. Click **OK**.
1. In the left pane, click the arrow beside **Forward Lookup** and click **contoso.com**.

    In Server Manager > IPAM > MONITOR AND MANAGE > DNS Zones > Forward Lookup > contoso.com, you should see the MX record, you just created.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **pm-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **pm-srv1.ad.adatum.com**.
1. Expand **pm-srv1.ad.adatum.com**, **Forward Lookup Zones** and click **contoso.com**.

    You should see the MX record you just created.

## Exercise 4: Managing IP addresses

1. [Allocate and manage a static IP address](#task-1-allocate-and-manage-a-static-ip-address) by finding the first available IP address in the external address range and create DNS host record with the name web1 in contoso.com
1. [Allocate and manage a dynamic IP addresses](#task-2-allocate-and-manage-a-dynamic-ip-addresses): Convert the IP addres 10.1.3.8 into a DHCP reservation. Find the next available IP address in VNet3 and create a DHCP reservation and the DNS host record VN3-SRV2 in ad.adatum.com.
1. [Find host and user name for IP address](#task-3-find-host-and-user-name-for-ip-address) 10.1.1.2

### Task 1: Allocate and manage a static IP address

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click **IPAM**.
1. In Server Manager > IPAM > OVERVIEW, in the left pane, under **IP ADDRESS SPACE**, click **IP Address Range Groups**.
1. In Server Manager > IPAM > IP ADDRESS SPACE > IP Address Range Groups, in the context-menu of the range with a start address of **85.13.142.207**, click **Find and Allocate Available IP Address...**.
1. In Find and Allocate Available IP Address, wait for the **Ping Reply Status**. If it changes to **Reply**, click **Find Next**. If the next IP address also sends a reply, ignore it.

    Under **Basic Configurations**, review the settings.

1. Under **DNS Record Synchronization**, in **Device name**, type **web1**. In **Forward lookup zone**, click **contoso.com**. In **Forward lookup primary server**, click **pm-srv1.ad.adatum.com**. Activate **Automatically create DNS records for this IP address**.
1. Click **OK**.
1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **pm-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **pm-srv1.ad.adatum.com**.
1. Expand **pm-srv1.ad.adatum.com**, **Forward Lookup Zones** and click **contoso.com**.

    You should see the A record you just created.

### Task 2: Allocate and manage a dynamic IP addresses

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click **IPAM**.
1. In Server Manager > IPAM > OVERVIEW, in the left pane, under **IP ADDRESS SPACE**, click **IP Address Inventory**.
1. In Server Manager > IPAM > IP ADDRESS SPACE > IP Address Inventory, in the context-menu of **10.1.3.8**, click **Edit IP Address...**.
1. In Edit IPv4 Address, under **Basic Configurations**, in MAC address, type **00-15-5d-00-03-08**.
1. In the left pane, click **DHCP Reservation**.
1. Under DHCP Reservation Synchronization, activate **Associate MAC to Client ID**. In **Reservation Server name**, click **vn1-srv6.ad.adatum.com**. In **Reservation type**, click **Both**. Ensure **Update 'Managed By Service' and 'Service Instance' with the reservation server details** and **Automatically create DHCP reservation for this IP address** are activated.
1. Click **OK**.
1. Open **DHCP**.
1. In DHCP, in the context-menu of **DHCP**, click **Add Server...**
1. In Add Server, under **This server**, type **VN1-SRV6"** and click **OK**.
1. In **DHCP**, expand **vn1-srv6.ad.adatum.com**, **IPv4**, **Scope [10.1.3.0] VNet3** and click **Reservations**.

    You should see the reservation for 10.1.3.8.

1. Switch to **Server Manager**.
1. In Server Manager > IPAM > OVERVIEW, in the left pane, under **IP ADDRESS SPACE**, click **IP Address Range Groups**.
1. In Server Manager > IPAM > IP ADDRESS SPACE > IP Address Range Groups, in the context-menu of the range with a start address of **10.1.3.2**, click **Find and Allocate Available IP Address...**.
1. In Find and Allocate Available IP Address, wait for the **Ping Reply Status**. If it changes to **Reply**, click **Find Next**. If the next IP address also sends a reply, ignore it.
1. Under **Basic Configurations**, in **MAC address**, type **00-15-5d-00-03-16**
1. In the left pane, click **DHCP Reservation**.
1. Under DHCP Reservation Synchronization, activate **Associate MAC to Client ID**. In **Reservation Server name**, click **vn1-srv6.ad.adatum.com**. In **Reservation type**, click **Both**. Ensure **Update 'Managed By Service' and 'Service Instance' with the reservation server details** and **Automatically create DHCP reservation for this IP address** are activated.
1. In the left pane, click **DNS Record**.
1. Under DNS Record Synchronization, in **Device name**, type **vn3-srv2**. In **Forward lookup zone**, click **ad.adatum.com*. In **Forward lookup primary server**, click **VN1-SRV1.ad.adatum.com**. Activate **Automatically create DNS records for this IP address**.
1. Click **OK**.
1. Switch to **DHCP**.
1. In **DHCP**, expand **vn1-srv6.ad.adatum.com**, **IPv4**., **Scope [10.1.3.0] VNet3** and click **Reservations**.

    You should see the reservation for 10.1.3.8.
1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **pm-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **pm-srv1.ad.adatum.com**.
1. Expand **pm-srv1.ad.adatum.com**, **Forward Lookup Zones** and click **contoso.com**.

    You should see the A record you just created.

### Task 3: Find host and user name for IP address

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click **IPAM**.
1. In Server Manager > IPAM > OVERVIEW, in the left pane, click **EVENT CATALOG**.
1. In the left pane, under **IP Address Tracking**, click **By IP Address**.
1. Under Server Manager > IPAM > EVENT CATALOG > IP Address Tracking > By IP Address, in **By IP Address**, type 10.1.1.2. In the two date fields below, type today's date. Click **Search**.

    You should see various events that let you correlate the IP address to a client ID, a host name and even a user name to a given point in time.

    If nothing is found, find the IP address of CL1 and try again.

If time permits, perform similar queries by host name, by user name, or by client id.