# Lab: Managing Hyper-V

## Required VMs

* VN1-SRV1
* PM-SRV1
* PM-SRV2
* CL1

## Setup

On CL1, sign in as **ad\Administrator**.

### Known issues and workarounds

1. <https://github.com/EnterpriseTrainingCenter/WindowsServer/issues/103>

## Introduction

## Exercises

1. [Managing virtual disks](#exercise-1-managing-virtual-disks)
1. [Working with checkpoints](#exercise-2-working-with-checkpoints)
1. [Managing virtual switches](#exercise-3-managing-virtual-switches)
1. [Replicating virtual machines](#exercise-4-replicating-virtual-machines)

## Exercise 1: Managing virtual disks

1. [Create a differencing disk](#task-1-create-a-differencing-disk) based on TinyCorePure64.vhdx with the name PM-SRV21.vhdx
1. [Create a virtual machine from a differencing disk](#task-2-create-a-virtual-machine-from-a-differencing-disk) with the name PM-SRV21 and 256 MB of memory
1. [Move a parent disk](#task-3-move-a-parent-disk) to a different location
1. [Reconnect the differencing disk](#task-4-reconnect-the-differencing-disk)

    > What happens, if you try to start PM-SRV21 after you moved the parent disk?

    > What happens, if you inspect the virtual hard disk of PM-SRV21 after you moved the parent disk?

    > What happens, if you inspect the virtual hard disk of PM-SRV21 after you reconnected to the parent disk?

1. [Merge the differencing disk](#task-5-merge-the-differencing-disk) to a new disk
1. [Configure the virtual machine to use the merged disk](#task-6-configure-the-virtual-machine-to-use-the-merged-disk)
1. [Move virtual machine data](#task-7-move-virtual-machine-data) of PM-SRV21 to a folder with the same name

### Task 1: Create a differencing disk

Perform this task on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. In the context-menu of **PM-SRV1**, click **New**, **Hard Disk...**
1. In New Virtual Hard Disk Wizard, on page Before Your Begin, click **Next >**.
1. On page Choose Disk Format, ensure **VHDX** is selected and click **Next >**.
1. On page Choose Disk type, click **Differencing** and click **Next >**.
1. On page Specify Name and Location, In **Name** type **PM-SRV21** and click **Next >**.
1. On page Configure Disk, under **Specify the virtual hard disk that you want to use as the parent for the new differencing virtual hard disk**, click **Browse...**.
1. In Open, expand **pm-srv1.ad.adatum.com**, **Local Disk (C:)** and click **LabResources**.
1. Click **TinyCorePure64.vhdx** and click **Open**.
1. In New Virtual Hard Disk Wizard, on page **Configure Disk**, click **Next >**.
1. On page Summary, click **Finish**.

### Task 2: Create a virtual machine from a differencing disk

Perform this task on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. In the context-menu of **PM-SRV1**, click **New**, **Virtual Machine...**
1. In New Virtual Machine Wizard, on page Before You Begin, click **Next >**.
1. On page Specify name and Location, in **Name**, type **PM-SRV21** and click **Next >**.
1. On page Specify Generation, click **Generation 1** and click **Next >**.
1. On page Assign Memory, in **Startup Memory**, type **256** and click **Next >**.
1. On page Configure Networking, in **Connection**, click **External** and click **Next >**.
1. On page Connect virtual hard disk, click **Use an exsiting virtual hard disk** and click **Browse...**
1. In Open, ensure the path **Remote File Browser > pm-srv1.ad.adatum.com > C: > hyper-v > Virtual Hard Disks** is opened, click **PM-SRV21.vhdx** and click **Open**.
1. In **New Virtual Machine Wizard**, on page **Connect virtual hard disk**, click **Next >**.
1. On page Summary, click **Finish**.

### Task 3: Move a parent disk

Perform this task on CL1.

1. Open **Terminal**.
1. On PM-SRV1, move **C:\\LabResources\\TinyCorePure64.vhdx** to **C:\\Hyper-V\\Virtual Hard Disks\\**.

    ````powershell
    Invoke-Command -ComputerName PM-SRV1 -ScriptBlock {
        Move-Item `
            -Path 'C:\LabResources\TinyCorePure64.vhdx' `
            -Destination 'C:\Hyper-V\Virtual Hard Disks\'
    }
    ````

### Task 4: Reconnect the differencing disk

Perform this task on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. Under Virtual Machines, in the context-menu of **PM-SRV21**, click **Start**.

    > You will receive an error message about a file not found.

1. In the message box, click **Close**.
1. In **Hyper-V Manager**, under **Virtual Machines**, in the context-menu of **PM-SRV21**, click **Settings...**.
1. In Settings for PM-SRV21 on PM-SRV1, click **Hard Drive**.
1. Under Hard Drive, under **Virtual hard disk**, click **Inspect**.

    > You will receive an error message.

1. In the message box, click **Close**.
1. In **Settings for PM-SRV21 on PM-SRV1**, under **Hard Drive**, under **Virtual hard disk**, click **Edit**.
1. In Edit Virtual Hard Disk Wizard, on page Locate Disk, click **Next >**.
1. On page Choose Action, click **Next >**.
1. On page Configure Disk, note the **Former location of parent virtual hard disk** and click **Browse...**
1. In Open, ensure the path **Remote File Browser > pm-srv1.ad.adatum.com > C: > hyper-v > Virtual Hard Disks** is opened, click **TinyCorePure64.vhdx** and click **Open**.
1. In **Edit Virtual Hard Disk Wizard**, on page **Configure Disk**, click **Next >**.
1. On page Summary, click **Finish**.
1. In **Settings for PM-SRV21 on PM-SRV1**, under **Hard Drive**, under **Virtual hard disk**, click **Inspect**.

    > Data about the virtual hard disk is shown.

1. In Virtual Hard Disk Properties, click **Inspect Parent...**

    > Data about the parent disk is shown.

1. In Virtual Hard Disk Properties, click **Close**.
1. In Virtual Hard Disk Properties, click **Close**.
1. In **Settings for PM-SRV21 on PM-SRV1**, click **Cancel**.

### Task 5: Merge the differencing disk

Perform this task on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. In the context-menu of **PM-SRV1**, click **Edit Disk...**
1. In Edit Virtual Hard Disk Wizard, on page Before You Begin, click **Next >**.
1. On page Locate Disk, click **Browse...**
1. In Open, ensure the path **Remote File Browser > pm-srv1.ad.adatum.com > C: > hyper-v > Virtual Hard Disks** is opened, click **PM-SRV21.vhdx** and click **Open**.
1. In **Edit Virtual Hard Disk Wizard**, on page **Locate Disk**, click **Next >**.
1. On page Choose Action, click **Merge** and click **Next >**.
1. On page Configure Disk, click **To a new virtual hard disk**. In **Location**, type **C:\\hyper-v\\Virtual Hard Disks\\PM-SRV21-Merged.vhdx**. Click **Next >**.
1. On page Summary, click **Finish**.

### Task 6: Configure the virtual machine to use the merged disk

Perform this task on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. Under Virtual Machines, in the context-menu of **PM-SRV21**, click **Settings...**.
1. In Settings for PM-SRV21 on PM-SRV1, click **Hard Drive**.
1. Under Hard Drive, under **Virtual hard disk**, click **Browse...**.
1. In Open, ensure the path **Remote File Browser > pm-srv1.ad.adatum.com > C: > hyper-v > Virtual Hard Disks** is opened, click **PM-SRV21-Merged.vhdx** and click **Open**.
1. In **Settings for PM-SRV21 on PM-SRV1**, click **OK**.

### Task 7: Move virtual machine data

Peform this task on CL1.

1. Open **Terminal**.
1. In Terminal, on PM-SRV1, create a new folder **C:\\Hyper-V\\PM-SRV21**.

    ````powershell
    Invoke-Command -ComputerName PM-SRV1 -ScriptBlock { 
        New-Item -Path C:\Hyper-V\ -Name PM-SRV21 -ItemType Directory
    }
    ````

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. Under Virtual Machines, ensure the **State** of **PM-SRV21** is running. If it is not running, in the context-menu of **PM-SRV21**, click **Start**.

    Although you can certainly move a powered off virtual machine, the task is more interesting when moving a running virtual machine.

1. In the context-menu of **PM-SRV21**, click **Move...**.
1. In Move "PM-SRV21" Wizard, on page **Before You Begin**, click **Next >**.
1. On page Choose Move Type, click **Move the virtual machine's storage** and click **Next >**.
1. On page Choose Options for Moving Storage, ensure **Move All of the virtual machine's data to a single location** is selected and click **Next >**.
1. On page Virtual Machine, click **Browse...**
1. In Open, expand **pm-srv1.ad.adatum.com**, **Local Disk (C:)**, **Hyper-V**, click **pm-srv21** and click **Select Folder**.
1. In **Move "PM-SRV21" Wizard**, on page **Virtual Machine**, click **Next >**.
1. On page Summary, click **Finish**.
1. In **Hyper-V Manager**, under **Virtual Machines**, in the context-menu of **PM-SRV21**, click **Settings...**
1. In Settings for PM-SRV21 on PM-SRV1, click each **Hard Drive**.

    Under **Virtual hard disk**, the path should start with **C:\\hyper-v\\pm-srv21\\Virtual Hard Disks**.

1. Click **Checkpoints**.

    Under **Checkpoint File Location**, the path should start with **C:\\hyper-v\\pm-srv21**.

1. Click **Smart Paging File Location**.

    Under **Smart Paging File Location**, the path should start with **C:\\hyper-v\\pm-srv21**.

1. Click **Cancel**.

## Exercise 2: Working with checkpoints

1. [Create a checkpoints](#task-1-create-a-checkpoints): Create checkpoints on PM-SRV21 according to the table below.

    | Checkpoint           | Do this after the checkpoint       |
    |----------------------|------------------------------------|
    | Pre-Configuration    | Set the IP address to 10.1.200.168 |
    | Pre-Package-Install  | Install the Chromium browser       |
    | Post-Package-Install |                                    |

1. [Revert to checkpoints](#task-2-revert-to-checkpoints) and check the state of the virtual machine

    > What is the IP address of PM-SRV21 after you reverted to Pre-Configuration and what software is installed?

    > What is the IP address of PM-SRV21 after you reverted to Pre-Package-Install and what software is installed?

    > What is the IP address of PM-SRV21 after you reverted to Post-Package-Install and what software is installed?

1. [Delete checkpoints](#task-3-delete-checkpoints)

    > What happens to the virtual machine when you delete checkpoints?

### Task 1: Create a checkpoints

Perform this task on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. Under Virtual machines, in the context-menu of **PM-SRV21**, click **Checkpoint**.
1. In the message box Production checkpoint created, activate **Please don't show me this agein** and click **OK**.
1. In the bottom pane, under **Checkpoints**, in the context menu of checkpoint **PM-SRV21 - (** followed by the the current date and time, **Rename...**, and enter **Pre-Configuration**.
1. In the context-menu of **PM-SRV21**, click **Connect**.
1. In Connect to PM-SRV21, click **Connect**
1. In PM-SRV21 on PM-SRV1 - Virtual Machine Connection, click on the desktop, **System tools**, **ControlPanel**.
1. In ControlPanel, click **Network**.
1. In Network, under **IP Address**, type  **10.1.200.168**. Under **Gateway**, type **10.1.200.1**. Under **NameServers**, type **10.1.1.8**. Ensure that under **Save Configuration**, **Yes** is selected. Click **Apply** and click **Exit**.
1. Close **ControlPanel**.
1. In the menu of PM-SRV21 on PM-SRV1 - Virtual Machine Connection, click **Action**, **Checkpoint...**.
1. In Chekpoint Name, type **Pre-Package-Install** and click **Yes**.
1. In **PM-SRV21 on PM-SRV1 - Virtual Machine Connection**, click on the desktop, **SystemTools**, **Apps**.
1. In First run - would you like the sysstem to pick the fastest mirror, click **Yes**.
1. In Mirror picker, click **OK**.
1. In Apps: Regular Applications (tcz), click **Apps**, **Cloud (Remote)**, **Browse**.
1. Beside **Search**, enter **chromium**.
1. Under **Select Remote Extension**, click **chromium-browser.tcz**. In the drop-down below, click **Download + Load** and click **Go**.

    Wait for the installation to complete. This takes less a minute or two.

1. In the menu of PM-SRV21 on PM-SRV1 - Virtual Machine Connection, click **Action**, **Checkpoint...**.
1. In Chekpoint Name, type **Post-Package-Install** and click **Yes**.

### Task 2: Revert to checkpoints

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. Under Virtual machines, click **PM-SRV21**.
1. In the bottom pane, under **Checkpoints**, in the context-menu of **Pre-Configuration**, click **Apply...**
1. In Apply Checkpoint, click **Apply**.
1. In the context-menu of **PM-SRV21**, click **Connect**.

    > The Chromium browser is not available anymore.

1. In PM-SRV21 on PM-SRV1 - Virtual Machine Connection, click on the desktop, **Applicaitons**, **Terminal**.
1. In Terminal, query the interface configuration.

    ````shell
    ifconfig
    ````

    > eth0 does not have an IP address

1. Switch to **Hyper-V Manager**.
1. In Hyper-V Manager, under **Checkpoints**, in the context-menu of **Pre-Package-Install** click **Apply**.
1. In Apply Checkpoint, click **Apply**.
1. Switch to **PM-SRV21 on PM-SRV1 - Virtual Machine Connection**.

    > The Chromium browser is not available still.

1. Click on the desktop, **Applicaitons**, **Terminal**.
1. In Terminal, query the interface configuration.

    ````shell
    ifconfig
    ````

    > eth0 has the IP address 10.1.200.168

1. Switch to **Hyper-V Manager**.
1. In Hyper-V Manager, under **Checkpoints**, in the context-menu of **Post-Package-Install** click **Apply**.
1. In Apply Checkpoint, click **Apply**.
1. Switch to **PM-SRV21 on PM-SRV1 - Virtual Machine Connection**.

    > The Chromium browser is installed.

### Task 3: Delete checkpoints

Perform this task on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. Under Virtual machines, click **PM-SRV21**.
1. In the bottom pane, under **Checkpoints**, in the context-menu of **Pre-Package-Install**, click **Delete Checkpoint...**
1. In the message box Delete Checkpoint, click **Delete**.

    In **Hyper-V Manager**, the **Status** of **PM-SRV21** will show a short merge process. Because this virtual machine is very small, you might miss the message.

1. In the context-menu of **Pre-Configuration**, click **Delete Checkpoint Subtree...**
1. In the message box Delete Checkpoint, click **Delete**.

    > The virtual machine keeps running all the time.

## Exercise 3: Managing virtual switches

1. [Verify the current network configuration](#task-1-verify-the-current-network-configuration) on PM-SRV1

    > Do you have a network connection from CL1 to PM-SRV21?

    > Which network adapters are available on PM-SRV1?

    > Which protocols are bound to the network adapters on PM-SRV1?

    > Which IP addresses are assigned to the network adapters on PM-SRV1?

1. [Create a private virtual switch and connect the virtual machines](#task-2-create-a-private-virtual-switch-and-connect-the-virtual-machines) on PM-SRV1
1. [Verify the functionality of a private virtual switch](#task-3-verify-the-functionality-of-a-private-virtual-switch)

    > Do you have a network connection from CL1 to PM-SRV21?

    > Which network adapters are available on PM-SRV1?

    > Can the virtual machines connect to each other?

1. [Create an internal virtual switch](#task-4-create-an-internal-virtual-switch-and-connect-the-virtual-machines)
1. [Verify the functionality of an internal switch](#task-5-verify-the-functionality-of-an-internal-virtual-switch)

    > What are the changes to the network configuration of PM-SRV1?

    > What is the IP address of any new network adapter on PM-SRV1?

    Configure any new network adapter on PM-SRV1 with the IP address 10.0.201.1/24.

    Change the IP addresses of PM-SRV21 and PM-SRV21 to 10.0.201.160/24 and 10.0.201.168/24.

    > Can the virtual machines connect to the host?

    > Can the virtual machines connect to each other?

    > Can the virtual machines connect to the internet?

1. [Configure and verify host-based NAT](#task-6-configure-and-verify-host-based-nat) on PM-SRV1

    > Can the virtual machines connect to the internet?

    > What are the first two hops of the trace route of the virtual machines when connecting to the internet?

### Task 1: Verify the current network configuration

Perform this task on CL1.

1. Open **Terminal**.
1. Test the net connection to the IP address of PM-SRV21.

    ````powershell
    Test-NetConnection 10.1.200.168
    ````

    > This succeeds, because PM-SRV21 is connected with an external switch.

1. In Terminal, open a remote PowerShell session to **PM-SRV1**.

    ````powershell
    Enter-PSSession PM-SRV1
    ````

1. Read the net adapters.

    ```powershell
    Get-NetAdapter
    ````

    > Three network adapters are returned:
    >
    >   * Perimeter0
    >   * Perimeter1
    >   * vEthernet (External)
    >
    > The MacAddress of Perimeter0 and vEthernet (External) are equal.

1. Read the enabled net adapter bindings.

    ````powershell
    Get-NetAdapterBinding | Where-Object { $PSItem.Enabled }
    ````

    > Perimeter0 has a binding to Hyper-V Extensible Virtual Switch only. It does not have a binding with any Internet Protocol.

1. Read the IPv4 addresses and format the output as table with the columns **InterfaceAlias** and **IPAddress**.

    ````powershell
    Get-NetIPAddress -AddressFamily IPv4 |
    Format-Table InterfaceAlias, IPAddress
    ````

    > Perimeter0 does not have an IP address. vEthernet (External) received the IP address of Perimeter0.

1. Exit from the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

### Task 2: Create a private virtual switch and connect the virtual machines

Perform this task on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. In the context-menu of **PM-SRV1**, click **Virtual Switch Manager...**.
1. In Virtual Switch Manager for PM-SRV1, in the left pane, ensure **New virtual network switch** is selected. In the right pane, click **Private** and click **Create Virtual Switch**.
1. Under **Name**, type **Private** and click **OK**.
1. In **Hyper-V Manager**, under **Virtual Machines**, in the context-menu of **PM-SRV20**, click **Settings...**.
1. In Settings for PM-SRV20 on PM-SRV1, click **Network Adapter**.
1. Under Network Adapter, under **Virtual switch**, click **Private** and click **OK**.
1. In **Hyper-V Manager**, under **Virtual Machines**, in the context-menu of **PM-SRV21**, click **Settings...**.
1. In Settings for PM-SRV21 on PM-SRV1, click **Network Adapter**.
1. Under Network Adapter, under **Virtual switch**, click **Private** and click **OK**.

### Task 3: Verify the functionality of a private virtual switch

Perform this task on CL1.

1. Open **Terminal**.
1. Test the net connection to the IP address of PM-SRV21.

    ````powershell
    Test-NetConnection 10.1.200.168
    ````

    > This fails, because the virtual machine is on a private switch and not reachable for computers on different networks or the host.

1. Open a remote PowerShell session to PM-SRV1.

    ````powershell
    Enter-PSSession PM-SRV1
    ````

1. Read the network adapters on PM-SRV1.

    ````powershell
    Get-NetAdapter
    ````

    > There is not change in the network adapters from the previous state.

1. Within the session to PM-SRV1, open a remote PowerShell session to PM-SRV20.

    ````powershell
    Enter-PSSession -VMName PM-SRV20
    ````

1. In Windows PowerShell Credential Request, enter the credentials of **ad\Administrator** or **PM-SRV20\Administrator**.
1. At the prompt [PM-SRV1]: [PM-SRV20], test the network connection to **PM-SRV21**.

    ````PowerShell
    Test-NetConnection 10.1.200.168
    ````

    > The connection test suceeds, because virtual machines on the same private network can communicate with each other.

1. Exit from the remote PowerShell sessions.

    ````powershell
    Exit-PSSession
    Exit-PSSession
    ````

### Task 4: Create an internal virtual switch and connect the virtual machines

Perform this task on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. In the context-menu of **PM-SRV1**, click **Virtual Switch Manager...**.
1. In Virtual Switch Manager for PM-SRV1, in the left pane, ensure **New virtual network switch** is selected. In the right pane, click **Internal** and click **Create Virtual Switch**.
1. Under **Name**, type **Internal** and click **OK**.
1. In **Hyper-V Manager**, under **Virtual Machines**, in the context-menu of **PM-SRV21**, click **Settings...**.
1. In Settings for PM-SRV21 on PM-SRV1, click **Network Adapter**.
1. Under Network Adapter, under **Virtual switch**, click **Internal** and click **OK**.
1. In **Hyper-V Manager**, under **Virtual Machines**, in the context-menu of **PM-SRV21**, click **Settings...**.
1. In Settings for PM-SRV21 on PM-SRV1, click **Network Adapter**.
1. Under Network Adapter, under **Virtual switch**, click **Internal** and click **OK**.

### Task 5: Verify the functionality of an internal virtual switch

Perform this task on CL1.

1. Open **Terminal**.
1. Open a remote PowerShell session to **PM-SRV1**.

    ````powershell
    Enter-PSSession PM-SRV1
    ````

1. Read the network adapters on PM-SRV1.

    ````powershell
    Get-NetAdapter
    ````

    > A new network adapter with the name vEthernet (Internal) was created.

1. Read the IP address of the network adapter **vEthernet (Internal)**.

    ````powershell
    Get-NetIPAddress -InterfaceAlias 'vEthernet (Internal)'
    ````

    > The new network adapter has an APIPA address only.

1. Assign the IP address **10.1.201.1/24** to the **vEthernet (Internal)**.

    ````powershell
    New-NetIPAddress `
        -InterfaceAlias 'vEthernet (Internal)' `
        -IPAddress 10.1.201.1 `
        -AddressFamily IPv4 `
        -PrefixLength 24
    ````

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, **PM-SRV1**.
1. Under Virtual Machines, double-click **PM-SRV21**.
1. In PM-SRV21 on PM-SRV1 - Virtual Machine Connection, click on the desktop, **System tools**, **ControlPanel**.
1. In ControlPanel, click **Network**.
1. In Network, under **IP Address**, type  **10.1.201.168**. Under **Gateway**, type **10.1.201.1**. Under **NameServers**, type **10.1.1.8**. Ensure that under **Save Configuration**, **Yes** is selected. Click **Apply** and click **Exit**.
1. Close **ControlPanel**.
1. Click on the desktop, **Applications**, **Terminal**.
1. Test the network connection to the internet address **8.8.8.8**

    ````shell
    ping 8.8.8.8
    ````

    > You will not receive a response.

1. Press CTRL + C.
1. On CL1, switch to **Terminal**.

    The remote PowerShell session to pm-srv1 should be open still.

1. Test the network connection to PM-SRV21.

    ````powershell
    Test-NetConnection 10.1.201.168
    ````

    > The connection will succeed.

1. Exit from the remote PowerShell sessions.

    ````powershell
    Exit-PSSession
    ````

### Task 6: Configure and verify host-based NAT

Perform this task on CL1.

1. Open **Terminal**.
1. On PM-SRV1, create a NAT configuration for the internal IP address prefix 10.1.201.0/24.

    ````powershell
    Invoke-Command -ComputerName PM-SRV1 -ScriptBlock {
        New-NetNat `
            -Name 'Internal' -InternalIPInterfaceAddressPrefix 10.1.201.0/24
    }
    ````

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, **PM-SRV1**.
1. Under Virtual Machines, double-click **PM-SRV21**.
1. In PM-SRV21 on PM-SRV1 - Virtual Machine Connection, click on the desktop, **Applications**, **Terminal**.
1. Test the network connection to the internet address **8.8.8.8**

    ````shell
    ping 8.8.8.8
    ````

    > You will receive a responses.

1. Press CTRL + C.
1. Trace the route to **8.8.8.8**.

    ````shell
    traceroute 8.8.8.8
    ````

    > The first two hops in are 10.1.201.1 and 10.1.200.1. The first is the IP address of PM-SRV1 on the internal virtual switch, the second is the IP address of your host on another internal virtual switch.

## Exercise 4: Replicating virtual machines

1. [Configure the network](#task-1-configure-the-network) on PM-SRV2: Create an internal switch with the same name, IP and NAT configuration as on PM-SRV1
1. [Enable Hyper-V replication](#task-2-enable-hyper-v-replication) on PM-SRV1 and PM-SRV2
1. [Configure the firewall](#task-3-configure-the-firewall) on PM-SRV1 and PM-SRV2
1. [Configure replication of virtual machine](#task-4-configure-replication-of-virtual-machine) PM-SRV21
1. [Test planned failover](#task-5-test-planned-failover)

    > Can you perform a planned failover with the virtual machine running?

    > Can you perform a planned failover with the virtual machine turned off?

1. [Simulate a failure](#task-6-simulate-a-failure) by turning off PM-SRV2
1. [Perform an unplanned fail over](#task-7-perform-an-unplanned-fail-over)

    > What is the initial state of PM-SRV21 on PM-SRV1?

1. [Recover from failure](#task-8-recover-from-failure) by starting PM-SRV2 again
1. [Enable replication again](#task-9-enable-replication-again)

    > What is the initial state of PM-SRV21 on PM-SRV2?

### Task 1: Configure the network

Perform these steps on CL1.

1. Start **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV2**.
1. In the context-menu of **PM-SRV2**, click **Virtual Switch Manager...**.
1. In Virtual Switch Manager for PM-SRV2, in the left pane, ensure **New virtual network switch** is selected. In the right pane, click **Internal** and click **Create Virtual Switch**.
1. Under **Name**, type **Internal** and click **OK**.
1. Open **Terminal**.
1. Open a remote PowerShell session to **PM-SRV2**.

    ````powershell
    Enter-PSSession PM-SRV2
    ````

1. Assign the IP address **10.1.201.1/24** to the **vEthernet (Internal)**.

    ````powershell
    New-NetIPAddress `
        -InterfaceAlias 'vEthernet (Internal)' `
        -IPAddress 10.1.201.1 `
        -AddressFamily IPv4 `
        -PrefixLength 24
    ````

1. Create a NAT configuration for the internal IP address prefix 10.1.201.0/24.

    ````powershell
    New-NetNat -Name 'Internal' -InternalIPInterfaceAddressPrefix 10.1.201.0/24
    ````

1. Exit from the remote PowerShell sessions.

    ````powershell
    Exit-PSSession
    ````

### Task 2: Enable Hyper-V replication

Perform these steps on CL1.

1. Start **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. In the context menu of **PM-SRV1**, click **Hyper-V Settings...**
1. In Hyper-V Settings for PM-SRV1, click **Replication Configuration**.
1. Under Replication Configuration, activate the checkbox **Enable the computer as a Replica server**. Activate the checkbox **Use Kerberos (HTTP)**. In **Specify the port**, ensure **80** is filled in. Under **Authorization and storage**, click **Allow replication from any authenticated server**. Click **OK**.
1. In the message box Configure the firewall to allow inbound traffic, click **OK**.

Repeat this task for PM-SRV2.

### Task 3 Configure the firewall

Perform this task on CL1.

1. Open **Terminal**.
1. In Terminal, enable the firewall rule for Hyper-V Replica Listener on **PM-SRV1**, and **PM-SRV2**.

    ````powershell
    Invoke-Command -ComputerName PM-SRV1, PM-SRV2 -ScriptBlock {
        Set-NetFirewallRule -Name VIRT-HVRHTTPL-In-TCP-NoScope -Enabled True
    }
    ````

### Task 4: Configure replication of virtual machine

Perform these steps on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. Under Virtual Machines, in the context-menu of **PM-SRV21**, click **Enable Replication...**
1. In Enable Replication for PM-SRV21, on page Before Your Begin, click **Next >**.
1. On page Specify Replica Server, in **Replica Server** type **PM-SRV2.ad.adatum.com**, and click **Next >**.
1. On page Specify Connection Parameters, ensure **Use Kerberos authentication (HTTP)** is selected and click **Next >**.
1. On page **Choose Replication VHDs**, ensure all Virtual Hard Disks are activated and click **Next >**.
1. On page Configure Replication Frequency, under **Chose the frequency at which changes will be sent to the Replica Server**, click **30 seconds** and click on **Next >**.
1. On page Configure Additional Recovery Points, select **Create additional hourly recovery points**. In **Coverage provided by additional recovery points (in hours)** type 12. Activate **Volume Shadow Copy Service (VSS) snapshot frequency (in hours)** and enter 2. Click **Next >**.
1. On page Choose Initial Replication Method, ensure **Send initial copy immediately over the network** and **Start replication immediately** is selected. Click **Next >**.
1. On page Summary, click **Finish**.
1. In **Hyper-V Manager**, click **PM-SRV21**.
1. In the bottom pane, click the tab **Replication**. Wait until the initial replication has finished. The **Replication State** changes to **Replication enabled**.
1. In the context-menu of **PM-SRV21**, click **Replication**, **View Replication Health...**
1. In Replication Health for PM-SRV21, review the data and click **Close**.
1. In **Hyper-V Manager**, in the left pane, click **PM-SRV2**.

    > Under Virtual machines, PM-SRV21 is turned off.

1. In the bottom pane, click the tab **Replication**. The **Replication State** should be **Replication enabled**.
1. In the context-menu of **PM-SRV21**, click **Replication**, **View Replication Health...**
1. In Replication Health for PM-SRV21, review the data and click **Close**.
1. In **Hyper-V Manager**, under **Virtual Machines**, in the context-menu of **PM-SRV21**, click **Settings...**.
1. In Settings for PM-SRV21 on PM-SRV2, click **Hard Drive**.

    > The path to the virtual hard disk starts with C:\\hyper-v\\Virtual Hard Disks\\Hyper-V Replica\\Virtual hard disks\\.

1. Click **Checkpoints**.

    > The path of Checkpoint File Location is C:\\hyper-v\\Virtual Hard Disks\\Hyper-V Replica.

1. Click **Smart Paging File Location**.

    > The path of Smart Paging File Location is C:\\hyper-v\\Virtual Hard Disks\\Hyper-V Replica.

1. Click **Cancel**.

### Task 5: Test planned failover

Perform these steps on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. Under Virtual Machines, in the context-menu of **PM-SRV21**, click **Replication**, **Planned Failover...**.
1. In Planned Failover, activate the checkbox **Reverse the replication direction after failover** and ensure, **Start the Replica virtual machine after failover** is activated. Click **Fail Over**.

   > The planned failover failed, because the virtual machine must be turned off.

1. In the message box **The virtual machine is not prepared for planned failover**, click **Close**.
1. In **Planned Failover**, click **Cancel**.
1. In **Hyper-V Manager**, under **Virtual Machines**, in the context-menu of **PM-SRV21**, click **Shut Down...**
1. Under Virtual Machines, in the context-menu of **PM-SRV21**, click **Replication**, **Planned Failover...**.
1. In Planned Failover, activate the checkbox **Reverse the replication direction after failover** and ensure, **Start the Replica virtual machine after failover** is activated. Click **Fail Over**.

   > The planned failover completes successfully.

1. In the message box Failover completed successfully, click **Close**.
1. In **Hyper-V Manager**, in the left pane, click **PM-SRV2**.

    > The virtual machine PM-SRV21 has started and the replication is working.

### Task 6: Simulate a failure

Perform these steps on the host.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click the name of your computer.
1. Under Virtual Machines, in the context-menu of **WIN-PM-SRV2**, click **Turn Off...**

### Task 7: Perform an unplanned fail over

Perform these steps on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.

    > The virtual machine PM-SRV21 is off.

1. In the context menu of **PM-SRV21**, click **Replication**, **Failover...**.
1. In Failover, click **Latest Recovery Point** and click **Fail Over**.

### Task 8: Recover from failure

Perform this task on the host.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click the name of your computer.
1. Under Virtual Machines, in the context-menu of **WIN-PM-SRV2**, click **Start**.

### Task 9: Enable replication again

Perform this task on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV2**.

    > The virtual machine PM-SRV21 is Paused.

1. In the context-menu of **PM-SRV21**, click **Turn Off...**
1. In Turn Off Machine, click **Turn Off**.
1. In **Hyper-V Manager**, in the left pane, click **PM-SRV1**.
1. Under Virtual Machines, in the context-menu of **PM-SRV21**, click Replication, **Reverse Replication...**
1. In Reverve Replication Wizard for PM-SRV21, on page Before you Begin, click **Next >**.
1. On page Specify Replica Server, in **Replica server**, ensure **PM-SRV2.ad.adatum.com** is filled in and click **Next >**.
1. On page Specify Connection Parameters, ensure **Use Kerberos authentication (HTTP)** is selected and click **Next >**.
1. On page Configure Replication Frequency, under **Chose the frequency at which changes will be sent to the Replica Server**, ensure **30 seconds** is selected and click on **Next >**.
1. On page Configure Additional Recovery Points, ensure **Create additional hourly recovery points** is selected. In **Coverage provided by additional recovery points (in hours)**, ensure **12** is filled in. Ensure, **Volume Shadow Copy Service (VSS) snapshot frequency (in hours)** is activated and **2** is filled in. Click **Next >**.
1. On page Choose Initial Replication Method, ensure **Send initial copy immediately over the network** and **Start replication immediately** is selected. Click **Next >**.
1. On page Summary, click **Finish**.
