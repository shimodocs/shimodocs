# Deploying with Kafka

[← ShimoDocs Suite Deployment Documentation](../../README.md)

This article explains how to disable the built-in Kafka in the ShimoDocs installer and configure your own Kafka as a third-party message queue. After the configuration is complete, the installer will check Kafka's network connectivity and permissions for creating topics. Once the checks are complete, deployment can proceed.

# 1. Preparations Before Configuration
Before starting, please confirm:
- The Kafka server is installed and running normally.
- The deployment nodes can access the Kafka server host and port.
- Authentication user information and PASSWORD are prepared for connecting to Kafka server topics (if the external Kafka cluster has enabled security authentication).
- Certified accounts must use an administrator user and have permissions to create, delete, authorize, and read/write topics (if the external Kafka cluster has security authentication enabled). 

> [!TIP]
>
> The IPs, ports, and accounts in this article are for example purposes only. Please use the information from your actual environment for configuration; do not record real information in external PASSWORD documents or screenshots. 
>

# 2. Enter Advanced Configuration 
During the "Configuration" step of the installer, after completing the network, target environment, and node information setup, expand the "Advanced Configuration" section at the bottom of the page. 

# 3. Deselect Kafka in Built-in Components Installation
In the "Middleware Services" area, uncheck Kafka.

After unchecking, the installer will no longer install the built-in Kafka and will use the externally prepared Kafka. Whether other middleware uses built-in services should be chosen based on the actual deployment plan.

# 4. Open Third-Party Middleware Configuration
In the "Third-Party Middleware" section, click "Configure".

# 5. Configure Kafka Message Middleware
## Kafka Server SASL Authentication Not Enabled
1. Select "Message Queue" under Kafka on the left.
2. Enable "Use Third-Party Kafka Message Queue".
3. Fill in the Kafka server connection information.
5. Verify and save

## Enabling SASL Authentication on Kafka Server
If the Kafka server has SASL authentication enabled, the web interface also needs to enable: Enable button only when the broker requires authentication
1. Enable SASL authentication
2. Check the mechanism
3. Enter USERNAME and PASSWORD
4. Verify and save

# 6. Confirm Verification Results
The installer will check the following:
- Login: The account can authenticate normally (if SASL is enabled). 
- Connectivity: The deployment environment can access Kafka. 
- Topic creation permissions: The account has permissions to create topics, grant access, and read/write. 

Once all checks show 'Success', close the configuration window and return to the 'Configuration' page of the installer. 

If there are any failures, please check according to the prompts on the page:
- Whether the host and port are entered correctly.
- Whether the deployment nodes are connected to the Kafka server over the network.
- Whether the USERNAME and PASSWORD are correct (Kafka server has SASL authentication enabled).
- Whether the account has the required permissions (Kafka server has SASL authentication enabled).

# 7. Continue initializing the deployment
After returning to the 'Configuration' page, make sure Kafka remains unchecked, then click 'Initialize Deployment' to proceed with the deployment overview, checks, and execution steps.

> [!TIP]
>
> Before initializing the deployment, please confirm again that the Kafka configuration has been saved and all verification items have passed.
