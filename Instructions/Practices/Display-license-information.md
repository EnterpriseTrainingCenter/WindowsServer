# Practice: Display license information

## Required VMs

* VN1-SRV1
* CL1
* VN1-SRV5

## Task

Display help for the license manager tool and display the licensing information of VN1-SRV2.

## Instructions

Perform these steps on CL1.

1. Sign in as **ad\\Administrator**.
1. Open **Windows Terminal**.
1. Open a remote PowerShell session to **VN1-SRV5**.

    ````powershell
    Enter-PSSession VN1-SRV5
    ````

1. Display help of the license manager tool.

    ````powershell
    cscript $env:windir\system32\slmgr.vbs /?
    ````

1. Display licensing information.

    ````powershell
    cscript $env:windir\system32\slmgr.vbs /dli
    ````
