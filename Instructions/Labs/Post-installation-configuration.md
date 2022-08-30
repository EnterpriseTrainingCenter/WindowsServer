# Lab: Post installation configuration

## Required VMs

* VN1-DC1
* VN1-GW1
* VN1-FS2
* VN1-Core1
* VN1-Core2

## Setup

On **CL1**, logon as **smart\Administrator**.

## Introduction

After the initial setup, Windows Servers require some post-installation configuration like configuring TCP/IP. joining the domain, or setting some management options. Depending on the applications you are planning to install, you might also need the app compatiblity feature on demand.

## Exercises

1. [Configure password, TCP/IP, domain join and time zone on Windows Server with Desktop Experience](#exercise-1-configure-password-tcpip-domain-join-and-time-zone-on-windows-server-with-desktop-experience)
1. [Configure password, TCP/IP, domain join and time zone on Windows Server](#exercise-2-configure-password-tcpip-domain-join-and-time-zone-on-windows-server)
1. [Install App Compatibility Feature on Demand](#exercise-3-install-app-compatibility-feature-on-demand)

## Exercise 1: Configure password, TCP/IP, domain join and time zone on Windows Server with Desktop Experience

1. [Set the default Administrator password](#task-1-set-the-default-administrator-password-on-vn1-fs2)
1. [Configure the Ethernet adapter with the IPv4 address 10.1.1.64/24, default gateway 10.1.1.1 and DNS server 10.1.1.8](#task-2-configure-the-ethernet-adapter-on-vn1-fs2)
1. [Set the computer name to VN1-FS2 and join the domain smart.etc](#task-3-set-the-computer-name-to-vn1-fs2-and-join-the-domain)
1. [Add the VN1-FS2 to Windows Admin Center](#task-4-add-the-VN1-FS2-to-windows-admin-center)

    > Why does single sign-on not work with the new server?

1. [Set the time zone](#task-5-set-the-time-zone-on-vn1-fs2)

### Task 1: Set the default Administrator password on VN1-FS2

Perform this task on VN1-FS2.

1. In Customize settings, enter a secure password in Password and **Reenter password** and click **Finish**.
1. Logon as **Administrator** with the password from the previous step.

### Task 2: Configure the Ethernet adapter on VN1-FS2

#### Desktop experience

Perform this task on VN1-FS2.

1. In **Server Manager**, in the left pane, click **Local Server**.
1. Beside **Ethernet**, click **IPv4 address assigned by DHCP, IPv6 enabled**.
1. In **Network Connections**, in the context-menu of **Ethernet**, click **Properties**.
1. In Ethernet Properties, click **Interent Protocol Version 4 (TCP/IPv4)** and click **Properties**.
1. In Internet Protocol Version 4 (TCP/IPv4) Properties, click **Use the following IP address**.
1. In **IP address**, enter 10.1.1.64.
1. In **Subnet mask**, enter 255.255.255.0.
1. In **Default gateway**, enter 10.1.1.1.
1. In **Preferred DNS server**, enter 10.1.1.8.
1. Click **OK**.
1. In **Ethernet Properties**, click **OK**.
1. Close **Network Connections**.

#### PowerShell

Perform this task on VN1-FS2.

1. Run **Windows PowerShell** as Administrator.
1. Configure the network adapter **Ethernet** with the IP address **10.1.1.64/24** and the default gateway 10.1.1.1.

    ````powershell
    New-NetIPAddress `
        -InterfaceAlias Ethernet `
        -AddressFamily IPv4 `
        -IPAddress 10.1.1.64 `
        -PrefixLength 24 `
        -DefaultGateway 10.1.1.1
    ````

1. Configure the network adapter **Ethernet** to use the DNS server addresss **10.1.1.8**

    ````powershell
    Set-DnsClientServerAddress `
        -InterfaceAlias Ethernet `
        -ServerAddresses 10.1.1.8
    ````

### Task 3: Set the computer name to VN1-FS2 and join the domain

#### Desktop experience

Perform this task on VN1-FS2.

1. In **Server Manager**, in the left pane, click **Local Server**.
1. Click **Workgroup**.
1. In System Properties, click **Change...**.
1. In Computer Name/Domain Change, in **Computer name**, enter **VN1-FS2**.
1. Click **Domain** and enter **smart.etc**.
1. In Windows Security, enter the credentials of **smart\Administrator**.
1. In **Welcome to the smart.etc domain**, click **OK**.
1. In **System Properties**, click **Close**.
1. In **You must restart your computer to apply these change**, click **Restart Now**.

#### Windows Admin Center

Perform this task on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **Add**.
1. In Add or create resources, under **Servers**, click **Add**.
1. On the tab **Add one**, type 10.1.1.64.
1. Click **Use another account for this connection**.
1. Enter the credentials for the default Administrator account and click **Add**.
1. Click **10.1.1.64**.
1. Connected to 10.1.1.64, in Overview, click **Edit Computer ID**.
1. In the pane Edit computer ID, in **Computer name**, enter **VN1-FS2**.
1. Click **Domain** and enter **smart.etc.**.
1. Click **Next**.
1. Under **Current domain**, enter the credentials of **smart\Administrator** and click **Save**.
1. In **Overview**, click **Restart**.
1. In **Restart the computer**, click **Yes**.
1. In Windows Admin Center, activate the checkbox beside **10.1.1.64**.
1. Click **Remove**.

#### PowerShell

Perform this task on VN1-FS2.

1. Run **Windows PowerShell** as Administrator.
1. Rename the computer to **VN1-FS2** and join it to the domain **smart.etc**.

    ````powershell
    Add-Computer -NewName VN1-FS2 -DomainName smart.etc -Restart
    ````

1. Enter the credentials for **smart\Administrator**.

### Task 4: Add the VN1-FS2 to Windows Admin Center

Perform this task von CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **Add**.
1. In Add or create resources, under **Servers**, click **Add**.
1. Click the tab **Search Active Directory**.
1. Enter **VN1-FS2** and click **Search**.
1. Activate the checkbox left to **VN1-FS2.smart.etc** and click **Add**.
1. In Windows Admin Center, click **vn1-fs2.smart.etc.**.

    > A pane **Specify your credentials** opens. Single sign-on does not work, because you did not set up Kerberos constrained delegation for the new server.

1. Click **Cancel**.
1. In Windows Admin Center, click **vn1-dc1.smart.etc**.
1. Connected to vn1-dc1.smart.etc, under Tools, click **Active Directory**.
1. Under Active Directory Domain Services, in **Search Active Directory**, type **VN1-FS2** and click **Search**.
1. In the results, click **VN1-FS2** and click **Properties+*.
1. In Computer properties: VN1-FS2$, click **Add a Windows Admin Center gateway**.
1. In the pane Trust a Windows Admin Center gateway, in **SamAccountName**, enter **VN1-GW1** and click **OK**.
1. On the top-left, click **Windows Admin Center**.
1. In Windows Admin Center, click **vn1-fs2.smart.etc.**.

    > Windows Admin Center should connect to the server without additional credentials. If a pane **Specify your credentials** appears, click **Cancel** and try again.

### Task 5: Set the time zone on VN1-FS2

#### Desktop Experience

Perform this task von VN1-FS2.

1. Logon as **smart\Administrator**.
1. In **Server Manager**, in the left pane, click **Local Server**.
1. In Properties For VN1-FS2, right to **Time zone**, click **(UTC-08:00) Pacific Time (US & Canada)**.
1. In Date and Time, click **Change time zone...**.
1. In Time Zone Settings, under **Time zone**, select your local time zone and click **OK**.
1. In **Date and Time**, click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Set the time zone of VN1-FS2 to the same time zone as CL1.

    ````powershell
    $timezone = Get-TimeZone
    Invoke-Command -ComputerName vn1-fs2 { Set-TimeZone $using:timezone.id }
    ````

## Exercise 2: Configure password, TCP/IP, domain join and time zone on Windows Server

1. [Set the default Administrator password](#task-1-set-the-default-administrator-password-on-vn1-core2)
1. [Configure the Ethernet adapter with the IPv4 address 10.1.1.64/24, default gateway 10.1.1.1 and DNS server 10.1.1.8](#task-2-configure-the-ethernet-adapter-on-vn1-core2)
1. [Set the computer name to VN1-FS2 and join the domain smart.etc](#task-3-set-the-computer-name-to-vn1-core2-and-join-the-domain)
1. [Add the VN1-FS2 to Windows Admin Center](#task-4-add-the-vn1-core2-to-windows-admin-center)

    > Why does single sign-on not work with the new server?

1. [Set the time zone](#task-5-set-the-time-zone-on-vn1-core2)

### Task 1: Set the default Administrator password on VN1-Core2

Perform this task on VN1-Core2.

1. Under **The user's password must be changed before signing in**, use the up/down arrow keys to select **OK** and press ENTER.
1. Under **Enter new credentials for Administrator or hit ESC to cancel**, beside **New password**, enter a secure password and press TAB.
1. Beside **Confirm password**, enter the secure password again and press ENTER.
1. Under **Your password has been changed.** press ENTER.

### Task 2: Configure the Ethernet adapter on VN1-Core2

#### Sconfig

Perform this task on VN1-Core2.

1. In SConfig, enter **8**.
1. In Network settings, enter  **1**.
1. In Network adapter settings, enter **1**.
1. Beside **Select (D)HCP or (S)tatic IP address (Blank=Canel)**, enter **S**.
1. Beside **Enter static IP address (Blan=Cancel)**, enter **10.1.1.80**.
1. Beside **Enter subnet mask (Blank=255.255.255.0)**, press ENTER.
1. Beside **Enter default gateway (Blank=Cancel)**, enter **10.1.1.1**.
1. Under 4 success messages, press ENTER.
1. In **SConfig**, enter **8**.
1. In Network settings, enter  **1**.
1. In Network adapter settings, enter **2**.
1. Beside **Enter new preferred DNS server (Blank=Cancel)**, enter **10.1.1.8**.
1. Beside **Enter alternate DNS server (Blank=Cancel)**, press ENTER.
1. Under a success message, press ENTER.

#### PowerShell

Perform this task on VN1-Core2.

1. In SConfig, enter **15**.
1. Configure the network adapter **Ethernet** with the IP address **10.1.1.64/24** and the default gateway 10.1.1.1.

    ````powershell
    New-NetIPAddress `
        -InterfaceAlias Ethernet `
        -AddressFamily IPv4 `
        -IPAddress 10.1.1.80 `
        -PrefixLength 24 `
        -DefaultGateway 10.1.1.1
    ````

1. Configure the network adapter **Ethernet** to use the DNS server addresss **10.1.1.8**

    ````powershell
    Set-DnsClientServerAddress `
        -InterfaceAlias Ethernet `
        -ServerAddresses 10.1.1.8
    ````

### Task 3: Set the computer name to VN1-Core2 and join the domain

#### SConfig

Perform this task on VN1-Core2.

1. In SConfig, enter **1**.
1. Under **Change domain/workgroup membership**, enter **d**.
1. Beside **Name of domain to join (Blank=Cancel)**, enter **smart.etc**.
1. Beside **Specify an authorized domain\user (Blank=Canel)**, enter **smart\Administrator**.
1. Enter the password for smart\Administrator.
1. After you receive a success message, beside **Do you want to change the computer name befor restarting?**, enter **y**.
1. Beside **Enter new computer name (Blank=Cancel)**, enter **VN1-Core2**.
1. Enter the password for smart\Administrator.
1. Beside **Restart now? (Y)es or (N)o**, enter **y**.

#### Windows Admin Center

Perform this task on CL1.

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **Add**.
1. In Add or create resources, under **Servers**, click **Add**.
1. On the tab **Add one**, type 10.1.1.80.
1. Click **Use another account for this connection**.
1. Enter the credentials for the default Administrator account and click **Add**.
1. Click **10.1.1.80**.
1. Connected to 10.1.1.80, in Overview, click **Edit Computer ID**.
1. In the pane Edit computer ID, in **Computer name**, enter **VN1-CORE2**.
1. Click **Domain** and enter **smart.etc.**.
1. Click **Next**.
1. Under **Current domain**, enter the credentials of **smart\Administrator** and click **Save**.1. In **Overview**, click **Restart**.
1. In Windows Admin Center, activate the checkbox beside **10.1.1.80**.
1. Click **Remove**.

#### PowerShell

Perform this task on VN1-Core2.

1. Ensure, you are on the command line (PowerShell).
1. Rename the computer to **VN1-Core2** and join it to the domain **smart.etc**.

    ````powershell
    Add-Computer -NewName VN1-Core2 -DomainName smart.etc -Restart
    ````

1. Enter the credentials for **smart\Administrator**.

### Task 4: Add the VN1-Core2 to Windows Admin Center

Perform this task von CL1.

1. Open **Windows Terminal**.
1. Configure Kerberos constrained delegation to allow the computer account of **VN1-FS2** to be delegated to the Windows Admin Center gateway **VN1-GW1**.

    ````powershell
    $gateway = Get-ADComputer vn1-gw1
    Set-ADComputer vn1-Core2 -PrincipalsAllowedToDelegateToAccount $gateway
    ````

1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **Add**.
1. In Add or create resources, under **Servers**, click **Add**.
1. Click the tab **Search Active Directory**.
1. Enter **VN1-Core2** and click **Search**.
1. Activate the checkbox left to **VN1-CORE2.smart.etc** and click **Add**.
1. In Windows Admin Center, click **vn1-core2.smart.etc.**.

    > Windows Admin Center should connect to the server without additional credentials. If it does not work initially, wait a minute, refresh the page and try again.

### Task 5: Set the time zone on VN1-Core2

#### SConfig

Perform this task von VN1-Core2.

1. In the menu of **Connection to virtual Computer**, click **View**, **Enhanced Session** (Ansicht, **Erweiterte Sitzung**) to disable the enhanced session mode.
1. In the menu of **Connection to virtual Computer**, click **Action**, **CTRL+ALT+DEL** (**Aktion**, **STRG+ALT+ENTF**).
1. Under **Enter credentials for Administrator or hit ESC to switch users/sign-in methods**, press **ESC**.
1. Under **Select a sign-in option for Administrator or hit ESC to switch users**, press **ESC** again.
1. Under **Select a user**, use the up/down arrow keys to select **Other user** and press ENTER.
1. Under **Enter credentials for Other user or hit ESC to switch users/sign-in methods**, beside **User name**, enter **smart\Administrator** and press TAB.
1. Beside **Password**, enter the password for smart\Administrator and press ENTER.
1. In SConfig, enter **9**.
1. In Date and Time, click **Change time zone...**.
1. In Time Zone Settings, under **Time zone**, select your local time zone and click **OK**.
1. In **Date and Time**, click **OK**.

#### PowerShell

Perform this task on CL1.

1. Open **Windows Terminal**.
1. Set the time zone of VN1-FS2 to the same time zone as CL1.

    ````powershell
    $timezone = Get-TimeZone
    Invoke-Command -ComputerName vn1-core2 { Set-TimeZone $using:timezone.id }
    ````

## Exercise 3: Install App Compatibility Feature on Demand

1. [Install the Application Compatibility Feature on VN1-Core1](#task-2-install-the-application-compatibility-feature)
1. [Install Internet Explorer 11 on VN1-Core1](#task-3-install-internet-explorer-11)
1. [Verify the installation of the App Compatibility Feature](#task-4-verify-the-installation-of-the-app-compatibility-feature)

### Task 1: Install the Application Compatibility Feature

Perform this task on VN1-Core1.

1. Mount the Windows Server Languages and Optional Features ISO image file.

    ````powershell
    $isoPath = 'C:\LabResources\20348.1.210507-1500.fe_release_amd64fre_SERVER_LOF_PACKAGES_OEM.iso'
    $fodIso = Mount-DiskImage –ImagePath $isoPath
    $fodDriveLetter = ($fodIso | Get-Volume).DriveLetter
    ````

1. Install the Application Compatibility Feature.

    ````powershell
    $name = 'ServerCore.AppCompatibility~~~~0.0.1.0'
    $source = "${fodDriveLetter}:\LanguagesAndOptionalFeatures\"
    Add-WindowsCapability -Online -Name $name -Source $source -LimitAccess
    ````

1. Restart the computer.

    ````powershell
    Restart-Computer
    ````

### Task 2: Install Internet Explorer 11

Perform this task on VN1-Core1.

1. Login as **smart\Administrator**.
1. Ensure, you are on the command line (exit SConfig, if necessary).
1. Mount the Windows Server Languages and Optional Features ISO image file.

    ````powershell
    $isoPath = 'C:\LabResources\20348.1.210507-1500.fe_release_amd64fre_SERVER_LOF_PACKAGES_OEM.iso'
    $fodIso = Mount-DiskImage –ImagePath $isoPath
    $fodDriveLetter = ($fodIso | Get-Volume).DriveLetter
    ````

    Tip: These are the same commands as in the previous task. You can use the up/down arrow keys to repeat commands from the previous task.

1. Install Internet Explorer 11.

    ````powershell
    
    $packagePath = `
        "${fodDriveLetter}:\LanguagesAndOptionalFeatures\Microsoft-Windows-InternetExplorer-Optional-Package~31bf3856ad364e35~amd64~~.cab"

    Add-WindowsPackage -Online -PackagePath $packagePath

1. Under **Do you want to restart the computer to complete this operation now?**, enter **Y**.

### Task 3: Verify the installation of the App Compatibility Feature

Perform this task on VN1-Core1.

1. Login as **smart\Administrator**.
1. Ensure, you are on the command line (exit SConfig, if necessary).
1. Try to run the follwoing tools.

    ````powershell
    mmc.exe
    eventvwr.msc
    perfmon.exe
    resmon.exe
    devmgmt.msc
    explorer.exe
    powershell_ise.exe
    diskmgmt.msc
    CluAdmin.msc
    & 'C:\Program Files\Internet Explorer\iexplore.exe'
    ````

1. Close all open graphical applications.