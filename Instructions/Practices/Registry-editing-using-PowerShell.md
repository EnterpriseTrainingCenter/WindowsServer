# Practice: Registry editing using PowerShell

## Required VMs

* VN1-DC1
* CL1

## Task

On CL1, list the keys and values of the registry key ‘HKLM:\system\CurrentControlSet\services\Tcpip\Parameters’. Within that key, set the value IPEnableRouter to 1 and verify the change. Remove the value IPEnablerouter and verify the change again.

## Instructions

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. From the context-menu of **Start** (you can press WIN + X), launch **Windows Terminal** as Administrator.
1. Store the registry key path **HKLM:\system\CurrentControlSet\services\Tcpip\Parameters** in a variable.

    ````powershell
    $path = 'HKLM:\system\CurrentControlSet\services\Tcpip\Parameters'
    ````

1. List the contents of the registry key.

    ````powershell
    Get-ChildItem $path
    ````

1. Within the key set value of **IPEnableRouter** to 1.

    ````powershell
    $name = 'IPEnableRouter'
    Set-ItemProperty -Path $path -Name $name -Value 1
    ````

1. Verify the changed value.

    ````powershell
    Get-ItemProperty -Path $path -Name $name
    ````

1. Remove the value.

    ````powershell
    Remove-ItemProperty -Path $path -Name $name
    ````

1. Verify the value was removed.

    ````powershell
    Get-ItemProperty -Path $path -Name $name
    ````

    An error message should be returned.
