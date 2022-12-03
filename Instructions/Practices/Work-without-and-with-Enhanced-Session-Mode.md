# Practice: Work without and with Enhanced Session Mode

## Required VMs

* VN1-SRV1
* PM-SRV1
* CL1

## Task

In PM-SRV20, explore the clipboard features without and with Enhanced Session Mode.

## Instructions

Perform this task on CL1.

1. Sign in as **ad\Administrator**.
1. Open **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. In the context-menu of **PM-SRV20**, click **Connect...**.
1. In PM-SRV20 on PM-SRV1 - Virtual Machine Connection, on the menu click **Action**, **Ctrl+Alt+Delete**.
1. At the prompt Enter credentials for Administrator or hit ESC to switch users/sign-in methods, enter the local Administrator password.
1. In SConfig, use the mouse to select the **Computer name** (**PM-SRV20**) and press CTRL + C.
1. Enter **15**.
1. Press CTRL + V.

    > Can you use the clipboard within a virtual machine?

1. Press ESC.
1. On CL1, open **Notepad**.
1. In Notepad, press CTRl + V.

    > Can you paste clipboard text from the virtual machine to the host?

1. Switch to **Hyper-V Manager**.
1. In Hyper-V Manager, click **PM-SRV1**.
1. In the context-menu of **PM-SRV1**, click **Hyper-V Settings**.
1. In Hyper-V Settings for PM-SRV1, in the left pane, click **Enhanced Session Mode Policy**.
1. Under Enhanced Session Mode Policy, activate **Allow enhanced session mode**.
1. In the left pane, click **Enhanced Session Mode**.
1. Under Enhanced Session Mode, ensure **Use enhanced session mode** is activated.
1. Click **OK**.
1. Switch to **PM-SRV20 on PM-SRV1 - Virtual Machine Connection**.
1. On the menu, click **View**, **Enhanced session**.
1. In **Connect to PM-SRV20**, select a resolution of your choice and click **Connect**.
1. At the Password prompt, enter the password of the local Administrator.
1. Exit to SConfig.

    ````powershell
    Exit
    ````

1. In SConfig, use the mouse to select the **Computer name** (**PM-SRV20**) again and press CTRL + C.
1. On CL1, switch to **Notepad**.
1. Press CTRL + V.

    > Can you paste clipboard text from the virtual machine to the host?

1. In Notepad, type **Get-NetIPConfiguration**, select the text and press CTRL + C.
1. Switch to **PM-SRV20 on PM-SRV1 - Virtual Machine Connection**.
1. In SConfig, enter **15**.
1. At the prompt, press CTRL + V.

    > Can you use the clipboard within a virtual machine?

1. Press ENTER.

    The current IP configuration will be returned.

Leave the virtual machine in its current state for the next exercise.
