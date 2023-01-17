# Lab: Data Deduplication

## Required VMs

* VN1-SRV1
* VN1-SRV10
* CL1

## Setup

On CL1, sign in as **ad\Administrator**.

## Introduction

As storage spaces on the file server is valuable, Adatum want to save space using data deduplication.

## Exercises

1. [Install and configure Data Deduplication](#exercise-1-install-and-configure-data-deduplication)
1. [Test deduplication](#exercise-2-test-deduplication)

## Exercise 1: Install and configure Data Deduplication

1. [Install the Data Deduplication role service](#task-1-install-the-data-deduplication-role-service) on VN1-SRV10
1. [Configure deduplication](#task-2-configure-deduplication) on Volume D: and set the minimum age for deduplated files to 0

### Task 1: Install the Data Deduplication role service

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, in the menu, click **Manage**, **Add Roles and Reatures**.
1. In the Add Rules and Features Wizard, on the page **Before You Begin**, click **Next >**.
1. On the page Installation Type, ensure **Role-based or feature-based installation** is selected and click **Next >**.
1. On the page Server Selection, click **VN1-SRV10.ad.adatum.com** and click **Next >**.
1. On the page Server Roles, expand **File and Storage Services**, **File and iSCSI Services**, and activate the checkbox next to **Data Deduplication** and click **Next >**.
1. On the page Features, click **Next >**.
1. On the page Confirmation, verify your selection and click **Install**.
1. On the page **Results**, click **Close**.

#### Windows Admin Center

Perform this task on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, on the connections page, click **vn1-srv10.ad.adatum.com**.
1. Connected to vn1-srv10.ad.adatum.com, under **Tools**, click **Roles & features**.
1. In Roles and features, expand **File and Storage Services**, **File and iSCSI Services**, click **Data Deduplication**, and click **Install**.
1. In the pane Install Role and Features, activate the checkbox **Reboot the server automatically, if required** and click **Yes**.

    After a few minutes, a notification **Install Roles and Features** appears. If you missed the notification, a small number appears beside the icon *Notifications* (in form of a bell) at the top-right of Windows Admin Center.

#### PowerShell

Perform this task on CL1.

1. Open **Terminal**.
1. Install the Data Deduplication role service on **VN1-10**.

    ````powershell
    Install-WindowsFeature `
        -ComputerName VN1-SRV10 `
        -Name FS-Data-Deduplication `
        -IncludeManagementTools `
        -Restart 
    ````

### Task 2: Configure deduplication

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click **File and Storage Services**.
1. In File and Storage Services, click **Disks**.
1. In Disks, under **DISKS**, under **VN1-SRV10**, click disk **Number** **1**.
1. Under **VOLUMES**, in the context-menu of **D:**, click **Configure Data Deduplication...**
1. In NTFS 1TB (D:\\) Deduplication Settings, beside **Data deduplication**, click **General purpose file server**. Beside **Deduplicate files older than (in days)**, type **0**. Click **OK**.

    Before clicking OK, you might click **Set Deduplication Schedule...** and examine the settings there.

#### PowerShell

1. Open **Terminal**.
1. Open a remote PowerShell session to **VN1-SRV10**.

    ````powershell
    Enter-PSSession VN1-SRV10
    ````

1. Enable deduplication on Volume **D** with the **Default** usage type.

   ````powershell
   $volume = 'D:'
   Enable-DedupVolume -Volume $volume -UsageType Default
   ````

1. Change the minimum file age of deduplicated files to zero.

   ````powershell
   Set-DedupVolume –Volume $volume –MinimumFileAgeDays 0
   ````

1. Retrieve volumes with deduplication enabled and take a note of the current savings rate on drive **D:**.

   ````powershell
   Get-DedupVolume
   ````

1. Review the deduplication schedules

   ````powershell
   Get-DedupSchedule
   ````

1. Exit from the remote PowerShell session

    ````powershell
    Exit-PSSession
    ````

## Exercise 2: Test deduplication

1. [Run optimization](#task-1-run-optimization)
1. [Validate deduplication](#task-2-validate-deduplication)

    > How much space was saved by deduplication?

### Task 1: Run optimization

#### Desktop Experience

Perform these steps on CL1.

1. Open **Task Scheduler**.
1. In Task Scheduler, in the context-menu of **Task Scheduler (local)**, click **Connect To Another Computer...**
1. In Select Computer, in **Another computer**, type **VN1-SRV10** and click **OK**.
1. In **Task Scheduler**, expand **Task Scheduler (vn1-srv10.ad.adatum.com)**, **Task Scheduler Library**, **Microsoft**, **Windows**, and click **Deduplication**.
1. In Deduplication, from the context menu of the job **BackgroundOptimization**, click **Run**. **Status** changes to **Running**.
1. In pane **Actions**, click refresh until **Status** changes to **Ready**.

#### PowerShell

Perform these steps on CL1.

1. Open **Terminal**.
1. Open a remote PowerShell session to **VN1-SRV10**.

    ````powershell
    Enter-PSSession VN1-SRV10
    ````

1. Start the optimization on volume **D:**.

   ````powershell
   Start-DedupJob –Volume D: –Type Optimization
   ````

1. Review the running deduplication process.

   ````powershell
   Get-DedupJob
   ````

   Repeat until the **State** is **Completed**

1. Exit from the remote PowerShell session

    ````powershell
    Exit-PSSession
    ````

### Task 2: Validate deduplication

#### Desktop experience

Perform this task on CL1.

1. Open **Server Manager**.
1. In Server Manager, click **File and Storage Services**.
1. In File and Storage Services, click **Disks**.
1. In Disks, under **DISKS**, under **VN1-SRV10**, click disk **Number** **1**.
1. Under **VOLUMES**, in the context-menu of **D:**, click **Properties**.

   > Beside Deduplication saving, a few MB should be displayed at least.

#### PowerShell

Perform these steps on CL1.

1. Open **Terminal**.
1. Open a remote PowerShell session to **VN1-SRV10**.

    ````powershell
    Enter-PSSession VN1-SRV10
    ````

1. Start the optimization on volume **D:**.

    ````powershell
    Get-DedupVolume
    ````

    > The saved space should be a few MB at least.

1. Exit from the remote PowerShell session

    ````powershell
    Exit-PSSession
    ````
