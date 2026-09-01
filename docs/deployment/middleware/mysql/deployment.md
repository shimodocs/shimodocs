# Deployment with MySQL 8

[← ShimoDocs Suite Deployment Documentation](../../README.md)

This article explains how to disable the built-in MySQL during the ShimoDocs installer and configure your own MySQL as a third-party relational database. Once the configuration is complete, the installer will check database login, network connection, and table creation permissions. After the checks are completed, you can proceed with the deployment.

# 1. Preparations Before Configuration
Before starting, please make sure that:
- MySQL 8.0 is installed and running normally.
- The deployment node can access the MySQL database host and port.
- The database host, port, USERNAME, and PASSWORD are ready.
- The database account has permissions to log in, connect, create tables, and delete tables.

> [!TIP]
> The IPs, ports, and accounts in this article are for example purposes only. Please use your actual environment information for configuration; do not record real information in external PASSWORD documents or screenshots. 
>
# 2. Enter Advanced Configuration
During the “Configuration” step of the installer, after completing the network, target environment, and node information setup, expand the “Advanced Configuration” section at the bottom of the page.

# 3. Uncheck Built-in MySQL Installation
In the “Middleware Services” area, uncheck MySQL.

After unchecking, the installer will no longer install the built-in MySQL and will use the externally prepared MySQL 8.0 later. Whether to use other middleware’s built-in services should be chosen according to the actual deployment plan.

# 4. Open Third-Party Middleware Configuration
In the “Third-Party Middleware” section, click “Configure.”

# 5. Configure MySQL Database
1. Select “Relational Database” on the left side under RDB.
2. Enable "Use third-party relational database".
3. Under "Dialect", select "Standard". MySQL 
4. Fill in the database connection information.
5. Validate and save.

# 6. Confirm validation results
The installer will check the following:

- Login: Whether the database account can log in normally.
- Connectivity: Whether the deployment environment can access the database.
- Table creation permissions: Whether the database account has permissions to create and drop tables.

When all items show "Success", close the configuration window and return to the installer "Configuration" page.

If there is any failure, check according to the prompts on the page:
- Whether the host and port are filled in correctly.
- Whether the network between the deployment node and the database is connected.
- Whether the USERNAME and PASSWORD are correct.
- Whether the database account has the required permissions.

# 7. Continue initializing the deployment
After returning to the "Configuration" page, make sure that if MySQL is still unchecked, click "Initialize Deployment" to continue completing the deployment overview, check, and execution steps.

> [!TIP]
>
> Before initializing the deployment, please confirm again that the MySQL 8.0 configuration has been saved and all validation items have been passed.
