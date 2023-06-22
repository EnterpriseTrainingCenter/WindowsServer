# Lab: Managing DNS

## Reuqired VMs

* VN1-SRV1
* VN2-SRV1
* PM-SRV1
* PM-SRV2
* CL1

## Setup

On CL1, sign in as **ad\Administrator**.

## Introduction

Adatum wants to host its public DNS zone in the perimeter network. Therefore, a new primary zone with resource records must be created. To provide fault-tolerance, the new zone must be replicated to a second DNS server using zone transfers. Moreover, Adatum expands to china. A new child domain for China must be created for the internal DNS domain.

Because from the intranet, some resources in adatum.com can only be accessed through internal IP address, you must implement split-brain DNS. You want to evaluate complete split-brain DNS, a pinpoint DNS zone, and DNS policies to achieve this goal.

Adatum discovers that it would be practical to be able to resolve IP addresses to host names. Therefore, you need to implement reverse lookup zones.

Adatum uses some old NetBIOS applications, which cannot use FQDNs. To avoid the implementation of WINS, Adatum decides to implement the Global Names zone.

Finally, Adatum discovers that the DNS server for China is used by other DNS servers as a forwarder for the parent domain. For performance reasons, Adatum wants to avoid this. Therefore, the conditional forwarder needs to be replaced by a stub zone.

## Exercises

1. [Managing primary zones and delegation](#exercise-1-managing-primary-zones-and-delegation)
1. [Managing secondary zones](#exercise-2-managing-secondary-zones)
1. [Managing split-brain DNS](#exercise-3-managing-split-brain-dns)
1. [Managing DNS policies](#exercise-4-managing-dns-policies)
1. [Managing reverse lookup zones](#exercise-5-managing-reverse-lookup-zones)
1. [Managing the Global Names zone](#exercise-6-managing-the-global-names-zone)
1. [Managing stub zones](#exercise-7-managing-stub-zones)

## Exercise 1: Managing primary zones and delegation

1. [Create a primary zone](#task-1-create-a-primary-zone) adatum.com without enabling dynamic updates on PM-SRV1
1. [Create resource records](#task-2-create-resource-records) in adatum.com according to the table below

    | Type  | Name                   | Value                                          |
    |-------|------------------------|------------------------------------------------|
    | A     | remote                 | 20.53.203.50                                   |
    | A     | remote                 | 20.81.111.85                                   |
    | MX    | @                      | 0 adatum-com.mail.protection.outlook.com       |
    | TXT   | @                      | v=spf1 include:spf.protection.outlook.com -all |
    | CNAME | autodiscover           | autodiscover.outlook.com                       |
    | CNAME | sip                    | sipdir.online.lync.com                         |
    | CNAME | lyncdiscover           | webdir.online.lync.com                         |
    | SRV   | _sip._tls              | 100 1 443 sipdir.online.lync.com               |
    | SRV   | _sipfederationtls._tcp | 100 1 5061 sipfed.online.lync.com              |
    | CNAME | enterpriseregistration | enterpriseregistration.windows.net             |
    | CNAME | enterpriseenrollment   | enterpriseenrollment.manage.microsoft.com      |

1. [Verify the resource records by resolving the names](#task-3-verify-the-resource-records-by-resolving-the-names)
1. [Create a primary zone](#task-4-create-a-primary-zone) china.ad.adatum.com on VN2-SRV1
1. [Create resource records](#task-5-create-resource-records) in china.ad.adatum.com according to the table below

    | Type | Name | Value     |
    |------|------|-----------|
    | A    | @    | 10.1.2.8  |
    | A    | @    | 10.1.2.16 |

1. [Verify the name resolution](#task-6-verify-the-name-resolution) of china.ad.adatum.com

    > Why are you unable to resolve the name when using VN1-SRV1 as DNS server?

1. [Create a delegation](#task-7-create-a-delegation) for china.ad.adatum.com on VN1-SRV1
1. [Verify the name resolution](#task-8-verify-the-name-resolution-of-the-delegated-domain) of the delegated domain

### Task 1: Create a primary zone

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **pm-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **pm-srv1.ad.adatum.com**

    If pm-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **pm-srv1.ad.adatum.com** below and click **OK**.

1. Expand **pm-srv1.ad.adatum.com** and click **Forward Lookup Zones**.
1. In the context-menu of **Forward Lookup Zones**, click **New Zone...**
1. In the New Zone Wizard, on page Welcome to the New Zone Wizard, click **Next >**.
1. On page Zone Type, ensure **Primary zone** is selected and click **Next >**.
1. On page Zone Name, under **Zone name**, type **adatum.com** and click **Next >**.
1. On page Zone File, ensure **Create a new file with this file name** is selected and **adatum.com.dns** is filled in. Click **Next >**
1. On page Dynamic Update, ensure **Do not allow dynamic updates** is selected and click **Next >**.
1. On page Completing the New Zone Wizard, click **Finish**.

### Task 2: Create resource records

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **pm-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **pm-srv1.ad.adatum.com**

    If pm-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **pm-srv1.ad.adatum.com** below and click **OK**.

1. Expand **pm-srv1.ad.adatum.com**, **Forward Lookup Zones**, and click **adatum.com**.
1. In the context-menu of **adatum.com**, click **New Host (A or AAAA)...**
1. In New Host, under **Name (uses parent domain name if blank)**, type **remote**. Under IP address, type **20.53.203.50**. Click **Add Host**.
1. In the message box The host record remote.adatum.com was successfully created, click **OK**.
1. In **New Host**, under **Name (uses parent domain name if blank)**, type **remote**. Under IP address, type **20.81.111.85**. Click **Add Host**.
1. In the message box The host record remote.adatum.com was successfully created, click **OK**.
1. In **New Host**, click **Done**.
1. In **DNS Manager**, in the context-menu of **adatum.com**, click **New Mail Exchanger (MX)...**
1. In New Resource Record, leave the text field under **Host or child domain** blank. In **Fully qualified domain name (FQDN) of mail server**, type **adatum-com.mail.protection.outlook.com**. In **Mail server priority**, type **0**. Click **OK**.
1. In **DNS Manager**, in the context-menu of **adatum.com**, click **Other New Records...**
1. In Resource Record Type, under **Select a resource record type**, click **Text (TXT)** and click **Create Record...**
1. In New Resource Record, leave the text field under **Record name (uses parent domain if left blank)** blank. In **Text**, type **v=spf1 include:spf.protection.outlook.com -all**. Click **OK**.
1. In **DNS Manager**, in the context-menu of **adatum.com**, click **Other New Records...**
1. In **Resource Record Type**, under **Select a resource record type**, click **Service Location (SRV)** and click **Create Record...**
1. In New Resource Record, in **Service**, type **_sip**. In **Protocol**, type **_tls**. In **Priority**, type **100**. In **Weight**, type **1**. In **Port**, type **443**. In **Host offering this service**, type **sipdir.online.lync.com**. Click **OK**.
1. In **Resource Record Type**, under **Select a resource record type**, click **Service Location (SRV)** and click **Create Record...**
1. In New Resource Record, in **Service**, type **_sipfederationtls**. In **Protocol**, type **_tcp**. In **Priority**, type **100**. In **Weight**, type **1**. In **Port**, type **5061**. In **Host offering this service**, type **sipfed.online.lync.com**. Click **OK**.
1. In **Resource Record Type**, click **Done**.
1. In **DNS Manager**, in the context-menu of **adatum.com**, click **New Alias (CNAME)**
1. In New Resource Record, under **Alias name (uses parent domain if left blank)**, type **autodiscover**. Under **Fully qualified domain name (FQDN) for target host**, type **autodiscover.outlook.com**. Click **OK**.

Repeat the last two steps to create remaining aliases according to the table below.

| Alias name             | FQDN for target host                      |
|------------------------|-------------------------------------------|
| sip                    | sipdir.online.lync.com                    |
| lyncdiscover           | webdir.online.lync.com                    |
| enterpriseregistration | enterpriseregistration.windows.net        |
| enterpriseenrollment   | enterpriseenrollment.manage.microsoft.com |

### Task 3: Verify the resource records by resolving the names

Perform this task on CL1.

1. Open **Terminal**.
1. Resolve the host name remote.adatum.com on PM-SRV1.

    ````powershell
    $server = 'PM-SRV1'
    Resolve-DnsName -Name remote.adatum.com -Type A -Server $server
    ````

    > You should get the IP addresses 20.53.203.50 and 20.81.111.85 as response.

1. Resolve the mail exchanger for adatum.com on PM-SRV1.

    ````powershell
    Resolve-DnsName -Name adatum.com -Type MX -Server $server
    ````

    > You should get adatum-com.mail.protection.outlook.com as response.

1. Resolve the text record for adatum.com on PM-SRV1.

    ````powershell
    Resolve-DnsName -Name adatum.com -Type TXT -Server $server
    ````

    > You should get {v=spf1 include:spf.protection.outlook.com -all} as response.

1. Resolve the service location record for _sip._tls.adatum.com on PM-SRV1.

    ````powershell
    Resolve-DnsName -Name _sip._tls.adatum.com -Type SRV -Server $server
    ````

    > You should get sipdir.online.lync.com as NameTarget with priority 100, weight 1, and port 443 as response.

1. Resolve the service location record for _sipfederationtls._tcp on PM-SRV1.

    ````powershell
    Resolve-DnsName `
        -Name _sipfederationtls._tcp.adatum.com -Type SRV -Server $server
    ````

    > You should get sipfed.online.lync.com as NameTarget with priority 100, weight 1, and port 5061 as response.

1. Resolve the alias record for autodiscover.adatum.com on PM-SRV1.

    ````powershell
    Resolve-DnsName -Name autodiscover.adatum.com -Type CNAME -Server $server
    ````

    > You should get autodiscover.outlook.com as response.

Repeat the last step to verify the alias records according to the table below.

| Alias name             | FQDN for target host                      |
|------------------------|-------------------------------------------|
| sip                    | sipdir.online.lync.com                    |
| lyncdiscover           | webdir.online.lync.com                    |
| enterpriseregistration | enterpriseregistration.windows.net        |
| enterpriseenrollment   | enterpriseenrollment.manage.microsoft.com |

### Task 4: Create a primary zone

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn2-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn2-srv1.ad.adatum.com**

    If vn2-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn2-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn2-srv1.ad.adatum.com** and click **Forward Lookup Zones**.
1. In the context-menu of **Forward Lookup Zones**, click **New Zone...**
1. In the New Zone Wizard, on page Welcome to the New Zone Wizard, click **Next >**.
1. On page Zone Type, ensure **Primary zone** is selected and click **Next >**.
1. On page Zone Name, under **Zone name**, type **china.ad.adatum.com** and click **Next >**.
1. On page Zone File, ensure **Create a new file with this file name** is selected and **china.ad.adatum.com.dns** is filled in. Click **Next >**
1. On page Dynamic Update, ensure **Do not allow dynamic updates** is selected and click **Next >**.
1. On page Completing the New Zone Wizard, click **Finish**.

### Task 5: Create resource records

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn2-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn2-srv1.ad.adatum.com**

    If vn2-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn2-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn2-srv1.ad.adatum.com**, **Forward Lookup Zones**, and click **china.ad.adatum.com**.
1. In the context-menu of **adatum.com**, click **New Host (A or AAAA)...**
1. In New Host, leave **Name (uses parent domain name if blank)** blank. Under IP address, type **10.1.2.8**. Click **Add Host**.
1. In the message box The host record china.ad.adatum.com was successfully created, click **OK**.
1. In **New Host**, leave **Name (uses parent domain name if blank)** blank. Under IP address, type **10.1.2.16**. Click **Add Host**.
1. In the message box The host record china.ad.adatum.com was successfully created, click **OK**.
1. In **New Host**, click **Done**.

### Task 6: Verify the name resolution

Perform this task on CL1.

1. Open **Terminal**.
1. Resolve the host name china.ad.adatum.com on VN2-SRV1.

    ````powershell
    Resolve-DnsName `
        -Name china.ad.adatum.com -Type A -Server vn2-srv1.ad.adatum.com
    ````

    > You should get the IP addresses 10.1.2.8 and 10.1.2.16 as response.

1. Resolve the host name china.ad.adatum.com using the default DNS server.

    ````powershell
    Resolve-DnsName -Name china.ad.adatum.com -Type A
    ````

    > You should get an error message DNS name does not exist, because the DNS server vn1-srv1.ad.adatum.com does not know the sub-domain china.ad.adatum.com.

### Task 7: Create a delegation

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn1-srv1.ad.adatum.com**

    If vn1-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn1-srv1.ad.adatum.com**, **Forward Lookup Zones**, and click **ad.adatum.com**.
1. In the context-menu of **ad.adatum.com**, click **New Delegation...**
1. In the New Delegation wizard, on page Welcome to the New Delegation Wizard, click **Next >**
1. On page Delegated Domain Name, under **Delegated domain**, type **china** and click **Next >**
1. On page Name Servers, click **Add...**
1. In New Name Server Record, under **Server fully qualified domain name (FQDN)**, type **vn2-srv1.ad.adatum.com** and click **Resolve**.
1. Click **OK**.
1. In the **New Delegation wizard**, on page Name Servers, click **Next >**
1. On page Completing the New Delegation Wizard, click **Finish**.

### Task 8: Verify the name resolution of the delegated domain

Perform this task on CL1.

1. Open **Terminal**.
1. Resolve the host name china.ad.adatum.com using the default DNS server with a non-recursive query.

    ````powershell
    Resolve-DnsName -Name china.ad.adatum.com -Type A -NoRecursion
    ````

    > You will get a DNS server failure, because vn1-srv1.ad.adatum.com is not authoritative for china.ad.adatum.com.

1. Query the name servers fro china.ad.adatum.com with a non-recursive query.

    ````powershell
    Resolve-DnsName -Name china.ad.adatum.com -Type NS -NoRecursion
    ````

    > You will get vn2-srv1.ad.adatum.com as response.

1. Resolve the host name china.ad.adatum.com using the default DNS server with a recursive query.

    ````powershell
    Resolve-DnsName -Name china.ad.adatum.com -Type A
    ````

    > You should get the IP addresses 10.1.2.8 and 10.1.2.16 as response.

1. Resolve the host name ad.adatum.com on vn2-srv1 with a non-recursive query.

    ````powershell
    Resolve-DnsName `
        -Name ad.adatum.com -Server vn2-srv1.ad.adatum.com -NoRecursion
    ````

    > You should get the IP address 10.1.1.8 as a response. The conditional forwarder always forwards the query to vn1-srv1.ad.adatum.com, regardless of the recursion flag. Because vn1-srv1.ad.adatum.com is authoritative for ad.adatum.com, you receive the response.

## Exercise 2: Managing secondary zones

1. [Add additional name server record](#task-1-add-additional-name-server-record) PM-SRV2 to adatum.com
1. [Create a secondary zone](#task-2-create-a-secondary-zone) for adatum.com on PM-SRV2
1. [Verify name resolution](#task-3-verify-name-resolution) for adatum.com on PM-SRV2

### Task 1: Add additional name server record

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **pm-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **pm-srv1.ad.adatum.com**

    If pm-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **pm-srv1.ad.adatum.com** below and click **OK**.

1. Expand **pm-srv1.ad.adatum.com** and click **Forward Lookup Zones**.
1. In the context-menu of **adatum.com**, click **Properties**.
1. In adatum.com Properties, click the tab **Name Servers**.
1. On tab Name Servers, click **Add...**.
1. In New Name Server Record, under **Server fully qualified domain name (FQDN)**, type **pm-srv2.ad.adatum.com** and click **Resolve**.

    Note: You can ignore the error message, that th server with this IP address is not authoritative for the required zone.

1. Click **OK**.
1. In **adatum.com Properties**, click **OK**.

### Task 2: Create a secondary zone

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **pm-srv2.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **pm-srv2.ad.adatum.com**.

    If pm-srv2.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **pm-srv2.ad.adatum.com** below and click **OK**.

1. Expand **pm-srv2.ad.adatum.com** and click **Forward Lookup Zones**.
1. In the context-menu of **Forward Lookup Zones**, click **New Zone...**
1. In the New Zone Wizard, on page Welcome to the New Zone Wizard, click **Next >**.
1. On page Zone Type, click **Secondary zone** and click **Next >**.
1. On page Zone Name, under **Zone name**, type **adatum.com** and click **Next >**.

    Note: You could also browse for the zone name.

1. On page Master DNS Servers, under **Master Servers** in **\<Click here to add and IP Address or DNS name\>**, enter **pm-srv1.ad.adatum.com**.
1. Click the entry **No such host is known** and click **Delete**.
1. Click **Next >**.
1. On page Completing the New Zone Wizard, click **Finish**.

### Task 3: Verify name resolution

Perform this task on CL1.

1. Open **Terminal**.
1. Resolve the host name **remote.adatum.com** on **pm-srv2.ad.adatum.com**.

    ````powershell
    Resolve-DnsName `
        -Name remote.adatum.com -Type A -Server pm-srv2.ad.adatum.com
    ````

    > You should get the IP addresses 20.53.203.50 and 20.81.111.85 as response.

If time permits, you may refer to [Exercise 1, Task 3: Verify the resource records by resolving the names](#task-3-verify-the-resource-records-by-resolving-the-names) to query additional records on pm-srv2.ad.adatum.com.

## Exercise 3: Managing split-brain DNS

Note: If you run short on time, you may skip the first 4 tasks in this exercise.

1. [Create an Active Directory integrated primary zone](#task-1-create-an-active-directory-integrated-primary-zone) adatum.com with forest-wide replication without enabling dynamic updates on VN1-SRV1
1. [Create resource records in the Active Directory integrated zone](#task-2-create-resource-records-in-the-active-directory-integrated-zone) adatum.com according to the table below

    | Type  | Name                   | Value                                          |
    |-------|------------------------|------------------------------------------------|
    | A     | remote                 | 10.1.200.24                                    |
    | A     | remote                 | 10.1.200.32                                    |
    | MX    | @                      | 0 smtp.ad.adatum.com                           |
    | TXT   | @                      | v=spf1 include:spf.protection.outlook.com -all |
    | CNAME | autodiscover           | autodiscover.outlook.com                       |
    | CNAME | sip                    | sipdir.online.lync.com                         |
    | CNAME | lyncdiscover           | webdir.online.lync.com                         |
    | SRV   | _sip._tls              | 100 1 443 sipdir.online.lync.com               |
    | SRV   | _sipfederationtls._tcp | 100 1 5061 sipfed.online.lync.com              |
    | CNAME | enterpriseregistration | enterpriseregistration.windows.net             |
    | CNAME | enterpriseenrollment   | enterpriseenrollment.manage.microsoft.com      |

    > What is the main problem with split-brain DNS?

1. [Verify the resource records by resolving the names](#task-3-verify-the-resource-records-by-resolving-the-names) on VN1-SRV1
1. [Delete the zone adatum.com](#task-4-delete-the-zone) from VN1-SRV1
1. [Create an Active Directory integrated conditional forwarder](#task-5-create-an-active-directory-integrated-conditional-forwarder) for adatum.com replicated forest-wide pointing to PM-SRV1 and PM-SRV2

    Note: In a real-world scenario, this task would not be necessary for a public zone.

1. [Create an Active Directory integrated primary pinpoint DNS zone](#task-6-create-an-active-directory-integrated-primary-pinpoint-dns-zone) remote.adatum.com with forest-wide replication without enabling dynamic updates on VN1-SRV1
1. [Create resource records in the pinpoint DNS zone](#task-7-create-resource-records-in-the-pinpoint-dns-zone) remote.adatum.com according to the table below

    | Type  | Name   | Value       |
    |-------|--------|-------------|
    | A     | @      | 10.1.200.24 |
    | A     | @      | 10.1.200.32 |

    > Why is the pinpoint DNS zone preferable over a complete split-brain DNS zone?

    > Why would you choose a complete split-brain DNS zone over the pinpoint DNS zone?

1. [Verify the resource records in the pinpoint DNS zone](#task-8-verify-the-resource-records-in-the-pinpoint-dns-zone) on VN1-SRV1

> Is there any difference to the complete split-brain DNS zone in the responses?

### Task 1: Create an Active Directory integrated primary zone

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn1-srv1.ad.adatum.com**

    If vn1-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn1-srv1.ad.adatum.com** and click **Forward Lookup Zones**.
1. In the context-menu of **Forward Lookup Zones**, click **New Zone...**
1. In the New Zone Wizard, on page Welcome to the New Zone Wizard, click **Next >**.
1. On page Zone Type, ensure **Primary zone** is selected, **Store this zone in Active Directory (available only if DNS server is a writeable domain controller)** is activated, and click **Next >**.
1. On page Active Directory Zone Replication Scope, click **To all DNS servers running on domain controller in this forest: ad.adatum.com** and click **Next >**.
1. On page Zone Name, under **Zone name**, type **adatum.com** and click **Next >**.
1. On page Dynamic Update, click **Do not allow dynamic updates** and click **Next >**.
1. On page Completing the New Zone Wizard, click **Finish**.

### Task 2: Create resource records in the Active Directory integrated zone

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn1-srv1.ad.adatum.com**

    If vn1-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn1-srv1.ad.adatum.com**, **Forward Lookup Zones**, and click **adatum.com**.
1. In the context-menu of **adatum.com**, click **New Host (A or AAAA)...**
1. In New Host, under **Name (uses parent domain name if blank)**, type **remote**. Under IP address, type **10.1.200.24**. Click **Add Host**.
1. In the message box The host record remote.adatum.com was successfully created, click **OK**.
1. In **New Host**, under **Name (uses parent domain name if blank)**, type **remote**. Under IP address, type **10.1.200.32**. Click **Add Host**.
1. In the message box The host record remote.adatum.com was successfully created, click **OK**.
1. In **New Host**, click **Done**.
1. In **DNS Manager**, in the context-menu of **adatum.com**, click **New Mail Exchanger (MX)...**
1. In New Resource Record, leave the text field under **Host or child domain** blank. In **Fully qualified domain name (FQDN) of mail server**, type **smtp.ad.adatum.com**. In **Mail server priority**, type **0**. Click **OK**.
1. In **DNS Manager**, in the context-menu of **adatum.com**, click **Other New Records...**
1. In Resource Record Type, under **Select a resource record type**, click **Text (TXT)** and click **Create Record...**
1. In New Resource Record, leave the text field under **Record name (uses parent domain if left blank)** blank. In **Text**, type **v=spf1 include:spf.protection.outlook.com -all**. Click **OK**.
1. In **Resource Record Type**, under **Select a resource record type**, click **Service Location (SRV)** and click **Create Record...**
1. In New Resource Record, in **Service**, type **_sip**. In **Protocol**, type **_tls**. In **Priority**, type **100**. In **Weight**, type **1**. In **Port**, type **443**. In **Host offering this service**, type **sipdir.online.lync.com**. Click **OK**.
1. In **Resource Record Type**, under **Select a resource record type**, click **Service Location (SRV)** and click **Create Record...**
1. In New Resource Record, in **Service**, type **_sipfederationtls**. In **Protocol**, type **_tcp**. In **Priority**, type **100**. In **Weight**, type **1**. In **Port**, type **5061**. In **Host offering this service**, type **sipfed.online.lync.com**. Click **OK**.
1. In **Resource Record Type**, click **Done**.
1. In **DNS Manager**, in the context-menu of **adatum.com**, click **New Alias (CNAME)**
1. In New Resource Record, under **Alias name (uses parent domain if left blank)**, type **autodiscover**. Under **Fully qualified domain name (FQDN) for target host**, type **autodiscover.outlook.com**. Click **OK**.

Repeat the last two steps to create remaining aliases according to the table below.

| Alias name             | FQDN for target host                      |
|------------------------|-------------------------------------------|
| sip                    | sipdir.online.lync.com                    |
| lyncdiscover           | webdir.online.lync.com                    |
| enterpriseregistration | enterpriseregistration.windows.net        |
| enterpriseenrollment   | enterpriseenrollment.manage.microsoft.com |

> Manually maintaining all the duplicate DNS records in two zones, can be cumbersome and error-prone.

### Task 3: Verify the resource records by resolving the names on VN1-SRV1

Perform this task on CL1.

1. Open **Terminal**.
1. Resolve the host name remote.adatum.com on VN1-SRV1.

    ````powershell
    $server = 'VN1-SRV1'
    Resolve-DnsName -Name remote.adatum.com -Type A -Server $server
    ````

    > You should get the IP addresses 10.1.200.24 and 10.1.200.32 as response.

1. Resolve the mail exchanger for adatum.com on PM-SRV1.

    ````powershell
    Resolve-DnsName -Name adatum.com -Type MX -Server $server
    ````

    > You should get smtp.ad.adatum.com as response.

1. Resolve the text record for adatum.com on PM-SRV1.

    ````powershell
    Resolve-DnsName -Name adatum.com -Type TXT -Server $server
    ````

    > You should get {v=spf1 include:spf.protection.outlook.com -all} as response.

1. Resolve the service location record for _sip._tls.adatum.com on PM-SRV1.

    ````powershell
    Resolve-DnsName -Name _sip._tls.adatum.com -Type SRV -Server $server
    ````

    > You should get sipdir.online.lync.com as NameTarget with priority 100, weight 1, and port 443 as response.

1. Resolve the service location record for _sipfederationtls._tcp on PM-SRV1.

    ````powershell
    Resolve-DnsName `
        -Name _sipfederationtls._tcp.adatum.com -Type SRV -Server $server
    ````

    > You should get sipfed.online.lync.com as NameTarget with priority 100, weight 1, and port 5061 as response.

1. Resolve the alias record for autodiscover.adatum.com on PM-SRV1.

    ````powershell
    Resolve-DnsName -Name autodiscover.adatum.com -Type CNAME -Server $server
    ````

    > You should get autodiscover.outlook.com as response.

Repeat the last step to verify the alias records according to the table below.

| Alias name             | FQDN for target host                      |
|------------------------|-------------------------------------------|
| sip                    | sipdir.online.lync.com                    |
| lyncdiscover           | webdir.online.lync.com                    |
| enterpriseregistration | enterpriseregistration.windows.net        |
| enterpriseenrollment   | enterpriseenrollment.manage.microsoft.com |

### Task 4: Delete the zone

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn1-srv1.ad.adatum.com**

    If vn1-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn1-srv1.ad.adatum.com**, **Forward Lookup Zones**, and click **adatum.com**.
1. In the context-menu of **adatum.com**, click **Delete**.
1. In the message box DNS, click **Yes**.

### Task 5: Create an Active Directory integrated conditional forwarder

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn1-srv1.ad.adatum.com**

    If vn1-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn1-srv1.ad.adatum.com** and click **Conditional Forwarders**.
1. In the context-menu of **Conditional Forwarders**, click **New Conditional Forwarders...**
1. In New Conditional Forwarder, under **DNS Domain**, type **adatum.com**. In **\<Click here to add an IP Address or DNS Name\>**, enter **pm-srv1.ad.adatum.com**.
1. Click the entry **No such host is known.** and click **Delete**.
1. In **\<Click here to add an IP Address or DNS Name\>**, enter **pm-srv2.ad.adatum.com**.
1. Click the entry **No such host is known.** and click **Delete**.
1. Activate the checkbox **Store this conditional forwarder in Active Directory, and replicate it as follows** and, below, ensure **All DNS servers in this forest** is selected.
1. Click **OK**.

### Task 6: Create an Active Directory integrated primary pinpoint DNS zone

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn1-srv1.ad.adatum.com**

    If vn1-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn1-srv1.ad.adatum.com** and click **Forward Lookup Zones**.
1. In the context-menu of **Forward Lookup Zones**, click **New Zone...**
1. In the New Zone Wizard, on page Welcome to the New Zone Wizard, click **Next >**.
1. On page Zone Type, ensure **Primary zone** is selected, **Store this zone in Active Directory (available only if DNS server is a writeable domain controller)** is activated, and click **Next >**.
1. On page Active Directory Zone Replication Scope, click **To all DNS servers running on domain controller in this forest: ad.adatum.com** and click **Next >**.
1. On page Zone Name, under **Zone name**, type **remote.adatum.com** and click **Next >**.
1. On page Dynamic Update, click **Do not allow dynamic updates** and click **Next >**.
1. On page Completing the New Zone Wizard, click **Finish**.

### Task 7: Create resource records in the pinpoint DNS zone

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn1-srv1.ad.adatum.com**

    If vn1-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn1-srv1.ad.adatum.com**, **Forward Lookup Zones**, and click **remote.adatum.com**.
1. In the context-menu of **remote.adatum.com**, click **New Host (A or AAAA)...**
1. In New Host, leave **Name (uses parent domain name if blank)** blank. Under IP address, type **10.1.200.24**. Click **Add Host**.
1. In the message box The host record remote.adatum.com was successfully created, click **OK**.
1. In **New Host**, leave **Name (uses parent domain name if blank)** blank. Under IP address, type **10.1.200.32**. Click **Add Host**.
1. In the message box The host record remote.adatum.com was successfully created, click **OK**.
1. In **New Host**, click **Done**.

> The maintenance effort for a pinpoint DNS zone is significantly lower than for a full split-brain DNS zone, because most records to not have to be replicated manually.

> However, the pinpoint DNS zone is not possible, if the split-brain records are parent domain's A records, or if other record types need to be implemented as split-brain.

### Task 8: Verify the resource records in the pinpoint DNS zone

Refer to [Task 3: Verify the resource records by resolving the names on VN1-SRV1](#task-3-verify-the-resource-records-by-resolving-the-names-on-vn1-srv1) to verify the correct resolution of all records in adatum.com.

> For the MX record, you will receive adatum-com.mail.protection.outlook.com as response.

## Exercise 4: Managing DNS policies

1. [Create a zone scope](#task-1-create-a-zone-scope) Intranet for the adatum.com zone on PM-SRV1 and PM-SRV2
1. [Create resource records in zone scope](#task-2-create-resource-records-in-zone-scope) according to the table below

    | Type | Name   | Value                |
    |------|--------|----------------------|
    | A    | remote | 10.1.200.24          |
    | A    | remote | 10.1.200.32          |
    | MX   | @      | 0 smtp.ad.adatum.com |

1. [Create client subnets and a query resolution policy](#task-3-create-client-subnets-and-a-query-resolution-policy): Create subnets for VNet1, VNet2, and VNet3. The query policy should respond from the scope Intranet for clients in these subnet.

1. [Verify scoped name resolution](#task-4-verify-scoped-name-resolution)

    > Querying the A record of remote.adatum.com from CL1, what is the response?

    > Querying the MX record of adatum.com from CL1, what is the response?

    > Querying the CNAME record of autodiscover.adatum.com from CL1, what is the response?

    > Querying the A record of remote.adatum.com from PM-SRV1, what is the response?

    > Querying the MX record of adatum.com from PM-SRV1, what is the response?

    > Querying the CNAME record of autodiscover.adatum.com from PM-SRV1, what is the response?

    > How do DNS policies compare to split-brain or pinpoint DNS zones in this case?

### Task 1: Create a zone scope

Perform this task on CL1.

1. Open **Terminal**.
1. On **PM-SRV1** and **PM-SRV2**, add a zone scope named **Intranet** to the zone **adatum.com**.

    ````powershell
    $computerName = 'pm-srv1.ad.adatum.com', 'pm-srv2.ad.adatum.com'
    $zoneName = 'adatum.com'
    $zoneScope = 'Intranet'

    Add-DnsServerZoneScope `
        -ComputerName $computerName[0] -ZoneName $zoneName -ZoneScope $zoneScope 
    ````

1. Query the zone scopes of **adatum.com**.

    ````powershell
    Get-DnsServerZoneScope -ComputerName $computerName[0] -ZoneName $zoneName
    ````

    You should see two zone scopes with the names adatum.com and Intranet on each server.

1. Copy the zone scopes except for the default scope to **PM-SRV2**.

    ````powershell
    Get-DnsServerZoneScope -ComputerName $computerName[0] -ZoneName $zoneName | 
    Where-Object { $PSItem.ZoneScope -ne $zoneName } | 
    Add-DnsServerZoneScope -ComputerName $computerName[1]
    ````

1. On **PM-SRV1**, configure the primary zone to allow zone transfers to **PM-SRV2**.

    ````powershell
    $secondaryServers = (
            Resolve-DnsName -Name $computerName[1] -Type A
        ).IPAddress

    Set-DnsServerPrimaryZone `
        -ComputerName $computerName[0] `
        -Name $zoneName `
        -SecondaryServers $secondaryServers `
        -SecureSecondaries TransferToSecureServers
    ````

    Note: You must configure zone transfers explicitely when using zone scopes. The automatic permission for servers listed as name servers, does not work.

1. Add a name server record for pm-srv2.ad.adatum.com to the new zone scope.

    ````powershell
    Add-DnsServerResourceRecord `
        -ComputerName $computerName[0] `
        -ZoneName $zoneName `
        -ZoneScope $zoneScope `
        -NS `
        -Name '@' `
        -NameServer $computerName[1]
    ````

### Task 2: Create resource records in zone scope

Perform this task on CL1.

1. Open **Terminal**.
1. On **PM-SRV1**, add **A** records to the scope **Intranet** with the name **remote** and the IP addresses **10.1.200.24** and **10.1.200.32**.

    ````powershell
    $computerName = 'pm-srv1.ad.adatum.com', 'pm-srv2.ad.adatum.com'
    $zoneName = 'adatum.com'
    $zoneScope = 'Intranet'
    $name = 'remote'
    Add-DnsServerResourceRecord `
        -ComputerName $computerName[0] `
        -ZoneName $zoneName `
        -ZoneScope $zoneScope `
        -A `
        -Name $name `
        -IPv4Address 10.1.200.24
    Add-DnsServerResourceRecord `
        -ComputerName $computerName[0] `
        -ZoneName $zoneName `
        -ZoneScope $zoneScope `
        -A `
        -Name $name `
        -IPv4Address 10.1.200.32
    ````

1. Add an **MX** record to the scope with the name of the parent domain and the mail exchanger **smpt.ad.adatum.com** with the preference of **0**.

    ````powershell
    Add-DnsServerResourceRecord `
        -ComputerName $computerName[0] `
        -ZoneName $zoneName `
        -ZoneScope $zoneScope `
        -MX `
        -Name '@' `
        -MailExchange smtp.ad.adatum.com `
        -Preference 0
    ````

1. Query the zone scopes of **adatum.com** on PM-SRV1 and PM-SRV2.

    ````powershell
    Get-DnsServerZoneScope -ComputerName $computerName[0] -ZoneName $zoneName
    Get-DnsServerZoneScope -ComputerName $computerName[1] -ZoneName $zoneName
    ````

    You should see two zone scopes with the names adatum.com and Intranet on each server.

1. Query the records in the scope **adatum.com**.

    ````powershell
    Get-DnsServerResourceRecord `
        -ComputerName $computerName[0] `
        -ZoneName $zoneName `
        -ZoneScope adatum.com
    Get-DnsServerResourceRecord `
        -ComputerName $computerName[1] `
        -ZoneName $zoneName `
        -ZoneScope adatum.com
    ````

    For each server, you should see approximately 18 records including CNAME and SRV records.

1. Query the records in the scope **Intranet**.

    ````powershell
    Get-DnsServerResourceRecord `
        -ComputerName $computerName[0] `
        -ZoneName $zoneName `
        -ZoneScope Intranet
    Get-DnsServerResourceRecord `
        -ComputerName $computerName[1] `
        -ZoneName $zoneName `
        -ZoneScope Intranet
    ````

    For each server, you should see approximately 6 records. There are no CNAME or SRV records.

### Task 3: Create client subnets and a query resolution policy

Perform this task on CL1.

1. Open **Terminal**.
1. On **PM-SRV1**, create client subnets for **VNet1**, **VNet2**, and **VNet3**.

    ````powershell
    $computerName = 'pm-srv1.ad.adatum.com', 'pm-srv2.ad.adatum.com'
    Add-DnsServerClientSubnet `
        -ComputerName $computerName[0] -Name VNet1 -IPv4Subnet 10.1.1.0/24
    Add-DnsServerClientSubnet `
        -ComputerName $computerName[0] -Name VNet2 -IPv4Subnet 10.1.2.0/24
    Add-DnsServerClientSubnet `
        -ComputerName $computerName[0] -Name VNet3 -IPv4Subnet 10.1.3.0/24
    ````

1. Copy the client subnets to **PM-SRV2**.

    ````powershell
    Get-DnsServerClientSubnet -ComputerName $computerName[0] |
    Add-DnsServerClientSubnet -ComputerName $computerName[1]
    ````

1. On **PM-SRV1** and **PM-SRV2**, create client subnets for **VNet1**, **VNet2**, and **VNet3**.

    ````powershell
    $computerName = 'pm-srv1.ad.adatum.com', 'pm-srv2.ad.adatum.com'
    $zoneName = 'adatum.com'
    $computerName | ForEach-Object {
        Add-DnsServerQueryResolutionPolicy `
            -ComputerName $PSItem `
            -ZoneName $zoneName `
            -Name IntranetPolicy `
            -ClientSubnet 'eq, VNet1, VNet2, VNet3' `
            -Action ALLOW `
            -ZoneScope Intranet
    }
    ````

### Task 4: Verify scoped name resolution

Perform this task on CL1.

1. Open **Terminal**.
1. Resolve the name **remote.adatum.com** using **PM-SRV1**.

    ````powershell
    $server = 'PM-SRV1'
    Resolve-DnsName -Name remote.adatum.com -Type A -Server $server
    ````

    > This returns the IP addresses 10.1.200.24 and 10.1.200.32.

1. Query the **MX** record for **adatum.com** using **PM-SRV1**.

    ````powershell
    Resolve-DnsName -Name adatum.com -Type MX -Server $server
    ````

    > This returns smtp.ad.adatum.com.

1. Resolve the **CNAME** of **autodiscover.adatum.com** using **PM-SRV1**.

    ````powershell
    Resolve-DnsName -Name autodiscover.adatum.com -Type CNAME -Server $server
    ````

    > This returns an error message DNS name does not exist, because this record does not exist in the Intranet scope.

1. Open a remote PowerShell session to **PM-SRV1**.

    ````powershell
    Enter-PSSession PM-SRV1
    ````

1. Resolve the name **remote.adatum.com** using **PM-SRV1**.

    ````powershell
    $server = 'PM-SRV1'
    Resolve-DnsName -Name remote.adatum.com -Type A -Server $server
    ````

    > This returns the IP addresses 20.81.111.85 and 20.53.203.50.

1. Query the **MX** record for **adatum.com** using **PM-SRV1**.

    ````powershell
    Resolve-DnsName -Name adatum.com -Type MX -Server $server
    ````

    > This returns adatum-com.mail.protection.outlook.com.

1. Resolve the **CNAME** of **autodiscover.adatum.com** using **PM-SRV1**.

    ````powershell
    Resolve-DnsName -Name autodiscover.adatum.com -Type CNAME -Server $server
    ````

    > This returns autodiscover.outlook.com.

1. Exit from the remote PowerShell session

    ````powershell
    Exit-PSSession
    ````

> DNS policies are similar to a complete split-brain DNS implementation regarding features and maintenance. But instead of two DNS server instances, only one instance is required.

## Exercise 5: Managing reverse lookup zones

1. [Create reverse lookup zones](#task-1-create-reverse-lookup-zones) for 10.1 on VN1-SRV1 as Active Directory integrated zone with forest-wide replication and secure dynamic updates enabled and for 10.1.2.0 on VN2-SRV1 with dynamic updates disabled
1. [Create a delegation for reverse lookup zone](#task-2-create-a-delegation-for-reverse-lookup-zone) for 10.1.2 on VN1-SRV1 pointing to vn2-srv1.ad.adatum.com
1. [Create a conditional forwarder for the reverse lookup zone](#task-3-create-a-conditional-forwarder-for-the-reverse-lookup-zone) for 10.1 pointing to vn1-srv1.ad.adatum.com on VN2-SRV1.
1. [Create PTR records](#task-4-create-ptr-records) in the zone for 10.1.2.0 according to the table below

    | IP Address | Name                   |
    |------------|------------------------|
    | 10.1.2.8   | vn2-srv1.ad.adatum.com |
    | 10.1.2.16  | vn2-srv2.ad.adatum.com |

1. [Register computers in DNS](#task-5-register-computers-in-dns): VN1-SRV1 and CL1

    Note: You could also wait for the computers to register automatically, but for the purpose of this lab, this would take too long.

1. [Verify the PTR records](#task-6-verify-the-ptr-records) of 10.1.1.8, 10.1.1.128, 10.1.2.8, and 10.1.2.16.

### Task 1: Create reverse lookup zones

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn1-srv1.ad.adatum.com**

    If vn1-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn1-srv1.ad.adatum.com** and click **Reverse Lookup Zones**.
1. In the context-menu of **Reverse Lookup Zones**, click **New Zone...**
1. In the New Zone Wizard, on page Welcome to the New Zone Wizard, click **Next >**.
1. On page Zone Type, ensure **Primary zone** is selected, **Store the zone in Active Directory (available only if DNS server is a writeable domain controller)** is activated, and click **Next >**.
1. On page Active Directory Zone Replication Scope, click **to all DNS servers running on domain controllers in this forest: ad.adatum.com** and click **Next >**.
1. On page Reverse Lookup Zone Name, ensure **IPv4 Reverse Lookup Zone** is selected and click **Next >**.
1. On page Reverse Lookup Zone Name, ensure **Network ID** is selected. Below, type **10.1**. Click **Next >**.
1. On page Dynamic Update, ensure **Allow only secure dynamic updates (recommended for Active Directory)** is selected and click **Next >**.
1. On page Completing the New Zone Wizard, click **Finish**.
1. In DNS Manager, click **vn2-srv1.ad.adatum.com**

    If vn2-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn2-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn2-srv1.ad.adatum.com** and click **Reverse Lookup Zones**.
1. In the context-menu of **Reverse Lookup Zones**, click **New Zone...**
1. In the New Zone Wizard, on page Welcome to the New Zone Wizard, click **Next >**.
1. On page Zone Type, ensure **Primary zone** is selected  and click **Next >**.
1. On page Reverse Lookup Zone Name, ensure **IPv4 Reverse Lookup Zone** is selected and click **Next >**.
1. On page Reverse Lookup Zone Name, ensure **Network ID** is selected. Below, type **10.1.2**. Click **Next >**.
1. On page Zone File, ensure **Create a new file with this file name** is selected and **2.1.10.in-addr.arpa.dns** is filled in. Click **Next >**.
1. On page Dynamic Update, ensure **Do not allow dynamic updates** is selected and click **Next >**.
1. On page Completing the New Zone Wizard, click **Finish**.

### Task 2: Create a delegation for reverse lookup zone

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn1-srv1.ad.adatum.com**

    If vn1-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn1-srv1.ad.adatum.com**, **Reverse Lookup Zones**, and click **1.10.in-addr.arpa**.
1. In the context-menu of **1.10.in-addr.arpa**, click **New Delegation...**
1. In the New Delegation wizard, on page Welcome to the New Delegation Wizard, click **Next >**
1. On page Delegated Domain Name, under **Delegated domain**, type **2** and click **Next >**
1. On page Name Servers, click **Add...**
1. In New Name Server Record, under **Server fully qualified domain name (FQDN)**, type **vn2-srv1.ad.adatum.com** and click **Resolve**.
1. Click **OK**.
1. In the **New Delegation wizard**, on page Name Servers, click **Next >**
1. On page Completing the New Delegation Wizard, click **Finish**.

### Task 3: Create a conditional forwarder for the reverse lookup zone

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn2-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn2-srv1.ad.adatum.com**

    If vn2-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn2-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn2-srv1.ad.adatum.com** and click **Conditional Forwarders**.
1. In the context-menu of **Conditional Forwarders**, click **New Conditional Forwarders...**
1. In New Conditional Forwarder, under **DNS Domain**, type **1.10.in-addr.arpa**. In **\<Click here to add an IP Address or DNS Name\>**, enter **vn1-srv1.ad.adatum.com**.
1. Click the entry **No such host is known.** and click **Delete**.
1. Click **OK**.

### Task 4: Create PTR records

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn2-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn2-srv1.ad.adatum.com**

    If vn2-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn2-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn2-srv1.ad.adatum.com**, **Reverse Lookup Zones**, and click **2.1.10.in-addr.arpa**.
1. In the context-menu of **2.1.10.in-addr.arpa**, click **New Pointer (PTR)...**
1. In New Resource Record, under **Host IP Address**, type **10.1.2.8**. Under **Host name**, type **vn2-srv1.ad.adatum.com** and click **OK**.

    You may also browse for the host name.

1. In the context-menu of **2.1.10.in-addr.arpa**, click **New Pointer (PTR)...**
1. In New Resource Record, under **Host IP Address**, type **10.1.2.16**. Under **Host name**, type **vn2-srv2.ad.adatum.com** and click **OK**.

### Task 5: Register computers in DNS

Perform this task on CL1.

1. Open **Terminal**.
1. Register the local computer in DNS.

    ````powershell
    Register-DnsClient
    ````

1. Register **VN1-SRV1** in DNS.

    ````powershell
    Invoke-Command -ComputerName VN1-SRV1 -ScriptBlock { Register-DnsClient }
    ```

### Task 6: Verify the PTR records

Perform this task on CL1.

1. Open **Terminal**.
1. Query the name corresponding to IP address **10.1.1.8**.

    ````powershell
    Resolve-DnsName -Name 10.1.1.8 -Type PTR
    ````

    Note: You could also query for 8.1.1.10.in-addr.arpa with the same result.

    > This should return VN1-SRV1.ad.adatum.com as response.

Repeat the last step to verify the IP addresses and host names according to the table below.

| IP address | NameHost               |
|------------|------------------------|
| 10.1.1.128 | CL1.ad.adatum.com      |
| 10.1.2.8   | vn2-srv1.ad.adatum.com |
| 10.1.2.16  | vn2-srv2.ad.adatum.com |

## Exercise 6: Managing the Global Names zone

1. [Enable the GlobalNames zone functionality](#task-1-enable-the-globalnames-zone-functionality) on VN1-SRV1
1. [Create the Global Names zone](#task-2-create-the-global-names-zone)
1. [Create global name records](#task-3-create-global-name-records) according to the table below

    | Alias name | FQDN for target host   |
    |------------|------------------------|
    | dc1        | vn1-srv1.ad.adatum.com |
    | rca        | vn1-srv2.ad.adatum.com |
    | sql1       | vn1-srv3.ad.adatum.com |

1. [Verify the name resolution of global names](#task-4-verify-the-name-resolution-of-global-names)

### Task 1: Enable the GlobalNames zone functionality

Perform this task on CL1.

1. Open **Terminal**.
1. Enable the Global Names zzone on **VN1-SRV1**.

    ````powershell
    Set-DnsServerGlobalNameZone -Enable $true -ComputerName vn1-srv1
    ````

### Task 2: Create the Global Names zone

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn1-srv1.ad.adatum.com**

    If vn1-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn1-srv1.ad.adatum.com** and click **Forward Lookup Zones**.
1. In the context-menu of **Forward Lookup Zones**, click **New Zone...**
1. In the New Zone Wizard, on page Welcome to the New Zone Wizard, click **Next >**.
1. On page Zone Type, ensure **Primary zone** is selected, **Store this zone in Active Directory (available only if DNS server is a writeable domain controller)** is activated, and click **Next >**.
1. On page Active Directory Zone Replication Scope, click **To all DNS servers running on domain controller in this forest: ad.adatum.com** and click **Next >**.
1. On page Zone Name, under **Zone name**, type **GlobalNames** and click **Next >**.
1. On page Dynamic Update, click **Do not allow dynamic updates** and click **Next >**.
1. On page Completing the New Zone Wizard, click **Finish**.

### Task 3: Create global name records

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn1-srv1.ad.adatum.com**

    If vn1-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn1-srv1.ad.adatum.com**, **Forward Lookup Zones**, and click **adatum.com**.
1. In the context-menu of **adatum.com**, click **New Alias (CNAME)**
1. In New Resource Record, under **Alias name (uses parent domain if left blank)**, type **dc1**. Under **Fully qualified domain name (FQDN) for target host**, type **vn1-srv1.ad.adatum.com**. Click **OK**.

Repeat the last two steps to create remaining aliases according to the table below.

| Alias name | FQDN for target host   |
|------------|------------------------|
| rca        | vn1-srv2.ad.adatum.com |
| sql1       | vn1-srv3.ad.adatum.com |

### Task 4: Verify the name resolution of global names

Perform this task on CL1.

1. Open **Terminal**.
1. Resolve the name **dc1**.

    ````powershell
    Resolve-DnsName -Name dc1
    ````

    > This should return vn1-srv1.ad.adatum.com as response.

Repeat the last step for the names **rca** and **sql1**. This should return responses according to the table below.

| Alias name | FQDN for target host   |
|------------|------------------------|
| rca        | vn1-srv2.ad.adatum.com |
| sql1       | vn1-srv3.ad.adatum.com |

## Exercise 7: Managing stub zones

1. [Verify non-recursive resolution using a conditional forwarder](#task-1-verify-non-recursive-resolution-using-a-conditional-forwarder) of ad.adatum.com on VN2-SRV1

    > What is the response?

1. [Remove the conditional forwarder](#task-2-remove-the-conditional-forwarder) ad.adatum.com from VN2-SRV1
1. [Allow zone transfers](#task-3-allow-zone-transfers) of ad.adatum.com to VN2-SRV1
1. [Create a stub zone](#task-4-create-a-stub-zone) for ad.adatum.com on VN2-SRV1

    > Which entries get transferred to a stub zone?

1. [Verify non-recursive and recursive resolution using a stub zone](#task-5-verify-non-recursive-and-recursive-resolution-using-a-stub-zone) of ad.adatum.com on VN2-SRV1

    > What response do you get from a non-recursive query?

    > What response do you get from a recursive query?

    > What response do you get from a non-recursive query, that can be answered from the cache?

### Task 1: Verify non-recursive resolution using a conditional forwarder

Perform this task on CL1.

1. Open **Terminal**.
1. Resolve the name **ad.adatum.com** on **VN2-SRV1** without recursion.

    ````powershell
    Resolve-DnsName `
        -Name ad.adatum.com -Type A -Server vn2-srv1.ad.adatum.com -NoRecursion
    ````

    > This should return 10.1.1.8 as response.

### Task 2: Remove the conditional forwarder

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn2-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn2-srv1.ad.adatum.com**

    If vn2-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn2-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn2-srv1.ad.adatum.com**, and click **Conditional Forwarders**.
1. In the context-menu of **ad.adatum.com**, click **Delete**.
1. In the message box Do you want to delete the conditional forwarder ad.adatum.com from the server, click **Yes**.

### Task 3: Allow zone transfers

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn1-srv1.ad.adatum.com**

    If vn1-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn1-srv1.ad.adatum.com**, **Forward Lookup Zones**.
1. In the context-menu of **ad.adatum.com**, click **Properties**.
1. In ad.adatum.com Properties, click the tab **Zone Transfers**.
1. On the tab Zone Transfers, activate the checkbox **Allow zone transfers**. Click **Only to the following servers** and click **Edit**.
1. In Allow Zone Transfers, in **\<Click here to add an IP Address or DNS Name\>**, enter **vn2-srv1.ad.adatum.com**.
1. Click the entry **No such host is known** and click **Delete**.
1. Click **OK**.
1. In **ad.adatum.com Properties**, click **OK**.

### Task 4: Create a stub zone

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn2-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn2-srv1.ad.adatum.com**

    If vn2-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn2-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn1-srv2.ad.adatum.com** and click **Forward Lookup Zones**.
1. In the context-menu of **Forward Lookup Zones**, click **New Zone...**
1. In the New Zone Wizard, on page Welcome to the New Zone Wizard, click **Next >**.
1. On page Zone Type, click **Stub zone** and click **Next >**.
1. On page Zone Name, under **Zone name**, type **ad.adatum.com** and click **Next >**.

    Note: You could also browse for the zone name.

1. On page Zone File, ensure **Create a new file with this file name** is selected and **ad.adatum.com.dns** is filled in. Click **Next >**.
1. On page Master DNS Servers, under **Master Servers** in **\<Click here to add and IP Address or DNS name\>**, enter **vn1-srv1.ad.adatum.com**.
1. Click the entry **No such host is known** and click **Delete**.
1. Click **Next >**.
1. On page Completing the New Zone Wizard, click **Finish**.
1. In **DNS Manager**, under **Forward Lookup Zones**, click **ad.adatum.com**.

    If you see an error message, wait a few seconds and click *Refresh*.

    > You should see the SOA, NS records and the A record for vn1-srv1.

### Task 5: Verify non-recursive and recursive resolution using a stub zone

Perform this task on CL1.

1. Open **Terminal**.
1. Clear the DNS server cache on **VN2-SRV1**.

    ````powershell
    $server = 'VN2-SRV1'
    Clear-DnsServerCache -ComputerName $server -Force
    ````

1. Resolve the name **ad.adatum.com** on **VN2-SRV1** without recursion.

    ````powershell
    Resolve-DnsName -Name ad.adatum.com -Type A -Server $server -NoRecursion
    ````

    > This should return an error message, because a stub zone does not forward queries flagged with no recursion.

1. Resolve the name **ad.adatum.com** on **VN2-SRV1**.

    ````powershell
    Resolve-DnsName -Name ad.adatum.com -Type A -Server $server
    ````

    > This should return 10.1.1.8 as response.

1. Resolve the name **ad.adatum.com** on **VN2-SRV1** without recursion.

    ````powershell
    Resolve-DnsName -Name ad.adatum.com -Type A -Server $server -NoRecursion
    ````

    > This should return 10.1.1.8 as response, the query can be answered from the cache.
