# Deploying with Object Storage

[← ShimoDocs Suite Deployment Documentation](../../README.md)

This article explains how to disable the built-in MinIO in the ShimoDocs installer and configure your own S3 object storage as a third-party S3 object storage. Once configured, the installer will check the S3 object storage network connection, authentication information, and the read/write permissions of the bucket. Once the check passes, deployment can continue.

# 1. Preparations Before Configuration
Before starting, please confirm:
- S3 object storage is installed and running normally.
- K8s cluster nodes can access the host and port of the object storage.
- The authentication AK/SK for connecting to the object storage is ready.
- S3 storage must support client browser access.
- It is recommended to use a separate instance.
- Standard S3 must provide standard protocol access addresses for both internal and external networks. 
- ShimoDocs business standard S3 buckets should be created in advance. 
- S3 storage must use SSD disks. 

## ShimoDocs Business Standard S3 Bucket List 

| Bucket Name | Access Permission | Allowed Sources | Allowed Methods | Access Mode | Exposed Headers | 
| --- | --- | --- | --- | --- | --- | 
| Auto Mention | Private Read/Write | - | - | Internal Network |  | 
| Generate Payload | Private Read/Write | - | - | Internal Network |  | 
| FC Task | Private Read/Write | - | - | Internal Network |  | 
| File Change Set | Private Read/Write | - | - | Internal Network |
| Calculated Files | Private Read/Write | * | GET/HEAD | Intranet Extranet | 
| File Content | Private Read/Write | * | GET/HEAD | Intranet Extranet | x-amz-meta-head x-amz-meta-length x-amz-meta-bytes x-amz-meta-delta-version x-amz-meta-eek x-amz-meta-formula-cache x-amz-meta-compressor |
| File Template | Private Read/Write | - | - | Intranet Extranet |
| Worksheet History | Private Read/Write | - | GET/HEAD | Intranet Extranet | Access-Control-Allow-Origin x-amz-meta-compressor |
| Service Document History | Private Read/Write | * | GET/HEAD | Intranet | Access-Control-Allow-Origin x-amz-meta-compressor | 
| Graphite Assets | Public Read Private Write | * | GET/HEAD | Intranet Extranet | 
| Graphite Attachments | Private Read/Write | * | GET/POST/PUT/HEAD | Intranet Extranet |  |  |  |
| Graphite Images | Private Read/Write | * | GET/POST/PUT/HEAD | Internal Network External Network |  |  |  |
| Graphite Users | Private Read/Write | - | - | Internal Network External Network |  |  |  |
| Graphite Avatar | Public Read Private Write | * | GET | Internal Network External Network |  |  |  |
| Preview | Private Read/Write | * | GET/HEAD | Internal Network External Network | Accept-Ranges x-amz-meta-head x-amz-meta-length x-amz-meta-bytes x-amz-meta-delta-version x-amz-meta-eek x-amz-meta-formula-cache x-amz-meta-compressor |  |  |
| svc-drive | Private Read/Write | * | GET/POST/PUT | Internal Network External Network | Accept-Ranges |  |  |
| svc-table | Private Read/Write | * | GET/HEAD | Internal Network External Network |  |  |  |
| File Snapshot | Private Read/Write | - | - | Internal Network External Network |  |  |  |

## Bucket Configuration Instructions
- Exposed_ It is recommended to specify the header name in headers instead of using the * symbol, because some vendors may not support the * symbol, such as Huawei Cloud OBS and Alibaba OSS.
- The bucket name can be configured with a prefix according to the company's requirements to avoid duplication.

# 2. Enter Advanced Settings
In the "Configuration" step of the installer, after completing the network, target environment, and node information configuration, expand the "Advanced Configuration" at the bottom of the page.

# 3. Uncheck Built-in MinIO Installation
In the "Middleware Services" area, uncheck MinIO.

After unchecking, the installer will no longer install the built-in MinIO, and will use the externally prepared S3 object storage that has been prepared later. Whether other middleware uses built-in services should be selected according to the actual deployment plan.

# 4. Open Third-Party Middleware Configuration
In the "Third-Party Middleware" area, click "Configure."

# 5. Configure S3 Object Storage
1. Select "Object Storage" on the left side of S3.
2. Enable "MinIO Object Storage."
3. For main service/editor interaction, enter respectively: AK/SK, internal endpoint, public endpoint, host, port, SSL, and other information.
4. Verify and save.

> [!TIP]
>
> Main service: the object storage instance used for services other than collaborative editing
> Editor interaction: the object storage instance used by the collaborative editing service
> Note: The main service and editor interactions can use the same object storage instance, but providing a separate instance for editor interactions can achieve better collaborative editing performance.

## Bucket Naming

> [!NOTE]
>
> When multiple business applications share the same S3 instance, customers can add prefixes according to the ShimoDocs bucket naming rules to help distinguish different businesses and manage buckets.

# 6. Confirm Verification Results
The installer will check the following:
- Login: This account can authenticate successfully
- Connectivity: The deployment environment can access the S3 object storage
- Permissions: This account has permissions for connection, authentication, and bucket read/write operations

When all checks show "Success," close the configuration window and return to the "Configuration" page of the installer.

If there are any failures, please check according to the page prompts:
- Whether the host and port are correctly filled in.
- Whether the network between the deployment node and the S3 object storage is connected.
- Whether USERNAME and PASSWORD are correct. 
- Whether the account has the required permissions (connection and authentication, bucket read/write permissions, etc.).

# 7. Continue initializing deployment
After returning to the “Configuration” page, make sure S3 object storage remains unchecked, then click “Initialize Deployment” to continue completing the deployment overview, checks, and execution steps.

> [!TIP]
>
> Before initializing the deployment, please reconfirm that the S3 object storage configuration has been saved and all verification items have passed.
