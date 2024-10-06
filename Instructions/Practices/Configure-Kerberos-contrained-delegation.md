# Practice: Configure Kerberos constrained delegation

## Required VMs

* VN1-SRV4
* VN1-SRV5
* VN1-SRV7
* CL1
* CL2
* CL4

If you did not complete the lab [Deploying domain controllers](Deploying-domain-controllers.md), in addition to the VMs above, **VN1-SRV1** is required. If VN1-SRV1 is already shut down after the lab, do not start it.

## Setup

If you skipped the practice [Install Remote Server Administration Tools](Practices/Install-Remote-Server-Administration-Tools.md), on **CL1**, in **Terminal**, execute ````C:\LabResources\Solutions\Install-RemoteServerAdministrationTools.ps1````.

If you skipped the practice [Install Windows Admin Center using a script](Install-Windows-Admin-Center-using-a-script.md), on **VN1-SRV4**, run ````C:\LabResources\Solution\Install-AdminCenter.ps1````.

If you skipped exercise 2 of the lab [Multi domain environments](../Labs/Multi-domain-environments.md#exercise-2-deploy-a-child-domain) (meaning, you do not have the clients.ad.adatum.com domain),  join **CL4** to the domain **ad.adatum.com**. Moreover, in the command ````Set-ADComputer```` remove the parameter ````-Server````.

## Task

Allow Windows Admin Center to use single-sign on to CL4.

## Instructions

Perform these steps on CL2.

1. Sign in as **Stefan@ad.adatum.com**.
1. Use Microsoft Edge to navigate to <https://admincenter>.
1. In Windows Admin Center, click **Add**.
1. In the pane Add or create resources, under **Windows PCs**, click **Add**.
1. In the right pane, under **Computer name**, type **CL4**.

    > An error message is displayed, that credentials are needed.

1. Click **Add**.
1. Click **CL4**.

    > A pane will prompt you to specify your credentials.

1. In the pane **Specify your credentials**, click **Cancel**.

Perform these steps on CL1.

1. Sign in as **Administrator@ad.adatum.com**.
1. Run **Terminal**.
1. Retrieve the computer account of **VN1-SRV4** and store it in a variable.

    ````powershell
    $wacComputer = Get-ADComputer VN1-SRV4
    ````

1. Allow CL4 to be delegated to the Windows Admin Center computer.

    ````powershell
    Set-ADComputer `
        -Identity CL4 `
        -Server VN1-SRV7 `
        -PrincipalsAllowedToDelegateToAccount $wacComputer
    ````

Perform these steps on CL2.

1. In Windows Admin Center, click **CL4**.

    > The connection should be successful without requiring additional credentials.

1. Sign out.
