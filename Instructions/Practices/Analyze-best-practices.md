# Practice: Analyze best practices

## Required VMs

* VN1-SRV1
* CL1

## Task

Run the Best Practices Analyzer on VN1-SRV1 for Domain Services, File Services, and DNS Server, and review the results.

## Instructions

For this practice, it is recommended to perform both experiences, Desktop and PowerShell.

### Desktop experience

Perform these steps on CL1.

1. On **CL1**, sign in as **ad\Administrator**.
1. Open **Server Manager**.
1. In Server Manager, click **All Servers**.
1. Under Server Manager > All Servers, click **VN1-SRV1**.
1. Under **BEST PRACTICES ANALYZER** (you might have to scroll down), click **Tasks**, **Start BPA Scan**.
1. In Select Servers, activate the checkbox beside **VN1-SRV1.ad.adatum.com** and click **Start Scan**.

    Review some of the findings of Best Practices Analyzer.

### PowerShell

Perform these steps on CL1.

1. On **CL1**, sign in as **ad\Administrator**.
1. Open **Terminal**.
1. Create a remote PowerShell session to **VN1-SRV1** and store it in a variable.

    ````powershell
    $pSSession = New-PSSession -ComputerName VN1-SRV11. Query the BPA models on **VN1-SRV1**.
    ````

1. On VN1-SRV1, query the available BPA models.

    ````powershell
    Invoke-Command -Session $pSSession -ScriptBlock { Get-BpaModel }
    ````

1. Create an array of BPA models to scan.

    ````powershell
    $models = 'DirectoryServices', 'DNSServer', 'FileServices' |
        foreach-object { "Microsoft/Windows/$PSItem" }
    ````

1. Run the BPA for the selected models.

    ````powershell
    Invoke-Command -Session $pSSession -ScriptBlock { 
        $using:models | ForEach-Object { Invoke-BpaModel -Id $PSItem }
    }
    ````

1. Query the BPA results for the selected models and store them in a variable.

    ````powershell
    $bPAresult = Invoke-Command -Session $pSSession -ScriptBlock { $using:models | ForEach-Object { Get-BpaResult -Id $PSItem }}
    ````

1. View the BPA results in a grid.

    ````powershell
    $bPAresult | Out-GridView
    ````

1. Save the BPA results to a HTML file.

    ````powershell
    $bPAresult | ConvertTo-Html | Out-File ~\Desktop\BPAResults.html
    ````

1. From the desktop, open the file **BPAResults.html** and review it.
1. Switch back to **Terminal**.
1. Save the BPA results to a CSV file.

    ````powershell
    $bPAresult | Export-Csv -Path ~\Desktop\BPAResults.csv
    ````

1. From the desktop, open the file **BPAResults.csv** and review it.
