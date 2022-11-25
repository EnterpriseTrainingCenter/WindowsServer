# Practice: Enable the Active Directory Recycle Bin

## Required VMs

* VN1-SRV1
* CL1

## Task

Enable the Active Directory  using either Active Directory Administrative Center or PowerShell.

## Instructions

### Active Directory Administrative Center

Perform these steps on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, in the left pane, click **ad (local)**.
1. In the pane **Tasks**, under **ad (local)**, click **Enable Recycle Bin...**.
1. In Enable Recycle Bin Confirmation, click **OK**.
1. In the top right, click the icon *Refresh*.

### PowerShell

Perform these steps on CL1

1. Open **Terminal**.
1. Enable Athe Active Directory Recycle Bin.

    `````powershell
    $domainFQDN = 'ad.adatum.com'
    $domainDN = 'DC=ad, DC=adatum, DC=com'
    Enable-ADOptionalFeature `
        -Identity `
            "CN=Recycle Bin Feature, CN=Optional Features, CN=Directory Service, CN=Windows NT, CN=Services, CN=Configuration, $domainDN" `
        -Scope ForestOrConfigurationSet `
        –Target $domainFQDN
    ````
