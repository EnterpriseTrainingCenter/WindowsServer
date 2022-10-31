# Practice: Update the Active Directory schema

## Required VMs

* VN1-SRV1
* CL1

## Task

Update the Active Directory schema to the current version.

## Instructions

Perform these steps on the host.

1. In the menu of **"WIN-CL1" on "..." - Connection to virtual computer**, click Media, DVD Drive, **Insert disk...**
1. In **Open**, open **C:\Labs\ISOs\2022_x64_EN_Eval.iso**.

Perform these steps on VN1-CL1.

1. Logon as **ad\Administrator**.
1. Open **Terminal**.
1. Prepare the forest for the latest version of Windows Server.

    ````powershell
    D:\support\adprep\adprep.exe /forestprep
    ````

1. Enter **c** to continue.

    A message will be displayed, telling that the forest-wide information has already been updated. There is not change in the Active Directory schema between Windows Server 2019 and 2022.

1. Prepare the domain for the latest version of Windows Server.

    ````powershell
    D:\support\adprep\adprep.exe /domainprep /gpprep
    ````

    Again, a message will be displayed, telling that the domain-wide information has already been updated.

Perform these steps on the host.

1. In the menu of **"WIN-CL1" on "..." - Connection to virtual computer**, click Media, DVD Drive, **Eject "2022_x64_EN_Eval.iso"**.
