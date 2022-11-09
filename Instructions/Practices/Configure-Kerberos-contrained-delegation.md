# Practice: Configure Kerberos constrained delegation

## Required VMs

* VN1-SRV7
* PM-SRV2
* CL1

## Task

Allow Windows Admin Center to use single-sign on to CL4.

## Instructions

Perform these steps on CL2.

1. Sign in as **Stefan@adatum.com**.
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

1. Sign in as **Administrator@clients.ad.adatum.com**.
1. Run **Terminal**.
1. Retrieve the computer account of **VN1-SRV4** and store it in a variable.

    ````powershell
    $wacComputer = Get-ADComputer VN1-SRV4
    ````

1. Allow CL4 to be delegated to the Windows Admin Center computer.

    ````powershell
    Set-ADComputer `
        -Identity CL4 `
        -Server VN1-SRV8 `
        -PrincipalsAllowedToDelegateToAccount $wacComputer
    ````

Perform these steps on CL2.

1. In Windows Admin Center, click **CL4**.

    > The connection should be successful without requiring additional credentials.

1. Sign out.
