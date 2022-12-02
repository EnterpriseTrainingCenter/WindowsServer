# Lab: Installing and managing Hyper-V

## Required VMs

* VN1-SRV1
* PM-SRV1
* PM-SRV2
* CL1

## Setup

On CL1, sign in as **ad\Administrator**.

At the time of writing this lab, there was a bug in the setup preventing PM-SRV1 and PM-SRV2 from being joined to the domain. As a workaround, perform the steps outlined at <https://github.com/EnterpriseTrainingCenter/WindowsServer/issues/102> 

## Introduction

## Exercises

1. Installing and configuring Hyper-V
1. Managing virtual machines
1. Managing virtual disks
1. Working with checkpoints
1. Managing virtual switches
1. Replicating virtual machines

## Exercise 1: Installing and configuring Hyper-V

1. Configure nested virtualization
1. Install the Hyper-V role
1. Install the Hyper-V management tools
1. Configure basic Hyper-V settings
1. Create an external virtual switch

### Task 1: Configure nested virtualization

Perform this task on the host.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click the name of your compuer.
1. Under Virtual Machines, in the context-menu of **WIN-PM-SRV1**, click **Shut down...**.
1. In the context-menu of **WIN-PM-SRV1**, click **Settings...**
1. In Settings for WIN-PM-SRV1, in the left pane, click **Memory**.
1. Under Memory, in **RAM**, type 4096. Deactivate **Enable Dynamic Memory**.
1. In the left pane, expand the first **Network Adapter** and click **Advanced Features**.
1. Under Advanced Features, **MAC address**, activate **Enable MAC address spoofing**.
1. In the left pane, expand the second **Network Adapter** and click **Advanced Features**.
1. Under Advanced Features, **MAC address**, activate **Enable MAC address spoofing** and click **OK**.
1. Run **Windows PowerShell** or **Terminal** with Administrator rights.
1. In Windows PowerShell or Terminal, for **WIN-PM-SRV1** expose the virtualtion extensions to the virtual machine.

    ````powershell
    Set-VMProcessor -VMName 'WIN-PM-SRV1' -ExposeVirtualizationExtensions $true
    ````

1. Switch to **Hyper-V Manager**.
1. In the context-menu of **WIN-PM-SRV1**, click **Start**.

Repeat this task for **WIN-PM-SRV2**.

### Task 2: Install Hyper-V

#### Desktop Experience

Perform this task on CL1.

1. Open Server Manager.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on page **Before You Begin**, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-base installation** is selected and click **Next >**.
1. On page Server Selection, click **PM-SRV1.ad.adatum.com** and click **Next >**.
1. On page Server Roles, activate **Hyper-V**
1. In Add features that are required for Hyper-V, click **Add Features**.
1. In **Add Roles and Features Wizard**, on page **Server Roles**, click **Next >**.
1. On page Features, click **Next >**.
1. On page Hyper-V, click **Next >**.
1. On page Virtual Switches, click **Next >**.
1. On page Virtual Machine Migration, click **Next >**.
1. On page Default Stores, in **Default location for virtual hard disk files**, type **C:\\ClusterStorage\\Volume*x*\\Hyper-V\\Virtual Hard Disks**, where x is the volume number you recorded for the 80 GB disk in the previous exercise. In **Default location for virtualmachine configuration files**, type **C:\\ClusterStorage\\Volume*x*\\Hyper-V**, where x is the volume number you recorded for the 80 GB disk in the previous exercise. Click **Next >**.
1. On page Confirmation, activate **Restart the destination server automatically if required** and click **Install**.
1. On  page **Results**, wait for the installation to succeed, then click **Close**.

Repeat the steps of this task to install the role on **VN1-SRV6**, **VN1-SRV7**, **VN1-SRV8**, and **VN1-SRV9**

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install **Hyper-V** on **WIN-VN1-SRV6**, **WIN-VN1-SRV7**, **WIN-VN1-SRV8**, and **WIN-VN1-SRV9** without restarting them.

   ````powershell
   $computerName = @('VN1-SRV6', 'VN1-SRV7', 'VN1-SRV8', 'VN1-SRV9')
   Invoke-Command -ComputerName $computerName -ScriptBlock {
      Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
   }
   ````

1. Restart **WIN-VN1-SRV6**, **WIN-VN1-SRV7**, **WIN-VN1-SRV8**, and **WIN-VN1-SRV9** to keep the cluster running.

   ````powershell
   $computername | ForEach-Object {
      Restart-Computer -ComputerName $PSItem -WsmanAuthentication Default -Wait
   }
   ````

1. On **WIN-VN1-SRV6**, **WIN-VN1-SRV7**, **WIN-VN1-SRV8**, and **WIN-VN1-SRV9**, set the default stores to the CSV **Hyper-converged disks**.

    ````powershell
    Set-VMHost `
        -ComputerName $computerName `
        -VirtualHardDiskPath `
            'C:\ClusterStorage\Hyper-converged Disk\Hyper-V\Virtual Hard Disks' `
        -VirtualMachinePath 'C:\ClusterStorage\Hyper-converged Disk\Hyper-V'
    ````



## Exercise 2: Managing virtual machines

1. Create a virtual machine
1. Configure settings for the virtual machine
1. Configure Windows Server
1. Manage the status of the virtual machine
1. Administer the virtual operating system from the host

## Exercise 3: Managing virtual disks

1. Create an additional dynamic disk
1. Create a differencing disk
1. Create a virtual machine from a differencing disk
1. Merge a differencing disk
1. Move virtual machine data

## Exercise 4: Working with checkpoints

1. Create checkpoints
1. Revert to a checkpoint
1. Delete checkpoints

## Exercise 5: Managing virtual switches

1. Create a private virtual switch
1. Create an internal virtual switch
1. Configure host-based NAT

## Exercise 6: Replicating virtual machines
