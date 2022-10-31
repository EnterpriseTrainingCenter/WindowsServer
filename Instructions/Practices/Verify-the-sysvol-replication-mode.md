# Practice: Verify the SYSVOL replication mode

## Required VMs

* VN1-SRV1
* CL1

## Task

Verify that SYSVOL is replated using DFSR.

## Instructions

Perform these steps on VN1-CL1.

1. Logon as **ad\Administrator**.
1. Open **Terminal**.
1. Open a remote PowerShell session to VN1-SRV1.

    ````powershell
    Enter-PSSession -ComputerName VN1-SRV1
    ````

1. Get the global SYSVOL replication migration status.

    ````powershell
    dfsrmig /getglobalstate
    ````

    This should return the global state 'Eliminated'.

1. Exit the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````
