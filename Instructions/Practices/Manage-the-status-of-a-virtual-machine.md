# Practice: Manage the status of a virtual machine

## Required VMs

* VN1-SRV1
* PM-SRV1
* CL1

## Task

Save the virtual machine PM-SRV20.

> What happens to the memory occupied by the virtual machine?

Start the virtual machine PM-SRV20.

> What is the state of the virtual machine after the restart?

Pause the virtual machine PM-SRV20.

> What happens to the virtual machine when paused?

> Does the virtual machine consume CPU resources?

> Does the virtual machine occupy memory on the host?

Shut down the virtual machine PM-SRV20 from Hyper-V.

> Can you shut down the virtual machine from Hyper-V Manager gracefully?

Turn off the virtual machine PM-SRV20.

> Does the virtual machine shut down gracefully?

Reset the virtual machine PM-SRV20.

> Does the virtual machine shut down gracefully?

## Instructions

Perform this task on CL1.

1. Sign in as **ad\Administrator**.
1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. Under virtual machines, in the context-menu of **PM-SRV20**, click **Save**. Wait until **State** shows **Saved**.

    > The memory occupied by the virtual machine is released.

1. In the context-menu of **PM-SRV20**, click **Connect...**.
1. In PM-SRV20 on PM-SRV1 - Virtual Machine Connection, on the menu, click **Action**, **Start**.
1. In Connect to PM-SRV20, click **Connect**.
1. At the Password prompt, enter the password of the local Administrator.

    > The state of the virtual machine should be the same as when you saved the virtual machine.

1. Switch to **Hyper-V Manager**.
1. Under Virtual Machines, in the context-menu of **PM-SRV20**, click **Pause**. Wait until **State** shows **Paused**.
1. Switch to **PM-SRV20 on PM-SRV1 - Virtual Machine Connection**

    > The PM-SRV20 on PM-SRV1 - Virtual Machine Connection will be darkened. You cannot interact with the guest operating system anymore.
    
    > The virtual machine does not consume CPU resources anymore
    
    > The memory is still occupied.

1. On the menu, click **Action**, **Resume**.
1. In **Connect to PM-SRV20**, click **Connect**.

    > You can interact with the guest operating system again.

1. On the menu, click **View**. If there is a checkmark beside **Enhanced Session** click it to deactivate the Enhanced Session Mode.
1. On the menu, click **Action**, **Shut Down...**.
1. In the message box Shut Down Machine, click **Shut Down**.

    Alternatively, you can initiate the shut down from the context-menu of the virtual machine in Hyper-V Manager.

    > You can observe the virtual machine to shut down gracefully.

1. On the menu, click **Action**, **Start**.
1. Close **Connect to PM-SRV20** (do not enter Enhanced Session Mode).
1. On the menu, click **View**. If there is a checkmark beside **Enhanced Session** click it to deactivate the Enhanced Session Mode.
1. On the menu, click **Action**, **Turn Off...**.
1. In the message box Turn Off Machine, click **Turn Off**.

    Alternatively, you can turn off the virtual machine from the context-menu in Hyper-V Manager.

    > The virtual machine is turned off immediately without a graceful shut down.

1. On the menu, click **Action**, **Start**.
1. Close **Connect to PM-SRV20** (do not enter Enhanced Session Mode).
1. On the menu, click **View**. If there is a checkmark beside **Enhanced Session** click it to deactivate the Enhanced Session Mode.
1. On the menu, click **Action**, **Reset...**.
1. In the message box Reset Machine, click **Reset**.

    Alternatively, you can reset the virtual machine from the context-menu in Hyper-V Manager.

    > The virtual machine is reset immediately without a graceful shut down.
