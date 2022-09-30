# Practice: Manage PowerShell output

## Required VMs

* VN1-SRV1
* CL1

## Task

On CL1, get all information about the running process of Terminal. Start all services configured for automatic start but not currently running.

## Instructions

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. From the context-menu of **Start** (you can press WIN + X), launch **Terminal** as Administrator.
1. Find the Terminal process

    ````powershell
    Get-Process *terminal*
    ````

1. Show detailed information about the Terminal process

    ````powershell
    Get-Process WindowsTerminal | Format-List
    ````

1. Show more information about the Terminal process

    ````powershell
    Get-Process WindowsTerminal | Format-List *
    ````

1. Get a list of installed services.

    ````powershell
    Get-Service
    ````

1. Get information about the returned class of Get-Service.

    ````powershell
    Get-Service | Get-Member
    ````

    Notice the **StartType** property.

1. List the services with **Name**, **Status**, and **StartType** properties only.

    ````powershell
    Get-Service | 
    Select-Object Name, Status, StartType
    ````

1. List all services with the StartType **Automatic**. Display Name, Status, and StartType.

    ````powershell
    Get-Service | 
    Where-Object { $PSItem.StartType -eq 'Automatic' } | 
    Select-Object Name, Status, StartType
    ````

1. List all services with the Status **Stopped**. Display Name, Status, and StartType.

    ````powershell
    Get-Service | 
    Where-Object { $PSItem.Status -eq 'Stopped' } | 
    Select-Object Name, Status, StartType
    ````

1. Now, list all services with the Status **Stopped** and the StartType **Automatic**. Display Name, Status, and StartType.

    ````powershell
    Get-Service | 
    Where-Object { 
        $PSItem.Status -eq 'Stopped' -and $PSitem.StartType -eq 'Automatic'
    } | 
    Select-Object Name, Status, StartType
    ````

1. Start all services with the Status **Stopped** and the StartType **Automatic**.

    ````powershell
    Get-Service | 
    Where-Object { 
        $PSItem.Status -eq 'Stopped' -and $PSitem.StartType -eq 'Automatic'
    } | 
    Start-Service
    ````

    Note: Some services will not start correctly, because the are terminated immediatly. Moreover, some services will stop running after a few seconds or minutes. This is normal.
