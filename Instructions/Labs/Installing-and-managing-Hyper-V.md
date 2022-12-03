# Lab: Installing and managing Hyper-V

## Required VMs

* VN1-SRV1
* PM-SRV1
* PM-SRV2
* CL1

## Setup

On CL1, sign in as **ad\Administrator**.

### Known issues and workarounds

1. <https://github.com/EnterpriseTrainingCenter/WindowsServer/issues/102>
1. <https://github.com/EnterpriseTrainingCenter/WindowsServer/issues/103>

## Introduction

## Exercises

1. [Managing virtual disks](#exercise-1-managing-virtual-disks)
1. [Working with checkpoints](#exercise-2-working-with-checkpoints)
1. [Managing virtual switches](#exercise-3-managing-virtual-switches)
1. [Replicating virtual machines](#exercise-4-replicating-virtual-machines)

## Exercise 1: Managing virtual disks

1. [Create a differencing disk](#task-1-create-a-differencing-disk) based on 2022_x64_Datacenter_EN_Core_Eval.vhdx with the name PM-SRV21.vhdx
1. [Create a virtual machine from a differencing disk](#task-2-create-a-virtual-machine-from-a-differencing-disk) with the name PM-SRV21 and 1 GB of memory
1. [Move a parent disk](#task-3-move-a-parent-disk) to a different location
1. [Reconnect the differencing disk](#task-4-reconnect-the-differencing-disk)
1. [Merge the differencing disk](#task-5-merge-the-differencing-disk) to a new disk
1. [Configure the virtual machine to use the merged disk](#task-6-configure-the-virtual-machine-to-use-the-merged-disk)
1. [Move virtual machine data](#task-7-move-virtual-machine-data) of PM-SRV20 to a folder with the same name

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
1. Click **2022_x64_Datacenter_EN_Core_Eval.vhdx** and click **Open**.
1. In New Virtual Hard Disk Wizard, on page **Configure Disk**, click **Next >**.
1. On page Summary, click **Finish**.

### Task 2: Create a virtual machine from a differencing disk

Perform this task on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. In the context-menu of **PM-SRV1**, click **New**, **Virtual Machine...**
1. In New Virtual Machine Wizard, on page Before You Begin, click **Next >**.
1. On page Specify name and Location, in **Name**, type **PM-SRV21** and click **Next >**.
1. On page Specify Generation, click **Generation 2** and click **Next >**.
1. On page Assign Memory, in **Startup Memory**, type **1024** and click **Next >**.
1. On page Configure Networking, in **Connection**, click **External** and click **Next >**.
1. On page Connect virtual hard disk, click **Use an exsiting virtual hard disk** and click **Browse...**
1. In Open, ensure the path **Remote File Browser > pm-srv1.ad.adatum.com > C: > hyper-v > Virtual Hard Disks** is opened, click **PM-SRV21.vhdx** and click **Open**.
1. In **New Virtual Machine Wizard**, on page **Connect virtual hard disk**, click **Next >**.
1. On page Summary, click **Finish**.

### Task 3: Move a parent disk

Perform this task on CL1.

1. Open **Terminal**.
1. In Terminal, open a remote PowerShell session to **PM-SRV1**.

    ````powershell
    Enter-PSSession PM-SRV1
    ````

1. Move **C:\\LabResources\\2022_x64_Datacenter_EN_Core_Eval.vhdx** to **C:\\Hyper-V\\Virtual Hard Disks\\**.

    ````powershell
    Move-Item `
        -Path 'C:\LabResources\2022_x64_Datacenter_EN_Core_Eval.vhdx' `
        -Destination 'C:\Hyper-V\Virtual Hard Disks\'
    ````

### Task 4: Reconnect the differencing disk

Perform this task on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. Under Virtual Machines, in the context-menu of **PM-SRV21**, click **Start**.

    > You will receive an error message about a broken chain on the differencing disk.

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
1. In Open, ensure the path **Remote File Browser > pm-srv1.ad.adatum.com > C: > hyper-v > Virtual Hard Disks** is opened, click **2022_x64_Datacenter_EN_Core_Eval.vhdx** and click **Open**.
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
1. In Terminal, on PM-SRV1, create a new folder **C:\\Hyper-V\\PM-SRV20**.

    ````powershell
    Invoke-Command -ComputerName PM-SRV1 -ScriptBlock { 
        New-Item -Path C:\Hyper-V\ -Name PM-SRV20 -ItemType Directory
    }
    ````

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. Under Virtual Machines, ensure the **State** of **PM-SRV20** is running. If it is not running, in the context-menu of **PM-SRV20**, click **Start**.

    Although you can certainly move a powered off virtual machine, the task is more interesting when moving a running virtual machine.

1. In the context-menu of **PM-SRV20**, click **Move...**.
1. In Move "PM-SRV20" Wizard, on page **Before You Begin**, click **Next >**.
1. On page Choose Move Type, click **Move the virtual machine's storage** and click **Next >**.
1. On page Choose Options for Moving Storage, ensure **Move All of the virtual machine's data to a single location** is selected and click **Next >**.
1. On page Virtual Machine, click **Browse...**
1. In Open, expand **pm-srv1.ad.adatum.com**, **Local Disk (C:)**, **Hyper-V**, click **pm-srv20** and click **Select Folder**.
1. In **Move "PM-SRV20" Wizard**, on page **Virtual Machine**, click **Next >**.
1. On page Summary, click **Finish**.
1. In **Hyper-V Manager**, under **Virtual Machines**, in the context-menu of **PM-SRV20**, click **Settings...**
1. In Settings for PM-SRV20 on PM-SRV1, click each **Hard Drive**.

    Under **Virtual hard disk**, the path should start with **C:\\hyper-v\\pm-srv20\\Virtual Hard Disks**.

1. Click **Checkpoints**.

    Under **Checkpoint File Location**, the path should start with **C:\\hyper-v\\pm-srv20**.

1. Click **Smart Paging File Location**.

    Under **Smart Paging File Location**, the path should start with **C:\\hyper-v\\pm-srv20**.

1. Click **Cancel**.

## Exercise 2: Working with checkpoints

1. [Create a checkpoint and run the post-installation configuration](#task-1-create-checkpoints-and-run-the-post-installation-configuration): Create checkpoints on PM-SRV21 according to the table below.

    | Checkpoint                          | Do this after the checkpoint                          |
    |---------------------------------------------------------------------------------------------|
    | Pre-Post-Installation-Configuration | Configure password and set IP address to 10.1.200.168 |
    | Pre-Domain-Join                     | Join to the domain                                    |
    | Post-Domain-Join                    |                                                       |

1. [Revert to checkpoints](#task-2-revert-to-checkpoints) and check the state of the virtual machine
1. [Delete checkpoints](#task-3-delete-checkpoints)

### Task 1: Create a checkpoints and run the post-installation configuration

Perform this task on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. Under Virtual machines, in the context-menu of **PM-SRV21**, click **Checkpoint**.
1. In the bottom pane, under **Checkpoints**, in the context menu of checkpoint **PM-SRV21 - (** followed by the the current date and time, **Rename...**, and enter **Pre-Post-Installation-Configuration**.
1. In the context-menu of **PM-SRV21**, click **Start**.
1. In the context-menu of **PM-SRV21**, click **Connect**.
1. In Connect to PM-SRV21, click **Connect**
1. In C:\\Windows\\system32\\LogonUI.exe, at the prompt The user's password must be changed before signing in, select **OK** and press ENTER.
1. In New password and Confirm password, enter a secure password and take a note.
1. At Your password has been changed, press ENTER.
1. In SConfig, enter **8**.
1. In Network settings, enter **1**.
1. In Network adapter settings, enter **1**.
1. At the prompt Select (D)HCP or (S)tatic IP address (Blank=Cancel), enter **S**.
1. At the prompt Enter static IP address (Blank=Cancel), enter **10.1.200.168**.
1. At the prompt Enter subnet mask (Blank=255.255.255.0), press ENTER.
1. At the prompt Enter default gateway (Blank=Cancel), enter **10.1.200.1**.
1. Under a number of success messages, press ENTER.
1. In SConfig, enter **8**.
1. In Network settings, enter **1**.
1. In Network adapter settings, enter **2**.
1. At the prompt Enter new preferred DNS server (Blank=Cancel), enter **10.1.1.8**.
1. At the prompt Enter alternate DNS server, press ENTER.
1. Under Sucessfully assigned DNS server(s), press ENTER.
1. In the menu, click **Action**, **Checkpoint...**.
1. In Chekpoint Name, type **Pre-Domain-Join** and click **Yes**.
1. In the message box Virtual Machine Checkpoint, click **OK**.
1. In **PM-SRV21 on PM-SRV1 - Virtual Machine Connection**, in SConfig, enter **1**.
1. At the prompt Join (D)omain or (W)orkgroup? (Blank=Cancel), enter **D**.
1. At the prompt Name of domain to join (Blank=Cancel), enter **ad.adatum.com**.
1. At the prompt Specify an authorized domain\user (Blank=Cancel), enter **ad\Administrator**.
1. At the prompt Password for ad\Administrator, enter the password of **ad\Administrator**.
1. At the prompt Do you want to change the computer name before restarting? (Y)es or (N)o, enter **Y**.
1. At the prompt Enter new computer name (Blank=Canel), enter **PM-SRV21**.
1. At the prompt Password for ad\Administrator, enter the password of **ad\Administrator**.
1. At the prompt Restart now? (Y)es or (N)o, enter **Y**.

    Wait for the sign in screen to appear.

1. In the menu, click **Action**, **Checkpoint...**.
1. In Chekpoint Name, type **Post-Domain-Join** and click **Yes**.
1. In the message box Virtual Machine Checkpoint, click **OK**.

### Task 2: Revert to checkpoints

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. Under Virtual machines, click **PM-SRV21**.
1. In the bottom pane, under **Checkpoints**, in the context-menu of **Pre-Post-Installation-Configuration**, click **Apply...**
1. In Apply Checkpoint, click **Apply**.
1. In the context-menu of **PM-SRV21**, click **Start**.
1. In the context-menu of **PM-SRV21**, click **Connect**.

    > After a few moments, you will be prompted to change the Administrator password again. You do not need to repeat the post-installation configuration now.

1. Close **PM-SRV21 on PM-SRV1 - Virtual Machine Connection**.
1. Switch to **Hyper-V Manager**.
1. In Hyper-V Manager, under **Checkpoints**, in the context-menu of **Pre-Domain-Join** click **Apply**.
1. In Apply Checkpoint, click **Apply**.
1. In the context-menu of **PM-SRV21**, click **Start**.
1. In the context-menu of **PM-SRV21**, click **Connect**.
1. In Connect to PM-SRV21, click **Connect**.
1. Sign in with the password you created in the previous task.
1. In SConfig, enter **8**.

    > The IP address is 10.1.200.168.

1. Close **PM-SRV21 on PM-SRV1 - Virtual Machine Connection**.
1. Switch to **Hyper-V Manager**.
1. In Hyper-V Manager, under **Checkpoints**, in the context-menu of **Post-Domain-Join** click **Apply**.
1. In Apply Checkpoint, click **Apply**.
1. In the context-menu of **PM-SRV21**, click **Start**.
1. In the context-menu of **PM-SRV21**, click **Connect**.
1. In Connect to PM-SRV21, click **Connect**.
1. On the menu, click **Action**, **Ctrl+Alt+Delete**.

    > The sign in screen shows the domain AD.

### Task 3: Delete checkpoints

Perform this task on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. Under Virtual machines, click **PM-SRV21**.
1. In the bottom pane, under **Checkpoints**, in the context-menu of **Pre-Domain-Join**, click **Delete Checkpoint...**
1. In the message box Delete Checkpoint, click **Delete**.

    In **Hyper-V Manager**, the **Status** of **PM-SRV21** will show a short merge process.

1. In the context-menu of **Pre-Post-Installation-Configuration**, click **Delete Checkpoint Subtree...**
1. In the message box Delete Checkpoint, click **Delete**.

    > The virtual machine keeps running all the time.

## Exercise 3: Managing virtual switches

1. [Verify the current network configuration](#task-1-verify-the-current-network-configuration) on PM-SRV1
1. [Create a private virtual switch and connect the virtual machines](#task-2-create-a-private-virtual-switch-and-connect-the-virtual-machines) on PM-SRV1
1. [Verify the functionality of a private virtual switch](#task-3-verify-the-functionality-of-a-private-virtual-switch)

    > Can you open a remote PowerShell session from CL1 to VN1-SRV20?
    
    > What are the changes in the network configuration of PM-SRV1?

    > Can the virtual machines connect to the host or any other computer in the network?

    > Can the virtual machines connect to each other?

1. [Create an internal virtual switch](#task-4-create-an-internal-virtual-switch-and-connect-the-virtual-machines)
1. [Verify the functionality of an internal switch](#task-5-verify-the-functionality-of-an-internal-virtual-switch)

    > What are the changes to the network configuration of PM-SRV1?

    > What is the IP address of any new network adapter on PM-SRV1?

    Configure any new network adapter on PM-SRV1 with the IP address 10.0.201.1/24.

    Change the IP addresses of PM-SRV20 and PM-SRV21 to 10.0.201.160/24 and 10.0.201.168/24.

    > Can the virtual machines connect to the host?

    > Can the virtual machines connect to each other?

    > Can the virtual machines connect to the internet?

1. [Configure and verify host-based NAT](#task-6-configure-and-verify-host-based-nat) on PM-SRV1

    > Can the virtual machines connect to the internet?

    > What are the first two hops of the trace route of the virtual machines when connecting to the internet?

### Task 1: Verify the current network configuration

Perform this task on CL1.

1. Open **Terminal**.
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
    >   * Vnet1-0
    >   * VNet1-1
    >   * vEthernet (External)

    > The MacAddress of VNet1-0 and vEthernet (External) are equal.

1. Read the enabled net adapter bindings.

    ````powershell
    Get-NetAdapterBinding | Where-Object { $PSItem.Enabled }
    ````

    > Vnet1-0 has a binding to Hyper-V Extensible Virtual Switch only. It does not have a binding with any Internet Protocol.

1. Read the IPv4 addresses and format the output as table with the columns **InterfaceAlias** and **IPAddress**.

    ````powershell
    Get-NetIPAddress -AddressFamily IPv4 |
    Format-Table InterfaceAlias, IPAddress
    ````

    > Vnet1-0 does not have an IP address. vEthernet (External) received the former IP address of Vnet1-0.

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
1. In Terminal, try to create a remote PowerShell session to PM-SRV20.

    ````powershell
    Enter-PSSession PM-SRV20
    ````

    > This fails, because the virtual machine is on a private switch and not reachable for the host.

1. Open a remote PowerShell session to PM-SRV1.

    ````powershell
    Enter-PSSession PM-SRV1
    ````

1. Read the network adapters on PM-SRV1.

    ````powershell
    Get-NetAdapter
    ````

    > There is not change in the network adapters from the previous state.

1. Open a remote PowerShell session to the virtual machine PM-SRV20.

    ````powershell
    Enter-PSSession -VMName PM-SRV20
    ````

1. At the Windows PowerShell Credential request, enter the credentials for **pm-srv20\Administrator**.

1. Test the network connection to the host (**10.1.200.8**).

    ````powershell
    Test-NetConnection 10.1.200.8
    ````

    > The connection will fail.

1. Test the network connection to **10.1.200.168**.

    ````powershell
    Test-NetConnection 10.1.200.168
    ````

    > The connection will fail because of the firewall configuration. Note the different error message. In the next step, you will see that the machines can connect to each other.

1. Query the ARP table.

    ````powershell
    arp -a
    `````

    > You see an entry for 10.1.200.168, but not for 10.1.1.8.

1. Exit from the remote PowerShell sessions.

    ````powershell
    Exit-PSSession
    Exit-PSSession

### Task 4: Create an internal virtual switch and connect the virtual machines

Perform this task on CL1.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. In the context-menu of **PM-SRV1**, click **Virtual Switch Manager...**.
1. In Virtual Switch Manager for PM-SRV1, in the left pane, ensure **New virtual network switch** is selected. In the right pane, click **Internal** and click **Create Virtual Switch**.
1. Under **Name**, type **Internal** and click **OK**.
1. In **Hyper-V Manager**, under **Virtual Machines**, in the context-menu of **PM-SRV20**, click **Settings...**.
1. In Settings for PM-SRV20 on PM-SRV1, click **Network Adapter**.
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

    > A new network adapter with the name vEthernet (External) was created.

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

1. Open a remote PowerShell session to the virtual machine **PM-SRV20**.

    ````powershell
    Enter-PSSession -VMName PM-SRV20
    ````

1. At the Windows PowerShell Credential request, enter the credentials for **PM-SRV20\Administrator**.

1. Remove all IP addresses from the network adapter.

    ````powershell
    Get-NetAdapter | Remove-NetIPAddress
    ````

1. At the prompt Are you sure you want to perform this action, enter **A**.
1. Remove all default routes.

    ````powershell
    Remove-NetRoute -DestinationPrefix 0.0.0.0/0
    ````

1. At the prompt Are you sure you want to perform this action, enter **A**.
1. Assign a new IP address 10.1.201.160/24 with the default gateway 10.1.201.1.

    ````powershell
    Get-NetAdapter | New-NetIPAddress `
        -IPAddress 10.1.201.160 `
        -DefaultGateway 10.1.201.1 `
        -AddressFamily IPv4 `
        -PrefixLength 24
    ````

1. Exit from the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

1. Open a remote PowerShell session to the virtual machine **PM-SRV21**.

    ````powershell
    Enter-PSSession -VMName PM-SRV21
    ````

1. At the Windows PowerShell Credential request, enter the credentials for **PM-SRV21\Administrator**.

1. Remove all IP addresses from the network adapter.

    ````powershell
    Get-NetAdapter | Remove-NetIPAddress
    ````

1. At the prompt Are you sure you want to perform this action, enter **A**.
1. Remove all default routes.

    ````powershell
    Remove-NetRoute -DestinationPrefix 0.0.0.0/0
    ````

1. At the prompt Are you sure you want to perform this action, enter **A**.
1. Assign a new IP address 10.1.201.168/24 with the default gateway 10.1.201.1.

    ````powershell
    Get-NetAdapter | New-NetIPAddress `
        -IPAddress 10.1.201.168 `
        -DefaultGateway 10.1.201.1 `
        -AddressFamily IPv4 `
        -PrefixLength 24
    ````

1. Test the network connection to the host (**10.1.201.1**).

    ````powershell
    Test-NetConnection 10.1.201.1
    ````

    > The connection will time out.

1. Test the network connection to **10.1.201.160**.

    ````powershell
    Test-NetConnection 10.1.201.160
    ````

    > The connection will time out.

1. Query the ARP table.

    ````powershell
    arp -a
    `````

    > You see an entry for 10.1.201.1 and 10.1.201.160.

1. Test the network connection to the internet address **8.8.8.8**

    ````powershell
    Test-NetConnection 8.8.8.8
    ````

    > The connection will time out.

1. Exit from the remote PowerShell sessions.

    ````powershell
    Exit-PSSession
    Exit-PSSession
    ````

### Task 6: Configure and verify host-based NAT

Perform this task on CL1.

1. Open **Terminal**.
1. Open a remote PowerShell session to **PM-SRV1**.

    ````powershell
    Enter-PSSession PM-SRV1
    ````

1. Create a NAT configuration for the internal IP address prefix 10.1.201.0/24.

    ````powershell
    New-NetNat -Name 'Internal' -InternalIPInterfaceAddressPrefix 10.1.201.0/24
    ````

1. Open a remote PowerShell session to the virtual machine **PM-SRV21**.

    ````powershell
    Enter-PSSession -VMName PM-SRV21
    ````

1. At the Windows PowerShell Credential request, enter the credentials for **PM-SRV21\Administrator**.
1. Test the network connection to the internet address **8.8.8.8**

    ````powershell
    Test-NetConnection 8.8.8.8
    ````

    > The connection succeeds.

1. Repeat the test and trace the route.

    ````powershell
    Test-NetConnection 8.8.8.8 -TraceRoute
    ````

    > The first two hops in are 10.1.201.1 and 10.1.200.1. The first is the IP address of PM-SRV1 on the internal virtual switch, the second is the IP address of your host on another internal virtual switch.

1. Exit from the remote PowerShell sessions.

    ````powershell
    Exit-PSSession
    Exit-PSSession
    ````

## Exercise 4: Replicating virtual machines

1. [Configure the network](#task-1-configure-the-network) on PM-SRV2: Create an internal switch with the same name, IP and NAT configuration as on PM-SRV1
1. [Enable Hyper-V replication](#task-2-enable-hyper-v-replication) on PM-SRV1 and PM-SRV2
1. [Configure the firewall](#task-3-configure-the-firewall) on PM-SRV1 and PM-SRV2
1. [Configure the replication of virtual machine](#task-4-configure-the replication of virtual machine) PM-SRV21
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
1. In Enable Replication for PM-SRV20, on page Before Your Begin, click **Next >**.
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
1. Under Virtual Machines, click **PM-SRV21**.

    > The virtual machine is turned off.

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

1. In the context menu of **PM-SRV1**, click **Replication**, **Failover...**.
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