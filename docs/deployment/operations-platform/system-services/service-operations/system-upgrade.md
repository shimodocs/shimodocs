# System Upgrade

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## Feature Overview

System upgrades are used to upload and apply new MDP installation packages. Before the official upgrade, the system will automatically check the upgrade package and the current environment, and display the configuration and service changes involved in this upgrade, helping you complete the version upgrade or routine maintenance.

This page also keeps upgrade history records, making it easy to view past upgrade records, execution status, and related logs.

**Note**: Major version upgrades may update the database schema. The upgrade process involves configuration changes, service restarts, and functional interface changes, which may affect user experience. It should be carried out during non-peak business hours.

## Accessing the Page

After logging into the management backend, select **System Upgrade** from the left navigation to enter the page.

System upgrades are only available to administrators. If you cannot see this menu, please contact the system administrator to confirm the permissions of your current account.

## Preparation Before Upgrade

Before starting the upgrade, it is recommended to confirm the following items:

- Use the upgrade package provided officially that matches the current product type and deployment method.
- The upgrade package is in `.tar.gz` format. Do not unzip or modify the files inside the package.
- It is recommended to perform the upgrade during non-peak business hours or maintenance windows.
- Ensure that the current services are running normally and notify the relevant users in advance.
- If the upgrade involves a license change, prepare the new license content in advance.

## Upgrade Steps

### 1. Upload the Upgrade Package

Click the upload area on the system upgrade page, or drag the upgrade package into the upload area. Once the upload is complete, the system will automatically parse and validate the upgrade package.

Validation mainly includes:

- The format and integrity of the installation package.
- Whether the installation package signature is valid.
- The version of the upgrade package and the upgrade plan.
- Whether the product type and deployment architecture match.
- Whether the current license supports this upgrade.

Validation results are divided into the following statuses:

- **Pass**: Verification is normal, you can continue.  
- **Change**: There are expected changes in this upgrade; please confirm the content before proceeding.  
- **Mismatch**: There is an issue preventing the upgrade; you need to replace the upgrade package or address the related configuration before re-uploading.  

### 2. Enter License

If the system determines that this upgrade requires a license update, the page will prompt you with the products that need updating and display information such as the current server machine code.  

After pasting the new license content, click **Verify and Temporarily Save**. The upgrade can only continue if the license is successfully verified. The temporarily saved license will automatically take effect after the upgrade is successfully applied. For reference regarding licenses, please see [License Management].  

If the page indicates that a license update is not required, you can proceed directly to the next step.  

### 3. Confirm Upgrade Package Content

The page will display the configuration files and service resources in the upgrade package. You can select specific files to view their content and confirm that the upgrade package aligns with the current upgrade target.

### 4. Confirm Changes

The system will compare the current environment with the upgrade package and display the resources that will be added, modified, deleted, or restarted during this upgrade.

Please pay special attention to confirming:

- Whether there are any unexpected resource deletions.
- Whether any important services need to be restarted.
- Whether the changes in the configuration files meet expectations.

### 5. Apply Updates

After confirming the above information is correct, click **Confirm to Start Update**. The system will create a pre-upgrade snapshot and begin applying the update package.

During the upgrade, the page will continuously display execution logs, including resource updates, service restarts, and status checks. When certain components restart, the management page may be temporarily inaccessible. Please wait a moment and then reopen the page to check the progress.

If execution fails, you can troubleshoot based on the logs and then click **Reapply**.

### 6. Complete Upgrade

After the upgrade task has been successfully executed, click **Complete** to finish the upgrade process.

The upgrade completion page will display the name and version of the upgrade package and provide the following actions:

- **View Execution Log**: View the full process of this upgrade.
- **Rollback to Pre-Upgrade Version**: Enter the snapshot from before the upgrade and follow the on-page instructions to perform the rollback.
- **Return to Application Update**: Go back to the system upgrade homepage.

## Upgrade History

The bottom of the system upgrade homepage will display the upgrade history, including the upgrade package name, version, creation time, and execution status.

Click the upgrade record to re-enter the corresponding process and view upgrade progress or historical execution results.

## Common Situations

- **Upgrade Package Verification Failed**: Please confirm the upgrade package's source, file integrity, product type, and deployment architecture are correct.
- **Version Mismatch**: Check the current system version and upgrade package version to ensure the correct upgrade path is used.
- **License Update Required**: Obtain a new license compatible with the target version and current running environment, validate it, and temporarily save it before continuing.
- **Upgrade process page temporarily unavailable**: This MDP service may be updating or restarting. Please wait and refresh the page.
- **Upgrade task failed**: Check the execution log to determine the cause; after resolving the issue, use **Reapply**.
- **Service abnormal after upgrade**: First, check the execution log and service status; if needed, you can roll back using a pre-upgrade snapshot.

> System upgrades will modify service configurations and may trigger service restarts. Please proceed only after confirming the upgrade package and changes are correct.
