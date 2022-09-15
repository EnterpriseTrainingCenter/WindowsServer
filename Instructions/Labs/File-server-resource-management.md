# Lab: File server resource management

## Required VMs

* VN1-DC1
* VN1-CL1
* VN1-FS1

## Setup

1. On **CL1**, sign in as **ad\\Administrator**.
2. Run **c:\\LabResources\\Solutions\\New-UsersShare.ps1**.
3. On **CL2**, sign in as **ad\Lara**.

## Introduction

On your file server, users save excessive amounts of data and inappropriate files. Therefore, you need quotas and file screens. Moreover, you have to classify confidential files and archive them after a year. Overall, you need reports about the activities on your file server.

## Exercises

1. [Manage quotas](#exercise-1-manage-quotas)
1. [Manage file screening](#exercise-2-manage-file-screening)
1. [Use folder management properties](#exercise-3-use-folder-management-properties)
1. [Manage classification](#exercise-4-manage-classification)
1. [Create storage reports](#exercise-5-create-storage-reports)
1. [Create a file management task](#exercise-6-create-a-file-management-task)

## Exercise 1: Manage quotas

1. [Create quota templates](#task-1-create-quota-templates). The first template should have a 75 MB hard limit. It should e-mail the user, when 85 and 95 % of the limit is reached, and e-mail the user and the administrators, when 100 % is reached. In all cases an event should be logged. The second template should be a copy of the 200 MB Limit with 50 MB Extension template. It should have a 50 MB hard limit and apply the 75 MB quota template, when 100 % is reached.
1. [Apply quotas](#task-2-apply-quotas). Apply the 75 MB Limit quota template to D:\\Shares\\IT. Auto-apply the 50 MB Limit with 25 MB Extension quota template to D:\\Shares\\Users.
1. [Verify the effect of quotas](#task-3-verify-the-effects-of-quotas)

    > Which capacity does a network drive mapped to one of the folders in the share \\\\VN1-FS1\\Users?

    > Which capacity is does the network drive have, after the quota limit was exceeded?

    > What happens, if the quota limit on \\\\VN1-FS1\\IT is exceeded?

    > Is it possible to increase the quota limit of an individual path of an auto-applied quota?

### Task 1: Create quota templates

#### Desktop experience

Perform this task on CL1.

1. Open **File Server Resource Manager**.
1. In File Server Resource Manager, in the left pane, in the context menu of **File Server Resource Manager**, click **Connect to Another Computer...**
1. In Connect to Another Computer, click **Another computer**, type **VN1-FS1**, and click **OK**.
1. In the left pane, expand **Quota Management** and click **Quota Templates**.
1. In the context-menu of **Quota Templates**, click **Create Quota Template...**.
1. In Create Quota Template, in **Template name**, type **75 MB Limit**.
1. In **Limit**, type **75** and ensure, **MB** is selected.
1. Ensure, **Hard quota. Do not allow users to exceed limit** is selected.
1. Under **Notification thresholds**, click **Add...**.

    > Note: If you receive an error message at this point, restart VN1-FS1 and start the task again.

1. In Add Threshold, on the tab E-mail Message, ensure that **Generate notifications when usage reaches %** is **85**.
1. Click the checkbox **Send e-mail to the user who exceeded the threshold**.
1. Click the tab **Event Log**.
1. Click the checkbox **Send warning to event log** and click **OK**.
1. In **Create Quota Template**, under **Notification thresholds**, click **Add...**.
1. In Add Threshold, on the tab E-mail Message, ensure that **Generate notifications when usage reaches %** is **95**.
1. Click the checkbox **Send e-mail to the user who exceeded the threshold**.
1. Click the tab **Event Log**.
1. Click the checkbox **Send warning to event log** and click **OK**.
1. In **Create Quota Template**, under **Notification thresholds**, click **Add...**.
1. In Add Threshold, on the tab E-mail Message, ensure that **Generate notifications when usage reaches %** is **100**.
1. Click the checkbox **Send e-mail to the following administrators**.
1. Click the checkbox **Send e-mail to the user who exceeded the threshold**.
1. Click the tab **Event Log**.
1. Click the checkbox **Send warning to event log** and click **OK**.
1. In **Create Quota Template**, click **OK**.
1. In the context-menu of **Quota Templates**, click **Create Quota Template...**.
1. In Create Quota template, in the drop-down **Copy properties from quota template**, click **200 MB Limit with 50 MB Extension** and click **Copy**.
1. In Create Quota Template, in **Template name**, type **50 MB Limit with 25 MB Extension**.
1. In **Limit**, type **50** and ensure, **MB** is selected.
1. Ensure, **Hard quota. Do not allow users to exceed limit** is selected.
1. Under **Notification thresholds**, click **Warning (100 %)** and click **Edit...**.
1. In 100% Threshold Properties, click the tab **Command**.
1. On tab Command, in **Command arguments** replace the text between the quotes with **75 MB Limit**. Command arguments should read:

    ````shell
    quota modify /path:[Quota Path] /sourcetemplate:"75 MB Limit"
    ````

1. Click **OK**.
1. In **Create Quota Template**, click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Open a remote PowerShell session to VN1-FS1.

    ````powershell
    Enter-PSSession VN1-FS1
    ````

1. Define the texts for the e-mails and event log messages.

    ````powershell
    $subject = '[Quota Threshold]% quota threshold exceeded'

    # @" "@ declares a multi-line string in PowerShell

    $body = @"
    User [Source Io Owner] has exceed the [Quota Threshold]% quota threshold for quota on [Quota Path] on server [Server].
    The quota limit is [Quota Limit MB] MB and the current usage is [Quota Used MB] MB ([Quota Used Percent]% of limit).'
    "@
    ````

1. Create one action to send a mail to the user and to log an event, and another action to send a mail to the admins and the user and log an event.

    ````powershell
    $actionNearLimit = @(
        (
            New-FsrmAction `
                -Type Email `
                -MailTo '[Source Io Owner Email]' `
                -Subject $subject -Body $body
        )
        (New-FsrmAction -Type Event -EventType Warning -Body $body)
    )

    $actionExceededLimit = @(
        (
            New-FsrmAction `
                -Type Email `
                -MailTo '[Admin Email], [Source Io Owner Email]' `
                -Subject $subject -Body $body
        )
        (New-FsrmAction -Type Event -EventType Warning -Body $body)
    )
    ````

1. Create an array of thresholds. When reaching 85 and 95 percent, the action sending an e-mail to the user and logging an event should execute. When reaching 100 percent, the action sending an e-mail to the admins and the user and logging an event should execute.

    ````powershell
    $threshold = @(
        (New-FsrmQuotaThreshold -Percentage 85 -Action $actionNearLimit)
        (New-FsrmQuotaThreshold -Percentage 95 -Action $actionNearLimit)
        (New-FsrmQuotaThreshold -Percentage 100 -Action $actionExceededLimit)
    )
    ````

1. Create a quota template with a hard limit of 75 MB using the array of thresholds from the previous step.

    ````powershell
    New-FsrmQuotaTemplate -Name '75 MB Limit' -Size 75MB -Threshold $threshold
    ````

1. Get the thresholds of the existing quota template **200 MB Limit with 50 MB Extension** to copy them later to a new quota template.

    ````powershell
    $quotaTemplate = Get-FsrmQuotaTemplate -Name '200 MB Limit with 50 MB Extension'
    $threshold = $quotaTemplate.Threshold
    ````

1. Get the actions of the 100 % threshold.

    ````powershell
    $action = ($threshold | Where-Object { $PSItem.Percentage -eq 100 }).Action
    ````

1. Modify the action command parameters of the action with the command **%windir%\\system32\\dirquota.exe** to apply the **75 MB Limit**.

    ````powershell
    (
        $action | 
        Where-Object { 
            $PSItem.Command -eq '%windir%\system32\dirquota.exe' 
        }
    ).CommandParameters = `
        'quota modify /path:[Quota Path] /sourcetemplate:"75 MB Limit"'
    ````

1. Create a quota template with a hard limit of 50 MB and a 25 MB extension using the array of thresholds from the previous step.

    ````powershell
    New-FsrmQuotaTemplate -Name '50 MB Limit with 25 MB Extension' -Size 50MB -Threshold $threshold
    ````

1. Exit the remote PowerShell Session.

    ````powershell
    Exit-PSSession
    ````

### Task 2: Apply quotas

#### Desktop experience

Perform this task on CL1.

1. Open **File Server Resource Manager**.
1. In File Server Resource Manager, in the left pane, in the context menu of **File Server Resource Manager**, click **Connect to Another Computer...**
1. In Connect to Another Computer, click **Another computer**, type **VN1-FS1**, and click **OK**.
1. In the left pane, expand **Quota Management** and click **Quotas**.
1. In the context-menu of **Quotas**, click **Create Quota...**.
1. In Create Quota, under **Quota path**, click **Browse...**
1. In Browse for Folder, select **d$\\Shares\\IT** and click **OK**.
1. In **Create Quota**, ensure, **Create quota on path** is selected.
1. In **Derive properties from this quota template (recommended)**, click **75 MB Limit** and click **Create**.
1. In the context-menu of **Quotas**, click **Create Quota...**.
1. In Create Quota, under **Quota path**, click **Browse...**
1. In Browse for Folder, select **d$\\Shares\\Users** and click **OK**.
1. In **Create Quota**, click **Auto apply template and create quotas on existing and new subfolders**.
1. In **Derive properties from this quota template (recommended)**, click **50 MB Limit with 25 MB Extension** and click **Create**.
1. In the context-menu of **Quotas**, click **Refresh**.

    > You should see an individual quota applied to each sub-folder of D:\\Shares\\Users.

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Open a remote PowerShell session to VN1-FS1.

    ````powershell
    Enter-PSSession VN1-FS1
    ````

1. Create a quota on **D:\\Shares\\IT** with the template **75 MB Limit**.

    ````powershell
    New-FSRMQuota -Path D:\Shares\IT\ -Template '75 MB Limit'
    ````

1. Create an auto-quota for all subfolders of **D:\\Shares\\Users** with the template **50 MB Limit with 25 MB extension**.

    ````powershell
    New-FsrmAutoQuota `
        -Path D:\Shares\Users\ `
        -Template '50 MB Limit with 25 MB Extension'
    ````

1. List the applied quotas.

    ````powershell
    Get-FsrmAutoQuota
    Get-FsrmQuota
    ````

    > Aside from the quotas you created with the commands above, you should get an individual quota applied to each sub-folder of D:\\Shares\\Users.

1. Exit the remote PowerShell Session.

    ````powershell
    Exit-PSSession
    ````

### Task 3: Verify the effects of quotas

Perform this task on CL1.

1. Open **File Explorer**, navigate to \\\\vn1-fs1\\Users
1. In File Explorer, in \\\\vn1-fs1\\Users create a new folder with the name **User11**.
1. Open **File Server Resource Manager**.
1. In File Server Resource Manager, in the left pane, in the context menu of **File Server Resource Manager**, click **Connect to Another Computer...**
1. In Connect to Another Computer, click **Another computer**, type **VN1-FS1**, and click **OK**.
1. In the left pane, expand **Quota Management** and click **Quotas**.

    > You should see a the quota 50 MB Limit with 25 MB Extension applied to D:\\Shares\\Users\\User11.

1. Open **Windows Terminal**.
1. Map a drive U to \\\\vn1-fs1\\Users\\User1

    ````powershell
    New-PSDrive -Name U -PSProvider FileSystem -Root \\vn1-fs1\Users\User1 -Persist
    ````

1. Switch to **File Explorer**.
1. In File Explorer, click **This PC**.
1. In This PC, in the context menu of **User1 (\\\\vn1-fs1\\Users) (U:)**, click **Properties**. On tab **General**, note the Capacity.

    > The capacity should be 50 MB.

1. Close **User1 (\\\\vn1-fs1\\Users) (U:) Properties**.
1. Copy all folders from **\\\\vn1-fs1\\it** to **U:**. Use either File Explorer, or in **Windows Terminal**, PowerShell.

    ````powershell
    Copy-Item \\vn1-fs1\IT\* u:\ -Recurse
    ````

    > You should receive an error message, that not enough space is on the disk.

1. In This PC, in the context menu of **User1 (\\\\vn1-fs1\\Users) (U:)**, click **Properties**. On tab **General**, note the Capacity.

    > The capacity should be 75 MB now.

1. Close **User1 (\\\\vn1-fs1\\Users) (U:) Properties**.
1. Retry to copy all folders from **\\\\vn1-fs1\\it** to **U:**. Use either File Explorer, or in **Windows Terminal**, PowerShell.

    ````powershell
    Copy-Item \\vn1-fs1\IT\* u:\ -Recurse -Force
    ````

    > The copy process should work now.

1. Copy **\\\\vn1-fs1\\c$\\LabResources\\Sample Documents\\Travel Packages** to **\\\\vn1-fs1\\IT**. Use either File Explorer, or in **Windows Terminal**, PowerShell.

    ````powershell
    Copy-Item `
        -Path '\\vn1-fs1\c$\LabResources\Sample Documents\Travel Packages\' `
        -Destination \\vn1-fs1\IT\ `
        -Recurse `
        -Force
    ````

    > You should receive error messages, that not enough space is on the disk. Even if you try to repeat the copy command, the error message will not disappear.

1. Copy **\\\\vn1-fs1\\c$\\LabResources\\Sample Documents\\Travel Packages** to **U:**. Use either File Explorer, or in **Windows Terminal**, PowerShell.

    ````powershell
    Copy-Item `
        -Path '\\vn1-fs1\c$\LabResources\Sample Documents\Travel Packages\' `
        -Destination U:\ `
        -Recurse `
        -Force
    ````

    > You should receive error messages, that not enough space is on the disk. Even if you try to repeat the copy command, the error message will not disappear.

1. Switch to **File Server Resource Manager**.
1. In **Quotas**, double-click the quota on **D:\\Shares\\Users\\User1**.
1. In Quota Properties of D:\\Shares\\Users\\User1, under **Limit**, type **100** and click **OK**.

    Alternatively you could use the following PowerShell commands in **Windows Terminal**:

    ````powershell
    Enter-PSSession VN1-FS1
    Set-FSRMQuota -Path D:\Shares\Users\User1\ -Size 100MB
    Exit-PSSesseion
    ````

1. Retry to copy **\\\\vn1-fs1\\c$\\LabResources\\Sample Documents\\Travel Packages** to **U:**. Use either File Explorer, or in **Windows Terminal**, PowerShell.

    ````powershell
    Copy-Item `
        -Path '\\vn1-fs1\c$\LabResources\Sample Documents\Travel Packages\' `
        -Destination U:\ `
        -Recurse `
        -Force
    ````

    > The copy process should succeed now.

1. Delete the folder **\\\\vn1-fs1\\IT\\Travel Packages**.

    ````powershell
    Remove-Item '\\vn1-fs1\IT\Travel Packages' -Recurse

1. Switch to **Windows Terminal** and remove the mapped network drive.

    ````powershell
    Remove-PSDrive -Name U
    ````

## Exercise 2: Manage file screening

1. [Create a file screen](#task-1-create-a-file-screen) blocking all executables on D:\, but allow them on D:\Shares\IT.
1. [Verify the effect of file screens](#task-2-verify-the-effect-of-file-screens)

    > Can you copy ps1-files to the Marketing share?

    > Can you copy ps1-files to the IT share?

### Task 1: Create a file screen

#### Desktop experience

Perform this task on CL1.

1. Open **File Server Resource Manager**.
1. In File Server Resource Manager, in the left pane, in the context menu of **File Server Resource Manager**, click **Connect to Another Computer...**
1. In Connect to Another Computer, click **Another computer**, type **VN1-FS1**, and click **OK**.
1. In **File Server Resource Manager**, in the left pane, expand **File Screening Management**, and click **File Screens**.
1. In the context-menu of **File Screens**, click **Create File Screen...**.
1. In Create File Screen, under **File screen path**, type **D:\\**.
1. Under **Derive properties from this file screen template (recommended)**, click **Block Executable Files** and click **Create**.
1. In the context-menu of **File Screens**, click **Create File Screen Exception...**.
1. In Create File Screen Exception, under **Exception path**, click **Browse...**.
1. In Browse For Folder, click **d$\\Shares\\IT** and click **OK**.
1. In **Create File Scren Exception**, under **File groups**, active the checkbox **Executable Files** and click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Open a remote PowerShell session to VN1-FS1.

    ````powershell
    Enter-PSSession VN1-FS1
    ````

1. Apply the file screen template **Block Executable Files** to **D:\\**.

    ````powershell
    New-FsrmFileScreen -Path d:\ -Template 'Block Executable Files'
    ````

1. Create a file screen exception for the file group **Executable Files** on **D:\\Shares\\IT**.

    ````powershell
    New-FsrmFileScreenException `
        -Path D:\Shares\IT\ `
        -IncludeGroup 'Executable Files'
    ````

1. Exit the remote PowerShell Session.

    ````powershell
    Exit-PSSession
    ````

### Task 2: Verify the effect of file screens

Perform this task on CL1.

1. Copy some ps1-files from **C:\\LabResources\\Solutions** to **\\\\VN1-FS1\\Marketing**.

    > You will receive an 'Access denied' error message.

1. In **\\\\VN1-FS1\\Marketing** try to make a copy of any other file.

    > The process should succeed, which proves, that permissions are not the problem.

1. Copy some ps1-files from **C:\\LabResources\\Solutions** to **\\\\VN1-FS1\\IT**.

    > The process should succeed.

## Exercise 3: Use folder management properties

1. [Add a value to the Folder Usage property](#task-1-add-a-value-to-the-folder-usage-property) called Setup files
1. [Set the Folder Usage property](#task-2-set-the-folder-usage-property) of D:\Shares\Users to User Files, and of all other folders in D:\Shares to Group Files.
1. [Create an Access Denied Assistance Message](#task-3-create-an-access-denied-assistance-message-for-a-folder) for a folder. Access denied for D:\Shares\Finance should generate the following text:

    Access to finance data is restricted. Please contact the finance manager to gain access.

1. [Verify the user-friendly access denied messages](#task-4-verify-the-user-friendly-access-denied-message)

### Task 1: Add a value to the Folder Usage property

#### Desktop experience

Perform this task on CL1.

1. Open **File Server Resource Manager**.
1. In File Server Resource Manager, in the left pane, in the context menu of **File Server Resource Manager**, click **Connect to Another Computer...**
1. In Connect to Another Computer, click **Another computer**, type **VN1-FS1**, and click **OK**.
1. In **File Server Resource Manager**, in the left pane, expand **Classification Management** and click **Classification Properties**.
1. In Classification Properties, double-click **Folder Usage**.
1. In Edit Local Classification Property, in the table at the bottom, click the row marked with an asterisk.
1. In the column value, type **Setup Files** and click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Open a remote PowerShell session to **VN1-FS1**.

    ````powershell
    Enter-PSSession VN1-FS1
    ````

1. Retrieve the property definition for **Folder Usage**

    ````powershell
    $propertyDefinition =  Get-FsrmClassificationPropertyDefinition | 
        Where-Object {$PSItem.Displayname -eq 'Folder Usage' }
    ````

    or

    ````powershell
    $propertyDefinition = `
        Get-FsrmClassificationPropertyDefinition -Name 'FolderUsage_MS'
    ````

1. Add **Setup Files** to the possible values.

    ````powershell
    $propertyDefinition.PossibleValue += `
        New-FsrmClassificationPropertyValue -Name 'Setup Files'
    ````

1. Change the property definition.

    ````powershell
    $propertyDefinition |
    Set-FsrmClassificationPropertyDefinition `
        -PossibleValue $propertyDefinition.PossibleValue
    ````

1. Exit the remote PowerShell Session.

    ````powershell
    Exit-PSSession
    ````

### Task 2: Set the Folder Usage property

#### Desktop experience

Perform this task on CL1.

1. Open **File Server Resource Manager**.
1. In File Server Resource Manager, in the left pane, in the context menu of **File Server Resource Manager**, click **Connect to Another Computer...**
1. In Connect to Another Computer, click **Another computer**, type **VN1-FS1**, and click **OK**.
1. In **File Server Resource Manager**, in the left pane, expand **Classification Management**.
1. In the context-menu of **Classification Properties**, click **Set Folder Management Properties...**.
1. In Set Folder Management Properties, under **Property**, click **Folder Usage**.
1. Click the button **Add...**.
1. In Add Values, under **Path**, click **Browse...**.
1. In Browse for Folder, click **D$\\Shares\\Users** and click **OK**.
1. In **Add Value**, activate the checkbox **User Files** and click **OK**.
1. In **Set Folder Management Properties**, click the button **Add...**.
1. In Add Values, under **Path**, click **Browse...**.
1. In Browse for Folder, click **D$\\Shares\\Finance** and click **OK**.
1. In **Add Value**, activate the checkbox **Group Files** and click **OK**.
1. Repeat the previous 4 steps for **D$\\Shares\\IT** and **D$\\Shares\\Marketing**.
1. In **Set Folder Management Properties**, click the button **Close**.

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Open a remote PowerShell session to **VN1-FS1**.

    ````powershell
    Enter-PSSession VN1-FS1
    ````

1. For **D:\\Shares\\Users**, set the folder usage property to **User Files**.

    ````powershell
    Set-FsrmMgmtProperty `
        -Namespace 'D:\Shares\Users' `
        -Name 'FolderUsage_MS' `
        -Value 'User Files'
    ````

1. For **D:\\Shares\\Finance**, **D:\\Shares\\IT**, and **D:\\Shares\\Marketing**, set the folder usage property to **Group Files**.

    ````powershell
    'D:\Shares\Finance\', 'D:\Shares\IT\', 'D:\Shares\Marketing\' | 
    ForEach-Object { 
        Set-FsrmMgmtProperty `
            -Namespace $PSItem `
            -Name 'FolderUsage_MS' `
            -Value 'Group Files' 
    }
    ````

1. Exit the remote PowerShell Session.

    ````powershell
    Exit-PSSession
    ````

### Task 3: Create an Access Denied Assistance Message for a folder

#### Desktop experience

Perform this task on CL1.

1. Open **File Server Resource Manager**.
1. In File Server Resource Manager, in the left pane, in the context menu of **File Server Resource Manager**, click **Connect to Another Computer...**
1. In Connect to Another Computer, click **Another computer**, type **VN1-FS1**, and click **OK**.
1. In **File Server Resource Manager**, in the left pane, expand **Classification Management**.
1. In the context-menu of **Classification Properties**, click **Set Folder Management Properties...**
1. In Set Folder Management Properties, under **Property**, ensure, **Access-Denied Assistance Message** is selected, and click the button **Add...**.
1. In Add Values, under **Path**, click **Browse**.
1. In Browse For Folder, click **D$\\Shares\\Finance** and click **OK**.
1. In **Add Values**, in **Value**, type **Access to finance data is restricted. Please contact the finance manager to gain access.**
1. Click **OK**.
1. In **Set Folder Management Properties**, click **Close**.

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Open a remote PowerShell session to **VN1-FS1**.

    ````powershell
    Enter-PSSession VN1-FS1
    ````

1. For **D:\\Shares\\Users**, set the folder usage property to **User Files**.

    ````powershell
    Set-FsrmMgmtProperty `
        -Namespace 'D:\Shares\Finance' `
        -Name 'AccessDeniedMessage_MS' `
        -Value 'Access to finance data is restricted. Please contact the finance manager to gain access.'
    ````

1. Exit the remote PowerShell Session.

    ````powershell
    Exit-PSSession
    ````

### Task 4: Verify the user-friendly access denied message

Perform this task on CL2.

1. In **File Explorer**, navigate to **\\\\VN1-FS1**.
1. In \\\\VN1-FS1 double-click **Marketing**.

    > You should see a custom error message as in [figure 1].

1. In \\\\VN1-FS1 double-click **Finance**.

    > You should see a custom error message as in [figure 2].

## Exercise 4: Manage classification

1. [Add a local property](#task-1-add-a-local-property) named Confidentiality with the possible values Confidential, PII, and Secret.
1. [Create classification rules](#task-2-create-classification-rules) for all group files according to the table below.

    | Rule name                                          | Confidentiality | Expression Type | Expression  |
    |----------------------------------------------------|-----------------|-----------------|-------------|
    | On payroll set confidentiality to PII              | PII             | String          | payroll     |
    | On vertraulich set confidentiality to confidential | Confidential    | String          | vertraulich |
    | On tax set confidentiality to secret               | Secret          | String          | tax         |

1. [Run the classification](#task-3-run-the-classification)

### Task 1: Add a local property

#### Desktop experience

Perform this task on CL1.

1. Open **File Server Resource Manager**.
1. In File Server Resource Manager, in the left pane, in the context menu of **File Server Resource Manager**, click **Connect to Another Computer...**
1. In Connect to Another Computer, click **Another computer**, type **VN1-FS1**, and click **OK**.
1. In **File Server Resource Manager**, in the left pane, expand **Classification Management** and click **Classification Properties**.
1. In the context-menu of **Classification Properties**, click **Create local Property...**
1. In Create Local Classification Property, in **Name**, type Confidentiality.
1. Under Property type, click **Single Choice**.
1. In the table at the bottom, type in indidivual rows: **Confidential**, **PII**, **Secret**.
1. Click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Open a remote PowerShell session to **VN1-FS1**.

    ````powershell
    Enter-PSSession VN1-FS1
    ````

1. Create a new classification property definition named **Confidentiality** with the type **single chose**, and the possible values **Confidential**, **PII**, and **Secret**.

    ````powershell
    New-FsrmClassificationPropertyDefinition `
        -Name Confidentiality `
        -Type SingleChoice `
        -PossibleValue @(
            (New-FsrmClassificationPropertyValue -Name 'Confidential')
            (New-FsrmClassificationPropertyValue -Name 'PII')
            (New-FsrmClassificationPropertyValue -Name 'Secret')
        )
    ````

1. Exit the remote PowerShell Session.

    ````powershell
    Exit-PSSession
    ````

### Task 2: Create classification rules

Create classification rules according to the table above. Use the different administration tools for this task. Below, generic instructions for each tool are provided.

#### Desktop experience

Perform this task on CL1.

1. Open **File Server Resource Manager**.
1. In File Server Resource Manager, in the left pane, in the context menu of **File Server Resource Manager**, click **Connect to Another Computer...**
1. In Connect to Another Computer, click **Another computer**, type **VN1-FS1**, and click **OK**.
1. In **File Server Resource Manager**, in the left pane, expand **Classification Management** and click **Classification Rules**.
1. In the context-menu of **Classification Rules**, click **Create Classification Rule...**
1. In Create Classification Rule, under **Rule name**, type the rule name.
1. Click the tab **Scope**.
1. On the tab Scope, activate the checkboxes **Group Files** and **User Files**.
1. Click the tab **Classification**.
1. On the tab Classification, under **Classification method**, click **Content Classifier**.
1. Under **Choose a property to assign to files**, click  **Confidentiality**.
1. Under **Specify a value**, click the value for confidentiality.
1. Under **Parameters**, click the button **Configure...**
1. In the table under **Specify the strings or regular expression patterns to look for in the file or file properties**, in the column **Expression Type**, click **String**. In the column **Expression**, type the expression, and click **OK**.
1. In **Create Classification Rule**, click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Open a remote PowerShell session to **VN1-FS1**.

    ````powershell
    Enter-PSSession VN1-FS1
    ````

1. Create the classification rule with the values from the table.

    ````powershell
    # TODO: Fill the values from the table
    $name = '' # Rule name
    $propertyValue = '' # Confidentiality
    $contentString = '' # Expression

    New-FsrmClassificationRule `
        -Name $name `
        -Namespace @('[FolderUsage_MS=User Files]', '[FolderUsage_MS=Group Files]') `
        -ClassificationMechanism 'Content Classifier' `
        -Property 'Confidentiality' `
        -PropertyValue $propertyValue `
        -ContentString $contentString
    ````

1. Exit the remote PowerShell Session.

    ````powershell
    Exit-PSSession
    ````

### Task 3: Run the classification

Note: You will verify the classification in the next exercise.

#### Desktop experience

Perform this task on CL1.

1. Open **File Server Resource Manager**.
1. In File Server Resource Manager, in the left pane, in the context menu of **File Server Resource Manager**, click **Connect to Another Computer...**
1. In Connect to Another Computer, click **Another computer**, type **VN1-FS1**, and click **OK**.
1. In **File Server Resource Manager**, in the left pane, expand **Classification Management** and click **Classification Rules**.
1. In the context-menu of **Classification Rules**, click **Run Classification With All Rules Now...**
1. In Run Classification, click **Run classification in the backgroun** and click **OK**.

Wait until, in the bottom pane, the Status changes to **Running** and is empty again. **Current processing** should read **Not running**.

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Open a remote PowerShell session to **VN1-FS1**.

    ````powershell
    Enter-PSSession VN1-FS1
    ````

1. Start the classification

    ````powershell
    Start-FsrmClassification
    ````

1. Retrieve the status of the classification.

    ````powershell
    Get-FsrmClassification
    ````

    Repeat this step until **Status** changes from **Queued** to **NotRunning**

1. Exit the remote PowerShell Session.

    ````powershell
    Exit-PSSession
    ````

## Exercise 5: Create storage reports

1. [Schedule a report task](#task-1-schedule-a-report-task) including all default reports for every Monday at 7:00 and run the task.
1. [Review the reports](#task-2-review-the-reports)

    > Which files are confidential, secret, or contain PII?

    > Why does the file screen audit report not list anything?

    > What are the largest files?

### Task 1: Schedule a report task

#### Desktop experience

Perform this task on CL1.

1. Open **File Server Resource Manager**.
1. In File Server Resource Manager, in the left pane, in the context menu of **File Server Resource Manager**, click **Connect to Another Computer...**
1. In Connect to Another Computer, click **Another computer**, type **VN1-FS1**, and click **OK**.
1. In **File Server Resource Manager**, in the left pane, click **Storage Reports Management**.
1. In the context-menu of **Storage Reports Management**, click **Schedule a new Report Task...**.
1. In Storage Reports Taks Properties, under **Report Name**, type **All reports**.
1. Under **Report data**, in addition to already activated reports, activate the checkbox **Files by Property** and click **Edit Parameters**.
1. In Report Parameters, under **Report Parameters**, click **Confidentiality** and click **OK**.
1. In **Storage Reports Taks Properties**, click the tab **Scope**.
1. On the tab Scope, activate the checkboxes **Group files** and **User Files**.
1. In **Storage Reports Taks Properties**, click the tab **Schedule**.
1. On the tab Schedule, in **Run at**, type **07:00:00**.
1. Activate the checkbox **Monday**.
1. Activate the checkbox **Limit (in hours)**, enter **2** , and click **OK**.
1. In **File Server Resource Manager**, in the context-menu of **All reports**, click **Run Report Task Now...**.
1. In Generate Storage Reports, click **OK**.

In **File Server Resource Manager**, wait until for **All reports** the **Last Result** equals **Success** before proceeding to the next task.

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Open a remote PowerShell session to VN1-FS1.

    ````powershell
    Enter-PSSession VN1-FS1
    ````

1. Create a schedule for **every Monday** at **7:00**.

    ````powershell
    $schedule = New-FsrmScheduledTask -Weekly Monday -Time 07:00:00
    ````

1. Create a scheduled storage report task for duplicate files, files by file group, files by owner, file screen audit, large files, least recently access files, most recently accessed files, and quota usage for user and group files in the **DHTML** format with the schedule from the previous step.

    ````powershell
    $name = 'All reports'
    New-FsrmStorageReport `
        -Name $name `
        -Namespace @('[FolderUsage_MS=User Files]', '[FolderUsage_MS=Group Files]') `
        -Schedule $schedule `
        -ReportFormat DHtml `
        -ReportType `
            DuplicateFiles, `
            FilesByFileGroup, `
            FilesByOwner, `
            FilesByProperty, `
            FileScreenAuditFiles, `
            LargeFiles, `
            LeastRecentlyAccessed, `
            MostRecentlyAccessed, `
            QuotaUsage `
        -PropertyName 'Confidentiality'
    ````

1. Start the storage report.

    ````powershell
    Start-FsrmStorageReport -Name $name
    ````

1. Under **Confirm**, enter **y**.
1. Retrieve the status of the storage report.

    ````powershell
    Get-FsrmStorageReport -Name $name
    ````

1. Repeat the last step until the **Status** changes from **Queued** to **NotRunning**.

1. Exit the remote PowerShell Session.

    ````powershell
    Exit-PSSession
    ````

### Task 2: Review the reports

Perform this task on CL1.

1. In **File Explorer**, navigate to **\\\\VN1-FS1\\IT\\StorageReports\\Interactive**.
1. Review each report.

    > Which files are confidential, secret, or contain PII?

    > The file screen audit report does not list anything, because there are no violations of file screens.

    > What are the largest files?

## Exercise 6: Create a file management task

1. Create a file management task to archive files with any confidentiality level 365 days after they have been last modified to D:\Archive.
1. Run the file management task
1. Review the results of the file management task

### Task 1: Create a file management task

Note: In real world, it would be recommended to configure notifications. Because notifications would cause the files to not being expired for some time, notifications will not be used in this lab. However, you may want to review the settings for notifications.

#### Desktop experience

Perform this task on CL1.

1. Open **File Server Resource Manager**.
1. In File Server Resource Manager, in the left pane, in the context menu of **File Server Resource Manager**, click **Connect to Another Computer...**
1. In Connect to Another Computer, click **Another computer**, type **VN1-FS1**, and click **OK**.
1. In **File Server Resource Manager**, in the left pane, click **File Management Tasks**.
1. In the context-menu of **File Management Tasks**, click **Create File Management Tasks...**
1. In Create File Management Task, on the tab General, under **Task name**, type **Expire files with confidentiality level after 365 days** and click the tab **Scope**.
1. On the tab Scope, activate the checkboxes **Group Files** and **User Files**, and click the tab **Action**
1. On the tab Action, in **Type**, click **File expiration**.
1. Beside **Expiration directory**, click **Browse...**
1. In Browse for Folder, click **D$**, click **Make New Folder**, type **Archive**, and click **OK**.
1. In **Create File Management Task**, click the tab **Condition**.
1. On the tab Condition, under **Property conditions**, click **Add...**.
1. In Property Condition, under **Property**, click **Confidentiality**, under **Operator**, click **Exist** and click **OK**.
1. In **Create File Management Task**, activate the checkbox **Days since file was last modified** and type **365**, then click the tab **Schedule**.
1. On the tab Schedule, in **Run at**, enter **07:00:00**, activate the checkbox **Saturday**, and click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Open a remote PowerShell session to VN1-FS1.

    ````powershell
    Enter-PSSession VN1-FS1
    ````

1. Create the folder **D:\\Archive**

    ````powershell
    $expirationFolder = 'D:\Archive'
    New-Item -Type Directory -Path $expirationFolder
    ````

1. Create a schedule for **every Saturday** at **7:00**.

    ````powershell
    $schedule = New-FsrmScheduledTask -Weekly Saturday -Time 07:00:00
    ````

1. Create the action to expire files to **D:\\Archive**.

    ````powershell
    $action = New-FsrmFmjAction `
        -Type Expiration `
        -ExpirationFolder $expirationFolder
    ````

1. Create the conditions for the task, that **Confidentiality** must exist and the file was modified at least 365 days before.

    ````powershell
    $condition =@(
        (New-FsrmFmjCondition -Property 'Confidentiality' -Condition Exist)
        (
            New-FsrmFmjCondition `
                -Property File.DateLastModified `
                -Condition LessThan `
                -Value 'Date.Now'
        )
    )
    ````

1. Create the file management task with the name **Expire files with confidentiality level after 365 days** for user and group files with the condition, action, and schedule from the previous steps. The report format should be DHTLM. Log information and error events.

    ````powershell
    New-FsrmFileManagementJob `
        -Name 'Expire files with confidentiality level after 365 days' `
        -Namespace `
            @('[FolderUsage_MS=User Files]', '[FolderUsage_MS=Group Files]') `
        -Condition $condition `
        -Action $action `
        -ReportFormat DHtml `
        -ReportLog Information, Error `
        -Schedule $schedule
    ````

1. Exit the remote PowerShell Session.

    ````powershell
    Exit-PSSession
    ````

### Task 2: Run the file management task

#### Desktop experience

Perform this task on CL1.

1. Open **File Server Resource Manager**.
1. In File Server Resource Manager, in the left pane, in the context menu of **File Server Resource Manager**, click **Connect to Another Computer...**
1. In Connect to Another Computer, click **Another computer**, type **VN1-FS1**, and click **OK**.
1. In **File Server Resource Manager**, in the left pane, click **File Management Tasks**.
1. In the context-menu of **Expire files with confidentiality level after 365 days**, click **Run File Management Task Now...**.
1. In Run File Management Task, ensure **Run the task in the background (recommended)** is selected and click **OK**.

Wait until the column **Last Run Time** changes to the current date and time.

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Open a remote PowerShell session to VN1-FS1.

    ````powershell
    Enter-PSSession VN1-FS1
    ````

1. Start the file management task **Expire files with confidentiality level after 365 days**.

    ````powershell
    $name = 'Expire files with confidentiality level after 365 days'
    Start-FsrmFileManagementJob -Name $name
    ````

1. Under **Confirm**, enter **y**.
1. Retrieve the status of the file management task.

    ````powershell
    Get-FsrmFileManagementJob -Name $name
    ````

    Repeat this step until **LastRun** changes to the current date and time.

1. Exit the remote PowerShell Session.

    ````powershell
    Exit-PSSession
    ````

### Task 3: Review the results of the file management task

Perform this task on CL1.

1. In **File Explorer**, navigate to **\\\\VN1-FS1\\IT\\StorageReports\\Interactive**.
1. Find the latest HTML file with a name starting with **FileManagement-Expire files with confidentiality level after 365 days** and open it.

    > Which files expired?

1. In **File Explorer**, navigate to **\\\\VN1-FS1\\D$\\Archive** and review the folders and files.

[figure 1]:/images/Access-Denied-Assistance-Message.png
[figure 2]:/images/Access-Denied-Assistance-Message-folder.png