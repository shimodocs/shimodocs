# Middleware Configuration

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## 1. Overview

Middleware Configuration is a page in the MDP Operations Platform used to integrate with various storage and middleware in the client environment, centralizing the management of connection information for components such as S3 object storage, Redis, message queue Kafka, relational database MySQL, document database MongoDB, and full-text search Elasticsearch. Configuration changes are sent to the client environment through asynchronous tasks, and the testing and deployment progress can be displayed in real time during the change process.

Main features:

- Configure connection information for each middleware (endpoint, access key, USERNAMEPASSWORD, etc.)
- Switch between different vendors (S3 and OSS, AWS / MinIO / Tencent Cloud COS / Huawei Cloud OBS, MySQL and DM Dameng)  
- Track dirty values in forms; only modified fields will be submitted  
- Each configuration section can be tested independently; publishing is only allowed after validation passes  
- One-click publishing: batch submit all dirty field configurations and execute asynchronously  

### 1.1 Applicable Users  

| Role             | Common Operations                                  |  
| ---------------- | ------------------------------------------------- |  
| Implementation Engineer | Fill in middleware connection information during initial deployment |
| Operations and Maintenance Duty | Credential replacement, endpoint changes, test connections | 
| Emergency Response | Switch backup middleware and modify timeout configuration |

### 1.2 Operations Not Recommended in This Module

Switching providers (e.g., S3 → OSS) involves large-scale downstream data migration changes and should be handled according to the change process. Bulk adjustment of connection information across multiple environments is not covered in this module; you need to enter each environment individually for page configuration. Middleware capacity planning and monitoring alerts are not included on this page; please use the cluster management and alert configuration modules.

---

## 2. Detailed Description of Each Middleware Configuration

### 2.1 S3 Object Storage

**Operation Steps**: Enter "Middleware Configuration" from the left menu. It defaults to this section → scroll down to see three configuration blocks in order: Public S3 instance settings, Collaborative Editing S3 instance settings, and Bucket settings.

#### 2.1.1 Public S3 and Collaborative Editing S3

**Operation Steps**: First fill in the public information S3 instance settings, then fill in the collaborative editing S3 instance settings, and finally fill in the bucket settings. After modifying any field, click the "Test Connection" at the bottom.  
The form fields for the two sections are the same:  
| Field               | Description                                                              | Required |  
| ----------------- | ---------------------------------------------------------------------- | ---- |
| Storage Type       | Dropdown select 'S3 (Object Storage)' or 'OSS (Alibaba Cloud)'                             | Yes   |
| Subtype             | Dynamically loaded based on storage type: for S3, options include AWS / Tencent Cloud COS / Huawei Cloud OBS / MinIO / Others; for OSS, only Alibaba Cloud OSS is available | Yes    |
| Access Key ID       | Credential identifier                                                        | Yes   |
| Secret Access Key   | Credential key, input box PASSWORD is hidden | Yes |
| Region              | For example `cn-north-1` | Yes |
| Enforce Path Style | Checkbox, whether to enable path-style access | None |
| SSL | Checkbox, whether to enable HTTPS | None |
| Endpoint | Internal service access address | Yes |
| Public Access Address | User-side access address | Yes |
| Address Replacement Rule | Regular expression or string used to map internal addresses to public addresses | Yes |

#### 2.1.2 Bucket Settings

**Steps**: All buckets returned by the server will be presented one by one, and you can fill in the CDN domain name as needed.

| Field       | Description                  |
| --------- | ------------------------- |
| Bucket Name | Name of the bucket         |
| Prefix      | Object storage path prefix  |
| CDN Domain | CDN Acceleration Domain |
| Enable CDN Authentication | Checkbox, when enabled two items 'Authentication Type' and 'Authentication Key' will be added |

> After enabling CDN authentication, the corresponding authentication type and authentication key are required.

### 2.2 Redis
**Steps**: Use the quick navigation on the right to click the Redis icon to scroll to this section → select a mode → fill in the address and PASSWORD → click 'Test Connection'.

Field Description:

| Field | Description |
| ----- | ----------- |
| Mode  | Standalone mode or Sentinel mode |
| Address | Connection address in standalone mode, e.g., `redis-sentinel-master-ss:6379` |
| Master Name | Required in sentinel mode, e.g., `mymaster` |
| Address List | Multiple addresses in sentinel mode, can be added/removed dynamically |
| PASSWORD | Required |

Switching modes will automatically reset the address, master name, and address list fields.

### 2.3 Kafka
**Operating Steps**: Click the icon in the quick navigation on the right of Kafka to scroll to this section → Fill in the Broker address → If SASL is enabled, expand the SASL subfields → Click "Test Connection".

Field Description:

| Field           | Description                                       |
| --------------- | ------------------------------------------------ |
| Broker Address  | Array, can be dynamically added/removed         |
| Topic Prefix    | Automatically added to the prefix of all topics |
| Enable SASL Authentication | Switch, when enabled adds three SASL configurations |
| Authentication Mechanism | PLAIN / SCRAM-SHA-256 / SCRAM-SHA-512 (when SASL is enabled) |
| USERNAME / PASSWORD | SASL credentials (when SASL is enabled) |

### 2.4 MySQL (Relational Database)
**Operation Steps**: In the quick navigation on the right, click the RDB icon and scroll to this section → select MySQL or DM Dameng → fill in host, port, USERNAMEPASSWORD → click "Test Connection".

Field Description:

| Field      | Description        |
| -------- | ---------------- |
| Database Type | MySQL or DM (Dameng) |
| Host Address  | For example `mysql-master` |
| Port          | 3306           |
| USERNAME / PASSWORD | Credentials      |

> The “RDB Relational Database” in the right-hand menu and page title corresponds to the MySQL configuration section.

### 2.5 MongoDB
**Steps**: Click the icon in the quick navigation to the right of MongoDB to scroll to this section → Fill in the connection string → Render each database credential according to the server configuration one by one → Click “Test Connection.”

Field Descriptions: 

| Field            | Description                         |
| ---------------- | ----------------------------------- |
| Connection String | For example `mongo-master:27017` |
| Each Database USERNAME / PASSWORD | Presented one by one for each database configured on the server |

### 2.6 Elasticsearch
**Steps:** Use the quick navigation on the right to click the Elasticsearch icon and scroll to this section → Fill in the host address and port → If authentication is enabled, fill in USERNAME and PASSWORD → Click "Test Connection".

Field Description:

| Field     | Description       | Required |
| ----      | --------------  | ----    |
| Host Address | For example, `elasticsearch` | Yes      |
| Port        | 9200             | Yes      |
| USERNAME    | ES credentials   | No       |
| PASSWORD    | ES Credential   | No       |

---

## 3. Common Operations 

### 3.1 Credential Update (e.g., Access Key Rotation) 

1. Go to Middleware Configuration
2. Replace Access Key ID and Access Key Secret in the ... S3 Public Card
3. Click “Test Connection” and wait for the green “Connection Test Successful” message
4. Repeat connection test for other modified parts
5. Click “Publish Configuration” at the bottom
6. The system prompts that an asynchronous task has been created and jumps to the task log tab

### 3.2 Switching Middleware Vendor 

1. Go to Middleware Configuration
2. Modify the “Storage Type/Subtype” in the corresponding card, as well as the new vendor's endpoint, access keys, address replacement rules, etc.
3. After modification, click “Test Connection” to verify
4. Click “Publish Configuration”

> Switching suppliers will involve reloading the connection pool of downstream services, so please avoid doing this during peak business hours; after switching, it is recommended to monitor the application logs for 5 to 10 minutes. 

### 3.3 Enabling Kafka SASL 

1. Go to the middleware configuration and locate the Kafka section 
2. Turn on the “Enable SASL authentication” switch, the SASL fields will expand 
3. Fill in the authentication mechanism, USERNAME, and PASSWORD 
4. Click “Test Connection” 
5. Once successful, click “Publish Configuration” 

### 3.4 Recovering from Error Operations 

Before clicking “Publish Configuration,” the form state will be saved in the browser’s localStorage. You can recover it in the following ways: 

- Click the “Reset All” button at the bottom, all fields will revert to the initial server values 

### 3.5 Asynchronous Task Tracking

After successfully publishing the configuration, the system will jump to the task log tab to display the task progress. Tasks may be long or short, depending on the number of middleware instances and the number of fields changed. 

---

## 4. Frequently Asked Questions

**Q1: Clicking on the quick navigation at the top right of Redis does not respond.**

The quick navigation on the right only scrolls to the corresponding section. If that section is not on the current page (for example, obscured by a pop-up), you can scroll the page or click the Redis icon in the right navigation again to reposition.

**Q2: After publishing the configuration, the status does not seem to update.**

The page will automatically refresh after publishing. If the browser does not refresh automatically, you can manually press F5 to get the latest configuration.

**Q3: The number in “N configurations modified” does not match the actual count.

Page counting is based on the dirty fields of the form. In some cases, such as modifications after a reset or dynamically adding/removing array items, it may cause inconsistent counts. You can click "Reset All" and refill the entries.

**Q: I can't find the Bucket I want to add in the Bucket settings card.**

The page renders existing Buckets based on server-side configuration. Adding a new Bucket requires modifying the underlying server configuration files, not through this page. If you need to add one, please contact the implementation engineer.

---

## Appendix A: Glossary

| Term       | Explanation                                                                |
| -------- | ----------------------------------------------------------------- |
| Server Configuration File | The ultimate source of configuration maintained by the platform, formed by merging the platform default values with the values published on this page |
| Bucket   | The location of the storage bucket S3 / OSS |
| Endpoint | Middleware service address, used for internal cluster access |
| Public Access Address | Middleware address visible to users |
| Address Mapping Rule | Regular expression or string to map internal addresses to public addresses |
| SASL     | Simple Authentication and Security Layer, used as an authentication mechanism for components like Kafka |
| Sentinel | One of Redis's high-availability schemes |
| DM       | Dameng Database (domestic relational database)                                             |
| Dirty Field       | Fields in the form that have been modified and are different from the initial value          |
