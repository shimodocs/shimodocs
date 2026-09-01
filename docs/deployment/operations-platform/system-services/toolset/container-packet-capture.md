# Container Packet Capture

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## Overview

Container packet capture is used to collect network data from running Pods. Kubernetes helps you analyze issues such as connection failures, request timeouts, TCP retransmissions, and network congestion.

After the capture is complete, you can download the PCAP file and further inspect it using network analysis tools like Wireshark.

## Accessing the Page

After logging into the management console, select **Container Packet Capture** in the left navigation to enter this page.

Container packet capture is only available for Kubernetes deployment environments and is only accessible to administrators.

## Starting Packet Capture

It is recommended to follow these steps:

1. Search for and locate the target Pod in the **Pods List**.
2. Ensure the Pod is in a running state, then click **Start Capture**.
3. Select the capture duration: 1 minute, 5 minutes, or 30 minutes.
4. Select the capture file size: 100 MB, 500 MB, or 1 GB.
5. Select filter criteria as needed, or manually enter a tcpdump filter expression.
6. View the complete capture command displayed on the page.
7. Click **Start Capture** to create the task.

Only one packet capture task can run on the same Pod at the same time. The task will automatically end after the set duration, or it can be stopped manually.

## Filter Criteria

Setting filter criteria can reduce irrelevant traffic and file size. The page provides some commonly used presets, such as:

- Traffic for a specified port.
- gRPC traffic.
- Specified host and port.
- HTTP POST requests.
- TCP connection establishment, retransmission, or small window packets.

You can also manually input using tcpdump syntax, for example:

```text
host 10.0.0.1 and port 80
```

If no filter conditions are specified, the task may collect a large amount of network traffic from the Pod.

## Managing Packet Capture Tasks

On the **Packet Capture Tasks** page, you can view the task ID, Pod, status, creation time, and run time.

- **Running**: The task can be manually stopped.
- **Completed**: The PCAP file can be downloaded.
- **Failed**: You can view the task logs to understand the reason for the failure.

The task list refreshes automatically, or you can click **Refresh** to manually get the latest status.

## Download and Analysis

After the task is completed, click **Download** to get the PCAP file. The download function depends on the system correctly configured object storage.

The PCAP file may contain request addresses, protocol data, or other sensitive information. Please provide it only to authorized personnel, and store or delete it properly after use.

## Common Scenarios

- **Pod not found**: The page only displays Pods that are running in the current environment. Please check the Pod status and deployment environment.
- **Unable to start packet capture**: Please ensure that there is no ongoing packet capture task on the Pod, and check the permissions and support of the Kubernetes ephemeral container.
- **Task execution failed**: Check the task logs to verify the filtering expressions, Pod status, and whether the packet capture components are working properly.
- **Unable to download files**: Please check the object storage configuration and network connection.
- **Packet capture file too large**: Reduce the packet capture duration and use more precise filtering expressions.

> Packet capture consumes certain network, CPU, and storage resources. Please avoid performing long-duration, high-traffic, and unfiltered captures during peak periods.
