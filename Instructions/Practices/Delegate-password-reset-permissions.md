# Practice: Delegate password reset permissions

## Required VMs

* VN1-SRV5
* VN1-SRV7
* CL1

## Task

The users Dante Dabney, Ida Alksne, Lara Raisic, and Stefan Deboer form the helpdesk at Adatum. They should be granted permissions to reset passwords for users in the organizational unit Marketing.

## Instructions

Perform these steps on CL1.

1. Sign in as **Administrator@ad.adatum.com**.
1. Open **Active Directory Users and Computers**.
1. In Active Directory Users and computers, expand **ad.adatum.com**, and click **IT**.
1. In the context-menu of **IT**, click **New**, **Group**.
1. In New Object - Group, in **Group name**, type **Helpdesk**. Under **Group scope**, ensure **Global** is selected. Under **Group type**, ensure **Security** is selected. Click **OK**.
1. In **Active Directory Users and Computers**, in **IT**, click **Dante Dabney**, hold down CTRL, and click **Ida Alksne**, **Lara Raisic**, and  **Stefan Deboer**.
1. In the **Tasks** pane, under **4 items selected**, click **Add to group...**.
1. In Select Groups, under **Enter the object names to select**, type **Helpdesk** and click **OK**.
1. In the context-menu of **ad.adatum.com**, click **Change Domain...**
1. In Change domain, click **Browse...**
1. In Browse for Domain, expand **ad.adatum.com**, click **clients.ad.adatum.com**, and click **OK**.
1. In **Change domain**, click **OK**.
1. In **Active Directory Users and Computers**, expand **clients.ad.adatum.com**.
1. In the context-menu of **clients.ad.adatum.com**, click **New**, **Organizational Unit**.
1. In New Object - Organizational Unit, in **Name**, type **Entitling Groups** and click **OK**.
1. In **Active Directory Users and Computers**, in the context-menu of **Entitling Groups**, click **New**, **Group**.
1. In New Object - Group, in **Group name**, type **OU Marketing Password Reset**. Under **Group scope**, click **Domain local**. Under **Group type**, ensure **Security** is selected. Click **OK**.
1. In **Active Directory Users and Computers**, in **Entitling Groups**, double-click **OU Marketing Password Reset**.
1. In OU Marketing Password Reset Properties, click the tab **Members**.
1. On tab Members, click **Add...**.
1. In Select Users, Contacts, Computers, Service Accounts, or Groups, click **Locations...**
1. In Locations, click **Entire Directory** and click **OK**.
1. In **Select Users, Contacts, Computers, Service Accounts, or Groups**, under **Enter the object names to select**, type **Helpdesk** and click **OK**.
1. In **OU Marketing Password Reset Properties**, click **OK**.
1. In **Active Directory Users and Computers**, in the context-menu of **Marketing**, click **Delegate Control...**.
1. In Delegation of Control Wizard, on page Welcome to the Delegation of Control Wizard, click **Next >**.
1. On page Users or Groups, click **Add...**.
1. In Select Users, Computers, or Groups, under **Enter the object names to select**, type **OU Marketing Password Reset** and click **OK**.
1. In **Delegation of Control Wizard**, on page **Users or Groups**, click **Next >**.
1. On page Tasks to Delegate, ensure **Delegate the following common tasks** is selected, activate **Reset user passwords and force password change at next logon** and click **Next >**
1. On page Completing the Delegation of Control Wizard, click **Finish**.
1. Sign out.
1. Sign in as **Ida@adatum.com**.
1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, on the menu, click **Manage**, **Add Navigation Nodes...**.
1. In Add Navigation Nodes, in the middle pane, click **Clients**, in the right pane, click **Marketing**, click **>>**, and click **OK**.
1. In **Active Directory Administrative Center**, in the left pane, click **clients-Marketing**.
1. In clients-Marketing, in the context-menu of **Leo Butcher**, click **Properties**.

    > You cannot edit any properties.

1. In **Leo Butcher**, click **Cancel**.
1. In **Active Directory Administrative Center**, in **clients-Marketing**, in the context-menu of **Leo Butcher**, click **Reset password...**.
1. In Reset Password, in **Password** and **Confirm password**, type a secure password and click **OK**.

    > You have reset the password of a user successfully.
