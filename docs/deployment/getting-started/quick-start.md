# Quick Start

[← ShimoDocs Suite Deployment Documentation](../README.md)

> [!TIP]
>
> This article explains how to use `mdp-installer` to quickly deploy a new ShimoDocs Suite environment.
>
> This article is aimed at the **All-in-One single-node online installation** scenario, suitable for first-time installation, product experience, feature verification, and deployment process practice. After completing this article, you can obtain the ShimoDocs Suite business access address and the MDP Operations Platform address.

> The IP addresses, installation package names, versions, and directories shown here are examples only. For an actual deployment, use the values provided with your environment and delivery package.

## 1. Deployment Process Overview

The entire process can be divided into the following 7 steps:

| Step | Actions to Complete | Completion Indicator |
| --- | --- | --- |
| 1. Prepare the server and installation materials | Ensure the server, installer, and ShimoDocs Suite distribution package are available | Able to log in to the server and locate the installation files |
| 2. Start the installer | Run `mdp-installer` on the installation node | Terminal displays the installer access address |
| 3. Upload the distribution package | Select the ShimoDocs Suite distribution package in the browser | The page shows the distribution package has passed verification |
| 4. Configure the deployment environment | Fill in the domain or IP, deployment mode, node login information, and data directory | Node verification successful, deployment overview accessible |
| 5. Environment check | Wait for the installer to check the server and deployment environment | No failures that block the installation |
| 6. Start installation | Confirm the check results and execute the deployment | Page shows installation completed |
| 7. Save Delivery Information | Save the access address and complete login, service, and function verification | Business pages and the MDP operation platform can be accessed |

## 2. Pre-Deployment Preparation

### 1. Prepare Installation Node

The installation node is used to run the installer and serves as the target server for this full-featured single-node deployment.

Before starting, please confirm:

- A usable server is ready and the SERVER_IP address has been obtained.
- The server can be accessed via SSH.
- The SSH user is `root`, or another account with the permissions required to perform deployment tasks.
- The server CPU architecture matches the installer and distribution package; for example, both use `x86_64`.
- The server meets the current deployment specifications; it is recommended to use a minimal installation of Ubuntu 24.04 LTS.
- The root partition and data space meet the current deployment requirements, and the data directory has been determined.
- The server time and timezone are correct, and time synchronization is normal.
- The installation node's `18080/TCP` port can be accessed using a browser from a computer.
- The server can access the internet to download deployment packages and image resources online.
- If business access uses a domain name, the domain name resolution has been completed in advance (optional).

The minimum server requirements are as follows:

| Operating System | Architecture | CPU | Memory | Disk |
| --- | --- | --- | --- | --- |
| Ubuntu 24.04 LTS | `x86_64` | 16 cores | 32 GB | 100 GB SSD |

In addition, please confirm:

- Do not create separate partitions for `/root`, `/var`, or `/tmp`.
- Before installation, do not deploy additional components on the server that may affect the installer checks, such as Docker or other Kubernetes components. 
- Port `22/TCP` is used for SSH, `18080/TCP` is used for the installer web interface, and `80/TCP` and `443/TCP` are used for business access.

> Before formal deployment, it is recommended to confirm the server specifications according to actual concurrency, file size, and availability requirements; the single-node process in this document is suitable for quick deployment and verification. For long-term operation or high availability, please use the corresponding cluster deployment plan.

### 2. Prepare Installation Materials

#### Obtain the Installer

Upload the provided installer to the `/root/` directory on the ShimoDocs installation node. You can choose any of the following methods:

- **Method 1: Upload with an SSH/SCP tool.** Upload `mdp-installer-amd64` to the `/root/` directory on the installation node.

#### Obtain the Distribution Package

Prepare the ShimoDocs Suite distribution package for this deployment. Upload the distribution package to the web installation page; the file name and version should match the actual delivery.

Example file name: `co1.8.20260711.3286-drive-release.tar.gz`

The material list is as follows:

| File | Description |
| --- | --- |
| `mdp-installer` installer | Choose the appropriate file according to the server architecture, for example, `mdp-installer-amd64`. |
| ShimoDocs Suite Distribution Package | The file name and version should match the actual delivered version, for example, `co1.8.20260711.3286-drive-release.tar.gz`. |

It is recommended to place the installer and related installation materials in the same working directory for subsequent retrieval and storage. Before using the distribution package, please ensure the files are complete and not corrupted by transfer tools.

## 3. Start the Installer

### 1. Log in to the Installation Node

Log in to the installation node via SSH and navigate to the directory where the installer is located, for example:

```bash
ssh root@<INSTALL_NODE_IP>
cd /root
```

### 2. Add Execute Permission

If the installer does not yet have execute permissions, run:

```bash
chmod +x ./mdp-installer-amd64
```
The file name in the command needs to be replaced with the actual installer name. 

### 3. Launch the web installation page 

Run:

```bash
./mdp-installer-amd64 server
```

If you need to continue running the installer after exiting the current terminal, you can use:

```bash
nohup ./mdp-installer-amd64 server > nohup.out 2>&1 &
```

After a successful launch, the terminal will display two addresses:

- `Local`: for the installation node itself only.
- `Network`: accessible by other computers on the same network.

If started in background mode, you can run the following command to view the installer output:

```bash
cat nohup.out
```

Open `Network` in your browser and visit the address displayed by the terminal, for example:

```text
http://<INSTALL_NODE_IP>:18080/
```

> During the installation process, please keep the installer process running. Do not close the installer process or stop the current service.

## 4. Upload the ShimoDocs Suite Release Package

### 1. Select the Release Package

After entering the installation page:

1. Click **Start Deployment** or the release package selection entry on the page.
2. Select the ShimoDocs Suite `.tar.gz` release package for this deployment.
3. Wait for the file to upload and complete verification.

### 2. Confirm Verification Results

After verification passes, the page will display the release package name and deployment configuration entries.

Please confirm the following information is correct:

- The package name matches the version delivered this time.
- The release package belongs to the ShimoDocs Suite product.
- The page does not indicate file corruption, format errors, or schema mismatches.

After confirmation, click **Continue** to proceed to deployment configuration.

If the verification fails, please reconfirm whether the distribution package is complete, the file type is correct, and whether the distribution package matches the server's CPU architecture.

## 5. Configure the Deployment Environment

### 1. Confirm Network Address

Check the hostname or IP address displayed on the page. This address should be the installation node address that users can normally access.

Do not enter `127.0.0.1`, and do not use a temporary address that can only be accessed by the current computer. When accessing through a domain name, ensure that the domain has been resolved to the correct service entry point.

### 2. Select Integrated Single-Node Mode

Select **Integrated Single-Node** in the deployment mode or target environment (the actual name displayed on the page is based on the current version).

In this mode, the installation node undertakes both the control and business roles for this deployment, making it suitable for product experience, functional verification, and lightweight single-node planning environments.

### 3. Configure Node SSH

The installer connects to the target node via SSH and executes deployment tasks. Please fill in:

- NODE_IP address
- SSH user, usually `root`.
- SSH port, usually the default value `22`.
- PASSWORD or private key authentication information.

After completion, click **Verify** to confirm that the SSH connection is successful.

> SSH credentials should only be used and stored in controlled environments. Do not write PASSWORD or private keys into public documents, screenshots, or chat records.

### 4. Set Data Directory and Other Configurations

Fill in or confirm the following configurations according to the page prompts:

| Configuration | Description |
| --- | --- |
| ACCESS_DOMAIN / IP | The address for users to access ShimoDocs Suite; when using an IP, please fill in the actual accessible address. |
| Deployment Mode | Select All-in-One single-node mode. |
| Node Data Directory | Used to store deployment data. Please ensure there is sufficient disk space and read/write permissions. |
| Offline Repository | This guide is for online installation; you can keep the default value on the page. |
| Third-Party Middleware | This guide uses the default deployment; if external middleware is required, confirm according to the current delivery requirements. |

If there are no special configuration requirements, the offline repository and third-party middleware can retain the default values. After confirmation, click **Initialize Deployment** at the bottom of the page.

## 6. Confirm Deployment Overview

The deployment overview is used to verify the installation configuration before the official check.

Please pay special attention to the following:

- Ensure the release package version and product name are correct.
- The ACCESS_DOMAIN or IP address is correct and is not `127.0.0.1`.
- Deployment mode is All-in-One single-node.
- NODE_IP, SSH user, and port are correct.  
- The data directory is correct and there is sufficient disk space.  
- Offline repositories and third-party middleware configurations comply with the current environment.  

After confirming that everything is correct, click **Continue** to proceed with the environment check.  

## 7. Execute Environment Check

The installer will check the nodes and deployment environment. The check process may take a few minutes, so please keep the page open.  

### 1. View Node Overview

The node overview shows the check progress, such as SSH connectivity, system and performance, storage and disk, network, and deployment environment.  

To view detailed results for a specific check, click the corresponding check item or detail entry.  

### 2. View Detailed Check Results

Detailed results usually include:  

- SSH connectivity and execution user permissions.  
- Operating system, CPU architecture, and number of cores.  
- Memory capacity, disk space, and directory permissions.  
- Time zone and time synchronization status.  
- Network, image resources, and connectivity with external services.
- There may be residual environmental factors on the server that affect deployment.

### 3. Understanding Check Status

| Status | Meaning | Next Action |
| --- | --- | --- |
| Success | The current check item meets deployment requirements | Continue to wait for other items to complete |
| Warning | Will not directly block deployment, but needs confirmation if it aligns with the current plan | Open details and continue after confirming the impact |
| Failed | The current issue may affect installation or product operation | Fix the issue first, then rescan |
| In Progress | The installer is performing the check | Wait for the check to complete, do not repeat the operation |

If an item stays in “In Progress” for a long time, you can first wait for the current disk or remote check to finish before deciding whether to rescan.

### 4. Handling Warnings and Failures

If the page shows a warning:

1. Open the detailed description of the corresponding check item.
2. Confirm whether the warning aligns with the current deployment plan.
3. If unsure, please save the page and the installer logs, then contact the implementation or operations personnel for confirmation.

If the page fails to display:

1. Follow the prompts to fix SSH, permissions, resources, disk, network, or middleware issues.
2. Click **Rescan**.
3. Confirm that the failed items have disappeared.

After confirming that there are no failed items that block deployment and all warnings have been acknowledged, click **Continue**.

## 8. Start Installation

### 1. Confirm Installation Plan

The page will display the installation plan and the tasks to be executed. After confirming everything is correct, click **Start Deployment**.

A prompt may appear on the page saying "Confirm start installation." Once started, the installation tasks will proceed as planned; if you need to adjust configurations, click **Cancel** to return to the previous step.

### 2. Check Deployment Progress

After initiating the deployment, the page will display the current task status, real-time logs, and execution time. Single-node deployment usually takes about 10 minutes, and the actual time will be affected by server performance and network bandwidth.

During the installation process, please note:

- Do not close the installer process.
- Do not restart the installation node.
- Do not refresh, go back, or resubmit the installation task.
- If the task fails, first check the first error in the corresponding task log and then handle it according to the prompts.

When the page shows **Installation Complete** or enters the **Deployment Delivery** page, it indicates that the installation task has been completed.

## 9. Save Delivery Information

The installation completion page will display the access information and check entries for this deployment. Please complete the following actions immediately:

1. Run post-installation service checks and confirm the results.
2. Use the access information on the deployment delivery page to open the ShimoDocs Suite business page and complete login verification.
3. Record the business access address for ShimoDocs Suite and the MDP operation platform address.
4. Save the initial account and temporary PASSWORD, and immediately change the initial PASSWORD after the first login.
5. Check the status of cluster nodes and applications on the MDP operation platform.

> Delivery information includes access addresses and initial credentials. Do not distribute screenshots, do not upload to public knowledge bases, and do not send through uncontrolled channels.

## 10. Check Deployment Results

After completing the installation, it is recommended to perform acceptance in the following order:

### 1. Check Post-Installation Services

Perform post-installation checks on the installation completion page to confirm that service test cases pass or that the results meet the expectations of the current environment.

If the check fails or partially passes, you can troubleshoot on the MDP operation platform.

### 2. Check the MDP Operation Platform

Log in to the MDP Operations Platform, go to **System Services → Cluster Management**, and confirm that the cluster nodes and applications are running normally.

### 3. Verify ShimoDocs Suite Functions

Log in to the ShimoDocs Suite frontend page and verify at least the following functions:

- Create a test file or suite.
- Edit content and save.
- Export files.
- Import files.

After completing all the above checks, indicate that this quick deployment is complete. If long-term operation, scaling, or high availability is needed in the future, switch to the appropriate deployment solution according to the actual scale, and complete licensing and business configuration.

## 11. Common Issues

### 1. Browser Cannot Open the Installation Page

Check in order:

- Whether the installer process is still running.
- Whether the access address uses the actual IP of the installation node or a resolvable domain name.
- Whether port `18080/TCP` is open.
- Whether the network between the computer running the browser and the installation node is connected.

### 2. Distribution Package Verification Failed

Check:

- Whether the uploaded file is a complete `.tar.gz` release package.
- Whether the file name and product type match this delivery.
- Whether the release package matches the server’s CPU architecture.
- Whether the file was corrupted during upload or transfer.

### 3. SSH Authentication Failed

Check:

- Whether NODE_IP and SSH port are correct.
- Whether the SSH user, PASSWORD, or private key is correct.
- Whether the SSH user has the necessary permissions for deployment.
- Whether firewalls or security groups allow SSH connections.

### 4. Warnings in Environment Check

Warnings will not directly prevent deployment, but you need to open the details to confirm their impact. If it involves disk performance, time synchronization, leftover configurations, or external services, first confirm whether it aligns with the current deployment plan before deciding whether to proceed.

### 5. Failures in Environment Check

Failure items need to be fixed first. Do not bypass the check to start the installation directly. After fixing, click **Rescan** to confirm that the failure items have passed.

### 6. Installation Task Failures

1. Open the execution log of the failed task.
2. Locate the first occurrence of the error message.
3. Save the installer logs, the name of the failed task, and the time it occurred.
4. After addressing the relevant issues with network, disk, image, middleware, or Kubernetes, continue according to the actual recovery method.
