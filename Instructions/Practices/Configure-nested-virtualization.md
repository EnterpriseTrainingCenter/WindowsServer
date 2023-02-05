# Practice: Configure nested virtualization

## Required VMs

None

## Task

For WIN-PM-SRV1 and WIN-PM-SRV2 disable dynamic memory, set the memory to 4 GB, activate MAC address spoofing on the network adapters and expose virtualization extensions to the virtual machines.

## Instructions

Perform these steps on the host.

1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click the name of your computer.
1. Under Virtual Machines, in the context-menu of **WIN-PM-SRV1**, click **Shut down...**.
1. In the context-menu of **WIN-PM-SRV1**, click **Settings...**
1. In Settings for WIN-PM-SRV1, in the left pane, click **Memory**.
1. Under Memory, in **RAM**, type **3072**. Deactivate **Enable Dynamic Memory**.
1. In the left pane, expand the first **Network Adapter** and click **Advanced Features**.
1. Under Advanced Features, **MAC address**, activate **Enable MAC address spoofing**.
1. In the left pane, expand the second **Network Adapter** and click **Advanced Features**.
1. Under Advanced Features, **MAC address**, activate **Enable MAC address spoofing** and click **OK**.
1. Run **Windows PowerShell** or **Terminal** with Administrator rights.
1. In Windows PowerShell or Terminal, for **WIN-PM-SRV1** expose the virtualization extensions to the virtual machine.

    ````powershell
    Set-VMProcessor -VMName 'WIN-PM-SRV1' -ExposeVirtualizationExtensions $true
    ````

1. Switch to **Hyper-V Manager**.
1. In the context-menu of **WIN-PM-SRV1**, click **Start**.

Repeat this task from step 3 for **WIN-PM-SRV2**.
