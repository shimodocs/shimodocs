# Real-Time Logs

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## Feature Overview

Real-time logs are used to view operation logs of services in the cluster online. Kubernetes helps you quickly locate service anomalies, request failures, and execution delays.

Main usage scenarios:
- Quickly filter logs triggered in real time
- A lightweight alternative when a full logging system is not deployed

Note: Real-time logs are obtained through the Kubernetes API. Log data may be affected by Kubernetes rolling updates.

## Accessing the Page

After logging into the management console, select **Real-Time Logs** from the left navigation to enter the page.

Real-time logs are only supported in deployment mode. If you do not see this menu in Kubernetes, please contact your system administrator to confirm the deployment mode and your current account's access permissions.

## Query Logs

It is recommended to follow the steps below:

1. Select the **cluster** and **NAMESPACE** you want to query.
2. Choose the log target, supporting Deployment, StatefulSet, or Pod, and you can select multiple targets at the same time.
3. Select the log range, you can query the last 100 lines, 1000 lines, 5000 lines, or logs from the past 1 minute to 24 hours.
4. To narrow the query results, you can fill in line-level filter conditions.
5. Click **Start** to have the page load logs within the selected range and continuously display newly generated logs.

Click **Stop** to end real-time fetching. When restarting the query, the logs on the current page will be cleared and new query results will be loaded.

## Log Filtering

Line-level filtering is case-insensitive for English letters. Press Enter to apply after entering the conditions. Common usage is as follows:

```text
error
error AND timeout
error OR warning
error NOT health
error AND (timeout OR deadline)
"connection refused"
```

- `AND`: Contains multiple conditions at the same time. 
- `OR`: Contains any condition. 
- `NOT`: Excludes specified content. 
- `()`: Combines multiple filter conditions. 
- `""`: Searches for complete phrases that include spaces. 

You can click the help button on the right side of the input box to view the full syntax examples. You can also use **Common Queries** to quickly fill in the preset log targets and filter conditions. 

## Viewing Logs 

The log list displays the log time, POD_NAME, and log content. 

- Click POD_NAME to copy the full name. 
- When the content is long, you can expand it to view the full log. 
- Logs are displayed in JSON format, can be expanded into formatted content, and support one-click copying. 
- When there are many logs, the page will automatically paginate, and you can use the buttons at the top of the list to quickly jump to the top or bottom. 

## Log Volume Distribution 

The log volume distribution chart on the page shows the number of logs in different time periods and displays the total number of log lines as well as the number of matching lines after filtering.

You can drag to select a time range on the distribution chart, and the log list will only display content within that time range, which is useful for quickly focusing on periods of sudden log spikes or anomalies.

## Page Operations

- **Start**: Pull logs based on the current conditions and continue receiving new logs.
- **Stop**: Stop receiving new logs; the logs that have already been loaded will remain on the page.
- **Clear**: Clear the currently displayed logs; if real-time pulling continues, new logs will keep appearing.

## Common Situations

- **No Logs**: Please ensure the target service is running and try expanding the log time range.
- **No Target Selected**: Please select at least one Deployment, StatefulSet, or Pod.
- **Too Many Targets**: A single query supports up to 20 actual Pods; please reduce your selection and try again.
- **Invalid filter condition**: Please check whether `AND`, `OR`, `NOT`, parentheses, or quotation marks are complete.
- **Log retrieval interrupted**: This may be caused by service restart, network changes, or insufficient permissions. You can click **Start** to try again.

> The page retains a maximum of 500,000 lines of logs. Once the limit is exceeded, older logs will be automatically removed.

## Sample Operation Interface

The figure below shows the workload selection, keyword filtering, and log viewing area of real-time logs.

Click **Select Cluster & NAMESPACE** to switch the target cluster, and then continue to select the workload you want to view. NAMESPACE

