# Middleware Check

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## Overview of Functions

Middleware check is used to verify whether system dependencies MySQL, Redis, Elasticsearch, S3, MongoDB, and Kafka can connect and read/write properly, helping you promptly identify underlying service anomalies.

This page supports real-time checks, scheduled checks, recent availability trends, history, as well as failure and recovery notifications.

## Accessing the Page

After logging into the admin backend, select **Middleware Check** in the left navigation to access this page.

Middleware check is only available to administrators. If you cannot see this menu, please contact the system administrator to confirm your account permissions.

## Real-time Check

On the **Default Overview** page, click **Real-time Check**, and the system will perform checks based on the saved check targets.

The check results may include the following statuses:

- **Normal**: The component connection and check operations were successful.
- **Failed**: The component could not be connected, read/write failed, or the response was abnormal.
- **Skipped**: The component is not configured in the current environment, or the check conditions are not met.

Clicking on the component result allows you to view the target address, response time, and error details.

## View Availability Trends

The overview page displays the latest availability of each component based on historical check results. It supports viewing status changes over the past 1 hour, 6 hours, 24 hours, 3 days, 7 days, 14 days, or 30 days.

Hovering over a time period allows you to see the number of checks, average response time, and recent errors during that period.

## Configure Scheduled Checks

On the **Schedules & Alerts** page, you can set:

- **Enable Scheduled Checks**: Once enabled, the system will automatically execute according to the configured interval.
- **Check Interval**: Supports 1 to 1440 minutes. 
- **Historical Retention Days**: Supports 7 to 365 days; setting it to `0` means no automatic cleanup will be performed. 
- **Check Target**: Select the middleware to be checked. 
- **Notification Channel**: Select the channel to receive check notifications. 
- **Notify on Failure**: Send a notification when the overall status changes from normal to abnormal. 
- **Notify on Recovery**: Send a notification when the abnormal status returns to normal.

Changes need to be applied by clicking **Save**. If there is no notification channel yet, please go to the **Notification Channel** page to create and enable a channel first.

## Viewing Check History

On the **History** page, you can view the check time, trigger method, execution duration, and final status.

Trigger methods include manual checks and scheduled checks. Click the record to see detailed results for each component in that check.

## Common Scenarios

- **No inspection records**: You can click **Check Now** first, or enable scheduled checks.
- **Component shows as skipped**: Please confirm that the corresponding middleware is configured and enabled in the system.
- **Check failed**: Check the network, account, connection address, and middleware service status according to the error details.
- **No notification received**: Please confirm that the notification channel is selected and enabled, and check the failure or recovery notification switches.
- **Prompt shows that the check is in progress**: Only one check task can be executed at the same time; please wait for the current task to complete before trying again.

> The check will perform a lightweight connection or read/write check on the middleware; it is recommended to set a reasonable check interval according to the scale of the environment.
