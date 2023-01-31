# Practice: Synchronize Windows Server Update Services languages, products, and categories

## Required VMs

* VN1-SRV1
* VN1-SRV5
* CL1

## Task

Configure VN1-SRV5 to synchronize from Microsoft Update and start synchronizing languages, products, and categories.

## Instructions

### Desktop experience

Perform these steps on CL1.

1. On **CL1**, sign in as **ad\Administrator**.
1. Open **Windows Server Update Services**.
1. In Update Services, in the context-menu of **Update Services**, click **Connect to Server...**
1. In Connect to Server, beside **Server name**, type **VN1-SRV5** and click **Connect**.

    The Windows Server Update Services Configuration Wizard:VN1-SRV5 opens. If not, follow these steps:

    1. In Update Services, expand **Update Services**, **VN1-SRV5** and click **Options**.
    1. In Options, click **WSUS Server Configuration Wizard**.
1. In Windows Server Update Services Configuration Wizard:VN1-SRV5, on page Before You Begin, click **Next >**.
1. On page Microsoft Update Improvement Program, click **Next >**.

    Optionally, you might deactivate **Yes, I would like to join the Microsoft Update Improvement Program** before clicking **Next >**.

1. On page Choose Upstream Server, ensure **Synchronize from Microsoft Update** is selected and click **Next >**.
1. On page Specify Proxy Server, ensure, **Use a proxy server when synchronizing** is deactivated and click **Next >**.
1. Click **Start Connecting**.

Do not wait for products and languages to finish synchronizing. This will take about 10 minutes.

Stay signed in and keep everything open for the next practice.

### PowerShell

Perform these steps on CL1.

1. On **CL1**, sign in as **ad\Administrator**.
1. Open **Terminal**.
1. Connect to Update Services.

    ````powershell
    $wsusServer = Get-WsusServer -Name vn1-srv5 -PortNumber 8530
    ````

1. Set Microsoft Update as update source.

    ````powershell
    Set-WsusServerSynchronization -UpdateServer $wsusServer -SyncFromMU
    ````

1. Start the first synchronization.

    ````powershell
    $subscription = $wsusServer.GetSubscription()
    $subscription.StartSynchronization()
    ````

1. Monitor the progress of the synchronization.

    ````powershell
    $subscription.GetSynchronizationProgress()
    $subscription.GetSynchronizationStatus()
    ````

Do not wait for products and languages to finish synchronizing. This will take about 10 minutes. You might repeat the commands of the last step until the status changes to **NotProcessing**.
