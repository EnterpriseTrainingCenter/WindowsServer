# Practice: Create an Entra ID tenant

## Setup

In your lab host provider, in course AZ-801, under **Module 9. Implementing operational monitoring in hybrid scenarios**, click **Launch Lab**.

Take a note of the credentials for Azure.

## Task

Create a new Microsoft Entra ID tenant.

## Instructions

1. Navigate to <https://portal.azure.com>.
1. Sign in using the credentials for Azure you noted in the setup.
1. Click **Create a resource**.
1. In Create a resource, in **Search services an marketplace**, type **Azure Active Directory**.
1. Click **Azure Active Directory**.
1. Under Azure Active Directory, click **Create**.
1. In the wizard Create a tenant, on page Basics, under **Tenant type**, ensure **Azure Active Directory** is selected and click **Next: Configuration >**.
1. On page Configuration, beside **Organization name**, type **Adatum**. Beside **Initial domain name** type a worldwide unique domain name, e.g. **Adatum*yymmxx***, where yy is the current year, mm is the current month and xx is a unique student number. Leave the field. If you see a message **Already in use by another directory**, type another domain name. Beside **Location**, click your country, e.g. Austria. Click **Review + create**.
1. On page Review + create, click **Create**.
1. In the pane Help us prove you're not a robot, complete the captcha and click **Submit**.

    The Azure Active Directory gets created. This can take a few minutes.

1. At the top, click the *Settings* gear.
1. In Portal settings | Directory + Subscriptions, beside **Adatum**, click **Switch**.
1. In **Search resource, services, and docs**, type and click **Azure Active Directory**.
1. Under Adatum | Overview, click **Users**.
1. Under Users, click **New user**, **Create new user**.
1. Under New user, under **Select template**, ensure **Create user** is selected.
1. Under **Identity** type a **User name** and **Name**. You may use your own name. Ensure, the domain you used to create Azure Active Directory, is selected.
1. Under **Password**, activate **Show Password** and take a note of the password.
1. Under **Group and roles**, beside **Roles**, click **User**.
1. In the pane Directory roles, active **Global administrator** and click **Select**.
1. Click **Create**.

    *Important*: Take a note of the full user name including the domain and the password.
