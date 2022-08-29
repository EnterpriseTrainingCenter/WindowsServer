# Practice and lab dependencies

This document lists all dependencies between labs and practices and catch up scripts to execute, if students skipped a lab.

The scripts are available in **C:\LabResources\Solutions** on each VM. Execute the scripts on the given VM to finish the practice or lab. If not indicated otherwise, execute the script with domain admin rights.

## Dependencies of practices

| Practice                                   | Dependencies                                                        |
|--------------------------------------------|---------------------------------------------------------------------|
| Explore Server Manager                     | Practice: Install Remote Server Administration Tools                |
| Install roles using Server Manager         | Practice: Explore Server Manager; Lab: Explore Windows Admin Center |
| Install roles using Windows Admin Center   | Lab: Explore Windows Admin Center                                   |
| Manage services using Windows Admin Center | Lab: Explore Windows Admin Center                                   |
| View events using Windows Admin Center     | Lab: Explore Windows Admin Center                                   |
| Manage local users                         | Practice: Create a custom Microsoft Management Console              |
|                                            | Lab: Explore Windows Admin Center                                   |
| Manage local groups                        | Practice: Manage local users                                        |
| Enable the Active Directory Recycle Bin    | Practice: Install Remote Server Administration Tools                |

## Dependencies of labs

| Lab                                                        |                                                                   |
|------------------------------------------------------------|-------------------------------------------------------------------|
| Explore Windows Admin Center                               | Practice: Install Windows Admin Center using a script             |
| Manage servers remotely using Microsoft Management Console | Practice: Install Remote Server Administration Tools              |
|                                                            | Lab: Explore Windows Admin Center                                 |
| Post installation configuration                            | Practice: Install Remote Server Administration Tools              |
|                                                            | Practice: Install Windows Admin Center using a script             |
|                                                            | Practice: Install Windows Server with Desktop Experience manually |
|                                                            | Practice: Install Windows Server manually                         |

## Catch up scripts for practices

| Practice                                     | VM        | Script                                      |
|----------------------------------------------|-----------|---------------------------------------------|
| Install Remote Server Administration Tools   | CL1       | Install-RemoteServerAdministrationTools.ps1 |
| Create a custom Microsoft Management Console | CL1       | New-CustomMMC.ps1                           |
| Explore Server Manager                       | CL1       | Add-ServerManagerServers.ps1                |
| Install Windows Admin Center using a script  | VN1-GW1   | Install-AdminCenter.ps1                     |
| Install Windows Terminal                     | VN1-FS1   | Install-WindowsTerminal.ps1                 |
| Install roles using Server Manager           | CL1       | Install-FileServer.ps1                      |
| Install roles using Windows Admin Center     | CL1       | Install-CAWebEnrollment.ps1                 |
| Manage features using PowerShell             | CL1       | Install-WindowsServerBackup.ps1             |
| Manage local users                           | CL1       | New-LocalAdmins.ps1                         |
| Manage local groups                          | CL1       | Add-LocalAdministratorsMember.ps1           |

## Catch up scripts for labs

| Lab                                                        | VM  | Script                                                       |
|------------------------------------------------------------|-----|--------------------------------------------------------------|
| Explore Windows Admin Center                               |     | No script available. Perform all exercises except exercise 3 |
| Manage servers remotely using Microsoft Management Console | CL1 | Enable-ComputerRemoteManagement.ps1                          |