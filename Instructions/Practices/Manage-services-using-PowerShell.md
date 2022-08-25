# Practice: Manage Services using PowerShell

## Required VMs

* VN1-DC1
* VN1-DC1
* VN1-FS1
* CL1

## Task

On CL1, list the services on VN1-DC1 with their status, name, display name, and start type. Stop the W32Time service on VN1-DC1, VN1-FS1, and VN1-RCA and disable it. Restart VN1-DC1, VN1-FS1, and VN1-RCA and verify the changes. Then set W32Time to start automatically and start it again.

## Instructions

Perform these steps on CL1.

1. Logon as **smart\Administrator**.
1. Run **Windows Terminal**.
1. List the services on VN1-DC1, with their status, name, display name, and start type.

    ````powershell
    Get-Service -Computername VN1-DC1 | Select-Object Status, Name, Displayname, StartType
    ````

    Notice the slow response. ````Get-Service```` uses an interface not optimized for PowerShell.

1. Press CTRL + C to interrupt the running command.
1. List the services on VN1-DC1, with their status, name, display name, and start type. This time, use PowerShell remoting.

    ````powershell
    Invoke-Command -Computername VN1-DC1 { 
        Get-Service | Select-Object Status, Name, Displayname, StartType 
    }
    ````

1. List the services on VN1-DC1, with their status, name, display name, and start type using remoting. Format the output as table.

    ````powershell
    Invoke-Command -Computername VN1-DC1 { 
        Get-Service | Select-Object Status, Name, Displayname, StartType 
    }
    ````

1. Verify the status and start type of the W32Time service on VN1-DC1, VN1-FS1, and VN1-RCA. Format the output as table.

    ````powershell
    $name = 'W32Time'
    $computername = @('VN1-DC1', 'VN1-FS1', 'VN1-RCA')
    Invoke-Command -Computername $computername { 
        Get-Service -Name $using:name | Select-Object Name, Status, StartType 
    } | 
    Format-Table
    ````

1. Stop the W32Time service on VN1-DC1, VN1-FS1, and VN1-RCA.

    ````powershell
    Invoke-Command -ComputerName $computername {
        Stop-Service -Name $using:name 
    }
    ````

1. Verify the status and start type of the W32Time service on VN1-DC1, VN1-FS1, and VN1-RCA. Format the output as table.

    ````powershell
    Invoke-Command -Computername $computername { 
        Get-Service -Name $using:name | Select-Object Name, Status, StartType 
    } | 
    Format-Table
    ````

    The status should be stopped on all computers.

1. Set the start type of the W32Time service to **Disabled** on VN1-DC1, VN1-FS1, and VN1-RCA.

    ````powershell
    Invoke-Command -ComputerName $computername { 
        Set-Service -Name $using:name -StartupType Disabled 
    }
    ````

1. Restart VN1-DC1, VN1-FS1, and VN1-RCA.

    ````powershell
    Invoke-Command -ComputerName $computername { Restart-Computer -Force }
    ````

    Wait until the logon screen appears on VN1-DC1.

1. Verify the status and start type of the W32Time service on VN1-DC1, VN1-FS1, and VN1-RCA. Format the output as table.

    ````powershell
    Invoke-Command -Computername $computername { 
        Get-Service -Name $using:name | Select-Object Name, Status, StartType 
    } | 
    Format-Table
    ````

    The status should be stopped on all computers.

1. Set the start type of the W32Time service to **Automatic** on VN1-DC1, VN1-FS1, and VN1-RCA.

    ````powershell
    Invoke-Command -ComputerName $computername { 
        Set-Service -Name $using:name -StartupType Automatic 
    }
    ````

1. Start the W32Time service on VN1-DC1, VN1-FS1, and VN1-RCA.

    ````powershell
    Invoke-Command -ComputerName $computername {
        Start-Service -Name $using:name 
    }
    ````

1. Verify the status and start type of the W32Time service on VN1-DC1, VN1-FS1, and VN1-RCA. Format the output as table.

    ````powershell
    Invoke-Command -Computername $computername { 
        Get-Service -Name $using:name | Select-Object Name, Status, StartType 
    } | 
    Format-Table
    ````

    The status should be started and the start type should be automatic on all computers.
