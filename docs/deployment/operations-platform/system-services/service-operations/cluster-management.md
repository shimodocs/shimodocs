# Cluster Management

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## 1. Functional Overview

The cluster management module is a console used for operating the Kubernetes cluster of the MDP and customer interface platform, targeting three scenarios: daily inspection, emergency troubleshooting, and resource changes. The goal of this module is to allow on-duty operations personnel to complete common troubleshooting and operational tasks without frequently switching to the local environment. `kubectl`.

Main functions:

- Cluster Overview: node health status, application running status
- Workload Management: view, restart, change replica counts, modify container resources, and view YAML applicable for Deployment, StatefulSet, DaemonSet, Pod, Job, and CronJob
- Configuration Management: View ConfigMap, Horizontal Pod Autoscaler (HPA)
- Network Resources: View Service, Ingress
- Pod-level Diagnostics: Real-time logs, crash logs, K8s events, web terminal, YAML view

### 1.1 Applicable Users

| Role       | Common Operations                               |
| ---------- | ------------------------------------------------|
| On-duty Ops | View node and Pod anomalies, query logs, view events |
| On-site Support | View Deployment replica status, image versions, resource requests/limits         |
| Emergency Troubleshooting | Restart Deployment or DaemonSet, adjust replica count, adjust CPU/memory |
| Capacity Planning | View current HPA replica count and upper/lower limits                             |

### 1.2 Operations Not Recommended in This Module

Deleting NAMESPACE, forcibly evicting Pods, modifying Secrets or RBAC resources and other sensitive operations are not available in this module. They need to be performed using the native `kubectl` or related change tools. Cross-cluster batch operations are not available; each operation only affects the currently selected cluster and NAMESPACE. For downloading large log files at once, it is recommended to use the Web terminal instead of the streaming log popup.

---

## 2. Entry and Navigation

Left-side menu: **Operations Management → Cluster Management**.

After entering, the **Deployments** menu is selected by default. NAMESPACE defaults to the first in the current cluster, and custom selection of clusters and NAMESPACE is supported.

---

## 3. Workloads

### 3.1 Deployment
**Steps**: Locate the target deployment → Click the pencil icon in the upper right → An edit window pops up → Enter new values → Confirm changes.

Fields supported for modification in the pop-up window:

- Number of replicas, minimum value is 0, must be an integer
- CPU requests/limits per container, unit is "core", can enter `1` or `1000m`
- Memory requests/limits per container, unit is Mi, can enter `512`

After submission, a rolling rebuild will be triggered. Fields not listed (image, environment variables, probes, etc.) will not be changed. 

#### 3.1.1 Deployment Restart
Steps: Find the target deployment → Click the circular arrow icon at the top right → Confirm that the popup appears → Check the cluster/load name → Confirm restart. NAMESPACE / load name → Confirm restart.

The confirmation popup clearly states: 'Restart will cause Pods to be rebuilt, and services may experience a brief interruption.' Restart will rebuild Pods on all nodes simultaneously.

### 3.2 Pods
**Operation steps**: Go to Pods from the left-hand menu → The area below lists all current Pods, supporting search by Namespace, POD_NAME, Pod IP, and NODE_IP.

This YAML is for viewing only.

### 3.3 Jobs and CronJobs

#### Jobs
**Steps**: Go to Jobs from the left menu → The table lists all current Jobs in the NAMESPACE.

You can search by Namespace and Name.

#### CronJobs
**Steps**: Go to CronJobs from the left menu → The table lists all current CronJobs in the NAMESPACE.

You can search by Namespace and Name.
Click **** **** to expand and display a subtable of all Pods corresponding to Jobs triggered by this CronJob.

### 3.4 DaemonSets
**Operation steps**: Go to DaemonSets from the left menu.

You can search by Namespace and workload name.
Supported operations:

- Modify: CPU/memory can be modified, the number of replicas cannot be modified.
- Restart: Rebuild Pods simultaneously on all nodes.
- YAML: View only.

### 3.5 StatefulSets
**Operation Steps**: Enter StatefulSets → Table View from the left menu.

Modify the number of replicas, CPU/memory of StatefulSets cannot be operated on, nor can restart or Pod list. Required changes should be made using native methods `kubectl` (see Appendix B).

---

## 4. Configuration

### 4.1 ConfigMaps
**Steps**: Enter ConfigMaps → Table to list all ConfigMaps in the current cluster NAMESPACE from the left menu.
[Cluster Management] does not support key-value editing. To make changes, please go to the Configuration Center.

### 4.2 HPA
**Operation Steps**: Enter HPA from the left menu → the table lists all HPA NAMESPACES under the current cluster.  

For viewing only. To modify the HPA minimum and maximum values, please use the native `kubectl`.

---

## 5. Network

### 5.1 Services
**Steps**: Use the left menu to enter Services → the table lists all service NAMESPACES under the current cluster.  

For viewing only. To make changes, please modify via MDP global configuration.

### 5.2 Ingress
**Steps**: Enter Ingress from the left menu → the table lists all Ingress NAMESPACES under the current cluster.  

For viewing only. To make changes, please modify via MDP global configuration.

---

## 6. Common Operations

### 6.1 Pod Troubleshooting

1. Use the top dropdown menu to switch to the corresponding cluster and NAMESPACE
2. Go to Pods in the left menu
3. Filter by POD_NAME or by IP as follows
4. Pay attention to the Phase field at the top of the card, prioritize handling `Failed` and `Pending`
5. The Condition corresponding to the gray health indicator is the problem point
6. Click the "Events" icon at the end of the row to find the root cause
7. Use "Logs" to view real-time output / use "Crash Logs" to view the last container output

### 6.2 Restart Deployment

1. Go to Deployments in the left menu
2. Find the target Deployment
3. Click the circular arrow icon at the top right
4. Confirm the cluster / NAMESPACE / workload name in the popup → confirm restart
5. Observe the Pod replica status progress bar at the bottom of the card to judge the reconstruction progress

### 6.3 Reduce Deployment Replicas for Verification

1. Go to the corresponding Deployment
2. Click the pencil "Edit" icon
3. Enter the new number of replicas (can be set to 0 during debugging)
4. Adjust CPU/memory as needed (optional)
5. Confirm the changes and wait for the rolling update

Before reducing the number of replicas, it is recommended to confirm with SRE colleagues whether the target value will affect online traffic.

### 6.4 Modify ConfigMap

The platform does not support editing ConfigMap key-value pairs in Cluster Management - Configuration - ConfigMap. Please go to the Configuration Center.

---

## 9. Frequently Asked Questions

**Q1: The overview at the top shows the application running rate is not 100%.**

This means that there are currently Pods in the NAMESPACE that are not in a ready state (including Pending, CrashLoopBackOff, Error, etc.). Please go to the Pods menu on the left, filter by POD_NAME or IP using the following conditions, and check the events and logs of each non-ready Pod.

**Question: After clicking "Modify Deployment," the popup window is empty.**

There are three common reasons: network fluctuations, too many `managedFields` in the resource object, or server API exceptions. Please disable retries first; if it is still empty, contact SRE and provide the cluster / namespace / workload name for troubleshooting.

**Question: Q3: The YAML popup content is very large.**

This is normal. K8s resource objects carry a large amount of metadata and conditions, with key content concentrated in the `spec` section.

**Question 4: No output in the log popup.**

The container may not be outputting logs to stdout/stderr. Please check the application's log output policy. If the container has crashed, use the "Crash Log" icon to get the output of the previous instance.

**Question 5: Changing the number of replicas or resources has no effect.**

The platform will issue a strategic merge patch, and K8s will enter the reconciliation process within a few seconds. If there is no change within 30 seconds, return to the native `kubectl describe deployment` to check the events.

**Question 6: Unable to modify StatefulSets, ConfigMaps, HPAServices, Ingresses.**

The platform only allows viewing these resources. Modifications should be made through the mdp global configuration, and only Services and Ingresses are supported.

---

---

## Appendix A: Key kubectl Commands Used by This Platform

The following commands can be executed directly on the host or maintenance terminal and can serve as an alternative path when the functions of this module are not covered.

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

`kubectl describe deployment <name> -n <ns>` can be used to troubleshoot the reconciliation progress issued after platform modifications.

Notes:
Modification of resources managed by deployment, configmap, ingress, sts, etc., should be avoided through MDP. The correct way to operate with kubectl is to use the MDP backend configuration.

## Appendix B: Glossary

| Term | Description |
| --------------- | ---------------------------------------------------- |
| Cluster        | Target K8s cluster, configured and delivered when MDP starts                              |
| Namespace      | K8s NAMESPACE, used for business or environment isolation                                   |
| Workload       | Workload, usually refers to Deployment, StatefulSet, DaemonSet, Job, CronJob |
| Pod            | The smallest scheduling unit in K8s, hosting 1 to N containers                              |
| HPA             | Horizontal Pod Autoscaler, horizontal scaling based on metrics                  |
| Requests / Limits | Container resource requests/limits, platform supports modifying both |
| Patch           | Partial update, the platform uses a strategy to merge patches                     |
| STS             | Abbreviation of StatefulSet                                       |
| DS              | Abbreviation of DaemonSet                                         |
