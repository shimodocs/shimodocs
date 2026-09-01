# Advanced Settings

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## Feature Overview

Advanced settings are used to manage the system's custom `pd-config` directly through YAML. This is suitable for handling advanced parameters or batch configurations that are not provided by the standard settings page.

The system merges custom configurations with factory default configurations. Custom values at the same path will override default values, while unspecified configurations will continue to use factory defaults.

## Accessing the Page

After logging into the admin backend, select **Advanced Settings** in the left navigation to access this page.

Advanced settings are only open to administrators. This page may affect the entire system, so it should be operated by personnel familiar with the MDP configuration structure.

## Page Description

The page is divided into two parts:

- **Factory Default pd-config**: The default configuration provided by the installation package, read-only.
- **Custom pd-config**: Currently saved customer custom configurations, which can be edited.

Custom configurations do not need to copy all default content; generally, only the configuration items that need to be overridden or added are retained.

## Modify and Publish Configuration

It is recommended to follow these steps:

1. Click **Refresh** to ensure the latest custom configuration is loaded.
2. Compare it with the factory default configuration on the left, and edit the content on the right in YAML.
3. Click **Publish**.
4. In the difference confirmation window, check the added, deleted, and modified content.
5. Use the previous/next difference buttons to check changes item by item.
6. After confirming there are no errors, click **Confirm Publish**.

After the publish is successful, the system will create a configuration application task and open the task log in a new window. Depending on the changes and system settings, relevant services may automatically restart.

## Configuration History

Click **History** to view previously published custom configurations, including record ID, creation time, and MD5.

- Click **View** to see the full YAML version history.
- After selecting two records, you can perform a difference comparison.

The current page does not provide a one-click restore button. To restore historical configurations, check the corresponding version, verify the content, manually copy it into the editing area, and republish.

## Notes

- YAML syntax must remain correct; pay attention to indentation, colons, and data types.
- Do not delete configuration items you do not understand casually.
- Before publishing, thoroughly check the differences to avoid overwriting changes recently submitted by other administrators.
- It is recommended to perform important changes during off-peak hours and to record the original configuration in advance.
- After publishing, check the task logs to ensure configuration application and service status checks have been completed.

## Common Situations

- **Publishing Failed**: Please check YAML format, field names, and configuration value types.
- **Service restart after deployment**: Configuration changes may require restarting related services, which is normal.
- **Pages temporarily inaccessible after deployment**: MDP or related services may be restarting; please wait a moment and refresh.
- **Configuration does not achieve the expected effect**: Please confirm whether the configuration path is correct, and check the final merged results and task logs.
- **Incorrect configuration modification**: Find the correct version from the history, copy the content, and redeploy.

> Advanced settings will affect system-level configuration and service operation. Do not directly deploy unverified configurations to the production environment.

## Example of the operation interface

The figure below shows the comparison editing interface between factory default configuration and custom configuration.

