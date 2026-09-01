# Single-Node Kubernetes Deployment

[← ShimoDocs Suite Deployment Documentation](../README.md)

## 1. Applicable Scenarios
- **K8s Single-Node Deployment**:
    - Suitable for lightweight small teams, small-scale use with fewer than 200 people, PoC, demo, feature verification, and short-term testing.
- Only one server is needed, which simultaneously serves as the installation node, K8s master node, and business worker node.
- **Note**
    - For official releases, long-term operation, or future high-availability expansion, it is recommended to use a K8s cluster deployment.

## 2. Deployment Process Overview

| Step | Operation Guide | Completion Indicator |
| --- | --- | --- |
| 1. Check System Environment | Confirm server resources, disk, network, time synchronization, and basic commands | Server meets deployment requirements |
| 2. Prepare installation materials | Obtain the installer and product installation packages; for offline environments, offline image packages are also required | File names matching the CPU architecture |
| 3. Upload installation materials | Upload the installer and installation packages to the deployment nodes | Files have been placed in the designated directory on the server |
| 4. Start the installer | Launch the `mdp-installer` webpage | The installer page can be accessed through a browser |
| 5. Web installation | Select the distribution package, configure nodes, complete environment checks, and start deployment | All installation tasks were successful |
| 6. Post-installation acceptance | Check the cluster, services, login, licenses, and business functions | Core functions can be used normally |

## 3. Preparations before deployment

### 3.1 Prepare server information

| Information | Example | Description |
| --- | --- | --- |
| INSTALL_NODE_IP | `<INSTALL_NODE_IP>` | Single-node K8s deployment uses only 1 server |
| CPU Architecture | `amd64` / `arm64` | The installer and installation package must match the server architecture |
| Network Environment | Online / Offline | Choose online if public network is accessible; choose offline for internal or isolated environments |
| Execution User | `root` or a user with `sudo` privileges | The installer needs to perform deployment tasks via SSH |
| SSH Port | `22` | Fill in the actual port if the SSH port has been changed |
| Access Protocol | HTTP / HTTPS | HTTP can be used for testing environments; HTTPS is recommended for production or external access |
| ACCESS_DOMAIN | `<ACCESS_DOMAIN>` or `<INSTALL_NODE_IP>` | The entry address for users to access ShimoDocs Suite |
| Data Directory | `/data` | It is recommended to use a separate data disk mount |

### 3.2 Prepare Installation Materials

| Material | Example File Name | Description |
| --- | --- | --- |
| Installer | `mdp-installer-amd64` | Suitable for example `amd64` architecture; for other architectures, replace with the actual file name |
| Product Installation Package | `co1.8.20260807.3639-drive-release..tar.gz` | For single-node K8s deployment, choose the distribution package whose file name does not include `k3s`; the file name should be based on actual delivery |
| Basic Offline Image Package | `smbase_image-amd64.tar.gz` | Only required for offline installation |
| Product Offline Image Package | `offline_app_image.tar.gz` | Only required for offline installation, must match the version of the product installation package |

Note:

- The filenames in the commands need to be replaced with the actual filenames, such as `mdp-installer-amd64`, `co1.8.<VERSION>-drive-release.tar.gz`.
- The product installation package, offline image package, and server CPU architecture must be consistent.
- Before offline installation, it is recommended to prepare the basic offline image package and the product offline image package all at once to avoid adding packages during the deployment process.

### 3.3 Check Server Resources

| Item | Recommended Requirement |
| --- | --- |
| Number of Servers | 1 |
| CPU | 16 cores or more |
| Memory | 32 GB or more |
| System Disk | Root directory `/` partition 100 GB or more |
| Data Disk | Independently mounted on `/data`, available space over 300 GB |
| Offline Installation | It is recommended to additionally reserve over 100 GB on the data disk for image packages and temporary extraction files |

Execute on the server: 

```bash
lscpu
free -g
df -h
timedatectl status
```

Confirm the following results:

- CPU, memory, and disk meet the deployment specifications.
- `/data` has been mounted to a separate data disk.
- System time synchronization is normal.
- The server can be accessed via SSH.
- The online installation environment can access the public network; the offline installation environment has prepared the offline image package.

### 3.4 Check Ports

| Port | Purpose |
| --- | --- |
| `22/TCP` | SSH login and execute installation tasks |
| `18080/TCP` | Installer webpage |
| `80/TCP` or `443/TCP` | ShimoDocs Suite access entry |

If the server has a firewall or security group enabled, please open the above ports in advance.

## 4. Upload Installation Tools and Packages

The following example uses the `amd64` architecture. For other architectures, please replace with the actual file name. 

### 4.1 Upload the installer 

Execute on the local computer:

```bash
scp mdp-installer-amd64 root@<INSTALL_NODE_IP>:/root/
```

### 4.2 Upload Offline Image Package

This step can be skipped during online installation.

For offline installation, the offline image package needs to be uploaded to the deployment node:

```bash
scp smbase_image-amd64.tar.gz offline_app_image.tar.gz root@<INSTALL_NODE_IP>:/root/
```

### 4.3 Log in to the Server

```bash
ssh root@<INSTALL_NODE_IP>
```

### 4.4 Add Execution Permission to the Installer

```bash
chmod +x /root/mdp-installer-amd64
```

### 4.5 Launch the Installation Program Webpage

Execute on the server:

```bash
cd /root
./mdp-installer-amd64 server
```

If you want the installer to run in the background, you can use:

```bash
nohup /root/mdp-installer-amd64 server > /root/mdp-installer.log 2>&1 &
```

Browser access: 

```text
http://<INSTALL_NODE_IP>:18080
```

## 5. Install via Web Page

### 5.1 Select Distribution Package

After entering the installer web page, select the product distribution package to be deployed this time.

For K8s single-node deployment, please choose a distribution package whose file name does not include `k3s`, for example:

```text
co1.8.20260807.3639-drive-release.tar.gz
```

### 5.2 Configure SSH Connection

The installer will log in to the deployment node via SSH and execute installation tasks. SSH settings support two authentication methods:

- Private key authentication.
- PASSWORD authentication.

It is recommended to use the `root` user or a user with `sudo` permissions for deployment. After filling in the information, you can test the connection first to ensure that the installer can log in to the deployment node successfully.

### 5.3 Confirm Basic Configuration

After selecting the distribution package, proceed to the next step. If there are no special requirements, you can keep the default configuration on the page; if the deployment environment already has a specific domain name, certificate, subnet, or middleware plan, please fill in according to the actual plan.

Key points to confirm during configuration:

- Ensure the access protocol and ACCESS_DOMAIN are filled in correctly.
- Pod CIDR and service CIDR should not overlap with existing networks, office networks, VPN, or IDC network segments.
- Use `/data` or the actual planned data disk directory as the data directory. 
- The online/offline installation method should match the current network environment.

### 5.4 Initial Deployment

After configuration is complete, click Initial Deployment. The page will display an overview of this deployment. Please focus on checking:

- Product package version.
- Deployment NODE_IP.
- SSH user and port.
- ACCESS_DOMAIN protocol.
- Data directory.
- Online or offline installation mode.
- Middleware selection.

Continue after confirming everything is correct.

### 5.5 Check System Environment

The installer will automatically check the server environment.

Continue deployment after the check passes. If there are failures, please address them according to the page prompts and recheck. Common resolutions include:

- Insufficient disk space: free up space or expand the data disk.
- Port unavailable: free up the port or adjust the port usage.
- SSH connection failed: Please check the account, PASSWORD, private key, port, and security group. 
- Time synchronization abnormal: Configure NTP or adjust the server time.
- Missing basic commands: Install the missing commands according to the system distribution.

### 5.6 Start Deployment

After the environment check passes, click to start deployment.

During the deployment process, you can view the execution logs of each component. During installation, please ensure:

- The installer process remains running.
- The browser can connect to the installation node network.
- Do not restart the server.
- Do not move or delete the installation package, offline image package, or data directory.

### 5.7 Wait for Installation to Complete

The installation process requires waiting for a period of time, depending on server performance, network environment, and image download speed.

When the page shows that all tasks have been successfully executed and there are no failed components, the deployment is complete.

### 5.8 Confirm Installation Results

After the installation is complete, the installer will display the deployment completion page and access entry information. Please make sure there are no failed tasks on the page before continuing to access the business system and MDP Operations Platform.

Access business address: 

```text
http://<ACCESS_DOMAIN>/
```

If HTTPS has been configured during the installation process, please visit: 

```text
https://<ACCESS_DOMAIN>/
```

After logging in with the default account or administrator account, please change the initial PASSWORD immediately.

Access the MDP Operations Platform:

```text
http://<ACCESS_DOMAIN>/mdp/
```

If you need to modify the MDP administrator PASSWORD, you can execute the following command on the deployment node to change or reset the PASSWORD. Please replace {password} with a new complex strong PASSWORD according to actual security requirements.

```bash
kubectl exec -it $(kubectl get pods -l app=mdp -o jsonpath='{.items[0].metadata.name}') -- reset-admin-password {password}
```

## 6. Post-Installation Acceptance

### 6.1 Check K8s Node Status

Execute on the deployment node:

```bash
kubectl get node
```

The node status should be `Ready`.

Continue checking services: 

```bash
kubectl get pod -A
```

Normal status is usually:

- `Running`: The service is running.
- `Completed`: The task has been completed.

If you encounter the following statuses `CrashLoopBackOff`, `ImagePullBackOff`, `Error`, `Pending`, please first check the corresponding Pod logs and handle them.

### 6.2 Check the access entry

Access ShimoDocs Suite through the browser access entry:

```text
http://<ACCESS_DOMAIN>/
```

If HTTPS is configured, please visit:

```text
https://<ACCESS_DOMAIN>/
```

Confirm that the login page can open normally.

### 6.3 Check the management backend and license

Confirm the following items:

- The management backend is accessible.
- Administrators can log in.
- The license page can be opened.
- Machine information can be viewed.
- Licenses can be applied for or updated according to the authorization process.

### 6.4 Check business functions

After logging in with a test account or an account created by the administrator, at minimum verify:

- Documents, spreadsheets, and presentations can be created.
- Documents can be edited and saved, and the content still exists after refresh.
- Multi-user collaborative editing is supported.
- File import and export work properly.
- Core functions such as search, team space, and contact list are available.

After logging in for the first time with the default test account, please change the PASSWORD.
The account PASSWORD is the deployment delivery account PASSWORD!
```text
ACCOUNT:autotest@example.com
PASSWORD:xxxxx
```

### 6.5 Stopping the Installer Process

After deployment is completed and acceptance is passed, you can stop the installer Web service:
Stop the installation web page:
Command to stop the installer:
```bash
ps -ef | grep mdp-installer | grep -v grep
kill <PID>
```

If the installer is launched in the background using `nohup`, you can also check the logs: 

```bash
tail -f /root/nohup.out
```

## 7. Common Troubleshooting

### 7.1 Browser Cannot Open Installer Page

Check the following:

- Whether the installer process is still running.
- Whether the port `18080` is blocked by the firewall or security group.
- Whether the IP accessed by the browser is INSTALL_NODE_IP.

You can perform the following on the server:

```bash
ps -ef | grep mdp-installer | grep -v grep
ss -lntp | grep 18080
```

### 7.2 Environment Check Failed

Handle each item according to the page prompts. After processing, return to the installer page and rerun the environment check.

Priority check items:

- Whether the CPU, memory, and disk meet the requirements.
- Whether `/data` is a separate data disk.
- Whether the server time is synchronized.
- Whether the SSH user has deployment permissions.

### 7.3 Offline Installation Image Pull Failed

Check directions:

- Whether the offline image package has been uploaded to the deployment node.
- Whether the basic offline image package and the product offline image package are complete.
- Whether the image package version matches the product installation package.
- Whether the private image repository address, account, and PASSWORD are correctly filled in.

### 7.4 Pod Stays in an Abnormal State for a Long Time

First, check the abnormal Pod:

```bash
kubectl get pod -A
```

Check the logs again: 

```bash
kubectl logs -n <namespace> <pod-name>
```

Handle image, configuration, resource, or dependency issues according to the logs.

## 8. Materials to Retain After Installation

After deployment, it is recommended to keep the following materials for subsequent maintenance, upgrades, and troubleshooting:

- INSTALL_NODE_IP, ACCESS_DOMAIN, and access protocol.
- Installer file name and version.
- Product installation package file name and version.
- Offline image package file name and version.
- Key webpage configuration screenshots.
- `kubectl get node` inspection results.
- `kubectl get pod -A` inspection results.
- License authorization records.
- Business function acceptance records.
- Issues encountered during deployment and their handling results.
