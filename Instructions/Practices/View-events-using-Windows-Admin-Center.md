# Practice: View events using Windows Admin Center

## Required VMs

* VN1-SRV1
* VN1-SRV4
* CL1

## Task

On CL1, view the events from the System log on VN1-SRV1 using Windows Admin Center. In the preview version, create a workspace that includes all warning, error, and critical events from the System and Application logs.

## Instructions

Perform these steps on CL1.

1. Logon as **ad\Administrator**.
1. Using Microsoft Edge, navigate to <https://admincenter>.
1. In Windows Admin Center, click **VN1-SRV1.ad.adatum.com**.
1. Connected to VN1-SRV1.ad.adatum.com, under **Tools**, click **Events**.
1. Under Events, **Windows Logs**, click **System**.
1. Click the icon *Filter*.
1. Click the checkbox **Select All** to deactivate all event levels. Activate the checkboxes **Critical**, **Error**, and **Warning** and click **Apply**.
1. Click one of the events and view its description and details.
1. At the top-right, activate **Preview Mode**.
1. Click **Blank workspace**.
1. Click **Add Events**.
1. Click **Select logs** and click **System**.
1. Click **Level**, deactivate the checkboxes **Information** and **Verbose**.
1. Click **Event ID** and make sure **Select All** is activated.
1. At the bottom click **Add Events**.
1. Repeat the steps above to include all critical, error and warning events from the log **Application**.
1. At the top, click **Save**, **Save workspace as**.
1. In **Workspace name**, type **System, Application: Warnings and more** and click **Save workspace**.
1. In the breadcrumb navigation at the top, click **Events**.
1. **System, Application: Warnings and more** to open the  workspace again.

    Note: Due to a bug in the preview version, you might not see events in the Application log. Select the log again to refresh the events.
