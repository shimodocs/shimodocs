# Data Backup

[← ShimoDocs Suite Deployment Documentation](../README.md)

This document explains the scope of data backup, recovery requirements, execution methods, and post-recovery verification items in a privatized environment for ShimoDocs.

This document covers the following content:

* Backup scope and responsibility boundaries
* Database backup and recovery requirements
* Object storage backup and recovery requirements
* Pre-recovery confirmation items
* Post-recovery verification items

This document does not cover the following content:

* Initial installation and deployment steps
* Upgrade and migration plans
* Instructions for recovery tools specific to third-party middleware vendors
* Production incident handling procedures

# 2. Backup Scope and Responsibility Boundaries

## 2.1 Backup Scope

The data that needs to be included in the backup scope in the ShimoDocs privatized environment includes:

* MySQL data
* MongoDB data
* Redis data

* Object storage data

* Installation configuration and environment parameter files

Data directories, backup directories, and backup retention periods are uniformly managed by the client.

## 2.2 Responsibility Boundaries

The boundaries of backup and recovery responsibilities are as follows:

* The client is responsible for formulating and executing the official backup strategy

* The client is responsible for the proper safekeeping of backup files, media security, and retention period management

* The client is responsible for recovery drills, recovery approvals, and acceptance of recovery results

* ShimoDocs can provide technical support and guidance for recovery operations

When it involves external middleware, self-built object storage, or client-maintained infrastructure, the backup and recovery strategy is entirely the responsibility of the client.

# 3. Confirmation Before Recovery Execution

Data recovery is a high-risk operation. The following confirmations must be completed before execution.

## 3.1 Target Confirmation

Before recovery, please clarify the following information:

* Target environment

* Target cluster, node NAMESPACE

* Data range to be restored

* Restoration time point

* Execution time window

## 3.2 Risk Confirmation

Before restoration, please confirm the following items:

* Whether this restoration will overwrite the current online data

* Whether downtime is required for this restoration

* Whether the latest backup has been added to the current online data

* Whether the rollback point after restoration failure is clear

## 3.3 Backup Validity Confirmation

Before restoration, please check the following:

* Whether the backup files are complete and readable

* Whether the backup time point meets the recovery objective

* Whether the backup directory is correctly mounted

* Whether all configuration files required for restoration are complete

* Whether the backup files have passed recoverability verification

# 4. Backup Strategy

## 4.1 Database Backup

The database backup standards are as follows:

|Scenario|Execution Method|Frequency|Retention Period|Description|
|:----|:----|:----|:----|:----|
|Using ShimoDocs built-in middleware|System scheduled backup|Once per day|7 days|Executed by scheduled tasks within the cluster|
|Using customer self-maintained middleware|Customer-side backup|Once per day or more|7 days or longer|Executed according to customer-side policies|

Database backups must at least cover:

* MySQL
* MongoDB
* Redis

## 4.2 Object Storage Backup

The standards for object storage backup are as follows:

|Data Type|Execution Method|Frequency|Retention Period|Description|
|:----|:----|:----|:----|:----|
|Object storage business data|Cold backup or disaster recovery replication|Executed according to business level|Executed according to customer policies|Covers document attachments and file objects|
|Object storage configuration data|Configuration backup|Backup synchronized after changes|Executed according to customer policies|Covers access parameters and mount information|



Multiple copies in object storage are part of the cluster redundancy mechanism and are not equivalent to data backup.

## 4.3 Configuration File Backup

The following configurations are included in the backup scope:

* Installation parameters
* Domain and protocol configurations
* External dependency addresses and port information
* Object storage access information
* Business-related configuration files

# 5. Database Recovery

This section applies to all data recovery for MySQL, MongoDB, and Redis.

## 5.1 Preparations Before Recovery

Complete the following preparations before performing database recovery:

* Prepare a recovery directory on the target node, for example, `/data/restore`
* Place the data to be recovered into the recovery directory
* Verify whether the middleware configurations in the `global_config.json` file match the current environment
* Check the recovery node, recovery point, execution window, and approval information

## 5.2 Backup Task Check

Check the scheduled database backup tasks:

```plain
kubectl get cronjob
```


The following information also needs to be recorded:

* CronJob name

* Last execution time

* Most recent execution result

* Backup file storage directory

## 5.3 Restore Execution

Database restoration is carried out through a one-time task, with the restore script located in the backup image.

The execution steps are as follows:

1. Prepare the restore task list `db-restore.yaml`

2. Modify `spec.template.spec.nodeName` to the node where the restore directory is located

3. Modify `hostPath.path` to the directory for data restoration

4. Execute the command `kubectl apply -f db-restore.yaml` to perform data restoration

An example task list is as follows:

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


## 5.4 Execution Instructions

After the database recovery task is executed, the following data will be rolled back:

* MySQL

* MongoDB

* Redis

During the recovery process, business data may be overwritten. Please complete downtime arrangements and data verification before execution.

# 6. Object Storage Recovery

This section applies to MinIO and S3-compatible object storage recovery.

## 6.1 Backup Methods

Common backup methods for object storage are as follows:

|Method|Applicable Scenario|Description|
|:----|:----|:----|
|Rsync Sync Copy|Standalone Environment|Suitable for directory-level cold backups|
|Disk Snapshot|Standalone Environment|Suitable for fast recovery on the same storage platform|
|`mc mirror`|Standalone or Cluster Environment|Suitable for cold backup and recovery of object data|
|Site Replication / Bucket Replication|Cluster Environment|Applicable for disaster recovery replication|

## 6.2 Recovery Execution

Common recovery methods in an independent environment are as follows:

* When using Rsync for backup, perform reverse synchronization to restore the data directory

```plain
rsync -av backup:/data/minio/ /data/minio/
```


* Perform reverse image restore when backing up using `mc mirror`

```plain
mc mirror backup-minio/ new-minio/
```


The cluster environment recovery guide is as follows:

* When a disaster recovery copy exists, recover according to the primary-backup switching plan

* When using cold backup, recover based on the content of the object storage data directory or image repository

## 6.3 Execution Instructions

Before recovering the object storage, the following items need to be confirmed:

* Recovery target bucket range

* Recovery point

* Whether to overwrite online objects

* Target storage path and permission configuration

* ACCESS_DOMAIN gateway configuration after recovery

# 7. Post-Recovery Verification

After recovery is completed, at least verify the following:

* Database service status is normal

* Object storage service status is normal

* Manageable through the management panel

* User login is normal

* Core documents can be created, edited, saved, imported, and exported normally

* Data recovery point meets expectations

After the recovery is completed, record the following information:

* Recovery execution time

* Data recovery time point

* Executor, approver, and checker

* Issues discovered after recovery
