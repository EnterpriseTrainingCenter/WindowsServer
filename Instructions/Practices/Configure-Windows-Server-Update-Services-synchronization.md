# Practice: Configure Windows Server Update Services synchronization

## Required VMs

* VN1-SRV1
* VN1-SRV5
* CL1

## Task

Confinue configuration of VN1-SRV5 from practice [Synchronize languages, products, and categories](Synchronize-languages-products-and-categories.md)


## Instructions

### Desktop experience

Perform these steps on CL1.

*Important*: If the Windows Server Update Services Configuration Wizard:VN1-SRV5 is not running anymore, perform these steps:

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

Continue with the configuration.

1. On page Choose Languages, ensure **Download updates in all languages, including new languages** is selected and click **Next >**.
1. On page Choose Products, activate the checkbox **All Products** and click **Next >**.
1. On page Choose Classifications, activate the checkbox **All Classifications** and click **Next >**.
1. On page Configure Sync Schedule, click **Synchronize automatically**. Beside **First synchronization**, enter a time off peak hours, e.g. 18:00:00. Beside **Synchronizations per day**, ensure **1** is filled in. Click **Next >**.
1. On page Finished, activate **Begin initial synchronization** and click **Next >**.
1. On page What's Next, click **Finish**.

### PowerShell

Perform these steps on CL1.

*Important*: If the Windows Server Update Services Configuration Wizard:VN1-SRV5 is not running anymore, perform these steps:


1. On **CL1**, sign in as **ad\Administrator**.
1. Open **Terminal**.
1. Connect to Update Services.

    ````powershell
    $wsusServer = Get-WsusServer -Name vn1-srv5 -PortNumber 8530
    ````

1. Enable all languages.

    ````powershell
    $configuration = $wsusServer.GetConfiguration()
    $configuration.AllUpdateLanguagesEnabled = $true
    $configuration.Save()
    ````

1. Enable all products.

    ````powershell
    Get-WsusProduct -UpdateServer $wsusServer | Set-WsusProduct
    ````

1. Enable all classifications.

    ````powershell
    Get-WsusClassification -UpdateServer $wsusServer | Set-WsusClassification
    ````

1. Configure the synchronization for off-peak-hours once a day, e.g., at 18:00:00.

    `````powershell
    $subscription = $wsusServer.GetSubscription()
    $subscription.SynchronizeAutomatically = $true
    $subscription.SynchronizeAutomaticallyTimeOfDay = `
        (New-TimeSpan -Hours 18 -Minutes 0 -Seconds 0)
    $subscription.NumberOfSynchronizationsPerDay = 1
    $subscription.Save()
    ````

1. Start synchronization.

    ````powershell
    $subscription.StartSynchronization()
    ````
