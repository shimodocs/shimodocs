# 数据备份

[← ShimoDocs Suite 部署文档](../README.md)

本文档说明了私有化环境的数据备份范围、恢复要求、执行方法及恢复后的校验项目。 ShimoDocs 

本文档涵盖以下内容：

* 备份范围及职责边界

* 数据库备份及恢复要求

* 对象存储备份及恢复要求

* 恢复前确认项目

* 恢复后校验项目

本文档不涵盖以下内容：

* 初始安装和部署步骤

* 升级和迁移方案

* 第三方中间件厂商特定的恢复工具说明

* 生产事故处理流程

# 2. 备份范围及职责边界

## 2.1 备份范围

需要纳入备份范围的数据在 ShimoDocs 私有化环境包括：

* MySQL 数据

* MongoDB 数据

* Redis 数据

* 对象存储数据 

* 安装配置和环境参数文件 

数据目录、备份目录及备份保留周期由客户端统一管理。 

## 2.2 责任边界 

备份和恢复责任的边界如下： 

* 客户端负责制定和执行正式的备份策略 

* 客户端负责备份文件的妥善保管、介质安全以及保留期管理 

* 客户端负责恢复演练、恢复审批以及恢复结果的验收 

* ShimoDocs 可以提供技术支持和恢复操作指导 

当涉及外部中间件、自建对象存储或客户端维护的基础设施时，备份和恢复策略完全由客户端承担。 

# 3. 恢复执行前确认 

数据恢复是一项高风险操作。执行前必须完成以下确认。 

## 3.1 目标确认 

在恢复之前，请明确以下信息： 

* 目标环境 

* 目标集群、节点 NAMESPACE 

* 需恢复的数据范围 

* 恢复的时间点 

* 执行时间窗口 

## 3.2 风险确认

在恢复之前请确认以下项目：

* 此次恢复是否会覆盖当前在线数据

* 此次恢复是否需要停机

* 最新备份是否已添加到当前在线数据中

* 在恢复失败后回滚点是否已明确

## 3.3 备份有效性确认

在恢复之前请检查以下内容：

* 备份文件是否完整且可读

* 备份时间点是否满足恢复目标

* 备份目录是否正确挂载

* 恢复所需的所有配置文件是否完整

* 备份文件是否已通过可恢复性验证

# 4. 备份策略

## 4.1 数据库备份

数据库备份标准如下：

|场景|执行方式|频率|保留期限|描述|
|:----|:----|:----|:----|:----|
|使用 ShimoDocs 内置中间件|系统计划备份|每天一次|7天|由集群内的计划任务执行|
|使用客户自维护的中间件|客户侧备份|每天一次或更多|7天或更长|根据客户侧策略执行|



数据库备份必须至少覆盖：

* MySQL

* MongoDB

* Redis

## 4.2 对象存储备份

对象存储备份标准如下：

|数据类型|执行方式|频率|保留期限|描述|
|:----|:----|:----|:----|:----|
|对象存储业务数据|冷备或灾难恢复复制|根据业务级别执行|根据客户策略执行|覆盖文档附件和文件对象|
|对象存储配置数据|配置备份|变更后同步备份|根据客户策略执行|覆盖访问参数和挂载信息|



对象存储中的多份拷贝是集群冗余机制的一部分，不等同于数据备份。

## 4.3 配置文件备份

以下配置包括在备份范围内：

* 安装参数

* 域和协议配置

* 外部依赖地址和端口信息

* 对象存储访问信息 

* 业务相关的配置文件 

# 5. 数据库恢复 

本节适用于所有数据恢复 MySQL, MongoDB和 Redis. 

## 5.1 恢复前的准备 

在执行数据库恢复前完成以下准备工作： 

* 在目标节点上准备一个恢复目录，例如， `/data/restore` 

* 将要恢复的数据放入恢复目录中 

* 验证 `global_config.json` 文件中的中间件配置是否与当前环境匹配 

* 检查恢复节点、恢复点、执行窗口和审批信息 

## 5.2 备份任务检查 

检查计划的数据库备份任务： 

```plain
kubectl get cronjob
```


还需记录以下信息：

* CronJob 名称

* 上次执行时间

* 最近一次执行结果

* 备份文件存储目录

## 5.3 恢复执行

数据库恢复通过一次性任务执行，恢复脚本位于备份镜像中。

执行步骤如下：

1. 准备恢复任务清单 `db-restore.yaml`

2. 修改 `spec.template.spec.nodeName` 到恢复目录所在的节点

3. 修改 `hostPath.path` 到数据恢复的目录

4. 执行 `kubectl apply -f db-restore.yaml` 命令以进行数据恢复

示例任务列表如下：

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  labels:
    job-name: db-restore
  name: db-restore
spec:
  template:
    metadata:
      labels:
        job-name: db-restore
      name: db-restore
    spec:
      containers:
      - command:
        - /bin/sh
        - -c
        - cd /data/pri-init/scripts/backup && sh restore_all.sh
        image: registryo.shimo.im/smbase/backup:co
        imagePullPolicy: Always
        name: db-restore
        volumeMounts:
        - name: db-config
          mountPath: /data/pri-init/scripts/global_config.json
          subPath: global_config.json
        - name: data
          mountPath: /backup
      dnsPolicy: ClusterFirst
      nodeName: master-1
      volumes:
      - name: db-config
        configMap:
          name: init-invoker
          items:
          - key: global_config.json
            path: global_config.json
      - name: data
        hostPath:
          path: /data/restore
      imagePullSecrets:
      - name: ee
      restartPolicy: Never
      schedulerName: default-scheduler
```


## 5.4 执行说明

数据库恢复任务执行后，以下数据将被回滚：

* MySQL

* MongoDB

* Redis

在恢复期间，业务数据可能会被覆盖。请在执行前完成停机安排和数据验证。

# 6. 对象存储恢复

本节适用于 MinIO 以及 S3-兼容对象存储恢复。

## 6.1 备份方法

对象存储的常用备份方法如下：

|方法|适用场景|描述|
|:----|:----|:----|
|Rsync 同步复制|独立环境|适合目录级冷备份|
|磁盘快照|独立环境|适合在同一存储平台上的快速恢复|
|`mc mirror`|独立或集群环境|适合对象数据的冷备份与恢复|
|站点复制 / 存储桶复制|集群环境|适用于灾难恢复复制|



## 6.2 恢复执行

在独立环境中常用的恢复方法如下：

* 使用 Rsync 进行备份时，进行反向同步以恢复数据目录

```plain
rsync -av backup:/data/minio/ /data/minio/
```


* 使用 `mc mirror` 进行备份时，执行反向镜像恢复

```plain
mc mirror backup-minio/ new-minio/
```


集群环境的恢复指南如下：

* 当存在灾难恢复副本时，按主备切换方案进行恢复

* 使用冷备份时，根据对象存储数据目录或镜像仓库的内容进行恢复

## 6.3 执行说明

在恢复对象存储之前，需要确认以下事项：

* 恢复目标桶范围

* 恢复点

* 是否覆盖在线对象

* 目标存储路径及权限配置

* ACCESS_DOMAIN 恢复后的网关配置

# 7. 恢复后验证

恢复完成后，至少验证以下内容：

* 数据库服务状态正常

* 对象存储服务状态正常

* 可通过管理面板管理

* 用户登录正常

* 核心文档可以正常创建、编辑、保存、导入和导出

* 数据恢复点符合预期

恢复完成后记录以下信息：

* 恢复执行时间

* 数据恢复时间点

* 执行人、批准人、检查人

* 恢复后发现的问题
