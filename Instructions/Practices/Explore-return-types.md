# Practice: Explore return types

## Required VMs

* VN1-DC1
* CL1

## Task

On CL1, find cmdlet to receive running processes and installed services. Try the cmdlets and display information about the returned classes.

## Instructions

Perform these steps on CL1.

1. Logon as **smart\Administrator**.
1. From the context-menu of **Start** (you can press WIN + X), launch **Windows Terminal** as Administrator.
1. Find a cmdlet to receive a list of running processes.

    ````powershell
    Get-Command -Verb Get -Noun *process*
    ````

1. Get short help about **Get-Process**.

    ````powershell
    Get-Help Get-Process
    ````

1. Receive a list of currently running processes.

    ````powershell
    Get-Process
    ````

1. Get information about the class, Get-Process returns, and its properties and methods.

    ````powershell
    Get-Process | Get-Member
    ````

1. Repeat the steps above to find a cmdlet to get a list of installed services, get help about the cmdlet, try it and get information about the returned class.
