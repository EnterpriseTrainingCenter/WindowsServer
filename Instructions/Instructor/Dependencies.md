# Practice and lab dependencies

This document lists all dependencies between labs and practices and catch up scripts to execute, if students skipped a lab.

The scripts are available in **C:\LabResources\Solutions** on each VM. Execute the scripts on the given VM to finish the practice or lab. If not indicated otherwise, execute the script with domain admin rights.

## Dependencies of practices

| Practice                                     | Dependencies                                                        |
|----------------------------------------------|---------------------------------------------------------------------|
| Create a custom Microsoft Management Console | Practice: Install Remote Server Administration Tools                |
| Explore Server Manager                       | Practice: Install Remote Server Administration Tools                |
| Install roles using Server Manager           | Practice: Explore Server Manager                                    |
| Install roles using Windows Admin Center     | Lab: Explore Windows Admin Center                                   |
| Manage features using PowerShell             | Practice: Install Remote Server Administration Tools                |
| Manage services using Windows Admin Center   | Lab: Explore Windows Admin Center                                   |
| View events using Windows Admin Center       | Lab: Explore Windows Admin Center                                   |
| Manage local users                           | Practice: Create a custom Microsoft Management Console              |
|                                              | Lab: Explore Windows Admin Center                                   |
| Manage local groups                          | Practice: Manage local users                                        |
| Enable the Active Directory Recycle Bin      | Practice: Install Remote Server Administration Tools                |
| Harden SMB                                   | Practice: Install prerequisites for file serving                    |
| Install File Server Resource Manager         | Practice: Install prerequisites for file serving                    |
| Configure e-mail notifications in FSRM       | Practice: Install File Server Resource Manager                      |
| Configure Access-Denied Assistance           | Practice: Install File Server Resource Manager                      |
| Configure a classification schedule          | Practice: Install File Server Resource Manager                      |
| Configure storage report options             | Practice: Install File Server Resource Manager                      |

## Dependencies of labs

| Lab                                                        |                                                                   |
|------------------------------------------------------------|-------------------------------------------------------------------|
| Explore Windows Admin Center                               | Practice: Install Windows Admin Center using a script             |
| Manage servers remotely using Microsoft Management Console | Practice: Create a custom Microsoft Management Console            |
|                                                            | Lab: Explore Windows Admin Center                                 |
| Post installation configuration                            | Practice: Install Remote Server Administration Tools              |
|                                                            | Practice: Install Windows Admin Center using a script             |
|                                                            | Practice: Install Windows Server with Desktop Experience manually |
|                                                            | Practice: Install Windows Server manually                         |
| Manage domain users, groups, and computers                 | Practice: Create a custom Microsoft Management Console            |
|                                                            | Lab: Explore Windows Admin Center                                 |
|                                                            | Practice: Manage local groups                                     |
|                                                            | Practice: Enable the Active Directory Recycle Bin                 |
| Manage local storage                                       | Lab: Manage servers remotely using Microsoft Management Console   |
|                                                            | Practice: Install roles using Server Manager                      |
| Manage file sharing                                        | Practice: Create a custom Microsoft Management Console            |
|                                                            | Lab: Explore Windows Admin Center                                 |
|                                                            | Lab: Manage local storage                                         |
| Monitor performance                                        | Practice: Install roles using Server Manager                      |
| File Server resource management                            | Practice: Configure e-mail notifications in FSRM                  |
|                                                            | Practice: Configure Access-Denied Assistance                      |
|                                                            | Practice: Configure a classification schedule                     |
|                                                            | Practice: Configure storage report options                        |

## Catch up scripts for practices

| Practice                                     | VM        | Script                                      |
|----------------------------------------------|-----------|---------------------------------------------|
| Install Remote Server Administration Tools   | CL1       | Install-RemoteServerAdministrationTools.ps1 |
| Create a custom Microsoft Management Console | CL1       | New-CustomMMC.ps1                           |
| Explore Server Manager                       | CL1       | Add-ServerManagerServers.ps1                |
| Install Windows Admin Center using a script  | VN1-GW1   | Install-AdminCenter.ps1                     |
| Install Windows Terminal                     | CL1       | Install-WindowsTerminal.ps1                 |
| Install roles using Server Manager           | CL1       | Install-FileServer.ps1                      |
| Install roles using Windows Admin Center     | CL1       | Install-CAWebEnrollment.ps1                 |
| Manage features using PowerShell             | CL1       | Install-WindowsServerBackup.ps1             |
| Manage local users                           | CL1       | New-LocalAdmins.ps1                         |
| Manage local groups                          | CL1       | Add-LocalAdministratorsMember.ps1           |
| Enable the Active Directory Recycle Bin      | CL1       | Enable-ADRecycleBin.ps1                     |
| Create links                                 | FS1       | New-Links.ps1                               |
| Install prerequisites for file serving       | CL1       | New-Shares.ps1                              |
| Harden SMB                                   | CL1       | Set-SMB.ps1                                 |
| Install File Server Resource Manager         | CL1       | Install-FSRM.ps1                            |
| Configure e-mail notifications in FSRM       | CL1       | Set-FSRMEmail.ps1                           |
| Configure Access-Denied Assistance           | CL1       | Enable-AccessDeniedAssistance.ps1           |
| Configure a classification schedule          | CL1       | Set-ClassificationSchedule.ps1              |
| Intall the Microsoft Office Filter Pack      | CL1       | Install-FilterPack.ps1                      |
| Configure storage report options             | CL1       | Set-FSRMReports.ps1                         |

## Catch up scripts for labs

| Lab                                                        | VM  | Script                                                       |
|------------------------------------------------------------|-----|--------------------------------------------------------------|
| Explore Windows Admin Center                               |     | No script available. Perform all exercises except exercise 3 |
| Manage servers remotely using Microsoft Management Console | CL1 | Enable-ComputerRemoteManagement.ps1                          |
| Manage domain users, groups, and computers                 | CL1 | New-ADObjects.ps1                                            |
| Manage local storage                                       | CL1 | New-Volumes.ps1                                              |
| Manage file sharing                                        | CL1 | New-Shares.ps1                                               |
| File Server Resource Management                            | CL1 | Invoke-FSRMConfiguration.ps1                                 |