# 集群管理

[← ShimoDocs Suite 部署文档](../../../README.md)

## 1. 功能概述

集群管理模块是一个控制台，用于 MDP 与客户接口的运营平台 Kubernetes 集群，针对三种场景：日常检查、紧急故障排除和资源变更。该模块的目标是允许值班运维人员在不频繁切换到本地的情况下完成常见的故障排除和操作任务。 `kubectl`.

主要功能：

- 集群概览：节点健康状况，应用运行状态
- 工作负载管理：查看、重启、更改副本数、修改容器资源，以及查看 YAML 适用于 Deployment、StatefulSet、DaemonSet、Pod、Job 和 CronJob
- 配置管理：查看 ConfigMap、水平 Pod 自动扩缩（HorizontalPodAutoscaler）HPA)
- 网络资源：查看 Service、Ingress
- Pod 级诊断：实时日志、崩溃日志， K8s 事件，Web 终端， YAML 视图

### 1.1 适用用户

| 角色        | 常用操作                                      |
| ----------- | ---------------------------------------------------- |
| 值班运维 | 查看节点和 Pod 异常，查询日志，查看事件 |
| 现场支持 | 查看 Deployment 副本状态、镜像版本、资源请求/限制         |
| 故障应急 | 重启 Deployment 或 DaemonSet，调整副本数量，调整 CPU/内存 |
| 容量规划 | 查看 HPA 当前副本数量及上下限                             |

### 1.2 本模块不推荐操作

删除 NAMESPACE、强制驱逐 Pods、修改 Secret 或 RBAC 资源等敏感操作在本模块中不可用，需要通过原生 `kubectl` 或相关变更工具执行。跨集群批量操作不可用；每次操作仅影响当前选中的集群和 NAMESPACE。对于一次下载大文件日志，建议使用 Web 终端，而不是流式日志弹窗。

---

## 2. 入口与导航

左侧菜单： **运维管理 → 集群管理**.

进入后， **Deployments** 菜单默认被选中。 NAMESPACE 默认为当前集群中的第一个，并且可以自定义选择集群和 NAMESPACE 被支持。

---

## 3. 工作负载

### 3.1 部署
**步骤**: 找到目标部署 → 点击右上角的铅笔图标 → 弹出编辑窗口 → 输入新值 → 确认更改。

弹出窗口中支持修改的字段： 

- 副本数量，最小值为0，必须为整数 
- CPU 每个容器的请求/限制，单位为“core”，可填写 `1` 或 `1000m` 
- 每个容器的内存请求/限制，单位为Mi，可填写 `512` 

提交后，将触发滚动重建。未列出的字段（镜像、环境变量、探针等）将不会被更改。 

#### 3.1.1 部署重启 
步骤：找到目标部署 → 点击右上角的循环箭头图标 → 确认弹出窗口出现 → 检查集群/负载名称 → 确认重启。 NAMESPACE / load name → 确认重启。 

确认弹窗上明确说明“重启将导致 Pods 被重建，服务可能会短暂中断。” 重启将同时重建所有节点上的 Pods。 

### 3.2 Pods
**操作步骤**：从左侧菜单进入 Pods → 下方区域列出当前所有 Pods NAMESPACE，支持按 Namespace 搜索， POD_NAME、Pod IP，以及 NODE_IP。 

此 YAML 仅供查看。

### 3.3 Jobs 和 CronJobs

#### Jobs
**步骤**：从左侧菜单进入 Jobs → 表格列出当前所有 Jobs NAMESPACE.

可按 Namespace 和 Name 搜索。

#### CronJobs
**步骤**：从左侧菜单进入 CronJobs → 表格列出当前所有 CronJobs NAMESPACE.

可按 Namespace 和 Name 搜索。 
点击 **** **** 点击展开以显示此 CronJob 触发的所有 Jobs 对应的 Pods 子表。 

### 3.4 DaemonSets 
**操作步骤**：从左侧菜单进入 DaemonSets。 

可按 Namespace 和工作负载名称搜索。
支持的操作：

- 修改： CPU / 内存可修改，副本数量不可修改。
- 重启：在所有节点上同时重建 Pods。
- YAML：仅供查看。

### 3.5 StatefulSets
**操作步骤**：从左侧菜单进入 StatefulSets → 表格视图。

修改副本数量, CPU/不支持对 StatefulSets 的内存、重启或 Pod 列表进行操作。所需的更改应使用原生方法进行 `kubectl` （参见附录 B）。

---

## 4. 配置

### 4.1 ConfigMaps
**步骤**：从左侧菜单进入 ConfigMaps → 表格列出当前集群中的所有 ConfigMaps NAMESPACE.
[集群管理] 不支持键值编辑。若需更改，请前往配置中心。

### 4.2 HPA
**操作步骤**：从左侧菜单进入 HPA → 表格列出当前集群下的所有 HPA NAMESPACE.

仅供查看。要修改 HPA 最小值和最大值，请使用原生 `kubectl`.

---

## 5. 网络

### 5.1 服务
**步骤**：使用左侧菜单进入服务 → 表格列出当前集群下的所有服务 NAMESPACE.

仅供查看。要进行更改，请通过 MDP 全局配置进行修改。 

### 5.2 Ingress
**步骤**：从左侧菜单进入 Ingress → 表格列出当前集群下的所有 Ingress NAMESPACE. 

仅供查看，若需更改，请通过 MDP 全局配置进行修改。

---

## 6. 常用操作

### 6.1 Pod 问题排查

1. 使用顶部下拉菜单切换至对应集群并 NAMESPACE
2. 在左侧菜单中转到 Pods
3. 按以下方式过滤 POD_NAME 或按 IP
4. 注意卡片顶部的 Phase 字段，优先处理 `Failed` 以及 `Pending`
5. 灰色健康指示器对应的 Condition 就是问题点
6. 点击行末的“Events”图标以查找根本原因
7. 使用“Logs”查看实时输出 / 使用“Crash Logs”查看最后一次容器输出

### 6.2 重启 Deployment

1. 在左侧菜单中转到 Deployments
2. 找到目标 Deployment
3. 点击右上角的圆形箭头图标
4. 通过验证集群/确认弹出窗口 NAMESPACE / 工作负载名称 → 确认重启
5. 观察卡片底部 Pod 副本状态进度条以判断重建进度

### 6.3 降低 Deployment 副本进行验证

1. 进入对应的 Deployment
2. 点击铅笔“编辑”图标
3. 输入新的副本数量（调试时可以设置为 0） 
4. 调整 CPU / 内存按需调整（可选） 
5. 确认更改并等待滚动更新 

在减少副本数量之前，建议确认 SRE 同事们，目标值是否会影响线上流量。 

### 6.4 修改 ConfigMap 

平台不支持在集群管理 - 配置 - ConfigMap 中编辑 ConfigMap 键值对。请前往配置中心。 

--- 

## 9. 常见问题解答 

**问1：顶部概览显示应用程序运行率不是100%。** 

这意味着当前存在 Pods NAMESPACE 不处于就绪状态的（包括 Pending、CrashLoopBackOff、Error 等）。请转到左侧的 Pods 菜单，并使用以下条件进行筛选 POD_NAME 或 IP，并检查每个非准备就绪 Pod 的事件和日志。 

**问：点击“修改部署”后弹出窗口为空。** 

有三个常见原因：网络抖动、资源对象中过多 `managedFields` 或服务器 API 例外。请先禁用重试；如果仍然为空，请联系 SRE 并提供集群 / 命名空间 / 工作负载名称以进行故障排除。 

**问：第三季度： YAML 弹出内容非常大。** 

正常现象。 K8s 资源对象携带大量元数据和条件，关键内容集中在 `spec` 部分。 

**问4：日志弹窗没有输出。** 

容器可能没有将日志输出到 stdout/stderr，请检查应用程序的日志输出策略。如果容器已崩溃，请使用“Crash Log”图标获取前一个实例的输出。 

**问5：修改副本数量或资源没有生效。** 

平台会发出战略合并补丁， K8s 并将在几秒钟内进入调和过程。如果30秒内没有变化，请返回本机 `kubectl describe deployment` 检查事件。 

**问6：无法修改 StatefulSets、ConfigMaps、 HPAServices、Ingresses。** 

平台仅允许查看这些资源。修改应通过 mdp 全局配置进行，且仅支持 Services 和 Ingresses。 

--- 

--- 

## 附录 A：关键 kubectl 本平台使用的命令 

以下命令可直接在主机或维护终端上执行，当此模块功能未覆盖时可作为替代路径。 

```bash
# View
kubectl get  statefulset <name> -n <ns>
kubectl get deployment <name> -n <ns>

# Restart STS / deployment
kubectl rollout restart statefulset/<name> -n <ns>
kubectl rollout restart deployment/<name> -n <ns>

# View the complete Ingress rule chain
kubectl describe ingress <name> -n <ns>
```

`kubectl describe deployment <name> -n <ns>` 可用于排查平台修改后下发的调和进度。

注意事项：
应避免通过 MDP 修改由如 deployment、configmap、ingress、sts 等管理的资源。 kubectl 正确的操作方式是使用 MDP 后端配置。

## 附录 B：术语表

| 术语               | 说明                                           |
| --------------- | ---------------------------------------------------- |
| 集群         | 目标 K8s 集群，在 MDP 启动时配置并下发                              |
| 命名空间       | K8s NAMESPACE，用于业务或环境隔离                                   |
| 工作负载        | 工作负载，通常指 Deployment、StatefulSet、DaemonSet、Job、CronJob |
| Pod             | 在中最小的调度单元 K8s，承载 1 到 N 个容器                              |
| HPA             | 水平 Pod 自动扩缩器，基于指标的水平扩缩                  |
| 请求 / 限制 | 容器资源保留 / 限制，平台支持修改两者 |
| 补丁           | 部分更新，该平台使用策略合并补丁                     |
| STS             | StatefulSet 的缩写                                       |
| DS              | DaemonSet 的缩写                                         |
