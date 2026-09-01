# Resource Planning

[← ShimoDocs Suite Deployment Documentation](../README.md)

## 1. Purpose of the Document

This document is intended to guide the planning of server and middleware resources in privatized deployment scenarios, for reference by implementation engineers, operations engineers, and pre-sales technical support personnel.

The content of the document is based on historical project capacity planning, example configurations, and middleware baselines. It can be used for pre-sales estimation, resource requests, implementation deployment, and subsequent expansion evaluation.

## 2. Scope and Description

### 2.1 Scope

This document is applicable to the preliminary planning of application nodes and middleware resources for different user scales in privatized deployment scenarios.

### 2.2 Description

* The configurations in this document are recommended configurations for early-stage project capacity assessment and resource planning.

* Application node resources and middleware resources should be calculated separately, and mixed planning is not recommended.

* In large-scale user scenarios, middleware resources need to be further calibrated based on business peaks, concurrency models, capacity stress test results, and production monitoring data.

* In the official production environment, it is recommended to reserve extra capacity and prioritize high availability construction.

* If using domestic CPU architecture servers, it is recommended to estimate overall resources as twice the standard specifications.

## 3. Planning Principles

### 3.1 Application and Middleware Deployment Principles

* For scenarios with fewer than 10,000 users, it is possible to evaluate whether to deploy some middleware K8s clusters among them.

* For scenarios with 10,000 users or more, it is recommended to fully separate the deployment of application nodes and middleware.

* Core middleware such as databases, caches, message queues, and search services are recommended to be deployed with high availability architectures first.

* Where conditions allow, it is recommended to prioritize the use of mature public cloud managed middleware services to improve stability and maintainability.

### 3.2 Object Storage Planning Principles

* Prefer to use public cloud object storage services, such as Alibaba Cloud OSS, Huawei Cloud OBS, Tencent Cloud COS, AWS S3.

* If using privately deployed object storage, SSD must use disks, and after capacity expansion, performance, stability, and operability need to be carefully evaluated.

* If the business involves a large number of large file uploads, downloads, previews, or large spreadsheet editing scenarios with multi-user collaboration, it is recommended to prioritize the use of independent object storage services.

## 4. Application Node Planning

### 4.1 Application Node Specification Classification

#### Specification A

* Recommended Specification: `24C / 48G / >=500G SSD * N`

* Applicable Scope: fewer than 10,000 users

* Applicable Functions:

   * Can support small and medium-sized enterprise scenarios

   * Middleware can be deployed in a K8s environment according to the project

   * Single node bears high load; when the node fails, the impact range is relatively large

#### Specification B

* Recommended Specification: `16C / 32G / >=300G SSD * N`

* Applicable Scope: 10,000 users and above

* Applicable Features:

   * Suitable for large-scale, high-availability deployment scenarios

   * Must use independent middleware

   * Uses a small-specification multi-node approach, providing more balanced scheduling and more flexible expansion

   * When a node is under maintenance or encounters problems, the impact on overall business is minimal



### 4.2 Application Node Computing Standards

Based on existing project examples and capacity calculation rules, it is recommended that application nodes be estimated according to the following formula:

`Number of nodes = Number of users × 0.03 ÷ 160`

It can be simply understood as:

`Number of nodes ≈ Number of users ÷ 5300`

Location:

* The concurrent user coefficient is estimated to be `0.03`.

* The capacity of a single node is approximately `16C / 32G` `150 ~ 180 QPS`.

* It is recommended to use `160 QPS/node` as the calculation basis.

* It is recommended to round up the calculation results and reserve extra capacity for expansion.

### 4.3 Recommended Configuration Table for Application Nodes

| User Scale (Number of Users) | Node Specifications | Recommended Quantity | Deployment Suggestions |
|:----|:----|:----|:----|
|500|24C / 48G / 500G SSD|1 unit|Can be deployed on a single machine; for high availability, it is recommended to deploy at least 3 servers|
|3000|24C / 48G / 500G SSD|3 units|Cluster mode, high availability deployment (minimum specification threshold for cluster deployment)|
|10,000|24C / 48G / 500G SSD|3 units|Cluster mode, high availability deployment; use of external middleware can be evaluated based on project needs|
|30,000|16 cores / 32G / 300G SSD|5 units|Cluster mode, high availability deployment, using dedicated middleware|
|50,000|16 cores / 32G / 300G SSD|10 units|Cluster mode, high availability deployment, using dedicated middleware|
|100,000|16 cores / 32G / 300G SSD|18 ~ 20 units|Recommended to start with 18 units and reserve capacity for expansion, using dedicated middleware|
|200,000|16 cores / 32G / 300G SSD|38 ~ 40 units|Recommended to build and deploy in phases|
|300,000|16 cores / 32G / 300G SSD|56 ~ 60 units|Recommended to build and deploy in phases|
|500,000|16 cores / 32G / 300G SSD|94 ~ 100 units|It is recommended to plan independent resource pools and construct and deploy in phases|
|700,000|16 cores / 32G / 300G SSD|132 ~ 140 units|It is recommended to plan independent resource pools and construct and deploy in phases|

### 4.4 Application Node Planning Conclusions

* For a user count below 10,000, it is recommended to use Specification A.

* For a user count of 10,000 or more, it is recommended to use Specification B.

* For a user scale of 100,000, you can start from 18 units according to the sample anchor point; other scales can be estimated using a unified formula and rounded up.

* For projects with continuous growth, it is recommended to adopt a phased expansion strategy to avoid excessive one-time investment.

## 5. Middleware Planning

### 5.1 Middleware Classification Principles

Current middleware resource planning is executed according to two baseline categories:

* `Users below 3,000`: Using a small-scale baseline configuration.

* `3000 users and above`: Using a large-scale baseline configuration.

For larger-scale scenarios, such as 10,000, 30,000, 50,000, 100,000, 200,000, 300,000, 500,000, 700,000 users, it is recommended to start uniformly from the "3000 users and above" baseline configuration and dynamically scale according to business growth.

### 5.2 Middleware Specification Baseline Table

| Middleware | Recommended Version | Below 3000 Users | 3000 Users and Above | High Availability Requirements | 
|:----|:----|:----|:----|:----|
|MySQL|MySQL 8.0|4 cores / 8G / 200G SSD|8 cores / 16G / 200G SSD|Master-slave switch high availability<br>Character set: utf8mb4<br>Time zone: Asia/Shanghai or UTC<br>Connections: max_connections ≥ 1000| 
|MongoDB|MongoDB 4.4|2 cores / 8G / 100G SSD|4 cores / 16G / 100G SSD|Replica set high availability cluster| 
|Redis|Redis 6.2.21|2 cores / 4G / 100G SSD|2 cores / 8G / 100G SSD|Master-slave/sentinel high availability, data persistence; cluster mode not supported; number of databases ≥ 64| 
|Kafka|Kafka 3.5|2 cores / 4G / 300G SSD|4 cores / 8G / 300G SSD|Number of brokers >= 3, default replication factor 3<br>Message retention: 72 hours (can be adjusted according to business requirements)<br>Maximum single message size per topic: 10 MB<br>Authentication: supports SASL encrypted access (PLAIN, SCRAM-SHA-256, SCRAM-SHA-512)|
|Elasticsearch|ES 8.18.5|2 cores / 4G / 200G SSD|4 cores / 8G / 200G SSD|Number of nodes >= 3<br>Required components:<br>analysis-ik (Chinese word segmentation),<br>analysis-pinyin (Pinyin word segmentation)|
|Object Storage|S3 Protocol Compatible|Compatible with S3|Compatible with S3 Protocol|Prefer public cloud, must support HTTPS external access|

Note:

* The above middleware specifications need to be scaled according to actual load.


## 6. Implementation and Operation Recommendations

### 6.1 Deployment Implementation Recommendations

* MySQL, MongoDB, Redis, Kafka, Elasticsearch are recommended to be deployed in high-availability cluster mode.

* If conditions permit, it is recommended to give priority to using public cloud managed database and middleware services to improve stability and maintainability.

* For scenarios with 10,000 or more users, it is recommended to deploy application nodes separately from middleware.

* For Kafka, it is recommended to use a dedicated instance to avoid sharing resources with other businesses.

### 6.2 Object Storage Implementation Recommendations

* It is recommended to give priority to using public cloud object storage products.

* If using private object storage, SSD must use disks. 

* If the team space involves scenarios of uploading, downloading, or previewing large files, the capacity, throughput, and bandwidth of object storage should be the main evaluation factors. 

### 6.3 Expansion Considerations 

In the following business scenarios, it is recommended to prioritize evaluating and increasing middleware resources: 

* Large numbers of attachments are uploaded, downloaded, or previewed 

* High-frequency full-text searches 

* Message accumulation or intensive asynchronous tasks 

* Bulk writes and statistical analysis during peak periods 

* Continuous growth of log volume 

Key metrics to focus on include: 

* Database: CPU, memory, disk IO 

* Redis: number of connections, hit rate, bandwidth usage 

* Kafka: number of brokers, message accumulation, disk space 

* Elasticsearch: number of nodes, index size, storage capacity 

* Object storage: read/write performance, request throughput, capacity, bandwidth






## 7. Conclusion 

* For small-scale scenarios (fewer than 10,000 users), it is recommended to use application node configurations of Specification A and evaluate on a project basis whether to deploy certain middleware within the cluster.

* For medium to large-scale scenarios (10,000 users and above), it is recommended to use application node configurations of Specification B, along with standalone middleware and a high-availability architecture.
* Middleware configuration is recommended based on two baselines: "under 3,000 users" and "3,000 users and above." For large projects, continuous scaling should be based on stress testing and monitoring data.
* Before formal implementation, resource planning confirmation, compatibility verification, and capacity stress testing should all be completed to avoid inconsistencies between deployment specifications and actual support capabilities.

* If using servers with a domestic CPU architecture, it is recommended to estimate resources at twice the standard specification.

* This manual is for reference in pre-installation selection and cannot replace on-site stress testing or final implementation.
