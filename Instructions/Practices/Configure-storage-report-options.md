# Practice: Configure storage report options

## Required VMs

* VN1-DC1
* VN1-FS1
* CL1

## Task

Set the minimum file size for the large file report to 1MB. Set the location for reports to sub-folders of d:\\Shares\\IT\\StorageReports\\.

## Instructions

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. Run **Windows Terminal** as Administrator.
1. Create a remote PowerShell session to **VN1-FS1**

    ````powershell
    Enter-PSSession VN1-FS1
    ````

1. Set the minimum file size for the large file report to **1 MB**.

    ````powershell
    Set-FsrmSetting `
        -ReportLargeFileMinimum 1MB
    ````

1. Set the location for incident reports to **d:\\Shares\\IT\\StorageReports\\Incident**.

    ````powershell
    $reportLocation = 'd:\Shares\IT\StorageReports'
    $reportLocationIncident = `
        Join-Path -Path $reportLocation -ChildPath 'Incident'
    New-Item -Type Directory -Path $reportLocationIncident
    Set-FsrmSetting `
        -ReportLocationIncident $reportLocationIncident
    ````

1. Set the location for scheduled reports to **d:\\Shares\\IT\\StorageReports\\Scheduled**.

    ````powershell
    $reportLocationScheduled = `
        Join-Path -Path $reportLocation -ChildPath 'Scheduled'
    New-Item -Type Directory -Path $reportLocationScheduled
    Set-FsrmSetting `
        -ReportLocationIncident $reportLocationScheduled
    ````

1. Set the location for on-demand reports to **d:\\Shares\\IT\\StorageReports\\Interactive**.

    ````powershell
    $reportLocationOnDemand = `
        Join-Path -Path $reportLocation -ChildPath 'Interactive'
    New-Item -Type Directory -Path $reportLocationOnDemand
    Set-FsrmSetting `
        -ReportLocationIncident $reportLocationOnDemand
    ````

1. Exit the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

Note: You could also shorten the commands above to

````powershell
$reportLocation = 'd:\Shares\IT\StorageReports'
$reportLocationIncident = `
    Join-Path -Path $reportLocation -ChildPath 'Incident'
$reportLocationScheduled = `
    Join-Path -Path $reportLocation -ChildPath 'Scheduled'
$reportLocationOnDemand = `
    Join-Path -Path $reportLocation -ChildPath 'Interactive'

$reportLocationIncident, $reportLocationScheduled, $reportLocationOnDemand |
ForEach-Object { New-Item -Type Directory -Path $PSItem }

Set-FsrmSetting `
    -ReportLargeFileMinimum 1MB `
    -ReportLocationIncident $reportLocationIncident `
    -ReportLocationScheduled $reportLocationScheduled `
    -ReportLocationOnDemand $reportLocationOnDemand 
````
