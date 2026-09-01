# High Availability Kubernetes Deployment

[← ShimoDocs Suite Deployment Documentation](../README.md)

## 1. Applicable Scenarios 

> [!TIP] 
> 
> K8s cluster deployment is suitable for production environments. Compared with single-machine deployment, cluster deployment is more suitable for long-term operation, scaling, and high availability scenarios. 

- For production environments, the recommended topology is `3 master + N worker`.
- Prepare at least 3 servers, all as master nodes. Worker nodes can initially reuse master nodes, and more worker nodes can be added according to scale later. 

## 2. Preparations Before Deployment 

### 2.1 Prepare the Following Information 

| Information | Example | Description | 
| --- | --- | --- | 
| Network Environment | Online / Offline | Choose online if public network access is supported; choose offline for internal network or isolated environments |
| INSTALL_NODE_IP | `<INSTALL_NODE_IP>` | Choose one machine as the installation node to start the web page | 
| Business NODE_IP | `<Node1IP>`, `<Node2IP>`, `<Node3IP>` | At least 3 servers | 
| Execution User | `root` | Installation commands should be run using `root` | 
| Access Protocol | HTTP / HTTPS | HTTPS is recommended for production environments | 
| ACCESS_DOMAIN | `<ACCESS_DOMAIN>` | User access address for ShimoDocs Suite | 
| Data Directory | `/data` | It is recommended that all nodes remain consistent |
| Installation Tool | `mdp-installer-${ARCH}` | Select the installer for the server architecture: use `amd64` for x86 or `arm64` for ARM |
| Product Installation Package | ShimoDocs Suite Installation Package | Use the actual delivered file name |
| Offline Image Package | `*.tar.gz` | Only required for offline installation |
| External Middleware | Yes / No | If external middleware is used, prepare its address, port, account, password, and other connection details in advance |

### 2.2 Minimum Server Requirements

| Item | Requirement |
| --- | --- |
| Number of Servers | 3 or more |
| Recommended Role | `3 master + N worker` |
| CPU per node | 16 cores or more |
| Memory per node | 32 GB or more |
| System disk | Root directory `/` partition 100 GB or more |
| Data disk | Separately mounted `/data`, available space 300 GB or more |
| Offline installation | It is recommended to reserve an additional 100 GB or more on the data disk of the installation node |

Note:

- Do not partition `/root`, `/var`, or `/tmp` separately.
- Do not put data on the system disk; put everything on `/data`.
- Time must be synchronized across all nodes.
- The installation node must be able to SSH to other nodes.

You can execute on each server:

```bash
lscpu
free -g
df -h
timedatectl status
```

Verify that the installation node can access other nodes: 

```bash
ssh root@<NODE2IP>
ssh root@<NODE3IP>
```

If the login fails, first check the SSH, PASSWORD, firewall, or security group settings, and then continue with the installation.

## 3. Upload the Installation Tool and Package
> [!TIP]
>
> - Make sure to modify the filenames in the commands according to the actual situation. For example, in an x86 architecture environment, the package name is mdp-installer-amd64.
> - Choose the appropriate upload method based on the actual scenario. This article uses the scp command line as an example, but you can also use other graphical SSH tools for uploading.

On your local computer, run the following command to transfer the installer to the installation node:

```bash
scp mdp-installer-amd64 root@<INSTALL_NODE_IP>:/root/
```

Offline installation still requires uploading the offline image package: 

```bash
scp smbase_image-amd64.tar.gz offline_app_image.tar.gz root@<INSTALL_NODE_IP>:/root/
```

Log in to the installation node: 

```bash
ssh root@<INSTALL_NODE_IP>
```

Grant execution permissions to the installer:

```bash
chmod +x /root/mdp-installer-amd64
```

Launch the installer webpage: 

```bash
nohup /root/mdp-installer-amd64 server --port 18080 &
```

Browser access: 

```text
http://<INSTALL_NODE_IP>:18080
```

## 4. Install via Web Page

### 4.1 Upload Product Installation Package

1. Open `http://<INSTALL_NODE_IP>:18080`.
2. Upload the ShimoDocs Suite installation package.
3. After the upload is complete, click `Continue`.

### 4.2 Configure ACCESS_DOMAIN

Enter the ShimoDocs Suite access address:

| Configuration Item | How to Fill In |
| --- | --- |
| ACCESS_DOMAIN / IP | `<ACCESS_DOMAIN>` |

### 4.3 Confirm Basic Configuration

| Configuration Item | How to Fill In |
| --- | --- |
| NODE_IP | Fill in the NODE IP for master node / worker node one by one |
| SSH Port | Usually `22` |
| SSH PASSWORD | `root` User PASSWORD |
| Node Type | `master`, `worker`, Install Node |
| Data Directory | `/data` |

Operation Steps:

1. Add INSTALL_NODE_IP.
2. Add the IP addresses of each master/worker node.
3. Assign node roles to each server.
4. Test connectivity from the install node to each node.
5. Fill in the data directory and container network segment.

Key points to confirm during configuration:

- The access protocol and ACCESS_DOMAIN are correctly filled in.
- Pod CIDR and service CIDR do not conflict with existing networks, office networks, VPN, or IDC network segments.
- The data directory uses `/data` or the actual planned data disk directory.
- The online/offline installation method should match the current network environment.  
- Offline installation requires uploading the offline base image package and the application image package. By default, it is an online installation, and it is necessary to ensure that the cluster can access the public network.

### 4.4 Initial Deployment

After the configuration is completed, click Initialize Deployment. The page will display an overview of this deployment; please pay special attention to:

- Product package version.  
- Deployment NODE_IP.  
- SSH user and port.  
- ACCESS_DOMAIN and protocol.  
- Data directories.  
- Online or offline installation mode.  
- Middleware selection.

Continue after confirming everything is correct.

### 4.5 Check System Environment

The installer will automatically check the server environment.

Continue deployment after the check passes. If there is any failure, handle it according to the page prompts and check again. Common solutions include:

- Insufficient disk space: clean up space or expand the data disk.  
- Port unavailable: free the port or adjust port usage.
- SSH connection failed: please check the account, password/private key, port, and security group.  
- Time synchronization exception: please configure or calibrate the server time. NTP  
- Missing basic commands: please install the missing commands according to your system distribution.  

### 4.6 Start Deployment

After the environment check passes, click Start Deployment.

During the deployment process, you can view the execution logs of each component. During installation, please ensure:

- The installation process remains running.  
- The browser can communicate with the installation node via the network.  
- The server is not restarted.  
- Do not move or delete the installation package, offline image package, or data directory.  

### 4.7 Wait for Installation to Complete

The installation process requires some time, depending on server performance, network environment, and image download speed.

When the page shows that all tasks have been successfully executed and no components have failed, the deployment is complete.

### 4.8 Confirm Installation Results

After the installation is complete, the installer will display the deployment completion page and access entry information. Please first confirm that there are no failed tasks on the page before continuing to access the business system. MDP Operations Platform.

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

## 5. Post-Installation Acceptance

### 5.1 Check K8s Node Status

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

If you encounter the following statuses `CrashLoopBackOff`, `ImagePullBackOff`, `Error`, `Pending`, please first check the corresponding Pod logs and handle accordingly.

### 5.2 Check Access Entry

Access ShimoDocs Suite through the browser access entry:

```text
http://<ACCESS_DOMAIN>/
```

If HTTPS is configured, please visit: 

```text
https://<ACCESS_DOMAIN>/
```

Confirm that the login page can be opened normally.

### 5.3 Check the Admin Backend and License

Confirm the following items:

- The admin backend is accessible.
- Admins can log in.
- The license page can be opened.
- Machine information can be viewed.
- Licenses can be applied for or updated according to the authorization process.

### 5.4 Check Business Functions

After logging in with a test account or an account created by the administrator, at a minimum, verify:

- Documents, spreadsheets, and presentations can be created.
- Documents can be edited, saved, or refreshed, and content remains.
- Multi-user collaborative editing is supported.
- File import and export work normally.
- Core functions such as search, team spaces, and contacts are available.

After the first login with the default test account, immediately update your PASSWORD.
The account PASSWORD is the PASSWORD used for deployment and delivery accounts!

```text
ACCOUNT:autotest@example.com
PASSWORD:xxxxxxx
```

### 5.5 Stop the Installer Process

After deployment is completed and accepted, you can stop the installer Web service.
Stop the installation webpage:
Command to stop the installer:

```bash
ps -ef | grep mdp-installer | grep -v grep
kill <PID>
```

If the installer is launched in the background using `nohup`, you can also check the logs: 

```bash
tail -f /root/nohup.out
```

## 6. Handling common issues

### 6.1 Browser cannot open the installation page

Check the following: 

- Whether the installer process is still running. 
- Whether the port is blocked by a firewall or security group `18080`. 
- Whether the browser's access IP is INSTALL_NODE_IP. 

You can perform the following on the server:

```bash
ps -ef | grep mdp-installer | grep -v grep
ss -lntp | grep 18080
```

### 6.2 Environment Check Failed

Handle each item according to the page prompts. After processing, return to the installer page and rerun the environment check.

Priority check items:

- Whether the CPU, memory, and disk meet the requirements.
- Whether `/data` is a dedicated data disk.
- Whether the server time is synchronized.
- Whether the SSH user has deployment permissions.

### 6.3 Offline Installation Image Pull Failed

Check directions:

- Whether the offline image package has been uploaded to the deployment node.
- Whether the basic offline image package and the product offline image package are complete.
- Whether the image package version matches the product installation package.
- Whether the private image repository address, account, and PASSWORD are correctly filled in.

### 6.4 Pod Remains in Abnormal Status for a Long Time

First, check the abnormal Pod:

```bash
kubectl get pod -A
```

Check the logs again: 

```bash
kubectl logs -n <namespace> <pod-name>
```

Handle image, configuration, resource, or dependency issues according to the logs.

## 7. Retain Materials After Installation

After deployment, it is recommended to retain the following materials for subsequent maintenance, upgrades, and troubleshooting:

- INSTALL_NODE_IP, ACCESS_DOMAIN, and access protocols.
- Installer file name and version.
- Product installation package file name and version.
- Offline image package file name and version.
- Key web configuration screenshots.
- `kubectl get node` check results.
- `kubectl get pod -A` check results.
- License authorization records.
- Business functionality acceptance records.
- Issues encountered during deployment and their resolution results.
