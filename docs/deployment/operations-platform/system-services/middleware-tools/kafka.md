# Kafka Tool

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

> [!TIP]
>
> The Kafka tool allows users to view the cluster status, topics, messages, consumer groups, and partition information in Kafka through the Redpanda console. It is commonly used to troubleshoot message writing, consumer backlog, and asynchronous link issues.
>
> Once the page loads successfully, the Redpanda console will be embedded within the MDP.

## 1. Accessing Kafka

1. Log in to the **MDP Operations Platform**.  
2. Select **System Services** in the left navigation bar.  
3. Expand **Middleware Tools**.  
4. Select **Kafka**.  
5. Wait for the Redpanda console to finish loading.

If the console is not yet ready, the page will indicate that it is starting or failed to start, and display error information.

## 2. View Cluster Overview

After entering Kafka, **the overview is displayed by default**.

You can view the following information:

| Information | Description |
| --- | --- |
| Cluster Status | The operating status of the cluster. |
| Cluster Storage Capacity | The current storage size of the cluster. |
| Cluster Version | Information about the cluster version. |
| Online Brokers | Number of online brokers. |
| Topics | Number of topics. |
| Replicas | Number of replicas. |
| Broker Details | Broker ID, status, and size. |

## 3. View Topics

1. In the left navigation of the Redpanda console, select **Topics**.
2. Find the target topic in the topic list.
3. Click the topic to go to the details page.
4. View information such as partitions, messages, and configurations of the topic.

Topic troubleshooting usually focuses on:

| Information | Description |
| --- | --- |
| Partition | The partition status of the topic. |
| Messages | The list of messages in the topic. |
| Configuration | Topic configuration, such as retention policy. |

## 4. View Messages

1. Enter the target topic.
2. Open the message viewing area.
3. Use the filters provided on the page to select partitions, positions, or time ranges.
4. View the message key, value, headers, partition, offset, and timestamp.

> Message content may contain business fields. When troubleshooting, prioritize locating messages using business ID, key, offset, and timestamp.

## 5. View Consumer Groups

1. In the Redpanda console, select **Consumer Groups** from the left navigation.
2. Search for or select the target consumer group.
3. Enter the consumer group details.
4. View the topics associated with the consumer group, partitions, current offset, log end offset, and lag.

## 6. Determine Consumer Lag

| Status | Description |
| --- | --- |
| Lag is 0 | There is no backlog for the current consumer group. |
| Lag keeps increasing | Consumption speed is lower than production speed. |
| Lag does not change but is not 0 | There may be stopped consumers, partition blockage, or consumption failures. |
| Lag of a single partition is significantly high | It may be caused by hot keys or abnormal consumption of that partition. |

## 7. Check Broker

1. On the overview page, find **Broker Details**.
2. Check Broker ID, running status, and storage size.
3. Click **View** to see broker details.

## 8. Common troubleshooting scenarios

| Scenario | Recommended action |
| --- | --- |
| Verify if Kafka is running normally | Check cluster status and online Brokers in the overview. |
| Confirm if any message has been written | Enter the Topic and check the messages. |
| Troubleshoot consumer lag | Enter the consumer group and check for lag. |
| Locate a single message | Can search by Topic, partition, offset, Key, or timestamp. |
| Confirm Topic configuration | Enter the Topic details and check the configuration. |
