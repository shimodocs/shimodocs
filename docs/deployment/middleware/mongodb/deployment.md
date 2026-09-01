# Deploying with MongoDB

[← ShimoDocs Suite Deployment Documentation](../../README.md)

This article explains how to disable the built-in MongoDB in the ShimoDocs installer and configure a customer-owned MongoDB as a third-party MongoDB document database. Once the configuration is complete, the installer will check the MongoDB network connection, connection, and authentication permissions. Once the checks pass, deployment can continue.

# 1. Preparations Before Configuration
Before starting, please ensure:
- The MongoDB server is installed and running normally.
- The deployment node can access the MongoDB server.
- Connection information and PASSWORD for authenticating the MongoDB server are ready.

> [!TIP] 
> 
> The IP, port, and account in this article are for example purposes only. Please use actual environment information for configuration, and do not record real information in public documents or screenshots. PASSWORD
> 

# 2. Enter Advanced Configuration
In the "Configuration" step of the installer, after completing the network, target environment, and node information configuration, expand the "Advanced Configuration" at the bottom of the page.

# 3. Uncheck Built-in MongoDB Installation
In the "Middleware Services" area, uncheck MongoDB.

After unchecking, the installer will no longer install the built-in MongoDB and will use an externally prepared MongoDB instead. For other middleware, whether to use built-in services should be selected based on the actual deployment plan.

# 4. Open Third-Party Middleware Configuration
In the "Third-Party Middleware" area, click "Configure".

# 5. Configure the MongoDB Document Database
1. Select "Document Database" on the left side of "MongoDB".
2. Enable "Use a third-party MongoDB document database."
3. Enter the host, port, USERNAME, PASSWORD, and connection string override.
4. Verify and save.

> [!WARNING]
>
> Note: If it is a third-party MongoDB, create a dedicated account for this application and follow the "Principle of Least Privilege," meaning the account should only have access to the specified database. It is necessary to assign a user and PASSWORD for each business database.

# 6. Confirm verification results
The installer will check the following:
- Login: The account can authenticate successfully.
- Connectivity: The deployment environment can access MongoDB.
- Permissions: The account has the rights to connect, authenticate, and execute commands.

Once all check items show "Success," close the configuration window and return to the installer’s "Configuration" page.

If there is any failure, check according to the prompts on the page:
- Whether the host and port are correctly filled in.
- Whether the network between the deployment node and ... can connect to the MongoDB server.  
- Whether the USERNAME and PASSWORD are correct.  
- Whether the account has the required permissions (connection and authentication, command permissions, etc.).  

# 7. Continue initializing the deployment  
After returning to the “Configuration” page, make sure MongoDB remains unchecked, then click “Initialize Deployment” to continue completing the deployment overview, checks, and execution steps.  

> [!TIP]  
>  
> Before initializing the deployment, please confirm again that the MongoDB configuration has been saved and all verification items have passed.
