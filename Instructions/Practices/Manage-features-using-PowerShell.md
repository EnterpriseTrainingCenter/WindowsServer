# Practice: Manage roles using PowerShell

## Required VMs

* VN1-DC1
* VN1-GW1
* VN1-FS1
* VN1-Core1
* VN1-RCA
* CL1

## Task

On CL1, use PowerShell to install the Windows Server Backup feature on VN1-DC1, VN1-GW1, VN1-FS1, VN1-Core1, and VN1-RCA.

## Instructions

Perform these steps on CL1.

1. Logon as **smart\Administrator**.
1. Open **Windows Terminal** as Administrator.
1. Create an array variable with the server names VN1-DC1, VN1-GW1, VN1-FS1, VN1-Core1, and VN1-RCA.

    ````powershell
    $computername = @('VN1-DC1', 'VN1-GW1', 'VN1-FS1', 'VN1-Core1', 'VN1-RCA')
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
