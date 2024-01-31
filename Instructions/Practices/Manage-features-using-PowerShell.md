# Practice: Manage features using PowerShell

## Required VMs

* VN1-SRV1
* VN1-SRV2
* VN1-SRV4
* VN1-SRV5
* VN1-SRV10
* CL1

## Setup

If you skipped the practice [Install Remote Server Administration Tools](Install-Remote-Server-Administration-Tools.md), on CL1, run ````C:\LabResources\Solutions\Install-RemoteServerAdministrationTools.ps1````.

## Task

On CL1, use PowerShell to install the Windows Server Backup feature on VN1-SRV1, VN1-SRV2, VN1-SRV4, VN1-SRV5, and VN1-SRV10.

## Instructions

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. Open **Terminal** as Administrator.
1. Create an array variable with the server names VN1-SRV1, VN1-SRV2, VN1-SRV4, VN1-SRV5, and VN1-SRV10.

    ````powershell
    $computername = @('VN1-SRV1', 'VN1-SRV2', 'VN1-SRV4', 'VN1-SRV5', 'VN1-SRV10')
    ````

1. Query the installed features of the first server.

    ````powershell
    Get-WindowsFeature -ComputerName $computername[0] | 
    Where-Object { $PSItem.InstallState -eq 'Installed' }
    ````

1. On the first server, find a feature for backup.

    ````powershell
    $windowsFeature = Get-WindowsFeature -ComputerName $computername[0] | 
    Where-Object { $PSItem.DisplayName -like '*backup*' }
    $windowsFeature
    ````

1. Install the feature for backup on all servers.

    ````powershell
    $computername | ForEach-Object { 
        Install-WindowsFeature `
            -Name $windowsFeature.Name `
            -ComputerName $PSItem `
            -Restart
    }
    ````

1. Verify the installation of the backup feature

    ````powershell
    $computername | Foreach-Object {
        Get-WindowsFeature -Name $windowsFeature.Name -ComputerName $PSItem `
    }
    ````
