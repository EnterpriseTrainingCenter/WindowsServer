# Practice: Use a Group Managed Service Account

## Required VMs

* VN1-SRV5
* PM-SRV2
* CL1

If you did not complete the lab [Deploying domain controllers](Deploying-domain-controllers.md), in addition to the VMs above, **VN1-SRV1** is required. If VN1-SRV1 is already shut down after the lab, do not start it.

## Setup

If you skipped the practice [Install Remote Server Administration Tools](Practices/Install-Remote-Server-Administration-Tools.md), on **CL1**, in **Terminal**, execute ````C:\LabResources\Solutions\Install-RemoteServerAdministrationTools.ps1````.

## Task

Create a Group Managed Service Account, install it on PM-SRV2 and use it to create a service.

## Instructions

Perform these steps on CL1.

1. Sign in as **Administrator@ad.adatum.com**.
1. Run **Terminal**.
1. Generate a new root key for the Microsoft Group KdsSvc within Active Directory.

    ````powershell
    Add-KdsRootKey -EffectiveTime (Get-Date).AddHours(-10)
    ````

    Note: In real-world you should omit the parameter ````-EffectiveImmediately```` and wait some hours to replicate the key throught your forest.

1. Create a new Group Managed Service Account with the name **MyService**, the DNS host name **myservice.ad.adatum.com**, and allow PM-SRV2 to retrieve the managed password.

    ````powershell
    New-ADServiceAccount `
        -Name MyService `
        -DNSHostName myservice.ad.adatum.com `
        -PrincipalsAllowedToRetrieveManagedPassword PM-SRV2$
    ````

Perform these steps on PM-SRV2.

1. Sign in as **Administrator@ad.adatum.com**.
1. In SConfig, enter **15**.
1. Install the PowerShell module for Active Directory.

    ````powershell
    Install-WindowsFeature -Name RSAT-AD-PowerShell
    ````

1. Install the Group Managed Service Account **MyService**.

    ````powershell
    Install-ADServiceAccount -Identity MyService
    ````

1. Install a test service **NotepadService** with the binary **C:\windows\System32\notepad.exe** to verify the Group Managed Service Account.

    ````powershell
    $name = 'NotepadService'
    New-Service `
        -Name $name `
        -BinaryPathName C:\windows\System32\notepad.exe
    ````

1. Get the new service using WMI.

    ````powershell
    $service = Get-WmiObject -Class Win32_Service -Filter "Name = '$name'"
    ````

1. Change the start account for the service to **ad\MyService$**

    ````powershell
    $service.Change($null, $null, $null, $null, $null, $null, 'ad\MyService$')
    ````

1. Verify the change.

    ````powershell
    $service = Get-WmiObject -Class Win32_Service -Filter "Name = '$name'"
    $service.StartName
    ````

    > The return value should bei ad\MyService$.

1. Open **Task Manager**.

    ````powershell
    taskmgr.exe
    ````

1. In Task Manager, click **More details**.
1. Click the tab Details. Move Task Manager side by side with the console window.
1. In console windows, start the service.

    ````powershell
    Start-Service -Name $name
    ````

    Note: PowerShell will seem to hang, because Notepad is not a real service. Continue to the next step. If you receive an error message before you can finish the next steps, run the command again.

1. Switch to **Task Manager**.

    > You should see a process notepad.exe running under the user name MyService$.

1. Sign out.

    ````powershell
    logoff
    ````
