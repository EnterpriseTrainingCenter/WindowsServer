# Lab: Storage Replica and stretched cluster

## Required VMs

* VN1-SRV1
* VN1-SRV4
* VN1-SRV5
* VN1-SRV10
* VN2-SRV1
* VN3-SRV1
* CL1

## Setup

1. On CL1, sign in as **ad\Administrator**.
2. On VN2-SRV1, sign in as **ad\Administrator**.

## Known issues

<https://github.com/EnterpriseTrainingCenter/WindowsServer/issues/108>

## Exercises

1. [Testing Storage Replica storage](#exercise-1-testing-storage-replica-storage)
1. [Create a stretched Hyper-V cluster](#exercise-2-create-a-stretched-hyper-v-cluster)
1. [Test stretched Hyper-V cluster failover](#exercise-3-test-stretched-hyper-v-cluster-failover)

## Exercise 1: Configure storage

### Task 1: Configure nested virtualization

Perform this task on the host.

1. Open **Windows PowerShell (Admin)**.
1. In Windows PowerShell (Admin), shut down **WIN-VN2-SRV1** and **WIN-VN3-SRV1**.

    ````powershell
    $vMName = @('WIN-VN2-SRV1', 'WIN-VN3-SRV1')
    $vMName | ForEach-Object { Stop-VM -VMName $PSItem }
    ````

1. Expose virtualization extensions.

    ````powershell
    Set-VMProcessor -VMName $vMName -ExposeVirtualizationExtensions $true
    ````

1. Enable MAC address spoofing.

    ````powershell
    Get-VMNetworkAdapter -VMName $vMName |
    Set-VMNetworkAdapter -MacAddressSpoofing On
    ````

1. Disable dynamic memory and set the startup memory to 4 GB.

    ````powershell
    Set-VM -VMName $vMName -StaticMemory -MemoryStartupBytes 4GB
    ````

1. Start the virtual machines.

    ````powershell
    Start-VM -VMName $vMName
    ````

### Task 2: Configure iSCSI targets and disks

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the left pange, click **File and Storage Services**.
1. In File and Storage Services, click **iSCSI**.
1. In iSCSI, in the right pane, in the drop-down **TASKS**, click **New iSCSI Virtual Disk...**.
1. In New iSCSI Virtual Disk Wizard, on page iSCSI Virtual Disk Location, under **Server**, click **VN1-SRV10**. Under **Storage location**, click **D:**. Click **Next >**.
1. On page Specify iSCSI virtual disk name, in **Name**, type **VN2-CLST1-Data** and click **Next >**.
1. On page Specify iSCSI virtual disk size, in **Size**, type **100** and ensure **GB** is selected. Ensure, **Dynamically expanding** is selected and click **Next >**.
1. On page Assign iSCSI target, click **New iSCSI target** and click **Next >**.
1. On page Specify target name, in **Name**, type **VN2-CLST1** and click **Next >**.
1. On page Specify access servers, click **Add...**.
1. In Add initiator ID, ensure **Query initiator computer for ID** is selected, type **VN2-SRV1** and click **OK**.
1. In **New iSCSI Virtual Disk Wizard**, on page **Specify access servers**, click **Add...**.
1. In Add initiator ID, ensure **Query initiator computer for ID** is selected and click **Browse...**.
1. In Select Computer, under **Enter the object name to select**, type **VN1-SRV5** and click **OK**.
1. In **Add initiator ID**, click **OK**.
1. In **New iSCSI Virtual Disk Wizard**, on page **Specify access servers**, click **Next >**.
1. On page Enable Authentication, click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.
1. In **Server Manager**, in **iSCSI**, in the right pane, in the drop-down **TASKS**, click **New iSCSI Virtual Disk...**.
1. In New iSCSI Virtual Disk Wizard, on page iSCSI Virtual Disk Location, under **Server**, click **VN1-SRV10**. Under **Storage location**, click **D:**. Click **Next >**.
1. On page Specify iSCSI virtual disk name, in **Name**, type **VN3-CLST1-Log** and click **Next >**.
1. On page Specify iSCSI virtual disk size, in **Size**, type **10** and ensure **GB** is selected. Ensure, **Dynamically expanding** is selected and click **Next >**.
1. On page Assign iSCSI target, ensure **Existing iSCSI target** is selected and click **vn2-clst1**. Click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.
1. In **Server Manager**, in **iSCSI**, in the right pane, in the drop-down **TASKS**, click **New iSCSI Virtual Disk...**.
1. In New iSCSI Virtual Disk Wizard, on page iSCSI Virtual Disk Location, under **Server**, click **VN1-SRV10**. Under **Storage location**, click **D:**. Click **Next >**.
1. On page Specify iSCSI virtual disk name, in **Name**, type **VN3-CLST1-Data** and click **Next >**.
1. On page Specify iSCSI virtual disk size, in **Size**, type **100** and ensure **GB** is selected. Ensure, **Dynamically expanding** is selected and click **Next >**.
1. On page Assign iSCSI target, click **New iSCSI target** and click **Next >**.
1. On page Specify target name, in **Name**, type **VN3-CLST1** and click **Next >**.
1. On page Specify access servers, click **Add...**.
1. In Add initiator ID, ensure **Query initiator computer for ID** is selected, type **VN3-SRV1** and click **OK**.
1. In **New iSCSI Virtual Disk Wizard**, on page **Specify access servers**, click **Add...**.
1. In Add initiator ID, ensure **Query initiator computer for ID** is selected and click **Browse...**.
1. In Select Computer, under **Enter the object name to select**, type **VN1-SRV5** and click **OK**.
1. In **Add initiator ID**, click **OK**.
1. In **New iSCSI Virtual Disk Wizard**, on page **Specify access servers**, click **Next >**.
1. On page Enable Authentication, click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.
1. In **Server Manager**, in **iSCSI**, in the right pane, in the drop-down **TASKS**, click **New iSCSI Virtual Disk...**.
1. In New iSCSI Virtual Disk Wizard, on page iSCSI Virtual Disk Location, under **Server**, click **VN1-SRV10**. Under **Storage location**, click **D:**. Click **Next >**.
1. On page Specify iSCSI virtual disk name, in **Name**, type **VN3-CLST1-Log** and click **Next >**.
1. On page Specify iSCSI virtual disk size, in **Size**, type **10** and ensure **GB** is selected. Ensure, **Dynamically expanding** is selected and click **Next >**.
1. On page Assign iSCSI target, ensure **Existing iSCSI target** is selected and click **vn3-clst1**. Click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.

#### PowerShell

Perform these steps on CL1.

1. Open **Terminal**.
1. In the new directory, create new iSCSI virtual disks.

   | File name             | Size   |
   |-----------------------|--------|
   | VN2-CLST1-Data.vhdx   | 100 GB |
   | VN2-CLST1-Log.vhdx    | 10 GB  |
   | VN3-CLST1-Data.vhdx   | 100 GB |
   | VN3-CLST1-Log.vhdx    | 10 GB  |

   ````powershell
   $path = "$($driveLetter):\$name"
   $diskParams = @(
      @{ Path = "$path\VN2-CLST1-Data.vhdx"; SizeBytes = 100GB }
      @{ Path = "$path\VN2-CLST1-Log.vhdx"; SizeBytes = 10GB }
      @{ Path = "$path\VN3-CLST1-Data.vhdx"; SizeBytes = 10GB }
      @{ Path = "$path\VN3-CLST1-Log.vhdx"; SizeBytes = 10GB }
   )
   $iscsiVirtualDisk = $diskParams | ForEach-Object {
      New-IscsiVirtualDisk `
         -Path $PSItem.Path `
         -SizeBytes $PSItem.SizeBytes `
         -ComputerName $computerName 
   }
   ````

1. On **VN1-SRV10**, Create a new iSCSI server targets with the names **VN2-CLST1** and **VN3-CLST1** for the initiators on **VN2-SRV1** and **VN3-SRV1**.

   ````powershell
   $initiatorIdPrefix = 'IQN:iqn.1991-05.com.microsoft:'
   New-IscsiServerTarget `
      -TargetName "$initatorIdPrefix.VN2-CLST1 `
      -InitiatorIds "VN2-SRV1" `
      -ComputerName $computerName
   New-IscsiServerTarget `
      -TargetName "$initatorIdPrefix.VN3-CLST1 `
      -InitiatorIds "VN3-SRV1" `
      -ComputerName $computerName
   ````

1. Add the iSCSI virtual disks to the iSCSI target.

   ````powershell
   $diskParams | Where-Object { $PSItem -like "$path\VN2-*" } | ForEach-Object { 
      Add-IscsiVirtualDiskTargetMapping `
         -TargetName "$initiatorIdPrefix.VN2-CLST1" `
         -Path $PSItem.Path `
         -ComputerName $computerName 
   }
   $diskParams | Where-Object { $PSItem -like "$path\VN3-*" } | ForEach-Object { 
      Add-IscsiVirtualDiskTargetMapping `
         -TargetName "$initiatorIdPrefix.VN3-CLST1" `
         -Path $PSItem.Path `
         -ComputerName $computerName 
   }
   ````

### Task 3: Connect to iSCSI targets

Perform this task on VN2-SRV1 and VN3-SRV1.

1. Open **iSCSI Initiator**.
1. In message box **Microsoft iSCSI**, click **Yes**.
1. In iSCSI Initiator Properties, in **Target**, type **VN1-SRV10** and click **Quick Connect...**
1. In Quick Connect, click **Done**.
1. In **iSCSI Initiator Properties**, click **OK**.

### Task 4: Create volumes

<!-- #### Desktop Experience
 -->
Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click **File and Storage Services**.
1. Under File and Storage Services, click **Disks**.
1. In Disks, under **VN2-SRV1**, in the context menu of the disk with a **Capacity** of **100 GB**, attached to **Bus Type** **iSCSI**, click **Bring online**.
1. In Bring Disk Online, click **Yes**.
1. In **Server Manager**, under **Disks**, in the context-menu the Disk of the disk with a **Capacity** of **100 GB**, attached to **Bus Type** **iSCSI**, click **New Volume...**.
1. In New Volume Wizard, on page Before You Begin, click **Next >**.
1. On page Server and Disk, ensure **VN2-SRV1** and a disk with **Capacity** of **100 GB** are selected and click **Next >**.
1. On page size, in **Volume size**, ensure **100,0** is filled in and **GB** is selected. Click **Next >**.
1. On page Drive Letter or Folder, ensure **Drive letter** and **D** is selected and click **Next >**.
1. On page File System Settings, in **File System**, click **ReFS**. In **Volume label**, type **Data**. Click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.
1. In **Server Manager**, under **Disks**, under **VN2-SRV1**, in the context menu of the disk with a **Capacity** of **10,0 GB**, attached to **Bus Type** **iSCSI**, click **Bring online**.
1. In Bring Disk Online, click **Yes**.
1. In **Server Manager**, under **Disks**, in the context-menu of the disk with a **Capacity** of **10,0 GB**, attached to **Bus Type** **iSCSI**, click **New Volume...**.
1. In New Volume Wizard, on page Before You Begin, click **Next >**.
1. On page Server and Disk, ensure **VN2-SRV1** and a disk with **Capacity** of **10,0 GB** are selected and click **Next >**.
1. On page size, in **Volume size**, ensure **9,98** is filled in and **GB** is selected. Click **Next >**.
1. On page Drive Letter or Folder, ensure **Drive letter** and **E** is selected and click **Next >**.
1. On page File System Settings, in **File System**, click **ReFS**. In **Volume label**, type **Data**. Click **Next >**.
1. On page Confirmation, click **Create**.
1. On page Results, click **Close**.

Repeat from step 4 for VN3-SRV1.

<!-- #### Windows Admin Center

Perform these steps on CL1.

1. Logon as **smart\administrator**
1. Open **Google Chrome**, and navigate to <https://admincenter.smart.etc> to open the Windows Admin Center Management Portal.
1. Connect to **sr1.smart.etc**.
1. Navigate to **Storage**.
1. Select **Disk 1** and click **Initialize disk** ([figure 1]). Initialize it as GPT Disk.
1. Repeat the previous step for **Disk 2**.
1. Create ReFS formatted volumes using the maximum size. Refer to the table below to assign drive letters and labels ([figure 2] and [figure 3]).

   | Disk size | Drive letter | Label |
   |-----------|--------------|-------|
   | 36 GB     | D            | Data  |
   | 10 GB     | E            | Log   |

1. For server **SR2.smart.etc**, initialize the disks **Disk 1** and **Disk 2** as GPT disks. On the disks, create volume like in the previous step.
 -->
<!-- #### PowerShell

Perform these steps on CL1.

1. Logon as **smart\administrator**
1. Open **Windows PowerShell**.
1. Define a variable $node to run commands on both nodes.

   ````powershell
   # Variables can store comma-separated lists of values, i. e. arrays
   $node = 'SR1', 'SR2'
   ````

1. Create CIM sessions to both nodes, to run commands remotely.

   ````powershell
   $cimSession = New-CimSession -ComputerName $node
   ````

1. On SR1 and SR2, initialize disks as GPT disks.

   ````powershell
   # Parameters supporting arrays accept comma-separated lists
   # In this case Initialize-Disk accepts <uint32[]> for the parameter -Number
   # See Get-Help Initialize-Disk for more information.
   Initialize-Disk -CimSession $cimSession -Number 1, 2 
   ````

1. Create ReFS formatted volumes using the maximum size. Refer to the table below to assign drive letters and labels ([figure 2] and [figure 3]).

   | Number | Disk size | Drive letter | Label |
   |--------|-----------|--------------|-------|
   | 1      | 36 GB     | D            | Data  |
   | 2      | 10 GB     | E            | Log   |

   ````powershell
   <#
   New-Volume does not support multiple CimSessions. Therefore, $cimSession
   must be iterated using ForEach-Object. On each iteration, $PSItem contains
   one single CimSession object.
   #>
   $cimSession | ForEach-Object {
      New-Volume `
         -CimSession $PSItem `
         -DiskNumber 1 `
         -FriendlyName 'Data' `
         -FileSystem ReFS `
         -DriveLetter D
      New-Volume `
         -CimSession $PSItem `
         -DiskNumber 2 `
         -FriendlyName 'Log' `
         -FileSystem ReFS `
         -DriveLetter E
   }
   ````

1. Leave Windows PowerShell open for the next task. -->

## Exercise 2: Testing Storage Replica storage


   > According to the report, will Storage Replica work in this environment?

### Task 5: Install Storage Replica feature

<!-- You can skip this task when using Windows Admin Center in the next exercise. The necessary features are installed when using them.
 -->

#### Desktop Experience

Perform this task on CL1.

1. Open Server Manager.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on page **Before You Begin**, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-base installation** is selected and click **Next >**.
1. On page Server Selection, click **VN2-SRV1.ad.adatum.com** and click **Next >**.
1. On page Server Roles, click **Next >**.
1. On page Features, activate **Failover Clustering**.
1. In Add features that are required for Failover Clustering, click **Add Features**.
1. In **Add Roles and Features Wizard**, on page **Features**, activate **Storage Replica**.
1. In Add features that are required for Storage Replica, click **Add Features**.
1. In **Add Roles and Features Wizard**, on page **Features**, click **Next >**.
1. On page Confirmation, verify your selection and click **Install**.
1. On  page **Results**, wait for the installation to succeed, then click **Close**.

Repeat the steps of this task to install the role on **VN3-SRV1**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install **Failover Clustering** on **VN1-SRV4** and **VN1-SRV5**.

    ````powershell
    Invoke-Command -ComputerName VN2-SRV1, VN3-SRV1 -ScriptBlock {
        Install-WindowsFeature `
            -Name Failover-Clustering, Storage-Replica `
            -IncludeManagementTools `
            -Restart
    }
    ````

### Task 6: Run the Storage Replica test

Perform this task on VN2-SRV1.

1. Open **Settings**.
1. In Settings, click **Time & Languange**.
1. Under Time & Language, cick **Region**.
1. In Region, under **Regional Format**, click **English (United States)**

   Note: Due to a bug in Windows Server, the Storage Replica test cannot plot the results with regional settings other than English (United States).

1. Open **Window PowerShell (Admin)**.
1. Create a new directory **C:\Temp**.

   ````powershell
   $resultPath = 'C:\Temp'
   New-Item -Path $resultPath -ItemType Directory
   ````

1. Test the Storage Replica topology. Wait for the command to complete.

   ````powershell
   Test-SRTopology `
      -SourceComputerName VN2-SRV1 `
      -DestinationComputerName VN3-SRV1 `
      -SourceVolumeName D: `
      -SourceLogVolumeName E: `
      -DestinationVolumeName D: `
      -DestinationLogVolumeName E: `
      -DurationInMinutes 2 `
      -ResultPath $resultPath
   ````

   This will take 2 - 3 minutes.

### Task 7: Evaluate the Storage Replica test

Perform this task on CL1.

From **\\\VN2-SRV1\\C$\\Temp** open the report in a browser.

> Discuss the results of the report with other students in the class.

## Exercise 2: Create a stretched Hyper-V cluster

### Task 1: Add cluster nodes to group

Perform this task on CL1.

1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **ad (local)**.
1. In ad (local), double-click **Entitling groups**.
1. In Entitling groups, double-click **Witness VN1-CLST2 Modify**.
1. In Witness VN1-CLST Modify, click **Members**.
1. Under Members, click **Add...**.
1. In Select Users, Contacts, Computers, Services Accounts, or Groups, click **Object Types...**.
1. In Object Types, activate **Computers** and click **OK**.
1. In **Select Users, Contacts, Computers, Services Accounts, or Groups**, under **Enter the object names to select**, type **VN2-SRV1; VN3-SRV1** and click **OK**.
1. In **Witness VN1-CLST Modify**, click **OK**.

### Task 2: Create a failover cluster

<!-- #### Windows Admin Center

Perform these steps on CL1.

1. In **Windows Admin Center**, click on the gear icon to open settings.
1. In **Settings**, click **Extensions**.
1. When updates are available, install the updates for all extensions.
1. On the top-left, click **Windows Admin Center** to return to All connections.
1. In **All connections**, click **Add**.
1. In panel **Add or create resources**, under **Server clusters**, click **Create new**. Select the correct options and click **Create**.
   * **Choose the cluster type**: **Windows server**
   * **Select the workload type**: **Cluster-aware roles and apps**
   * **Select server locations**: **All servers in one site**
1. On **Check prerequisites**, click **Next**.
1. On **Add servers**, enter the credentials of **smart\Administrator**, then enter **sr1** and click **Add**.
1. Enter **sr2** and click **Add**, then click **Next**.
1. On **Join a domain**, click **Next**.
1. On **Install features**, click **Install features**, then click **Next**.
1. On **Install updates**, ignore any errors and click **Next**.
1. On **Restart servers**, click **Restart servers**, if required. Then, click **Next: Clustering**.
1. On **Validate the cluster**, click **Validate**.
1. In the message box **Credential Service Provider (CredSSP)**, click **Yes**.
1. Review the validation results and click **Next**.
1. On **Create the cluster**, enter the cluster parameters and click **Create cluster**.
   * **Cluster name**: SR
   * **IP address**: 10.1.1.83
1. After the cluster was created, click **Finish**.
1. Click **Go to the connections list**.
1. Open **Windows PowerShell**.
1. Configure the cluster quorum settings to use a file share witness using \\dhcp\SR-fsw.

   ````powershell
   $node = 'sr1', 'sr2'
   $clusterName = 'sr'
   $cluster = Get-Cluster -Name sr
   Set-ClusterQuorum -Cluster $cluster -FileShareWitness '\\Dhcp\SR-fsw'
   ````

1. Configure stretched cluster site awareness using PowerShell. Create two sites: primary and secondary.

   ````powershell
   <#
   CIM sessions are a way to run commands using the CIM interface on remote
   computers 
   #>
   $cimSession = New-CimSession -ComputerName $node
   
   New-ClusterFaultDomain -CimSession $cimSession[0] -Name Primary -Type Site
   New-ClusterFaultDomain -CimSession $cimSession[0] -Name Secondary -Type Site
   ````

1. Add the nodes to their sites.

   ````powershell
   Set-ClusterFaultDomain `
      -CimSession $cimSession[0] `
      -Name $node[0] `
      -Parent Primary
   Set-ClusterFaultDomain `
      -CimSession $cimSession[0] `
      -Name $node[1] `
      -Parent Secondary
   ````
 -->
<!-- #### PowerShell
 -->
Perform this task on VN2-SRV1.

1. Open **Windows PowerShell (Admin)**
1. Crfeate a new failover cluster with the name **VN2-VN3-CLST** and the IP address **10.1.2.9** and **10.1.3.9**. Include **VN2-SRV1** and **VN3-SRV1** as nodes.

   ````powershell
   $cluster = New-Cluster `
      -Name VN2-VN3-CLST1 `
      -Node VN2-SRV1, VN3-SRV1 `
      -StaticAddress 10.1.2.9, 10.1.3.9 `
      -NoStorage `
      -AdministrativeAccessPoint ActiveDirectoryAndDns
   ````

1. Configure the cluster quorum settings to use a file share witness using **\\\\vn1-clst1-fs\\Witness VN1-CLST2**.

   ````powershell
   Set-ClusterQuorum -FileShareWitness '\\vn1-clst1-fs\Witness VN1-CLST2'
   ````

1. Set the cluster resiliency period to **10** seconds.

   ````powershell
   $cluster.ResiliencyDefaultPeriod = 10
   ````

1. Read the cluster fault domains.

   ````powershell
   Get-ClusterFaultDomain
   ````

   Note the sites and childrens.

1. Set the preferred site **Site 10.1.2.0/24**.

   ````powershell
   $cluster.PreferredSite = 'Site 10.1.2.0/24'
   ````


1. Add all Disks to the cluster.

   ````powershell
   Get-ClusterAvailableDisk -All | Add-ClusterDisk
   ````

<!-- 1. Configure Kerberos Constrained Delegation to allow SSO from Windows Admin Center.

   ````powershell
   $gw = Get-ADComputer -Identity "srv2"
   Set-ADComputer $clusterName -PrincipalsAllowedToDelegateToAccount $gw 
   ````
 -->
### Task 2: Configure storage replica

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, in the context-menu of **Failover Cluster Manager**, click **Connect to Cluster...**
1. Select Cluster, type **VN2-VN3-CLST1.ad.adatum.com** and click **OK**.
1. In Failover Cluster Manager, expand **VN2-VN3-CLST1.ad.adatum.com**, **Storage**, and click **Disks**.
1. Under Disks (4), in the context menu of the disk with **Capacity** of **100 GB** with a **Status** of **Online**, click **Add to Cluster Shared Volumes**.

### Task 3: Configure storage replica

Perform this task on VN2-SRV1.

1. Open **Windows PowerShell (Admin)**.
1. In Windows PowerShell, read the cluster shared volume.

   ````powershell
   $clusterSharedVolume = Get-ClusterSharedVolume
   ````

1. Read the volume name from the cluster shared volume

   ````powershell
   $sourceVolumeName = $clusterSharedVolume.SharedVolumeInfo.FriendlyVolumeName
   ````

1. Enable the replication.

   ````powershell
   New-SRPartnership `
      -SourceComputerName VN2-SRV1 `
      -SourceRGName 'Replication 1' `
      -SourceVolumeName $sourceVolumeName `
      -SourceLogVolumeName E: `
      -DestinationComputerName VN3-SRV1 `
      -DestinationRGName 'Replication 2' `
      -DestinationVolumeName D: `
      -DestinationLogVolumeName E: `
      -ReplicationMode 'Synchronous' `
      -Force
   ````

1. Get the initial block copy state. Take a note of the DataVolume property of the first replica. Repeat the command until **ReplicationStatus** changes to **ContinouslyReplicating**.

   ````powershell
   (Get-SRGroup).Replicas
   ````

You can continue with the lab, but the failover will only succeed, when the **ReplicationStatus** changed to **ContinouslyReplicating**.

## Exercise 3: Test stretched Hyper-V cluster failover

### Task 1: Install Hyper-V

#### Desktop Experience

Perform this task on CL1.

1. Open Server Manager.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on page **Before You Begin**, click **Next >**.
1. On page Installation Type, ensure **Role-based or feature-base installation** is selected and click **Next >**.
1. On page Server Selection, click **VN2-SRV1.ad.adatum.com** and click **Next >**.
1. On page Server Roles, activate **Hyper-V**
1. In Add features that are required for Hyper-V, click **Add Features**.
1. In **Add Roles and Features Wizard**, on page **Server Roles**, click **Next >**.
1. On page Features, click **Next >**.
1. On page Hyper-V, click **Next >**.
1. On page Virtual Switches, click **Next >**.
1. On page Virtual Machine Migration, click **Next >**.
1. On page Default Stores, in **Default location for virtual hard disk files**, type **C:\\ClusterStorage\\Volume1\\Hyper-V\\Virtual Hard Disks**. In **Default location for virtualmachine configuration files**, type **C:\\ClusterStorage\\Volume1\\Hyper-V**. Click **Next >**.
1. On page Confirmation, activate **Restart the destination server automatically if required** and click **Install**.
1. On  page **Results**, wait for the installation to succeed, then click **Close**.

Repeat the steps of this task to install the role on **VN3-SRV1**.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install **Hyper-V** on **VN2-SRV1** and **VN3-SRV1**.

    ````powershell
    $computerName = 'VN2-SRV1', 'VN3-SRV1'
    Invoke-Command -ComputerName $computerName -ScriptBlock {
        Install-WindowsFeature -Name Hyper-V -IncludeManagementTools -Restart
    }
    ````

1. On **VN2-SRV1**, set the default stores to the cluster shared volume **Volume1**.

   ````powershell
   Set-VMHost `
      -ComputerName VN2-SRV1 `
      -VirtualHardDiskPath `
         'C:\ClusterStorage\Volume1\Hyper-V\Virtual Hard Disks' `
        -VirtualMachinePath 'C:\ClusterStorage\Volume1\Hyper-V'
    ````

### Task 2: Create a virtual machine

Perform these steps on CL1.

1. In File Explorer, copy **\\\\vn2-srv1\\c$\\LabResources\\2022_x64_Datacenter_EN_Core_Eval.vhdx** to **\\vn2-srv1\\c$\\ClusterStorage\\Volume1\\Hyper-V\\Virtual Hard Disks**
1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN2-VN3-CLST1.ad.adatum.com** and click **Roles**.
1. In the context-menu of **Roles**, click **Virtual Machines...**, **New Hard Disk...**
1. In New Virtual Hard Disk, click **VN2-SRV1** and click **OK**.
1. In New Virtual Hard Disk Wizard, on page Before You Begin, click **Next >**.
1. On page Choose Disk Format, ensure **VHDX** is selected and click **Next >**.
1. On page Choose Disk Type, click **Differencing** and click **Next >**.
1. On page Specify Name and Location, in **Name**, type **VN2-SRV20.vhdx** and click **Next >**
1. On page Configure disk, click **Browse...**.
1. In Open, click **2022_x64_datacenter_en_core_eval.vhdx** and click **Open**.
1. In **New Virtual Hard Disk Wizard**, on page **Configure disk**, click **next >**.
1. On page Summary, click **Finish**.
1. In **Failover Cluster Manager**, in the context-menu of **Roles**, click **Virtual Machines...**, **New Virtual Machine...**
1. In New Virtual Machine, click **VN2-SRV1** and click **OK**.
1. In New Virtual Machine Wizard, on page Before You Begin, click **Next >**.
1. On page Specify Name and Location, in **Name**, type **VN2-SRV20** and click **Next >**.
1. On page Specify Generation, click **Generation 2** and click **Next >**.
1. On page Assign Memory, in **Startup memory**, type 1024 and click **Next >**.
1. On page Configure Networking, click **Next >**.
1. On page Connect Virtual Hard Disk, click **Use an existing virtual hard disk** and click **Browse**.
1. In Open, click **vn2-srv20.vhdx** and click **Open**.
1. In **New Virtual Machine Wizard**, on page **Connect Virtual Hard Disk**, click **Next >**.
1. On page Summary, click **Finish**.
1. In High Availability Wizard, click **Finish**.
1. In **Failover Cluster Manager**, under **Roles (1)**, in the context-menu of **VN2-SRV20**, click **Start**.
1. In the context-menu of **VN2-SRV20**, click **Connect...**.
1. In VN2-SRV20 on VN2-SRV1 - Virtual Machine Connection, at the prompt **The user's password must be changed before signing in.**, select **OK**.
1. In **New password** and **Confirm password**, enter a secure password.

SConfig will appear.

### Task 3: Simulate a failure

Perform this task on the host.

1. Open **Hyper-V Manager**.
1. Click the name of your computer.
1. Under Virtual Machines, in the context-menu of **VN2-SRV1**
1. In **Hyper-V Manager**, turn off **VN2-SRV1**, click **Turn off...**

### Task 4: Validate failover

Perform these steps on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, expand **VN2-VN3-CLST1.ad.adatum.com** and click **Roles**.

   > VN2-SRV20 should be running on node VN3-SRV1.

1. In the context-menu of **VN2-SRV20**, click **Connect...**.
1. In VN2-SRV20 on VN2-SRV1 - Virtual Machine Connection, sign in with the password you set before.

   > The sign in process should work.

1. In **Failover Cluster Manager**, expand *Storage* and click **Disks**.
1. Click the 100 GB disk, that is still online.
1. At the bottom pane, click on **Replication** and check the status.

### Task 5: Simulate recovery

#### Desktop Experience

Perform this task on the host computer.

Perform this task on the host.

1. Open **Hyper-V Manager**.
1. Click the name of your computer.
1. Under Virtual Machines, in the context-menu of **VN2-SRV1**, click **Start**.

### Task 6: Validate recovery

Perform this task on CL1.

1. Open **Failover Cluster Manager**.
1. In **Failover Cluster Manager**, click Nodes.

   > Both nodes should show as up.

1. Click **Disks**.

   > All Disks should be online.

1. In Disks (4), click **Cluster Disk 1** and click the tab **Replication**.

   > Replication should be healthy.

It can take a few minutes before everything is fine again.

### Task 7: Reverse replication

#### Desktop Experience

Perform these steps on CL1.

1. Open **Failover Cluster Manager**.
1. In Failover Cluster Manager, click on **Roles**.
1. Under Roles (1), in the context menu of **VN2-SRV20**, click **Move**, **Live Migration**, **Select Node...**
1. In Move Virtual Machine, click **VN2-SRV1** , and click **OK**.
1. In **Failover Cluster Manager**, expand **Storage** and click **Disks**.
1. In the context-menu of the 100 GB disk, assigned to **Cluster Shared Volume**, click **Move**, **Select Node...**.
1. In Move Cluster Shared Volume, click **VN2-SRV1** , and click **OK**.
