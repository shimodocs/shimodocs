# 高可用性 Kubernetes 部署

[← ShimoDocs Suite 部署文档](../README.md)

## 1. 适用场景 

> [!TIP] 
> 
> K8s 集群部署适用于生产环境。与单机部署相比，集群部署更适合长期运行、扩展和高可用场景。 

- 对于生产环境，建议使用 `3 master   N worker`. 
- 至少准备3台服务器，全部作为主节点。工作节点开始可以复用主节点，随后可根据规模增加工作节点。 

## 2. 部署前准备 

### 2.1 准备以下信息 

| 信息 | 示例 | 说明 | 
| --- | --- | --- | 
| 网络环境 | 在线 / 离线 | 如果支持公网访问选择在线；内部网络或隔离环境选择离线 | 
| INSTALL_NODE_IP | `<INSTALL_NODE_IP>` | 选择1台机器作为安装节点启动网页 | 
| 业务 NODE_IP | `<Node1IP>`, `<Node2IP>`, `<Node3IP>` | 至少3台服务器 | 
| 执行用户 | `root` | 安装命令应使用 `root` | 
| 访问协议 | HTTP / HTTPS | HTTPS 建议在生产环境中使用 | 
| ACCESS_DOMAIN | `<ACCESS_DOMAIN>` | 用户访问地址 ShimoDocs Suite |
| 数据目录 | `/data` | 建议所有节点保持一致 |
| 安装工具 | `mdp-installer-${Arch}` | 由提供的安装程序 ShimoDocs, `${Arch}` 区分不同芯片架构，其值可为x86架构的amd64或arm架构的arm64 |
| 产品安装包 | ShimoDocs Suite 安装包 | 使用实际交付的文件名 |
| 离线镜像包 | `*.tar.gz` | 仅在离线安装时需要 |
| 外部中间件 | 是 / 否 | 如有外部中间件，请提前准备地址、端口、账户等信息 PASSWORD 提前准备 |

### 2.2 最低服务器要求

| 项目 | 要求 |
| --- | --- |
| 服务器数量 | 3 个或更多 |
| 推荐角色 | `3 master   N worker` |
| CPU 每个节点 | 16 核或更多 |
| 每个节点内存 | 32 GB 或更多 |
| 系统盘 | 根目录 `/` 分区 100 GB 或更多 |
| 数据盘 | 单独挂载 `/data`, 可用空间 300 GB 或更多 |
| 离线安装 | 建议在安装节点的数据盘上预留额外 100 GB 或更多 |

注意:

- 不要分区 `/root`, `/var`, 或 `/tmp` 单独分区。 
- 不要把数据放在系统盘；全部放在 `/data`. 
- 所有节点的时间必须同步。 
- 安装节点必须能够通过访问其他节点 SSH. 

可以在每台服务器上执行: 

```bash
lscpu
free -g
df -h
timedatectl status
```

确认安装节点可以访问其他节点: 

```bash
ssh root@<NODE2IP>
ssh root@<NODE3IP>
```

如果登录失败，首先检查 SSH, PASSWORD、防火墙或安全组设置，然后再继续安装。

## 3. 上传安装工具和安装包
> [!TIP]
>
> - 确保根据实际情况修改命令中的文件名。例如，在 x86 架构环境中安装包名称为 mdp-installer-amd64。
> - 根据实际场景选择合适的上传方式。本文以 scp 命令行为例，但也可以使用其他图形化 SSH 工具进行上传。

在本地计算机上执行以下命令，将安装程序传输到安装节点:

```bash
scp mdp-installer-amd64 root@<INSTALL_NODE_IP>:/root/
```

离线安装仍然需要上传离线镜像包: 

```bash
scp smbase_image-amd64.tar.gz offline_app_image.tar.gz root@<INSTALL_NODE_IP>:/root/
```

登录到安装节点: 

```bash
ssh root@<INSTALL_NODE_IP>
```

赋予安装程序执行权限:

```bash
chmod +x /root/mdp-installer-amd64
```

启动安装程序网页: 

```bash
nohup /root/mdp-installer-amd64 server --port 18080 &
```

浏览器访问: 

```text
http://<INSTALL_NODE_IP>:18080
```

## 4. 通过网页安装

### 4.1 上传产品安装包

1. 打开 `http://<INSTALL_NODE_IP>:18080`.
2. 上传 ShimoDocs Suite 安装包。
3. 上传完成后，点击 `Continue`.

### 4.2 配置 ACCESS_DOMAIN

输入 ShimoDocs Suite 访问地址:

| 配置项 | 如何填写 |
| --- | --- |
| ACCESS_DOMAIN / IP | `<ACCESS_DOMAIN>` |

### 4.3 确认基本配置

| 配置项 | 如何填写 |
| --- | --- |
| NODE_IP | 填写主节点 / 工作节点 NODE_逐个填写 IP |
| SSH 端口 | 通常 `22` |
| SSH PASSWORD | `root` 用户 PASSWORD |
| 节点类型 | `master`, `worker`，安装节点 |
| 数据目录 | `/data` |

操作步骤：

1. 添加 INSTALL_NODE_IP。
2. 添加每个主节点/工作节点的 IP 地址。
3. 为每台服务器分配节点角色。
4. 测试安装节点到每个节点的连接性。
5. 填写数据目录和容器网络段。

配置期间需要确认的关键点：

- 访问协议和 ACCESS_DOMAIN 已正确填写。
- Pod CIDR 和服务 CIDR 不与现有网络、办公室网络、 VPN, 或 IDC 网络分段冲突。
- 数据目录使用 `/data` 或实际规划的数据盘目录。
- 在线/离线安装方式与当前网络环境一致。
- 离线安装需要上传离线基础镜像包和应用镜像包。默认情况下为在线安装，并且需要确保集群能访问公共网络。

### 4.4 初始部署

配置完成后，点击初始化部署。页面将显示此次部署概览；请特别注意：

- 产品包版本。
- 部署 NODE_IP。
- SSH 用户和端口。
- ACCESS_DOMAIN 和协议。
- 数据目录。
- 在线或离线安装模式。
- 中间件选择。

确认无误后继续。

### 4.5 检查系统环境

安装程序将自动检查服务器环境。

检查通过后继续部署。如有任何失败，请按照页面提示处理后重新检查。常见处理方向包括：

- 磁盘空间不足：清理空间或扩展数据盘。
- 端口不可用：请释放端口或调整端口使用。
- SSH 连接失败：请检查账号， PASSWORD私钥、端口和安全组。
- 时间同步异常：请配置或校准服务器时间。 NTP 
- 缺少基本命令：请根据系统发行版安装缺失的命令。

### 4.6 开始部署

环境检查通过后，点击开始部署。

部署过程中可以查看各组件的执行日志。安装过程中请确保： 

- 安装程序进程保持运行。 
- 浏览器能够通过网络与安装节点通信。 
- 服务器未重启。 
- 不要移动或删除安装包、离线镜像包或数据目录。 

### 4.7 等待安装完成

安装过程需要等待一些时间，具体时间取决于服务器性能、网络环境和镜像下载速度。

当页面显示所有任务已成功执行且没有失败组件时，即表示部署完成。

### 4.8 确认安装结果

安装完成后，安装程序将显示部署完成页面及访问入口信息。请先确认页面上没有失败任务，然后再继续访问业务系统。 MDP 运维平台。

访问营业地址： 

```text
http://<ACCESS_DOMAIN>/
```

如果 HTTPS 在安装过程中已配置，请访问： 

```text
https://<ACCESS_DOMAIN>/
```

使用默认账号或管理员账号登录后，请立即更改初始 PASSWORD 。

访问 MDP 运维平台：

```text
http://<ACCESS_DOMAIN>/mdp/
```

如果需要修改 MDP 管理员 PASSWORD，可以在部署节点执行以下命令以修改或重置 PASSWORD.
请将 {password} 替换为新的复杂强密码 PASSWORD 根据实际安全要求。

```bash
kubectl exec -it $(kubectl get pods -l app=mdp -o jsonpath='{.items[0].metadata.name}') -- reset-admin-password {password}
```

## 5. 安装后验收

### 5.1 检查 K8s 节点状态

在部署节点上执行：

```bash
kubectl get node
```

节点状态应为 `Ready`. 

继续检查服务： 

```bash
kubectl get pod -A
```

正常状态通常为： 

- `Running`：服务正在运行。 
- `Completed`：任务已执行完成。 

如果遇到如下状态 `CrashLoopBackOff`, `ImagePullBackOff`, `Error`, `Pending`，请先检查对应的 Pod 日志并进行处理。 

### 5.2 检查访问入口 

访问 ShimoDocs Suite 通过浏览器访问入口： 

```text
http://<ACCESS_DOMAIN>/
```

如果 HTTPS 已配置，请访问： 

```text
https://<ACCESS_DOMAIN>/
```

确认登录页面可以正常打开。 

### 5.3 检查管理后台和许可证 

确认以下项目： 

- 管理后台可访问。 
- 管理员可以登录。 
- 许可证页面可以打开。 
- 可以查看机器信息。 
- 可以根据授权流程申请或更新许可证。 

### 5.4 检查业务功能 

使用测试账户或管理员创建的账户登录后，至少验证： 

- 可以创建文档、表格、幻灯片。 
- 文档可以编辑、保存或刷新，内容仍然存在。 
- 支持多用户协作编辑。 
- 文件导入导出正常。 
- 搜索、团队空间、联系人等核心功能可用。 

首次登录默认测试账户后，请立即更新您的 PASSWORD 。 
账户 PASSWORD 是部署和交付账户 PASSWORD! 

```text
ACCOUNT:autotest@example.com
PASSWORD:xxxxxxx
```

### 5.5 停止安装程序进程

部署完成并验收后，可以停止安装程序 Web 服务
停止安装网页：
停止安装程序的命令： 

```bash
ps -ef | grep mdp-installer | grep -v grep
kill <PID>
```

如果安装程序是使用 `nohup`在后台启动的，也可以检查日志： 

```bash
tail -f /root/nohup.out
```

## 6. 常见问题处理

### 6.1 浏览器无法打开安装页面

检查以下内容：

- 安装程序进程是否仍在运行。
- 端口是否 `18080` 被防火墙或安全组阻塞。
- 浏览器访问的 IP 是否 INSTALL_NODE_IP。

您可以在服务器上执行：

```bash
ps -ef | grep mdp-installer | grep -v grep
ss -lntp | grep 18080
```

### 6.2 环境检查失败

根据页面提示处理每一项。处理后，返回安装程序页面并重新运行环境检查。

优先检查项：

- 是否 CPU，内存和磁盘满足要求。
- 是否 `/data` 是独立数据盘。
- 服务器时间是否同步。
- 是否 SSH 用户是否具有部署权限。

### 6.3 离线安装镜像拉取失败

检查方向：

- 离线镜像包是否已上传到部署节点。
- 基础离线镜像包和产品离线镜像包是否完整。
- 镜像包版本是否与产品安装包匹配。
- 私有镜像仓库地址、账号及 PASSWORD 已正确填写。

### 6.4 Pod 长时间保持异常状态

首先检查异常 Pod：

```bash
kubectl get pod -A
```

再次检查日志： 

```bash
kubectl logs -n <namespace> <pod-name>
```

根据日志处理镜像、配置、资源或依赖问题。

## 7. 安装后保留资料

部署后，建议保留以下资料以便后续维护、升级和故障排除：

- INSTALL_NODE_IP， ACCESS_DOMAIN及访问协议。
- 安装程序文件名及版本。
- 产品安装包文件名及版本。
- 离线镜像包文件名及版本。
- 网页关键配置截图。
- `kubectl get node` 检查结果。
- `kubectl get pod -A` 检查结果。
- 许可证授权记录。
- 业务功能验收记录。
- 部署过程中遇到的问题及其处理结果。
