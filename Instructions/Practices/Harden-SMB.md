# Practice: Harden SMB

## Required VMs

* VN1-DC1
* VN1-FS1
* VN1-CORE1
* CL1

## Task

Disable SMB 1.0 on VN1-FS1 and verify the shares are still accessible. Then remove SMB 1.0 from VN1-FS1.

Enable SMB encryption on server VN1-CORE1 and on the share \\\\VN1-FS1\\IT.

## Instructions

Perform these steps on CL1.

1. Sign in as **ad\Administrator**.
1. Run **Windows Terminal** as Administrator.
1. Create a remote PowerShell session with **VN1-FS1**.

    ````powershell
    Enter-PSSession -ComputerName VN1-FS1
    ````

1. Disable SMB 1.0.

    ````powershell
    Set-SmbServerConfiguration -EnableSMB1Protocol $false
    ````

1. Under **Confirm**, enter **y**.
1. Exit the remote PowerShell session, but leave Windows Terminal open.

    ````powershell
    Exit-PSSession
    ````

1. Switch to **File Explorer** and navigate to **\\\\vn1-fs1** and try to navigate into the shares.

    > Can you still access the shares?

1. Switch to **Windows Terminal**.
1. Uninstall SMB 1.0.

    ````powershell
    Remove-WindowsFeature -Name FS-SMB1 -ComputerName VN1-FS1
    ````

1. Verify, you are still able to access the shares on **VN1-FS1**.
1. Create a remote PowerShell session with **VN1-CORE1**.

    ````powershell
    Enter-PSSession -ComputerName VN1-CORE1
    ````

1. Enable SMB encryption for the server

    ````powershell
    Set-SmbServerConfiguration -EncryptData $true
    ````

1. Under **Confirm**, enter **y**.
1. Exit the remote PowerShell session, but leave Windows Terminal open.

    ````powershell
    Exit-PSSession
    ````

1. Enable encyprtion for the share **\\\\vn1-fs1\\IT**.

    ````powershell
    Invoke-Command -ComputerName VN1-FS1 -ScriptBlock {
        Set-SmbShare -Name IT -EncryptData $true -Force
    }
    ````
