# Practice: Configure password and account lockout policies

## Required VMs

* VN1-SRV5
* CL1

If you did not complete the lab [Deploying domain controllers](Deploying-domain-controllers.md), in addition to the VMs above, **VN1-SRV1** is required. If VN1-SRV1 is already shut down after the lab, do not start it.

## Setup

If you skipped the practice [Install Remote Server Administration Tools](Practices/Install-Remote-Server-Administration-Tools.md), on **CL1**, in **Terminal**, execute ````C:\LabResources\Solutions\Install-RemoteServerAdministrationTools.ps1````.

## Task

For the ad.adatum.com domain, configure the password policy to require passwords of at least 8 character in length. The passwords should not need to be complex and should not need to be changed regularly.

Configure a password lockout policy after 100 invalid attempts in 10 minutes. The account should be locked out for 10 minutes. Exclude the default administrator from the lockout policy.

## Instructions

Perform these steps on CL1.

1. Sign in as **Administrator@ad.adatum.com**.
1. Open **Group Policy Management**.
1. In Group Policy Management, expand **Forest: ad.adatum.com**, **Domains**, and click **ad.adatum.com**.
1. In the context-menu of **ad.adatum.com**, click **Create a GPO in this domain, and Link it here...**
1. In New GPO, under **Name**, type **Custom Domain Account Policies** and click **OK**.
1. In **Group Policy Management**, in the context-menu of **Custom Domain Account Policies**, click **Edit...**
1. In Group Policy Management Editor, under **Computer Configuration**, expand **Policies**, **Windows Settings**, **Security Settings**, **Account Policies** and click **Password Policy**.
1. In Password Policy, double-click **Maximum password age**.
1. In Maximum password age Properties, activate **Define this policy setting**. Under **Password will expire in**, type **0** and click **OK**.
1. In Suggested Value Changes, click **OK**.
1. In **Group Policy Management Editor**, in **Password Policy**, double-click **Minimum password age**.
1. In Minimum password age Properties, ensure **Define this policy setting** is activated. Under **Password can be changed after**, type **0** and click **OK**.
1. In **Group Policy Management Editor**, in **Password Policy**, double-click **Minimum password length**.
1. In Minimum password length Properties, activate **Define this policy setting**. Under **No password required**, type **8** (the label will change to **Password must be at least**) and click **OK**.
1. In **Group Policy Management Editor**, in **Password Policy**, double-click **Password must meet complexity requirements**.
1. In Password must meet complexity requirements Properties, activate **Define this policy setting**, ensure **Disabled** is selected, and click **OK**.
1. In **Group Policy Management Editor**, click **Account Lockout**.
1. In Account Lockout, double-click **Account lockout threshold**.
1. In Account lockout threshold Properties, activate **Define this policy setting**. Under **Account will lock out after**, type **100** and click **OK**.
1. In Suggested Value Changes, click **OK**.
1. In **Group Policy Management Editor**, in **Account Lockout**, double-click **Allow Administrator account lockout**.
1. In Allow Administrator account lockout Properties, ensure **Define this policy setting** is activated, click **Disabled**, and click **OK**.
1. Close **Group Policy Management Editor**.
1. In **Group Policy Management**, in **ad.adatum.com**, click the tab **Linked Group Policy Objects**.
1. On the tab Linked Group Policy Objects, click **Custom Domain Account Policies** and to the left, click the icon *Move link up*.
1. In the left pane, in the context menu of **Domain Controllers**, click **Group Policy Update...**
1. In Force Group Policy update, click **Yes**.
1. In Remote Group Policy update results, ensure the update has succeeded on all computers and click **Close**.
1. Sign out.
1. Sign as **Dita@ad.adatum.com**.
1. If you are not prompted to change the password at sign in, invoke CTRL+ALT+DEL (you might use the menu on the virtual machine connection) and click **Change a password**.
1. Try to set a password shorter than 8 characters.

    > You should receive a message, that the pasword does not meet the requirements of the domain.

1. Try to set a non-complex passwords with 8 character at least.

    > The password should be accepted.

1. Sign out.
