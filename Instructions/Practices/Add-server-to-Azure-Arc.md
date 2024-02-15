# Practice: Add server to Azure Arc

## Required VMs

* VN1-SRV1
* VN1-SRV5
* CL1

## Task

Onboard VN1-SRV5 to Azure Arc.

## Instructions

Perform this task on CL1.

1. Open **Microsoft Edge** and navigate to <https://portal.azure.com>
1. Sign in to Azure.
1. In **Search resources, services and docs (G+/)**, type **Azure Arc** and click it.
1. In Azure Arc, in Overview, on tab Get started, under **Add your infrastructure for free**, click **Add**.
1. On tab Infrastructure, under **Servers**, click **Add**.
1. Under **Add a single server**, click **Generate script**.
1. On page Basics, beside **Subscription** click the subscription of your choice. Beside **Resource group**, click **WinFAD**.
1. Beside **Region**, select a region close to you, e.g., (Europe) West Europe. Beside **Operating System**, ensure **Windows** is selected. Beside **Connectivity method**, ensure **Public endpoint** is selected. Click **Next**.
1. On page Tags, beside **Datacenter**, type **VN1**. Beside the other tags enter values of your choice. You may leave tags empty. Click **Next**.
1. On page Download and run script, click **Download** and make sure, the download succeeds. Click **Close**.

    You may have to continue on security warnings for the file to download.

1. Open **Terminal**.
1. Open a remote PowerShell session to **VN1-SRV5**.

    ````powershell
    $pSSession = New-PSSession -ComputerName VN1-SRV5
    ````

1. Copy the onboarding script to VN1-SRV5.

    ````powershell
    Copy-Item `
        -Path ~\Downloads\OnboardingScript.ps1 `
        -ToSession $pSSession `
        -Destination c:\Labresources
    ````

1. Enter the remote PowerShell session.

    ````powershell
    Enter-PSSession -Session $pSSession
    ````

1. Unblock the onboarding script file.

    ````powershell
    Unblock-File C:\LabResources\OnboardingScript.ps1
    ````

1. Execute the onboarding script.

    ````powershell
    C:\LabResources\OnboardingScript.ps1
    ````

    Wait for the message **To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the the code...**.

1. Copy the code in the message to the clipboard.
1. Hold down CTRL while clicking on <https://microsoft.com/devicelogin> in the message.
1. Switch to **Microsoft Edge**, if necessary.
1. On the page Sign in to your account, unter **Enter code**, paste the copied code and click **Next**.
1. Sign in to your Azure subscription.
1. At the prompt Are you trying to sign in to Azure Connected Machine Agent, click **Continue**.

1. Switch to the **Terminal**.

    Wait for the script to complete successfully.

1. Exit from the remote PowerShell session.

    ````powershell
    Exit-PSSession
    ````

1. Remove the remote PowerShell session.

    ````powershell
    Remove-PSSession -Session $pSSession
    ````

1. Switch to Microsoft Edge with the Azure Portal open.
1. In **Search resources, services and docs (G+/)**, type **VN1-SRV5** and click it.
1. In VN1-SRV5, in Overview, click the tab **Recommendations**.
