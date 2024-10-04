# Practice: Verify the SYSVOL replication mode

## Setup

You must have completed the lab [Deploying domain controllers](../Labs/Deploying-domain-controllers.md). If you skipped the lab, VM **VN1-SRV1** is required. In step 3 replace **VN1-SRV5** with **VN1-SRV1**.

## Required VMs

* VN1-SRV5
* CL1

## Task

Verify that SYSVOL is replicated using DFSR.

## Instructions

Perform these steps on VN1-CL1.

1. Logon as **ad\Administrator**.
1. Open **Terminal**.
1. Open a remote PowerShell session to VN1-SRV5.

    ````powershell
    Enter-PSSession -ComputerName VN1-SRV5
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
