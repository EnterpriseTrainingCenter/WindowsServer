# Practice: Configure a fine-grained password policy

## Required VMs

* VN1-SRV5
* CL1

If you did not complete the lab [Deploying domain controllers](Deploying-domain-controllers.md), in addition to the VMs above, **VN1-SRV1** is required. If VN1-SRV1 is already shut down after the lab, do not start it.

## Setup

If you skipped the practice [Install Remote Server Administration Tools](Practices/Install-Remote-Server-Administration-Tools.md), on **CL1**, in **Terminal**, execute ````C:\LabResources\Solutions\Install-RemoteServerAdministrationTools.ps1````.

## Task

In the ad.adatum.com, configure password settings for Domain Admins, that require complex passwords of at least 10 character in length.

## Instructions

Perform these steps on CL1.

1. Sign in as **Administrator@ad.adatum.com**.
1. Open **Active Directory Administrative Center**.
1. In Active Directory Administrative Center, click **ad (local)**.
1. In client (local), double-click **System**.
1. In System, double-click **Password Settings Container**.
1. In Password Settings Container, in the **Tasks** pane, click **New**, **Password Settings**.
1. In Create Password Settings, configure the following settings:

    * **Name**: Domain Administrators Password Settings
    * **Precendence**: 10
    * **Enforce minimum password length**: Activated
    * **Minimum password length (characters)**: 10
    * **Enforce password history**: Activated
    * **Number of passwords remembered**: 24
    * **Password must meet complexity requirements**: Activated
    * **Store password using reversible encryption**: Deactivated
    * **Protect from accidental deletion**: Activated
    * **Enforce minimum password age**: Deactivated
    * **Enforce maximum password age**: Deactivated
    * **Enforce account lockout policy**: Deactivated

1. Under **Directly Applies To**, click **Add...**
1. In Select Users or Groups, in **Enter the object names to select**, type **Domain Admins** and click **OK**.
1. In **Create Password Settings: Domain Administrators Password Settings**, click **OK**.
1. Invoke CTRL+ALT+DEL (you might use the menu on the virtual machine connection) and click **Change a password**.
1. Try to set a password with 8 or 9 letters, e.g. **cabaletta**

    > You should receive a message, that the pasword does not meet the requirements of the domain.

1. Try to set a non-complex password with 10 letters at least, e.g. **puzzlement**

    > You should receive a message, that the pasword does not meet the requirements of the domain.

1. Try to set a complex password with 10 letters at least. Don't forget to take a note.

    > The password should be accepted.

1. Sign out.
