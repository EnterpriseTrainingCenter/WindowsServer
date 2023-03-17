# Lab: Group Policies Management

## Required VMs

* VN1-SRV1
* CL1
* CL2

## Setup

1. On **CL1**, sign in as **ad\Administrator**.
1. On **CL2**, sign in as **ad\Administrator**.

## Introduction

Adatum wants to enable remote management for all computers in the domain without configuring each computer individually. Moreover, the Microsoft Store should be disabled for users in the organizational unit Marketing. You should use Group Policies to accomplish these requirements. Furthermore, you should create a report proofing the correct group policy settings are applied.

## Exercises

1. [Configure Windows Defender Firewall rules for remote administration](#exercise-1-configure-windows-defender-firewall-rules-for-remote-administration)
1. [Disable the Microsoft Store](#exercise-2-disable-the-microsoft-store)
1. [View Group Policy Results](#exercise-3-view-group-policy-results)

## Exercise 1: Configure Windows Defender Firewall rules for remote administration

1. [Create a group policies object](#task-1-create-a-group-policies-object)
1. [Create firewall rules for remote management](#task-2-create-firewall-rules-for-remote-management) in the group policies object
1. [Link the GPO](#task-3-link-the-gpo) to the domain root
1. [Verify GPO application](#task-4-verify-gpo-application) on CL2

    > Are the firewall rules for remote management enabled?

    > Can you disable the firewall rules configured by the GPO?

### Task 1: Create a group policies object

Perform this task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Forest: ad.adatum.com**, **Domains**, **ad.adatum.com**, and click **Group Policy Objects**.
1. In the context-menu of **Group Policy Objects**, click **New**.
1. In New GPO, under **Name**, type **Custom Computer Security Windows Defender Firewall remote administration** and click **OK**.

### Task 2: Create firewall rules for remote management

Perform this task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Forest: ad.adatum.com**, **Domains**, **ad.adatum.com**, and click **Group Policy Objects**.
1. In **Group Policy Management**, in the context-menu of **Custom Computer Security Windows Defender Firewall remote administration**, click **Edit**.
1. In Group Policy Management Editor, expand **Computer Configuration**, **Policies**, **Windows Settings**, **Security Settings**, **Windows Defender Firewall with Advanced Security**, **Windows Defender Firewall with Advanced Security - LDAP://...**, and click **Inbound Rules**.
1. In the context-menu of Inbound Rules, click **New Rule...**
1. In New Inbound Rule Wizard, on page Rule Type, click **Predefined**, and, below, click **Remote Event Log Management**, and click **Next >**.
1. On page Predefined rules, deactivate all rules with the value **Private, Public** in the column **Profile**. Click **Next >**.
1. On page Action, ensure **Allow the connection** is selected and click **Finish**.

    Repeat steps 5 - 8 for the predefined rules:

    * Remote Scheduled Tasks Management
    * Remote Service Management
    * Remote Shutdown
    * Remote Volume Management

### Task 3: Link the GPO

Perform this task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Forest: ad.adatum.com**, **Domains**, **ad.adatum.com**, and click **ad.adatum.com**.
1. In the right pane, click the tab **Linked Group Policy Objects**.
1. In the context-menu of **ad.adatum.com**, click **Link an Existing GPO...**
1. In Select GPO, click **Custom Computer Security Windows Defender Firewall remote administration** and click **OK**.

### Task 4: Verify GPO application

Perform this task on CL2.

1. Open **Terminal**.
1. In Terminal, refresh the group policies.

    ````shell
    gpupdate.exe
    ````

1. Open **Windows Defender Firewall with Advanced Security**.
1. In Windows Defender Firewall with Advanced Security, click **Inbound Rules**.

    > The following rules are enabled for die **Profile** **Domain**.
    >
    >   * Inbound Rule for Remote Shutdown (RPC-Ep-In)
    >   * Inbound Rule for Remote Shutdown (TCP-In)
    >   * Remote Event Log Management (NP-In)
    >   * Remote Event Log Management (RPC)
    >   * Remote Event Log Management (RPC-EPMAP)
    >   * Remote Scheduled Tasks Management (RPC)
    >   * Remote Scheduled Tasks Management (RPC-EPMAP)
    >   * Remote Service Management (NP-In)
    >   * Remote Service Management (RPC)
    >   * Remote Service Management (RPC-EPMAP)
    >   * Remote Volume Management - Virtual Disk Service (RPC)
    >   * Remote Volume Management - Virtual Disk Service Loader  (RPC)
    >   * Remote Volume Management (RPC-EPMAP)

1. Double-click on one of the rules listed in the previous step.

    > At the top of the tab **General**, the message **This rule has been applied by the system administrator and cannot be modified** should appear. Furthermore, the checkbox **Enabled** is activated and disabled.

1. Click **Cancel**.
1. Sign out.

## Exercise 2: Disable the Microsoft Store

1. [Create and link a group policies object](#task-1-create-and-link-a-group-policies-object) to the organizational unit Marketing
1. [Disable the store](#task-2-disable-the-store) in the GPO linked to Marketing
1. [Verify GPO application](#task-3-verify-gpo-application)

    > Can Bill open the store and why?

    > Can Pia open the store and why?

### Task 1: Create and link a group policies object

Perform this task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Forest: ad.adatum.com**, **Domains**, **ad.adatum.com**, and click **Marketing**.
1. In the context-menu of **Marketing**, click **Create a GPO in this domain, and Link it here...**.
1. In New GPO, under **Name**, type **Custom User Store Disable** and click **OK**.

### Task 2: Disable the store

Perform this task on CL1.

1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Forest: ad.adatum.com**, **Domains**, **ad.adatum.com**.
1. In the context-menu of **Custom User Store Disable**, click **Edit...**
1. In Group Policy Management Editor, expand **User Configuration**, **Policies**, **Administrative Templates**, **Windows Components**, and click **Store**.
1. In the right pane, double-click **Turn off the Store application**.
1. In Turn off the Store application, click **Enabled** and click **OK**.

### Task 3: Verify GPO application

Perform this task on CL2.

1. Sign in as **ad\Bill**.
1. Open **Microsoft Store**.

    You should see a message that Microsoft Store is blocked.

    > The store is blocked, because Bill is a user in the Marketing organizational unit.

1. Sign out.
1. Sign in as **ad\Pia**.
1. Open **Microsoft Store**.

    The store should be shown without any restrictions.

    > The store is not blocked, because Pia is a user in the Managers organizational unit.

## Exercise 3: View Group Policy Results

[Create and examine a group policy results report](#task-create-and-examine-a-group-policy-results-report) for AD\Bill on AD\CL2.

### Task: Create and examine a group policy results report

Perform this task on CL1.

1. Open **Group Policy management**.
1. In Group Policy Management, expand **Forest: ad.adatum.com** and click **Group Policy Results**.
1. In the context-menu of **Group Policy Results**, click **Group Policy Results Wizard...**.
1. In Group Policy Results Wizard, on page Welcome to the Group Policy Results Wizard, click **Next >**.
1. On page Computer Selection, click **Another computer** and, below, type **CL2**. Click **Next >**.
1. On page User Selection, click **AD\Bill** and click **Next >**.
1. On page Summary of Selections, click **Next >**.
1. On page Completing the Group Policy Results Wizard, click **Finish**.

    After a few seconds, you should see a report of the group policies applied to AD\bill on AD\CL2. Verify all sections of the report.

1. Click the tab **Policy Events**.
1. On the tab Policy Events, double click on events you are interested in and read the description.
