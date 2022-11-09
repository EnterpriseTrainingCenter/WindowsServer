# Practice: Restrict the Administrators group for clients

## Required VMs

* VN1-SRV5
* VN1-SRV7
* CL1
* CL4

## Task

In the clients.ad.adatum.com, create a group Client Computer Administrators and add the Helpdesk group as a member. Restrict the Administrators group of client computer in the domain to contain only the Client Computer Administrators group and the default Administrator only.

## Instructions

Perform these steps on CL1.

1. Sign in as **Administrator@cients.ad.adatum.com**.
1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **clients (local)**.
1. In clients (local), in the **Tasks** pane, under **clients (local)**, click **New**, **Organizational Unit**.
1. In Create Organizational Unit, in **Name**, type **Devices** and click **OK**.
1. In **Active Directory Administrative Center**, in **clients (local)**, double-click **Devices**.
1. In Devices, in the **Tasks** pane, under **Devices**, click **New**, **Organizational Unit**.
1. In Create Organizational Unit, in **Name**, type **Client computers** and click **OK**.
1. In **Active Directory Administrative Center**, click **clients (local)**.
1. In clients (local), double-click **Computers**.
1. In Computers, in the context-menu of **CL4**, click **Move...**
1. In Move, in the middle-pane, click **Devices**, in the right pane, click **Client computers** and click **OK**.
1. In **Active Directory Administrative Center**, click **clients (local)**.
1. In clients (local), double-click **Entitling Groups**.
1. In Entitling Groups, in the **Tasks** pane, under **Entitling Groups**, click **New**, **Group**.
1. In Create Group, in **Group name**, type **Client Computer Administrators**. In **Group type**, ensure **Security** is selected. In **Group scope**, click **Domain local**. Click **Members**.
1. Under Members, click **Add...**.
1. In Select Users, Contacts, Computers, Service Accounts, or Groups, click **Locations...**.
1. In Locations, click **Entire Directory** (or **ad.adatum.com**) and click **OK**.
1. In **Select Users, Contacts, Computers, Service Accounts, or Groups**, under **Enter the object names to select**, type **Helpdesk** and click **OK**.
1. In **Create Group: Client Computer Administrators**, click **OK**.
1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Forest: ad.adatum.com**, **Domains**, **clients.ad.adatum.com**, **Devices**, and click **Client computers**.
1. In the context-menu of **Client computers**, click **Create a GPO in this domain, and Link it here...**
1. In New GPO, unter **Name**, type **Custom Computer Restricted Groups Administrators to Clients Computer Administrators**.
1. In the context-menu of **Custom Computer Restricted Groups Administrators to Clients Computer Administrators**, click **Edit**
1. In Group Policy Management Editor, expand **Computer Configuration**, **Policies**, **Windows Settings**, **Security Settings**, and click **Restricted Groups**.
1. In the context-menu of **Restricted Groups**, click **Add Group...**.
1. In Add Group, click **Browse...**
1. In Select Groups, under **Enter object names to select**, type **Administrators** and click **OK**.
1. In **Add Group**, click **OK**.
1. In Administrators Properties, under **Members of this group**, click **Add...**
1. In Add Member, click **Browse...**
1. In Select Users, Service Accounts, or Groups, cick **Locations**.
1. In Locations, click **Entire Directory** and click **OK**.
1. In **Select Users, Service Accounts, or Groups**, click **Object Types**.
1. In Object Types, activate **Groups** and click **OK**.
1. In **Select Users, Service Accounts, or Groups**, under **Enter the object names to select**, type **Client Computer Administrators** and click **OK**.
1. In **Group Membership**, click **OK**.
1. In **Administrators Properties**, click **OK**.
1. Close **Group Policy Management Editor**.

Perform these steps on CL4.

1. Sign in as **.\Administrator**.
1. In the context-menu of *Start*, click **Windows PowerShell (Admin)**.
1. In Administrator: Windows Powershell, update group policies.

    ````powershell
    gpupdate.exe /force
    ````

1. In the context-menu of *Start*, click **Computer Management**.
1. In Computer Management, expand **Local Users and Groups** and click **Groups**.
1. Double-click **Administrators**.

    > Administrator and CLIENTS\Client Computer Administrators should be Members.

1. In Administrators Properties, click **Cancel**.
1. Sign out.
