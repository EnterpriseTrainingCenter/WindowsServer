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
|                                                            | Lab: Manage local storage                                         |
| Monitor performance                                        | Practice: Install roles using Server Manager                      |
|                                                            | Lab: Explore Windows Admin Center                                 |
| Audit file server events                                   | Practice: Install prerequisites for file serving                  |
| File Server resource management                            | Practice: Configure e-mail notifications in FSRM                  |
|                                                            | Practice: Configure Access-Denied Assistance                      |
|                                                            | Practice: Configure a classification schedule                     |
|                                                            | Practice: Configure storage report options                        |

## Catch-up scripts

The scripts may be run on any domain-joined machine. Some operations can be executed on a local machine only. In these cases, a warning is emitted with an instruction what to do. Some operations cannot be scripted. In these rare cases, a warning is displayed with a reference where to find instructions to perform it manually.

## Catch-up scripts for practices

| Practice                                     | Script                                      |
|----------------------------------------------|---------------------------------------------|
| Install Remote Server Administration Tools   | Install-RemoteServerAdministrationTools.ps1 |
| Create a custom Microsoft Management Console | New-CustomMMC.ps1                           |
| Explore Server Manager                       | Add-ServerManagerServers.ps1                |
| Install Windows Admin Center using a script  | Install-AdminCenter.ps1                     |
| Install Windows Terminal                     | Install-WindowsTerminal.ps1                 |
| Install roles using Server Manager           | Install-FileServer.ps1                      |
| Install roles using Windows Admin Center     | Install-CAWebEnrollment.ps1                 |
| Manage features using PowerShell             | Install-WindowsServerBackup.ps1             |
| Manage local users                           | New-LocalAdmins.ps1                         |
| Manage local groups                          | Add-LocalAdministratorsMember.ps1           |
| Enable the Active Directory Recycle Bin      | Enable-ADRecycleBin.ps1                     |
| Install prerequisites for file serving       | New-Shares.ps1                              |
| Harden SMB                                   | Set-SMB.ps1                                 |
| Install File Server Resource Manager         | Install-FSRM.ps1                            |
| Configure e-mail notifications in FSRM       | Set-FSRMEmail.ps1                           |
| Configure Access-Denied Assistance           | Enable-AccessDeniedAssistance.ps1           |
| Configure a classification schedule          | Set-ClassificationSchedule.ps1              |
| Intall the Microsoft Office Filter Pack      | Install-FilterPack.ps1                      |
| Configure storage report options             | Set-FSRMReports.ps1                         |

## Catch-up scripts for labs

| Lab                                                        | Script                                                       |
|------------------------------------------------------------|--------------------------------------------------------------|
| Explore Windows Admin Center                               | Add-WACServers.ps1                                           |
| Manage servers remotely using Microsoft Management Console | Enable-ComputerRemoteManagement.ps1                          |
| Manage domain users, groups, and computers                 | New-ADObjects.ps1                                            |
| Manage local storage                                       | New-Volumes.ps1                                              |
| Manage file sharing                                        | New-Shares.ps1                                               |
| File Server Resource Management                            | Invoke-FSRMConfiguration.ps1                                 |
| Audit file server events                                   | Enable-FSAuditing.ps1                                        |
