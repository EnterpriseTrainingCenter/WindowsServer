# Lab: Configuring and managing Storage Spaces Direct and hyper-converged virtualization

## Required VMs

* VN1-SRV1
* VN1-SRV4
* VN1-SRV5
* VN1-SRV6
* VN1-SRV7
* VN1-SRV8
* VN1-SRV9
* CL1

## Setup

1. On CL1, sign in as **ad\Administrator**.
1. On VN1-SRV6, sign in a **ad\Administrator**.
1. In Sconfig, enter **15**.

## Introduction

Adatum wants to evaluate Storage Spaces Direct. First, Storage Spaces Direct should be used as a Scale-Out File Server for the existing virtual machines running on another compute cluster. Then, a hyper-converged virtualization cluster running a mission-critical virtual machine should be created. The resiliency of the configuraton must be tested thoroughly.

## Exercises

1. [Configuration of Storage Spaces Direct](#exercise-1-configuration-of-storage-spaces-direct)
1. [Using a Scale-Out File Server with Hyper-V](#exercise-2-using-a-scale-out-file-server-with-hyper-v)
1. [Creating a hyper-converged virtualization cluster](#exercise-3-creating-a-hyper-converged-virtualization-cluster)
1. [Testing the resiliency of the hyper-converged virtualization](#exercise-4-testing-the-resiliency-of-the-hyper-converged-virtualization)
1. [Shut down the Storage Spaces Direct cluster](#exercise-5-shut-down-the-storage-spaces-direct-cluster)

## Exercise 1: Configuration of Storage Spaces Direct

1. [Install the failover clustering feature](#task-1-install-the-failover-clustering-feature) on VN1-SRV6, VN1-SRV7, VN1-SRV8, and VN1-SRV9
1. [Create a group](#task-2-create-a-group) in Active Directory with VN1-SRV6, VN1-SRV7, VN1-SRV8, and VN1-SRV9
1. [Create a witness share](#task-3-create-a-witness-share) on VN1-CLST1-FS and grant the group modify permissions
1. [Create a cluster with the NoStorage option](#task-4-create-a-cluster-with-the-nostorage-option) with the nodes VN1-SRV6, VN1-SRV7, VN1-SRV8, and VN1-SRV9 using the share as witness
1. [Enable Storage Spaces Direct](#task-5-enable-storage-spaces-direct) on the cluster
1. [Create virtual disks and volumes](#task-6-create-virtual-disks-and-volumes) on the storage pool according to the table below

   | Virtual disk name    | Size of performance tier | Size of capacity tier | Volume label         |
   |----------------------|--------------------------|-----------------------|----------------------|
   | SOFS disk            | 20 GB                    | 60 GB                 | Hyper-V Data         |
   | Hyper-converged disk | 40 GB                    | 120 GB                | Hyper-converged Data |

1. [Add the virtual disks as Cluster Shared Volume](#task-7-add-disks-as-cluster-shared-volume)

### Task 1: Install the failover clustering feature

For this task, for time purposes, it is recommended to use PowerShell.

#### Desktop Experience

Perform this task on CL1.

1. Open Server Manager.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on page **Before You Begin**, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-base installation** is selected and click **Next >**.
1. On page Server Selection, click **VN1-SRV6.ad.adatum.com** and click **Next >**.
1. On page Server Roles, click **Next >**.
1. On page Features, activate **Failover Clustering**.
1. In Add features that are required for Failover Clustering, click **Add Features**.
1. In **Add Roles and Features Wizard**, on page **Features**, click **Next >**.
1. On page Confirmation, verify your selection and click **Install**.
1. On  page **Results**, do not wait for the installation to succeed. Click **Close**.

Repeat the steps of this task to install the role on **VN1-SRV7**, **VN1-SRV8**, and **VN1-SRV9**.

Do not wait for the installation to succeed. Continue with the next task.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install **Failover Clustering** on **VN1-SRV6**, **VN1-SRV7**, **VN1-SRV8**, and **VN1-SRV9**

    ````powershell
    Invoke-Command `
      -ComputerName VN1-SRV6, VN1-SRV7, VN1-SRV8, VN1-SRV9 `
      -ScriptBlock {
        Install-WindowsFeature `
            -Name Failover-Clustering `
            -IncludeManagementTools `
            -Restart
    }
    ````

Do not wait for the installation to succeed. Continue with the next task.

### Task 2: Create a group

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **ad (local)**.
1. In the context-menu of **ad (local)**, click **New**, **Organizational Unit**.
1. In Create Organizational Unit, in **Name**, type **Entitling groups** and click **OK**.
1. In **Active Directory Administrative Center**, in **ad (local)**, double-click **Entitling groups**
1. In Entitling groups, in the pane **Tasks**, click **New**, **Group**.
1. In Create Group, in **Group name**, enter **Witness VN1-CLST2 Modify**. Under **Group type**, ensure **Security** is selected. Under **Group scope**, click **Domain local**. On the left, click **Members**. Under Members, click **Add...**
1. In Select Groups, Contacts, Computers, Service Accounts, or Groups, click **Object Types...**
1. In Object Types, activate **Computers** and click **OK**.
1. In **Select Groups, Contacts, Computers, Service Accounts, or Groups**, in **Enter the object names to select**, type **VN1-SRV6; VN1-SRV7; VN1-SRV8; VN1-SRV9** and click **OK**.
1. In **Create Group: Witness VN1-CLST2 Modify**, click OK.

### Task 3: Create a witness share

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST1.ad.adatum.com** and click **Roles**.
1. Under Roles (2), in the context-menu of **VN1-CLST1-FS**, click **Add File Share**.
1. In New Share Wizard, on page Select Profile, ensure **SMB Share - Quick** is selected and click **Next >**.
1. On page Share Location, under **Server**, click **vn1-clst1-fs**. Under **Share location**, click **Select by volume**. Under **Select by volume**, ensure, **D:** is selected and click **Next >**.
1. On page Share Name, in **Share name**, type **Witness VN1-CLST2** and click **Next >**.
1. On page Configure share settings, configure these settings and click **Next >**:

   * **Enable access-based enumeration**: activate
   * **Enable continuous availability**: activate
   * **Allow caching of share**: deactivate
   * **Encrypt data access**: activate

1. On page Permissions, click **Customize permissions...**
1. In Advanced Security Settings for Witness VN1-CLST2, on tab Permissions, click **Disable Inheritance**.
1. In Block inheritance, click **Convert inherited permisssions into explicit permissions on this object.**
1. In **Advanced Security Settings for Witness VN1-CLST2**, on tab **Permissions**, click one of the entries **Users** and click **Remove**. Repeat for the other entries **Users**.
1. Click **Add**.
1. In Permissions Entry for Witness VN1-CLST2, click **Select a principal**.
1. In Select User, Computer, Service Account or Group, under **Enter the object name to select**, type **Witness VN1-CLST2 Modify** and click **OK**.
1. In **Permissions Entry for Witness VN1-CLST2**, activate **Modify** and click **OK**.
1. In **Advanced Security Settings for Witness VN1-CLST2**, click the tab **Share**.
1. On the tab Share, click **Everyone** and click **Remove**.
1. Click **Add**.
1. In Permissions Entry for Witness VN1-CLST2, click **Select a principal**.
1. In Select User, Computer, Service Account or Group, under **Enter the object name to select**, type **Witness VN1-CLST2 Modify** and click **OK**.
1. In **Permissions Entry for Witness VN1-CLST2**, activate **Change** and click **OK**.
1. In **Advanced Security Settings for Witness VN1-CLST2**, on tab **Share**, click **Add**.
1. In Permissions Entry for Witness VN1-CLST2, click **Select a principal**.
1. In Select User, Computer, Service Account or Group, click **Locations...**
1. In Locations, click **vn1-clst1-fs.ad.adatum.com** and click **OK**.
1. In **Select User, Computer, Service Account or Group**, under **Enter the object name to select**, type **Administrators** and click **OK**.
1. In **Permissions Entry for Witness VN1-CLST2**, activate **Full Control** and click **OK**.
1. In **Advanced Security Settings for Witness VN1-CLST2**, click **OK**.
1. In **New Share Wizard**, on page **Permissions**, click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.

### Task 4: Create a cluster with the NoStorage option

Perform this task on VN1-SRV6.

Note: The failover cluster installation must have finished before.

1. Create a new cluster the **NoStorage** option and the static IP address 10.1.1.49, named **VN1-CLST2**, and **VN1-SRV6**, **VN1-SRV7**, **VN1-SRV8**, and **VN1-SRV9** as nodes.

   ````powershell
   $cluster = New-Cluster `
      -Name VN1-CLST2 `
      -Node 'VN1-SRV6', 'VN1-SRV7', 'VN1-SRV8', 'VN1-SRV9' `
      -StaticAddress 10.1.1.49 `
      -NoStorage
   ````

1. Configure the quorum to use the file share **\\\\VN1-CLST1-FS\\Witness VN1-CLST2**.

   ````powershell
   Set-ClusterQuorum `
      -Cluster $cluster -FileShareWitness '\\VN1-CLST1-FS\Witness VN1-CLST2\'
   ````

### Task 5: Enable Storage Spaces Direct

Peform these steps on CL1.

1. Open **Terminal**.
1. In Terminal, enable installation of Storage Spaces Direct on non-certified hardware.

   ````powershell
   $computername = 'VN1-SRV6', 'VN1-SRV7', 'VN1-SRV8', 'VN1-SRV9'

   Invoke-Command -Computername $computername -ScriptBlock {
       New-ItemProperty `
        -Path 'HKLM:\SYSTEM\CurrentControlSet\Services\ClusSvc\Parameters' `
        -Name S2D `
        -Value 1 `
        -PropertyType DWORD `
        -Force
    } 
   ````

1. Set the MediaType of all disks to **HDD**

   ````powershell
   Invoke-Command -Computername $computername -ScriptBlock {
       Get-PhysicalDisk | Set-PhysicalDisk -MediaType HDD
   }
   ````

1. Open a CIM session to the first node.

   ````powershell
   $cimSession = New-CimSession -ComputerName $computerName[0]
   ````

1. Activate Storage Spaces Direct.

   ````powershell
   Enable-ClusterS2D `
      -CimSession $cimSession `
      -CacheState Disabled `
      -SkipEligibilityChecks
   ````

1. At the prompt VN1-SRV6: Performing operation 'Enable Cluster Storage Spaces Direct' on Target 'VN1-CLST2', enter **Y**.
1. List the storage pools in the cluster.

   ````powershell
   Get-StoragePool -CimSession $cimSession
   ````

   > There is a new storage pool with the **FiendlyName** **S2D on VN1-CLST2** with a size of about 12 TB.

1. Set the cluster resiliency period to 10 seconds.

   ````powershell
   $cluster = Get-Cluster -Name VN1-CLST2
   $cluster.ResiliencyDefaultPeriod = 10 
   ````

### Task 6: Create virtual disks and volumes

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, in the context-menu of **Failover Cluster Manager**, click **Connect to a Cluster...**
1. In Select Cluster, click **Browse...**
1. In Browse for Clusters, click **Vn1-Clst2** and click **OK**.
1. In **Select Cluster**, click **OK**.
1. In **Failover Cluster Manager**, expand **VN1-CLST2.ad.adatum.com**, **Storage**, and click **Pools**.
1. Under Pools (1), in the context-menu of **Cluster Pool 1**, click **New Virtual Disk**.
1. In Select the storage pool, click **S2D on VN1-CLST2** and click **OK**.
1. In new Virtual Disk Wizard, on page Before You Begin, click **Next >**.
1. On page Virtual Disk Name, in **Name**, type **SOFS disk** and click **Next >**.
1. On page Size, under **Performance Tier**, in **Specify size**, type **20** and ensure **GB** is selected. Under **Capacity Tier**, in **Specify size**, type **60** and ensure **GB** is selected. Click **Next >**
1. On page Confirmation, click **Create**.
1. On page Results, ensure **Create a volume when this wizard closes** is activated and click **Close**.
1. In **New Volume Wizard**, on page Before You Begin, click **Next >**.
1. On page Server and Disk, under **Server**, ensure **vn1-clst2** is selected. Under Disk, ensure the **Virtual Disk** **SOFS disk** is selected. Click **Next >**.
1. On page Size, click **Next >**.
1. On page Drive Letter or Folder, ensure, beside **Drive letter**, **D** is selected and click **Next >**.
1. On page File System settings, beside **File System**, ensure **ReFS** is selected. In **Volume label**, type **Hyper-V Data**. Click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.
1. In **Failover Cluster Manager** under **Pools (1)**, click **Cluster Pool 1**.
1. In the bottom pane, click the tab **Virtual Disks**.
1. On the tab Virtual Disks, expand **Cluster Virtual Disk (SOFS disk)**.

   > The new volume is displayed.

1. Under Pools (1), in the context-menu of **Cluster Pool 1**, click **New Virtual Disk**.
1. In Select the storage pool, click **S2D on VN1-CLST2** and click **OK**.
1. In new Virtual Disk Wizard, on page Before You Begin, click **Next >**.
1. On page Virtual Disk Name, in **Name**, type **Hyper-converged disk** and click **Next >**.
1. On page Size, under **Performance Tier**, in **Specify size**, type **40** and ensure **GB** is selected. Under **Capacity Tier**, in **Specify size**, type **120** and ensure **GB** is selected. Click **Next >**
1. On page Confirmation, click **Create**.
1. On page Results, ensure **Create a volume when this wizard closes** is activated and click **Close**.
1. In **New Volume Wizard**, on page Before You Begin, click **Next >**.
1. On page Server and Disk, under **Server**, ensure **vn1-clst2** is selected. Under Disk, ensure the **Virtual Disk** **Hyper-converged disk** is selected. Click **Next >**.
1. On page Size, click **Next >**.
1. On page Drive Letter or Folder, ensure, beside **Drive letter**, **E** is selected and click **Next >**.
1. On page File System settings, beside **File System**, ensure **ReFS** is selected. In **Volume label**, type **Hyper-converged Data**. Click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.

### Task 7: Add disks as Cluster Shared Volume

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST2.ad.adatum.com**, **Storage**, and click **Disks**.
1. Under Disks (3), in the context-menu of **Cluster Virtual Disk (Hyper-converged disk)** click **Add to Cluster Shared Volumes**. Repeat for the **Cluster Virtual Disk (SOFS disk)**.

## Exercise 2: Using a Scale-Out File Server with Hyper-V

1. [Install the File Server role](#task-1-install-the-file-server-role) on VN1-SRV6, VN1-SRV7, VN1-SRV8, and VN1-SRV9
1. [Configure the Scale-Out File Server role](#task-2-configure-the-scale-out-file-server-role) on VN1-CLST2 with the client access point VN1-CLST2-SOFS
1. [Create a group](#task-3-create-a-group) in Active Directory with VN1-SRV4 and VN1-SRV5 as members
1. [Create a share](#task-4-create-a-share) on VN1-CLST2-SOFS for Hyper-V Data granting the group from the previous task full control
1. [Determine the owner node of the virtual machine](#task-5-determine-the-owner-node-of-the-virtual-machine) VN1-SRV23
1. [Move virtual machine data to the SOFS share](#task-6-move-virtual-machine-data-to-the-sofs-share)
1. [Verify move of the virtual machine](#task-7-verify-move-of-the-virtual-machine)

If time permits, change the virtual machine path and virtual hard disk path on VN1-SRV4 and VN1-SRV5 to the SOFS share. The next step would be the removal of the CSV for Hyper-V from VN1-CLST1.

### Task 1: Install the File Server role

It is recommended to use PowerShell for this task.

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Features**.
1. In Add Roles and Features Wizard, on page Before You Begin, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-based installation** is selected and click **Next >**.
1. On page Server Selection, click **VN1-SRV6.ad.adatum.com** and click **Next >**.
1. On page Server Roles, expand **File and Storage Services (1 of 12 installed)**, **File and iSCSI Services**, and activate **File Server** and click **Next >**.
1. On page Features, click **Next >**.
1. On page **Confirmation**, click **Install**.
1. On page **Results** and click **Close**.

Repeat from step 2 for VN1-SRV7, VN1-SRV8, and VN1-SRV9

#### PowerShell

Peform this task on CL1.

1. In the context menu of **Start**, click **Terminal**.
1. Install the windows feature **File Server** on **VN1-SRV10**.

    ````powershell
   Invoke-Command `
         -ComputerName VN1-SRV6, VN1-SRV7, VN1-SRV8, VN1-SRV9 `
         -ScriptBlock {
         Install-WindowsFeature `
               -Name FS-FileServer `
               -IncludeManagementTools `
               -Restart
      }
   ````

### Task 2: Configure the Scale-Out File Server role

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST2.ad.adatum.com**, **Storage**, and click **Roles**.
1. In the context-menu of **Roles**, click **Configure Role...**
1. In High Availability Wizard, on page Before You Begin, click **Next >**.
1. On page Server Role, click **File Server** and click **Next >**.
1. On page File Server Type, click **Scale-out File Server for application data** and click **Next >**.
1. On page Client Access Point, in **Name**, type **VN1-CLST2-SOFS** and click **Next >**.
1. On page Confirmation, click **Next >**.
1. On page Summary, click **Finish**.

### Task 3: Create a group

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **ad (local)**.
1. In the context-menu of **ad (local)**, click **New**, **Organizational Unit**.
1. In Create Organizational Unit, in **Name**, type **Entitling groups** and click **OK**.
1. In **Active Directory Administrative Center**, in **ad (local)**, double-click **Entitling groups**
1. In Entitling groups, in the pane **Tasks**, click **New**, **Group**.
1. In Create Group, in **Group name**, enter **Hyper-V Data Full Control**. Under **Group type**, ensure **Security** is selected. Under **Group scope**, click **Domain local**. On the left, click **Members**. Under Members, click **Add...**
1. On the left, click **Members**.
1. Under Members, click **Add...**
1. In Select Groups, Contacts, Computers, Service Accounts, or Groups, click **Object Types...**
1. In Object Types, activate **Computers** and click **OK**.
1. In **Select Groups, Contacts, Computers, Service Accounts, or Groups**, in **Enter the object names to select**, type **VN1-SRV4; VN1-SRV5** and click **OK**.
1. In **Create Group: Hyper-V Data Full Control**, click OK.

### Task 4: Create a share

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST2.ad.adatum.com** and click **Roles**.
1. Under Roles (2), in the context-menu of **VN1-CLST2-SOFS**, click **Add File Share**.
1. In New Share Wizard, on page Select Profile, ensure **SMB Share - Quick** is selected and click **Next >**.
1. On page Share Location, under **Server**, click **VN1-CLST2-SOFS**. Under **Share location**, click **Select by volume**. Under **Select by volume**, click **C:\ClusterStorage\SOFS disk**. Click **Next >**.
1. On page Share Name, in **Share name**, type **Hyper-V Data** and click **Next >**.
1. On page Configure share settings, configure these settings and click **Next >**:

   * **Enable access-based enumeration**: activate
   * **Enable continuous availability**: activate
   * **Allow caching of share**: deactivate
   * **Encrypt data access**: activate

1. On page Permissions, click **Customize permissions...**
1. In Advanced Security Settings for Hyper-V Data, on tab Permissions, click **Disable Inheritance**.
1. In Block inheritance, click **Convert inherited permisssions into explicit permissions on this object.**
1. In **Advanced Security Settings for Witness Hyper-V Data**, on tab **Permissions**, click one of the entries **Users** and click **Remove**. Repeat for the other entries other than **Administrators**, **SYSTEM**, and **CREATOR Owner**, e.g., **Everyone**
1. Click **Add**.
1. In Permissions Entry for Hyper-V Data, click **Select a principal**.
1. In Select User, Computer, Service Account or Group, under **Enter the object name to select**, type **Hyper-V Data Full Control** and click **OK**.
1. In **Permissions Entry for Witness Hyper-V Data**, activate **Full Control** and click **OK**.
1. In **Advanced Security Settings for Witness Hyper-V Data**, click the tab **Share**.
1. On the tab Share, click **Everyone** and click **Remove**.
1. Click **Add**.
1. In Permissions Entry for Witness Hyper-V Data, click **Select a principal**.
1. In Select User, Computer, Service Account or Group, under **Enter the object name to select**, type **Hyper-V Data Full Control** and click **OK**.
1. In **Permissions Entry for Witness Hyper-V Data**, activate **Full Control** and click **OK**.
1. In **Advanced Security Settings for Witness Hyper-V Data**, on tab **Share**, click **Add**.
1. In Permissions Entry for Witness Hyper-V Data, click **Select a principal**.
1. In Select User, Computer, Service Account or Group, click **Locations...**
1. In Locations, click **vn1-clst2-sofs.ad.adatum.com** and click **OK**.
1. In **Select User, Computer, Service Account or Group**, under **Enter the object name to select**, type **Administrators** and click **OK**.
1. In **Permissions Entry for Witness Hyper-V Data**, activate **Full Control** and click **OK**.
1. In **Advanced Security Settings for Witness Hyper-V Data**, click **OK**.
1. In **New Share Wizard**, on page **Permissions**, click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.

### Task 5: Determine the owner node of the virtual machine

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST1.ad.adatum.com** and click **Roles**. For **VN1-SRV23**, take a note of the **Owner Node**.

### Task 6: Move virtual machine data to the SOFS share

Perform this task on the owner node of VN1-SRV23.

1. Sign in as **ad\Administrator**.
1. In SConfig, enter **15**.
1. Move the data of VN1-SRV23 to **\\\\VN1-CLST2-SOFS\\Hyper-V Data\\**

   ````powershell
   Move-VMStorage `
      -Name VN1-SRV23 `
      -DestinationStoragePath '\\vn1-clst2-sofs2\Hyper-V Data'
   ````

### Task 7: Verify move of the virtual machine

Perform this task on CL1.

1. In **File Explorer** navigate to **\\\\vn1-clst2-sofs2\\Hyper-V Data** and browse through the folders.

   > The following folders are in the share:
   >
   >  * Snapshots
   >  * UndoLog Configuration
   >  * Virtual Hard Disks
   >  * Virtual Machines
   >
   > The folder **Virtual Hard Disks** contains **vn1-srv23.vhdx**. The folder **Virtual Machines** contains a set of files related to the virtual machine.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST1.ad.adatum.com** and click **Roles**.
1. Under Roles (3), in the context-menu of **VN1-SRV23**, click **Move**, **Live Migration**, **Select Node...**.
1. In Move Virtual Machine, click **OK**.

   > The virtual machine moves to the other node gracefully.

## Exercise 3: Creating a hyper-converged virtualization cluster

1. [Configure nested virtualization](#task-1-configure-nested-virtualization) for WIN-VN1-SRV6, WIN-VN1-SRV7, WIN-VN1-SRV8, and WIN-VN1-SRV9
1. [Install Hyper-V](#task-2-install-hyper-v) on WIN-VN1-SRV6, WIN-VN1-SRV7, WIN-VN1-SRV8, and WIN-VN1-SRV9
1. [Configure a virtual switch](#task-3-configure-a-virtual-switch) connected to the external network adapter on WIN-VN1-SRV6, WIN-VN1-SRV7, WIN-VN1-SRV8, and WIN-VN1-SRV9
1. [Create a virtual machine](#task-4-create-a-virtual-machine) on the cluster with 1 GB RAM using a copy of the VHDX file for Windows Server 2022 Core
1. [Configure Windows Server](#task-5-configure-windows-server) on the virtual machine with the IP address 10.1.1.192

### Task 1: Configure nested virtualization

Perform this task on the host.

1. Open **Windows PowerShell (Admin)**.
1. In Windows PowerShell (Admin), for **WIN-VN1-SRV6**, **WIN-VN1-SRV7**, **WIN-VN1-SRV8**, and **WIN-VN1-SRV9**, shut down the virtual machine, expose the virtualtion extensions to the virtual machine, enable MAC address spoofing, disable dynamic memory and set the startup memory to **3 GB**, and start the virtual machine again. Make sure all cluster services running keep on running.

    ````powershell
    $vMName = @('WIN-VN1-SRV6', 'WIN-VN1-SRV7', 'WIN-VN1-SRV8', 'WIN-VN1-SRV9')
    $vMName | ForEach-Object { 
      Stop-VM -VMName $PSItem
      Set-VMProcessor -VMName $PSItem -ExposeVirtualizationExtensions $true
      
      Get-VMNetworkAdapter -VMName $PSItem |
      Set-VMNetworkAdapter -MacAddressSpoofing On

      Set-VM -VMName $PSItem -StaticMemory -MemoryStartupBytes 3GB
      Start-VM -VMName $PSItem
   }
    ````

### Task 2: Install Hyper-V

#### Desktop Experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on page **Before You Begin**, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-base installation** is selected and click **Next >**.
1. On page Server Selection, click **VN1-SRV6.ad.adatum.com** and click **Next >**.
1. On page Server Roles, activate **Hyper-V**
1. In Add features that are required for Hyper-V, click **Add Features**.
1. In **Add Roles and Features Wizard**, on page **Server Roles**, click **Next >**.
1. On page Features, click **Next >**.
1. On page Hyper-V, click **Next >**.
1. On page Virtual Switches, click **Next >**.
1. On page Virtual Machine Migration, click **Next >**.
1. On page Default Stores, in **Default location for virtual hard disk files**, type **C:\\ClusterStorage\\Hyper-converged Disk\\Hyper-V\\Virtual Hard Disks**, where x is the volume number you recorded for the 80 GB disk in the previous exercise. In **Default location for virtualmachine configuration files**, type **C:\\ClusterStorage\\Hyper-converged Disk\\Hyper-V**, where x is the volume number you recorded for the 80 GB disk in the previous exercise. Click **Next >**.
1. On page Confirmation, activate **Restart the destination server automatically if required** and click **Install**.
1. On  page **Results**, wait for the installation to succeed, then click **Close**.

Repeat the steps of this task to install the role on **VN1-SRV6**, **VN1-SRV7**, **VN1-SRV8**, and **VN1-SRV9**

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install **Hyper-V** on **VN1-SRV6**, **VN1-SRV7**, **VN1-SRV8**, and **VN1-SRV9** without restarting them.

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

### Task 3: Configure a virtual switch

Perform this task on CL1.

1. Open **Terminal**.
1. On **WIN-VN1-SRV6**, **WIN-VN1-SRV7**, **WIN-VN1-SRV8**, and **WIN-VN1-SRV9**, create an external switch connected to the **VNet1** network adapter. Allow the management OS to use the adapter.

   ````powershell
   $computerName = @('VN1-SRV6', 'VN1-SRV7', 'VN1-SRV8', 'VN1-SRV9')
   New-VMSwitch `
      -ComputerName $computerName `
      -Name External `
      -NetAdapterName VNet1 `
      -AllowManagementOS $true
   ````

### Task 4: Create a virtual machine

Perform this task on CL1.

1. Open **File Explorer**.
1. In File Explorer, copy **\\\\VN1-SRV6\\C$\\LabResources\\2022_x64_Datacenter_EN_Core_Eval.vhdx** to **\\\\VN1-SRV6\\C$\\ClusterStorage\\Hyper-converged disk\\Hyper-V\\Virtual Hard Disks**. Replace x with the volume number of the 80 GB disk.
1. Rename **\\\\VN1-SRV4\\C$\\ClusterStorage\\Hyper-converged disk\\Hyper-V\\Virtual Hard Disks\\2022_x64_Datacenter_EN_Core_Eval.vhdx** to **VN1-SRV24.vhdx**
1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST2.ad.adatum.com** and click **Roles**.
1. In **Failover Cluster Manager**, in the context-menu of **Roles**, click **Virtual Machines...**, **New Virtual Machine...**.
1. In New Virtual Machine, click **VN1-SRV6** and click **OK**.
1. In New Virtual Machine Wizard, on page Before You Begin, click **Next >**.
1. On page Specify Name and Location, in **Name**, type **VN1-SRV24** and click **Next >**.
1. On page Specify Generation, click **Generation 2** and click **Next >**.
1. On page Assign Memory, in **Startup memory**, type **1024** and click **Next >**.
1. On page Configure Networking, in **Connection**, click **External** and click **Next >**.
1. On page Connect Virtual Hard Disk, click **Use an existing virtual hard disk** and click **Browse...**.
1. In Open, click **VN1-SRV24.vhdx** and click **Open**.
1. In **New Virtual Machine Wizard**, on page **Connect Virtual Hard Disk**, click **Next >**.
1. On page Completing the New Virtual Machine Wizard, click **Finish**.
1. In the **High Availability Wizard**, on page **Summary**, click **Finish**.
1. In **Failover Cluster Manager**, under **Roles (1)**, in the context-menu of **VN1-SRV23**, click **Start**.

### Task 5: Configure Windows Server

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST1.ad.adatum.com** and click **Roles**.
1. Under Roles (1), in the context-menu of **VN1-SRV23**, click **Connect...**.
1. In VN1-SRV24 on VN1-SRV4 - Virtual Machine Connection, at the prompt **The user's password must be changed before signing in.**, select **OK**.
1. In **New password** and **Confirm password**, enter a secure password.
1. At the prompt **Your password has been changed**, press **ENTER**.
1. In SConfig, enter **8**.
1. In Network settings, enter  **1**.
1. In Network adapter settings, enter **1**.
1. Beside **Select (D)HCP or (S)tatic IP address (Blank=Canel)**, enter **S**.
1. Beside **Enter static IP address (Blank=Cancel)**, enter **10.1.1.192**.
1. Beside **Enter subnet mask (Blank=255.255.255.0)**, press ENTER.
1. Beside **Enter default gateway (Blank=Cancel)**, enter **10.1.1.1**.
1. Under 4 success messages, press ENTER.
1. In **SConfig**, enter **15**.
1. Set the keyboard language, e.g., to German. You may replace the BCP 47 language tag of German by the language of your keyboard.

    ````powershell
    Set-WinUserLanguageList -LanguageList DE-DE 
    ````

1. At the prompt continue with this operation, enter **Y**.
1. Open Region.

    ````powershell
    intl.cpl
    ````

1. In Region, under **Format**, click the format of your region.
1. Click the tab **Administrative**.
1. On tab Administrative, click **Copy settings...**.
1. In the dialog Change Regional Options, click **Apply**.
1. In Welcome screen and new user account settings, activate the checkboxes **Welcome screen and system accounts** and **New user accounts**, and click **OK**.
1. In **Region** click **OK**.
1. Enable ICMP Echo requests on the firewall.

    ````powershell
    Enable-NetFirewallRule -Name 'CoreNet-Diag-ICMP4-EchoRequest-In'
    ````

## Exercise 4: Testing the resiliency of the hyper-converged virtualization

1. [Run monitoring tasks against the virtual machine](#task-1-run-monitoring-tasks-against-the-virtual-machine)
1. [Simulate a failure of nodes](#task-2-simulate-a-failure-of-nodes) owning the virtual machine

   > How many nodes can fail until the virtual machine finally fails?

1. [Examine the cause of the virtual machine failure](#task-3-examine-the-cause-of-the-virtual-machine-failure)
1. [Recover from failure](#task-4-recover-from-failure)
1. [Verify the recovery](#task-5-verify-the-recovery)

### Task 1: Run monitoring tasks against the virtual machine

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST1.ad.adatum.com** and click **Roles**. Take a note of the **Owner Node** of **VN1-SRV24**.
1. Under Roles (1), in the context-menu of **VN1-SRV24**, click **Connect...**.
1. Open **Terminal**.
1. In Terminal, test the connection to 10.1.1.192 or a long period.

    ````powershell
    Test-Connection -ComputerName 10.1.1.192 -Count 1000
    ````

    > You should get a response every few seconds.

1. Arrange the **Failover Cluster Manager**, **Terminal**, and **VN1-SRV24 on VN1-SRVx - Virtual Machine Connection** in a way, so that you can monitor them while continuing with the next task.

### Task 2: Simulate a failure of nodes

Perform this task on the host.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click your computer name.
1. Under Virtual Machines, in the context-menu of the Virtual Machines corresponding to the current owner of VN1-SRV24, click **Turn off...**.

   > After a few seconds, virtual machine should be running again on a different node and the connection test should continue successfully.

   Take a note of the new **Owner Node**.

1. In **Hyper-V Manager**, under **Virtual Machines**, in the context-menu of the Virtual Machines corresponding to the current owner of VN1-SRV24, click **Turn off...**.

   > After a few seconds, virtual machine should be running again on a different node and the connection test should continue successfully.

   Take a note of the new **Owner Node**.

1. In **Hyper-V Manager**, under **Virtual Machines**, in the context-menu of the Virtual Machines corresponding to the current owner of VN1-SRV24, click **Turn off...**.

   > While the virtual machine shows a **Status** of Running, the connection test keeps failing. After a few minutes, the status will change to **Failed**.

### Task 3: Examine the cause of the virtual machine failure

Perform this task on CL1.

1. In **Terminal**, press CTRL + C.
1. Close **VN1-SRV24 on VN1-SRVx - Virtual Machine Connection**.
1. In **Failover Cluster Manager**, expand **VN1-CLST2.ad.adatum.com**, **Storage** and click **Pools** and click **Cluster Pool 1**.

   > The **Health Status** is displayed as **Unknown**.

1. Under **Storage**, click **Disks**.

   > The **Health Status** of all disks is displayed as **Unknown**.

### Task 4: Recover from failure

Perform this task on the host.

In **Hyper-V-Manager**, in the context menu of the virtual machines **WIN-VN1-SRV6**, **WIN-VN1-SRV7**, **WIN-VN1-SRV8**, and **WIN-VN1-SRV9**, which are turned off, click **Start**.

### Task 5: Verify the recovery

Perform this task on CL1.

1. In **Failover Cluster Manager**, expand **VN1-CLST2.ad.adatum.com** and click **Nodes**. Wait, until all nodes show the **Status** of **Up**.
1. Under **Storage**, click **Disks**.

   > All disks are online again.

1. Click **Roles**.
1. In Roles (2), in the context-menu of **VN1-SRV24** click **Start**.

   > The virtual machine should start and run again.

## Exercise 5: Shut down the Storage Spaces Direct cluster

1. [Shut down the clustered virtual machines](#task-1-shut-down-the-clustered-virtual-machines) on VN1-CLST1 and VN1-CLST2
1. [Shut down the nodes](#task-2-shut-down-the-nodes) VN1-SRV6, VN1-SRV7, VN1-SRV8, and VN1-SRV9

### Task 1: Shut down the clustered virtual machines

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN1-CLST1.ad.adatum.com** and click **Roles**.
1. Under Roles (3), in the context-menu of **VN1-SRV23**, cick **Shut Down**.
1. Expand **VN1-CLST2.ad.adatum.com** and click **Roles**.
1. Under Roles (2), in the context-menu of **VN1-SRV24**, cick **Shut Down**.

### Task 2: Shut down the nodes

Perform this task on the host.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click the name of your computer.
1. Under Virtual Machines, in the context menu of **VN1-SRV6**, click **Shut down...**. Wait for the computer to turn off. Repeat for **VN1-SRV7**, **VN1-SRV8**, and **VN1-SRV9**.