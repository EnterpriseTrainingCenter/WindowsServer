# Practice: PowerShell remoting

## Required VMs

* VN1-SRV1
* VN1-SRV5
* VN1-SRV6
* CL1

## Task

On CL1, check the value IPEnableRouter on VN1-SRV5 using an interactive remote session. Check the same value on VN1-SRV6 by invoking remote commands.

## Instructions

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. From the context-menu of **Start** (you can press WIN + X), launch **Terminal** as Administrator.
1. Start an interactive remote session with **VN1-SRV5**.

    ````powershell
    Enter-PSSession VN1-SRV5
    ````

1. Query the value **IPEnableRouter** in the registry key **HKLM:\system\CurrentControlSet\services\Tcpip\Parameters**.

    ````powershell
    Get-ItemProperty `
        -Path HKLM:\system\CurrentControlSet\services\Tcpip\Parameters `
        -Name IPEnableRouter
    ````

1. Exit the remote session.

    ````powershell
    Exit-PSSession
    ````

1. Store the registry key path **HKLM:\system\CurrentControlSet\services\Tcpip\Parameters** in a variable.

    ````powershell
    $path = 'HKLM:\system\CurrentControlSet\services\Tcpip\Parameters'
    ````

1. Invoke a command with VN1-SRV6 to query the same registry value as above.

    ````powershell
    Invoke-Command -ComputerName VN1-SRV6 -ScriptBlock {
        Get-ItemProperty -Path $using:path -Name IPEnableRouter
    }
    ````
