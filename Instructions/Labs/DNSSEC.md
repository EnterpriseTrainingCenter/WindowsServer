# Lab: DNSSEC

## Required VMs

* VN1-SRV1
* VN2-SRV1
* PM-SRV1
* PM-SRV2
* CL1
* CL2

## Setup

On CL1 and CL2 sign in as **ad\Administrator**.

## Introduction

To improve security, Adatum wants to sign all DNS domains. For a pilot, Adatum wants to configure a set of test computers to use DNSSEC only for queries in the adatum.com namespace.

## Exercises

1. [Configuring the Name Resolution Policy Table](#exercise-1-configuring-the-name-resolution-policy-table)
1. [Signing zones](#exercise-2-signing-zones)
1. [Verifying name resolution with DNSSEC](#exercise-3-verifying-name-resolution-with-dnssec)

## Exercise 1: Configuring the Name Resolution Policy Table

1. [Create organizational units](#task-1-create-organizational-units) Devices in the domain ad.adatum.com and, under Devices, Client computers
1. [Create and configure a group policy object](#task-2-create-and-configure-a-group-policy-object) in the Client computers OU to require DNS clients to check that name and address data has been validated by the DNS server
1. [Move test client](#task-3-move-test-client) CL2 into the organizational unit Client computers
1. [Verify name resolution policies](#task-4-verify-name-resolution-policies) on CL2

    > Why can you still resolve ad.adatum.com using vn1-srv1.ad.adatum.com as DNS server?

    > Can you resolve ad.adatum.com using vn2-srv1.ad.adatum.com as DNS server?

### Task 1: Create organizational units

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **ad (local)**.
1. In ad (local), in the **Tasks** pane, under **ad (local)**, click **New**, **Organizational Unit**.
1. In Create Organizational Unit, in **Name**, type **Devices** and click **OK**.
1. In **Active Directory Administrative Center**, in **ad (local)**, double-click **Devices**.
1. In Devices, in the **Tasks** pane, under **Devices**, click **New**, **Organizational Unit**.
1. In Create Organizational Unit, in **Name**, type **Client computers** and click **OK**.

### Task 2: Create and configure a group policy object

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Forest: ad.adatum.com**, **Domains**, **ad.adatum.com**, **Devices**, and click **Client computers**.
1. In the context-menu of Client computers, click **Create a GPO in this domain, and link it here...**
1. In New GPO, under **Name**, type **Custom Computer NRPT DNSSec** and click **OK**.
1. In the context menu of **Custom Computer NRPT DNSSec**, click **Edit**.
1. In Group Policy Management Editor, expand **Custom Computer NRPT DNSSec**, **Computer Configuration**, **Policies**, **Windows Settings**, and click **Name Resolution Policy**.
1. In the right pane, under **Create Rules**, **To which part of the namespaces does this rule apply**, ensure **Suffix** is selected. To the right of **Suffix**, type **adatum.com**. Below, on the tab **DNSSEC**, activate **Enable DNSSEC in this rule** and **Require DNS clients to check that name and address data has been validated by the DNS server**. Click **Create**.
1. Scroll down and click **Apply**.
1. Close **Group Policy Management Editor**.

### Task 3: Move test client

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **Global Search**.
1. In Global Search, in **Search**, enter **CL2**.
1. In the context-menu of **CL2**, click **Move...**
1. In Move, in the middle pane, click **Devices** and, in the right pane, click **Client computers**. Click **OK**.

### Task 4: Verify name resolution policies

Perform this task on CL2.

1. Open **Terminal**.
1. Refresh group policies.

    ````powershell
    gpupdate.exe /force
    ````

1. Resolve the host name ad.adatum.com.

    ````powershell
    Resolve-DnsName -Name ad.adatum.com -Type A
    ````

    > This returns the IP address 10.1.1.8. Because the query is executed against the authoritative DNS server, DNSSEC is not required.

1. Resolve the host name china.ad.adatum.com.

    ````powershell
    Resolve-DnsName -Name china.ad.adatum.com -Type A
    ````

    > This returns the error message Unsecured DNS packet, because the zone is not signed yet.

1. Resolve the host name ad.adatum.com using vn2-srv1.ad.adatum.com as DNS server.

    ````powershell
    Resolve-DnsName -Name adatum.com -Type MX
    ````

    > This returns the error message Unsecured DNS packet, because the zone is not signed yet.

## Exercise 2: Signing zones

1. [Sign primary zone](#task-1-sign-primary-zone) adatum.com
1. [Sign primary zone with parameters of an existing zone](#task-2-sign-primary-zone-with-parameters-of-an-existing-zone): china.ad.adatum.com
1. [Sign Active Directory integrated zones with parameters of an existing zone](#task-3-sign-an-active-directory-integrated-zones-with-parameters-of-an-existing-zone): ad.adatum.com, _msdcs.ad.adatum.com, and remote.adatum.com
1. [Export DNSSEC public keys](#task-4-export-dnssec-public-keys) for the domains adatum.com and china.ad.adatum.com
1. [Import the trust anchors](#task-5-import-the-trust-anchors) on VN1-SRV1

### Task 1: Sign primary zone

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **pm-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **pm-srv1.ad.adatum.com**

    If pm-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **pm-srv1.ad.adatum.com** below and click **OK**.

1. Expand **pm-srv1.ad.adatum.com**, **Forward Lookup Zones**, and click **adatum.com**.
1. In the context-menu of **adatum.com**, click **DNSSEC**, **Sign the Zone**.
1. In the Zone Signing Wizard, on page DNS Security Extensions (DNSSEC), click **Next >**
1. On page Signing Options, ensure **Customize zone signing parameter** is selected, and click **Next >**.
1. On page Key Signing Key (KSK), click **Next >**.
1. On page Key Signing Key (KSK), click **Add**.
1. In New Key Signing Key (KSK), verify the default settings and click **OK**.

    | Label                                                    | Value                                   |
    |----------------------------------------------------------|-----------------------------------------|
    | Key Generation                                           | Generate new signing keys               |
    | Cryptographic algorithm                                  | RSA/SHA-256                             |
    | Key length (Bits)                                        | 2048                                    |
    | Select a key storage provider to generate and store keys | Microsoft Software Key Storage Provider |
    | DNSKEY RRSET signature validity period (hours)           | 168                                     |
    | Key Rollover                                             | Enable automatic rollover (activated)   |
    | Rollover frequency (days)                                | 755                                     |
    | Delay the first rollover by (days)                       | 0                                       |

1. In the **Zone Signing Wizard**, on page **Key Signing Key (KSK)**, click **Next >**.
1. On page Zone Signing Key (ZSK), click **Next >**.
1. On page Zone Signing Key (ZSK), click **Add**.
1. In New Zone Signing Key (KSK), verify the default settings and click **OK**.

    | Label                                                    | Value                                   |
    |----------------------------------------------------------|-----------------------------------------|
    | Cryptographic algorithm                                  | RSA/SHA-256                             |
    | Key length (Bits)                                        | 1024                                    |
    | Select a key storage provider to generate and store keys | Microsoft Software Key Storage Provider |
    | DNSKEY signature validity period (hours)                 | 168                                     |
    | DS signature validity period (hours)                     | 168                                     |
    | Zone record validity period (hours)                      | 240                                     |
    | Key Rollover                                             | Enable automatic rollover (activated)   |
    | Rollover frequency (days)                                | 90                                      |
    | Delay the first rollover by (days)                       | 0                                       |

1. In the **Zone Signing Wizard**, on page **Zone Signing Key (KSK)**, click **Next >**.
1. On page Next Secure (NSEC), ensure **Use NSEC3** is selected, **Iterations** are **50**, and **Generate and use a random salt of length** is **8**. Click **Next >**.
1. On page Trust Anchors (TAs), activate **Enable the distribution of trust anchors for this zone** and ensure **Enable automatic update of trust anchors on key rollover (RFC 5011)** is activated. Click **Next >**.
1. On page Signing and Polling Parameters, verify the default settings and click **OK**.

    | Label                                    | Value             |
    |------------------------------------------|-------------------|
    | DS record generation algorithm           | SHA-1 and SHA-256 |
    | DS record TTL (seconds)                  | 3600              |
    | DNSKEY record TTL (seconds)              | 3600              |
    | Secure delegation polling period (hours) | 12                |
    | Signature inception (hours)              | 1                 |

1. On page DNS Security Extensions (DNSSEC), click **Next >**.
1. On page Signing the Zone, click **Finish**.

### Task 2: Sign primary zone with parameters of an existing zone

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn2-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn2-srv1.ad.adatum.com**

    If vn2-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn2-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn2-srv1.ad.adatum.com**, **Forward Lookup Zones** and click **china.ad.adatum.com**.
1. In the context-menu of **china.ad.adatum.com**, click **DNSSEC**, **Sign the Zone**.
1. In the Zone Signing Wizard, on page DNS Security Extensions (DNSSEC), click **Next >**
1. On page Signing Options, click **Sign the zone with paramters of an existing zone**. Right to **Zone Name**, type **adatum.com** and click **Next >**.
1. On page DNS Security Extensions (DNSSEC), click **Next >**.
1. On page Signing the Zone, click **Finish**.

### Task 3: Sign an Active Directory integrated zones with parameters of an existing zone

Perform this task on CL1.

1. Open **DNS**.

    If the dialog **Connect to DNS Server** appears, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. In DNS Manager, click **vn1-srv1.ad.adatum.com**

    If vn1-srv1.ad.adatum.com is not available:

    1. In the context menu of **DNS**, click **Connect to DNS Server...**.
    1. In Connect to DNS Server, click **The following computer**, type **vn1-srv1.ad.adatum.com** below and click **OK**.

1. Expand **vn1-srv1.ad.adatum.com**, **Forward Lookup Zones** and click **ad.adatum.com**.
1. In the context-menu of **ad.adatum.com**, click **DNSSEC**, **Sign the Zone**.
1. In the Zone Signing Wizard, on page DNS Security Extensions (DNSSEC), click **Next >**
1. On page Signing Options, click **Sign the zone with paramters of an existing zone**. Right to **Zone Name**, type **adatum.com** and click **Next >**.
1. On page Key Master, ensure **The DNS server vn1-srv1 is the Key Master** is selected and click **Next >**.
1. On page DNS Security Extensions (DNSSEC), click **Next >**.
1. On page Signing the Zone, click **Finish**.

Repeat from step 3 for the zones **_msdcs.ad.adatum.com** and **remote.adatum.com**.

### Task 4: Export DNSSEC public keys

Perform this task on CL1.

1. Open **Terminal**.
1. Export the DNSSEC public keys for the zone **china.ad.adatum.com**.

    ````powershell
    $path = "$env:USERPROFILE\Documents\DNSSEC"
    New-Item -Path $path -ItemType Directory
    Export-DnsServerDnsSecPublicKey `
        -ComputerName VN2-SRV1 `
        -ZoneName china.ad.adatum.com `
        -DigestType Sha1 `
        -Path $path
    ````

1. Export the DNSSEC public keys for the zone **adatum.com**.

    ````powershell
    $path = "$env:USERPROFILE\Documents\DNSSEC"
    New-Item -Path $path -ItemType Directory
    Export-DnsServerDnsSecPublicKey `
        -ComputerName PM-SRV1 `
        -ZoneName adatum.com `
        -DigestType Sha1 `
        -Path $path
    ````

### Task 5: Import the trust anchors

Perform this task on CL1.

1. Open **Terminal**.
1. Import the trust anchors on **VN1-SRV1**.

    ````powershell
    $path = "$env:USERPROFILE\Documents\DNSSEC"
    Get-ChildItem -Path $path -Filter dsset-* | ForEach-Object { 
        Import-DnsServerTrustAnchor `
            -ComputerName VN1-SRV1 -DSSetFile $PSItem.FullName 
    }
    ````

## Exercise 3: Verifying name resolution with DNSSEC

[Perform queries in the signed domains](#task-perform-queries-in-the-signed-domains) on CL2

### Task: Perform queries in the signed domains

Perform this task on CL2.

1. Open **Terminal**.
1. Resolve the host name ad.adatum.com.

    ````powershell
    Resolve-DnsName -Name ad.adatum.com -Type A
    ````

    > This returns the IP address 10.1.1.8 and information about the DNSSEC signature.

1. Resolve the host name china.ad.adatum.com.

    ````powershell
    Resolve-DnsName -Name china.ad.adatum.com -Type A
    ````

    > This returns the IP addresses 10.1.2.8 and 10.1.2.16 as well as information about the DNSSEC signature.

1. Resolve the host name ad.adatum.com using vn2-srv1.ad.adatum.com as DNS server.

    ````powershell
    Resolve-DnsName -Name adatum.com -Type MX -Server vn2-srv1.ad.adatum.com
    ````

    > This returns smtp.ad.adatum.com or adatum-com.mail.protection.outlook.com and information about the DNSSEC signature.
