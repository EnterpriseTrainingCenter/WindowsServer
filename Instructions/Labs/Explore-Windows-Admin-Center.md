# Lab: Explore Windows Admin Center

## Required VMs

* VN1-SRV4
* VN1-SRV1
* VN1-SRV6
* VN1-SRV2
* VN1-SRV3
* VN1-SRV5
* VN1-SRV6
* VN1-SRV7
* VN1-SRV8
* VN3-SRV1
* CL1
* CL2

## Setup

On **CL1**, logon as **ad\Administrator**.

## Introduction

After you installed Windows Admin Center, you want to find out, what you can do with it. For this purpose, you create connections to all servers and clients and explore the available administration features. Moreover, you browse through the the available extensions and try to install one.

## Exercises

1. [Connect](#exercise-1-connect)
1. [Manage connections](#exercise-2-manage-connections)
1. [Install extensions](#exercise-3-install-extensions)
1. [Update extensions](#exercise-4-update-extensions)
1. [Explore the available administration features](#exercise-5-explore-the-available-administration-features)
1. [Use tags](#exercise-6-use-tags)

## Exercise 1: Connect

1. [Using Microsoft Edge, navigate to http://admincenter](#task-1-using-microsoft-edge-navigate-to-httpadmincenter).

    > What is the resulting URL?

1. [Using Microsoft Edge, navigate to https://admincenter.ad.adatum.com](#task-2-using-microsoft-edge-navigate-to-httpsadmincenteradadatumcom).

    > Why do you need to enter username and password?

1. [Add https://admincenter.ad.adatum.com to the Intranet zone](#task-3-add-httpsadmincenteradadatumcom-to-the-intranet-zone).

    > How could this change be accomplished for a large group of users?

1. [Verify the effects of adding a site to the Intranet zone](#task-4-verify-the-effects-of-adding-a-site-to-the-intranet-zone).

    > Do you still need to enter username and password when navigating to https://admincenter.ad.adatum.com?

### Task 1: Using Microsoft Edge, navigate to http://admincenter

Perform this task on CL1.

1. On the taskbar, click the **Microsoft Edge** icon.
1. On page **Welcome to Microsoft Edge, the best performing browser on Windows** , click **Start without your data**.
1. On page **We can help you import your browsing data from Google**, click **Continue without this data**.
1. On page **Always have access to your recent browsing data**, click **Don't allow** and **Confirm and continue**.
1. On page **Let's make the web work for you**, click **Don't allow** and **Confirm and start browsing**.
1. On page **Express yourself by customizing Microsoft Edge with themes**, click **Next**.
1. On page **Get to the sites you love right from your Windows taskbar**, click **Finish**.
1. Navigate to http://admincenter.

    > You will be redirected to https://admincenter.

### Task 2: Using Microsoft Edge, navigate to https://admincenter.ad.adatum.com

Perform this task on CL1.

1. In Microsoft Edge, navigate to https://admincenter.ad.adatum.com.
1. In Windows Security, sign in with **ad\Administrator**.

    > You have to enter username and password, because all sites containing a dot are assigned to the Internet zone by default. Moreover, Microsoft Edge does not send the Windows credentials to sites in the Internet zone by default.

### Task 3: Add https://admincenter.ad.adatum.com to the Intranet zone

Perform this task on CL1.

1. Click **Start**, and type **Internet Options**.
1. Click **Internet Options**.
1. In Internet Properties, click the tab **Security**.
1. On tab Security, click **Local intranet**.
1. Click the button **Sites**.
1. In Local intranet, click the button **Advanced**.
1. In Local intranet, under **Add this website to the zone**, enter **https://admincenter.etc.at** and click **Add**.
1. Click **Close**.
1. In Local intranet, click **OK**.
1. in Internet Properties, click **OK**.

> A group policy could be used to add a site or domain to the Intranet zone for a large group of users.

### Task 4: Verify the effects of adding a site to the Intranet zone

Perform this task on CL1.

1. Close all instances of Microsoft Edge.
1. Launch Microsoft Edge again.
1. In Microsoft Edge, navigate to https://admincenter.ad.adatum.com.

> You should not need to enter username or password.

## Exercise 2: Manage connections

[In Windows Admin Center, add connections to VN1-SRV1, VN1-SRV5, VN1-SRV6, VN1-SRV2, and CL2](#task-in-windows-admin-center-add-connections).

### Task: In Windows Admin Center, add connections

Perform this task on CL1.

1. In Windows Admin Center, on the connections page, click **Add**.
1. In the panel **Add or create resources**, under **Server**, click **Add**.
1. On the tab **Add one**, in **Server name**, enter **VN3-SRV1**.
1. After VN3-SRV1 was found, click **Add**.
1. Click **Add**.
1. In the panel **Add or create resources**, under **Windows PCs**, click **Add**.
1. Click the tab **Search Active Directory**.
1. Enter **CL2** and click **Search**.
1. Activate the checkbox left to **CL2.ad.adatum.com** and click **Add**.
1. In the panel **Add or create resources**, under **Server**, click **Add**.
1. Click the tab **Search Active Directory**.
1. Enter **VN1-\*** and click **Search**.
1. Activate the checkbox left to the column header **Name** to select all servers and click **Add**.

## Exercise 3: Install extensions

[Install the Active Directory extension](#task-1-install-the-active-directory-extension).

### Task: Install the Active Directory extension

Perform all tasks on CL1.

1. In Windows Admin Center, click the icon **Settings**.
1. In Settings, click **Extensions**.
1. On tab Available extensions, click **Active Directory** and click **Install**.

## Exercise 4: Update extensions

[Update all installed extensions of Windows Admin Center to the latest version](#task-update-all-installed-extensions-of-windows-admin-center-to-the-latest-version).

### Task: Update all installed extensions of Windows Admin Center to the latest version

Perform this task on CL1.

1. In Windows Admin Center, click the icon **Settings** (the gear icon).
1. In Settings, click **Extensions**.
1. In Extensions, click the tab **Installed extensions**.
1. Click the column **Status** to sort the extensions.
1. Click every extension with the status **Update available** and click **Update**.

## Exercise 5: Explore the available administration features

1. [Explore networks, updates, and the settings for power configuration and Remote Desktop on VN1-SRV1](#task-1-explore-networks-updates-and-the-settings-for-power-configuration-and-remote-desktop-on-VN1-SRV1).
2. [Find installed apps and running processes on VN1-SRV4](#task-2-find-installed-apps-and-running-processes-on-VN1-SRV4).
3. [Explore the registry, the scheduled tasks and the security of VN1-SRV6](#task-3-explore-the-registry-the-scheduled-tasks-and-the-security-of-VN1-SRV6).
4. [Explore apps, features, devices on CL2](#task-4-explore-apps-features-devices-on-cl2).

### Task 1: Explore networks, updates, and the settings for power configuration and Remote Desktop on VN1-SRV1

Perform this task on CL1.

1. In Windows Admin Center, on the connections page, click **VN1-SRV1.ad.adatum.com**. Explore the information on in the Overview tool.
1. In the Tools pane, click **Networks** and explore the available features.
1. In the Tools pane, click **Updates** and explore the available features.
1. In the Tools pane, click **Settings**.
1. In Settings, click **Power configuration** and explore the available features. 
1. In Settings, click **Remote Desktop** and explore the available features.

### Task 2: Find installed apps and running processes on VN1-SRV4

Perform this task on CL1.

1. In Windows Admin Center, click **Windows Admin Center** in the top-left corner to return to the connections page.
1. On the connections page, click **VN1-SRV4.ad.adatum.com**. Explore the information on in the Overview tool.
1. In the Tools pane, click **Installed apps** and explore the available features.
1. In the Tools pane, click **Processes** and explore the available features.

### Task 3: Explore the registry, the scheduled tasks and the security of VN1-SRV6

Perform this task on CL1.

1. In Windows Admin Center, click **Windows Admin Center** in the top-left corner to return to the connections page.
1. On the connections page, click **VN1-SRV6.ad.adatum.com**. Explore the information on in the Overview tool.
1. In the Tools pane, click **Registry** and explore the available features.
1. In the Tools pane, click **Scheduled tasks** and explore the available features.
1. In the Tools pane, click **Security** and explore the available features.

### Task 4: Explore apps, features, devices on CL2

Perform this task on CL1.

1. In Windows Admin Center, click **Windows Admin Center** in the top-left corner to return to the connections page.
1. On the connections page, click **CL2.ad.adatum.com**. Explore the information on in the Overview tool.
1. In the Tools pane, click **Apps & features** and explore the available features.
1. In the Tools pane, click **Devices** and explore the available features.

## Exercise 6: Use tags

1. [Add tags to the connections according to table below](#task-1-add-tags-to-the-connections).

    | Name                    | Tags                                                                 |
    |---------------------| ---------------------------------------------------------------------|
    | VN1-SRV5.ad.adatum.com | core                                                                 |
    | VN1-SRV1.ad.adatum.com   | desktop, dc, gc, domain naming, schema, primary, infrastructure, rid |
    | VN1-SRV6.ad.adatum.com   | desktop, file                                                        |
    | vn1-gw.ad.adatum.com    | core, windows admin center                                           |
    | VN1-SRV2.ad.adatum.com   | desktop, pki                                                         |
    | cl2.ad.adatum.com       | win11, office                                                        |

    > What is the most efficient way to add the tags manually?

1. [Filter the connection list to only show connections with the tag **core**](#task-2-filter-the-connection-list).

    > Which connections are displayed?

### Task 1: Add tags to the connections

Perform this task on CL1.

1. In Windows Admin Center, on the connections page, activate the checkbox left to **VN1-SRV5.ad.adatum.com**.
1. Click **Edit Tags**.
1. In Available tags, click **Add tags**, enter **core** and press ENTER.
1. Click **Save**.
1. Deactivate the checkbox left to **VN1-SRV5.ad.adatum.com**.
1. Activate the checkbox left to **VN1-SRV4.ad.adatum.com**.
1. Click **Edit Tags**.
1. In Available tags, activate **core**.

    > You can reuse tags.

1. Click **Add tags**, enter **Windows Admin Center** (with capitals) and press ENTER.

    > Tags can contain spaces. Moreover, the tags are normalized to lower case.

1. Click **Save**.
1. Dectivate the checkbox left to **VN1-SRV4.ad.adatum.com**.
1. Activate the checkboxes left to **VN1-SRV1.ad.adatum.com**, **VN1-SRV6.ad.adatum.com**, and **VN1-SRV2.ad.adatum.com**.

    > You can add tags to multiple connections in a single operation.

1. Click **Edit Tags**.
1. In Available tags, click **Add tags**, enter **desktop** and press ENTER.
1. Click **Save**.
1. Activate and deactivate the checkbox left to the column header **Name** to clear all selections.
1. Activate the checkbox left to **cl2.ad.adatum.com**.
1. Click **Add tags**, enter **win11, office** and press ENTER.

    > You can create multiple tags in a single operation by separating them with a comma.

1. Click **Save**.
1. Repeat the appropriate steps to add the remaining tags to the connections according to the table below.

    | Name                    | Tags                                                        |
    |---------------------| ------------------------------------------------------------|
    | VN1-SRV1.ad.adatum.com   | dc, gc, domain naming, schema, primary, infrastructure, rid |
    | VN1-SRV6.ad.adatum.com   | file                                                        |
    | VN1-SRV2.ad.adatum.com   | pki                                                         |

### Task 2: Filter the connection list

Perform this task on CL1.

1. In Windows Admin Center, on the connections page, click the icon *Filter*.
1. In Filter connections, activate the checkbox left to **core** and click **Save**.

    > Only the connections **VN1-SRV5.ad.adatum.com** and **VN1-SRV4.ad.adatum.com** should be displayed.

1. Click the icon *Filter* again.
1. In Filter connections, click **Clear filter** and click **Save**.
