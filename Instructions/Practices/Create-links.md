# Practice: Create links

## Required VMs

* VN1-DC1
* VN1-FS1

## Task

On VN1-FS1, create a hard link to C:\\BootStrap\\BootStrap.ps1 on C:\\setupscript.ps1. Delete the original file and recover it by creating a hard link from c:\\setupscript.ps1. Create a junction to C:\\Bootstrap on d:\\Setup and delete the file Labroot.cer. Create a symbolic link D:\Sysvol targeting \\\\vn1-dc1\\sysvol.

## Instructions

Perform these steps on VN1-FS1.

1. Sign in as **ad\\Administrator**.
1. Open **Windows PowerShell** as Administrator.
1. Create a hard link **C:\\setupscript.ps1** targeting **C:\\BootStrap\\BootStrap.ps1**.

    ````powershell
    $target = 'C:\BootStrap\BootStrap.ps1'
    $name = 'SetupScript.ps1'
    $path = 'C:\'
    New-Item -ItemType HardLink -Target $target  -Name $name -Path $path
    ````

1. Compare the content of **C:\\setupscript.ps1** with **C:\\BootStrap\\BootStrap.ps1**.

    ````powershell
    $hardlink = "$path\$name"
    Get-Content $target
    Get-Content $hardlink
    ````

1. Delete the original file **C:\\BootStrap\\BootStrap.ps1**.

    ````powershell
    Remove-Item $target
    ````

1. Get the content of **C:\\setupscript.ps1**.

    ````powershell
    Get-Content $hardlink
    ````

    You should still receive the content.

1. Recover the original file by creating a hard link.

    ````powershell
    New-Item `
        -ItemType HardLink `
        -Target $hardLink `
        -Name 'BootStrap.ps1' `
        -Path 'C:\BootStrap'
    ````

1. Create a junction **D:\\Setup** targeting **C:\\BootStrap**.

    ````powershell
    New-Item `
        -ItemType Junction `
        -Target 'C:\BootStrap\' `
        -Name 'Setup' `
        -Path 'D:\'
    ````

1. List the contents of **D:\\Setup**.

    ````powershell
    Get-ChildItem D:\Setup\
    ````

1. Delete **D:\\Setup\\LabRoot.cer**.

    ````powershell
    Remove-Item D:\Setup\LabRoot.cer
    ````

1. List the content of **C:\\Bootstrap**

    ````powershell
    Get-ChildItem C:\Bootstrap
    ````

    > Do you see **Labroot.cer**?

1. Create a symbolic link **D:\\Sysvol** targeting **\\\\VN1-DC1\\Sysvol**.

    ````powershell
    New-Item `
        -ItemType SymbolicLink `
        -Target \\vn1-dc1\sysvol `
        -Name Sysvol `
        -Path D:\
    ````

1. List the contents of **D:\\Sysvol**.

    ````powershell
    Get-ChildItem D:\Sysvol -Recurse
    ````
