# Practice: View events using PowerShell

## Required VMs

* VN1-SRV1
* CL1

## Task

On CL1, list the latest 20 events from the system log on VN1-SRV1. List all warnings, errors and audit failures from the system log on VM1-DC1. List all details for the oldest the events that DSC generated on VN1-SRV1.

## Instructions

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. Run **Windows Terminal**.
1. List the lastest 20 events from the system log on VN1-SRV1.

    ````powershell
    $computername = 'VN1-SRV1'
    Get-EventLog -LogName System -Newest 20 -Computername VN1-SRV1 # Error occurs
    Invoke-Command -ComputerName $computername { 
        Get-EventLog -LogName System -Newest 20 
    }
    ````


1. List the error, warning and audit failure events from the system log on VN1-SRV1.

    ````powershell
    Invoke-Command -ComputerName $computername {
        Get-EventLog -LogName System -EntryType Error, Warning, FailureAudit
    }
    ````

1. List all logs on VN1-SRV1.

    ````powershell
    Get-WinEvent -ListLog * -ComputerName $computername # Error occurs
    Invoke-Command -ComputerName $computername { Get-WinEvent -ListLog * }
    ````

1. List the events from the **Microsoft-Windows-DSC/Operational** log on VN1-SRV1.

    ````powershell
    Invoke-Command -ComputerName $computername { 
        Get-WinEvent -LogName Microsoft-Windows-DSC/Operational 
    }
    ````

1. List the latest 10 events from the **Microsoft-Windows-DSC/Operational** log on VN1-SRV1.

    ````powershell
    Invoke-Command -ComputerName $computername {
        Get-WinEvent -LogName Microsoft-Windows-DSC/Operational -MaxEvents 10 
    }
    ````

1. List the oldest 10 events from the **Microsoft-Windows-DSC/Operational** log on VN1-SRV1.

    ````powershell
    Invoke-Command -ComputerName $computername {
        Get-WinEvent `
            -LogName Microsoft-Windows-DSC/Operational `
            -MaxEvents 10 `
            -Oldest
    }
    ````

1. List the oldest 10 events from the **Microsoft-Windows-DSC/Operational** log on VN1-SRV1 with more details.

    ````powershell
    Invoke-Command -ComputerName $computername {
        Get-WinEvent `
            -LogName Microsoft-Windows-DSC/Operational `
            -MaxEvents 10 `
            -Oldest
    } |
    Format-List
    ````

1. Clear the Security log on VN1-SRV1.

    ````powershell
    Invoke-Command -ComputerName $computername { 
        Clear-EventLog -LogName Security 
    }
    ````

1. List all events from the Security log on VN1-SRV1.

    ````powershell
    Invoke-Command -ComputerName $computername { 
        Get-WinEvent -LogName Security 
    }
    ````

    Notice the event with Id 1102.
