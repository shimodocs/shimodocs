# Deploying with Redis

[← ShimoDocs Suite Deployment Documentation](../../README.md)

This article explains how to disable the built-in Redis in the ShimoDocs installer and configure the customer's own Redis as a third-party caching database. Once configured, the installer will check Redis network connectivity, connection, authentication, command execution, pub/sub permissions, etc. Once the checks pass, deployment can proceed.

# 1. Preparations Before Configuration
Before starting, please confirm:
- The Redis server has been installed and is running normally.
- The deployment node can access the host and port of the Redis server.
- Authentication user information and PASSWORD used to connect to the Redis server are ready.

> [!TIP]
> 
> The IP, port, and account in this article are examples. Please use actual environment information for configuration, and do not record real passwords in public documents or screenshots.
> 

# 2. Enter Advanced Configuration
In the "Configuration" step of the installer, after completing the network, target environment, and node information settings, expand the "Advanced Configuration" at the bottom of the page.

# 3. Uninstall Built-in Redis
In the "Middleware Services" section, uncheck Redis.

After unchecking, the installer will no longer install the built-in Redis and will use a prepared external Redis instead. Whether to use built-in services for other middleware should be chosen based on the actual deployment plan.

# 4. Open Third-Party Middleware Configuration
In the "Third-Party Middleware" area, click "Configure."

# 5. Configure Redis Cache Middleware
## Single-node Redis server
1. Select the "Cache" on the left side of Redis.
2. Enable "Use Third-Party Redis".
3. Click "Single Node".
4. Enter the host, port, and PASSWORD.
5. Verify and save.

## Redis Server Sentinel Cluster
1. Select "Redis Cache" on the left.
2. Enable "Use Third-Party Redis".
3. Click "Sentinel Cluster".
4. Enter "Master Node Name, SENTINEL PASSWORD, SENTINEL nodes".
5. Verify and save.

# 6. Confirm Verification Results
The installer will check the following:
- Login: The account can authenticate normally.
- Connectivity: The deployment environment can access Redis.
- Permissions: The account has permissions to connect, authenticate, execute commands, and publish/subscribe.

Once all checks show "Success", close the configuration window and return to the installer’s "Configuration" page.

If there are any failures, please check according to the prompts on the page:
- Whether the host and port are filled in correctly.
- Whether the network between the deployment node and ... allows the Redis server to connect.
- Whether the USERNAME and PASSWORD are correct.
- Whether the account has the required permissions (connection and authentication, command permissions, publish/subscribe permissions, etc.).

# 7. Continue initializing the deployment
After returning to the “Configuration” page, make sure Redis remains unchecked, and then click “Initialize Deployment” to continue with the deployment overview, checks, and execution steps.

> [!TIP]
>
> Before initializing the deployment, please confirm again that the Redis configuration has been saved and all validation items have passed.
