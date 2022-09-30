# Practice: Work with PowerShell arrays

## Required VMs

* VN1-SRV1
* VN1-SRV2
* VN1-SRV4
* VN1-SRV5
* VN1-SRV6
* CL1

## Task

On CL1, query the running services on VN1-SRV1, VN1-SRV4, VN1-SRV6, VN1-SRV5, VN1-SRV2 with a single command using iteration.

## Instructions

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. From the context-menu of **Start** (you can press WIN + X), launch **Terminal** as Administrator.
1. Store the strings VN1-SRV1, VN1-SRV4, VN1-SRV6, VN1-SRV5, VN1-SRV2 in the variable $servers.

    ````powershell
    $servers = @('VN1-SRV1', 'VN1-SRV4', 'VN1-SRV6', 'VN1-SRV5', 'VN1-SRV2')
    ````

1. Iterate through the array $servers and list the running services on each of the servers while measuring the executing time

    ````powershell
    Measure-Command {
        $servers | 
        ForEach-Object { 
            Get-Service -ComputerName $PSItem | 
            Where-Object { 
                $PSItem.Status -eq 'Running' 
            }
        } |
        Out-Default
    }
    ````

    Note: Get-Service may fail on some of the servers. This is no problem for this practice, but shows, that the iteration worked.

1. List the services on the computers in $array without iteration while measuring the execution time.

    ````powershell
    Measure-Command {
        Get-Service -ComputerName $servers | 
        Where-Object { 
            $PSItem.Status -eq 'Running' 
        } |
        Out-Default
    }

Note: Iteration takes more time, but sometimes it is necessary, because not parameters accept arrays as values.
