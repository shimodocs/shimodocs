# Configuration Center

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## Overview of Functions

The Configuration Center is used to view and modify application configurations for various services. The page displays both the factory template configuration and the currently used configuration, allowing you to easily understand configuration differences and publish changes in a controlled manner.

After a configuration is published, the system will save the modification and can automatically restart related services to apply the new configuration based on your selection.

## Accessing the Page

After logging into the management backend, select **Configuration Center** in the left-hand navigation to access this page.

The Configuration Center is only open to administrators. If you do not see this menu, please contact your system administrator to confirm your account permissions.

## Page Description

The page is mainly divided into three areas:

- **Application and File List**: Displays configurable files by application and supports searching by application name.
- **Factory Template Configuration**: Displays the original configuration provided in the installation package for viewing and reference only.
- **Current Active Configuration**: Displays the configuration currently used in the environment, which can be edited directly.

Configuration files are usually in JSON, YAML, or TOML format. Please maintain correct file syntax and data structure.

## Modifying and Publishing Configuration

It is recommended to follow these steps:

1. On the left side, select the application and configuration file you need to modify.
2. Refer to the factory template and modify the configuration content in the **Effective Configuration** area.
3. After modification, the page will show the status **Modified but Not Published**.
4. Click **Modified but Not Published**, or use `Ctrl S` (Windows) / `Command S` (macOS) to open the confirmation window.
5. Check the field path, modification type, and the new value to be published.
6. Choose whether to enable **Restart related services after publishing configuration** as needed.
7. Click **Publish Configuration** to complete the modifications.

If there are format errors in the configuration, the system will display an error and prevent publishing. Please correct them and try again.

## Change Confirmation

The confirmation window before publishing will show the differences of this modification:

- **Path**: The configuration path that has been changed.
- **Action**: The type of change, such as addition, modification, or deletion.
- **Value**: The new configuration value.

It is recommended to confirm each difference to avoid accidentally deleting configurations or modifying service parameters incorrectly.

## Service Restart

Some configurations only take effect after the service is restarted. By default, the page enables **Restart related services after publishing configuration**. After a successful publish, services related to the application will automatically restart.

If this option is turned off, the configuration will still be published, but the related services may need to be restarted manually later to apply the new settings.

During service restart, related functions may experience brief fluctuations; it is recommended to make important configuration changes during non-peak business hours.

## Common Situations

- **Application Not Found**: Please clear the search criteria or confirm that the target application has been correctly deployed.
- **Profile Cannot Be Loaded**: Please check the service status and the current account permissions before retrying.
- **Configuration Format Error**: Please check the indentation, brackets, quotes, and field formats in JSON, YAML, or TOML.
- **No Changes to Publish**: The actual configuration content has not undergone effective changes, so publishing is not necessary.
- **Changes Did Not Take Effect After Publishing**: Please confirm whether the related service has been restarted. If necessary, manually restart it and verify again.
- **Publishing Failed**: Please check the configuration content or service status according to the page prompts, handle any issues, and then republish.

> Configuration changes may affect service startup and business functions. Please only publish after fully confirming the changes.

## Operation Interface Example

The figure below shows the areas for selecting a profile, viewing configuration content, and editing.

