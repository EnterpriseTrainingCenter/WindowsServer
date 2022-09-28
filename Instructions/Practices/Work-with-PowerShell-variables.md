# Practice: Work with PowerShell variables

## Required VMs

* VN1-SRV1
* CL1

## Task

Store 'cl1' and 'ad.adatum.com' in separate variables and create a third variable for a FQDN using the two variables. Calculate the time until December 25th of the current year. Calculate the number of bytes of 7 TB.

## Instructions

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. From the context-menu of **Start** (you can press WIN + X), launch **Windows Terminal** as Administrator.
1. Store 'cl1' in the variable $hostname.

    ````powershell
    $hostname = 'cl1'
    ````

1. Verify the content of the variable $hostname.

    ````powershell
    $hostname
    ````

1. Store 'ad.adatum.com' in variable $zonename.

    ````powershell
    $zonename = 'ad.adatum.com'
    ````

1. Verify the content of the variable $zonename.

    ````powershell
    $zonename
    ````

1. Compose $hostname and $zonename and store it in a variable $fqdn

    ````powershell
    $fqdn = "$hostname.$zonename"
    ````

1. Verify the content of the variable $fqdn.

    ````powershell
    $fqdn
    ````

    The result should be 'cl1.ad.adatum.com'

1. Find a command to get the current date.

    ````powershell
    Get-Command -Noun *date*
    ````

1. Get help about Get-Date.

    ````powershell
    Get-Help Get-Date
    ````

1. Get the current date and store it in a variable $now.

    ````powershell
    $now = Get-Date
    ````

1. Verify the content of $now

    ````powershell
    $now
    ````

1. Get the members of $now.

    ````powershell
    $now | Get-Member
    ````

    Notice the Year property.

1. Get the current year.

    ````powershell
    $now.Year
    ````

1. Build a date using December 25th of the current year and store it in a variable $holiday.

    ````powershell
    $holiday = Get-Date -Day 25 -Month 12 -Year $now.Year
    ````

1. Verify the content of $holiday.

    ````powershell
    $holiday
    ````

1. Calculate the time difference between $holiday and $now

    ````powershell
    $holiday - $now
    ````

1. Calculate the number of bytes of 1 TB.

    ````powershell
    7TB
    ````
