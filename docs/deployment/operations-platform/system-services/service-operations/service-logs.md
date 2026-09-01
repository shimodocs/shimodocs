# Service Logs

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## Feature Overview 
The service logs feature is a Kibana-like log retrieval platform that can collect logs from various Pods and provides log search, query, and ratio analysis functions. ShimoDocs  

## Access and Navigation
Left menu: System Services --> Service Operations --> Service Logs

## SQL Mode
The input box supports ClickHouse SQL syntax queries. After entering SQL, you can execute the query in ClickHouse native mode.

As shown in the figure below, enter

``` sql
`_raw_log_` like '%access%'
```

Can be used to query all logs containing access. 

## Conditional Filtering
As shown in the figure below, click the "Add Condition" button to add a new filter condition.

## Ratio Analysis
As shown in the figure below, click the icon next to a field in the row record to open the drop-down menu. After selecting "Top Values," you can view the ratio of that field within the current time range in the upper right corner.

## Field Description

| Built-in Field | Description |
| --- | --- |
| Level | The error level of the log, including Information, Error, Warning |
| Container Name | CONTAINER_NAME |
| Method | The method in the access log; gRPC prints gRPC method, HTTP prints API path |
| Peer IP | Peer IP |
| Peer Name | The name of the peer, such as service name, etc. |
| Component | Components in the access log, such as server.begin |
| Duration | Time spent in the access log, in milliseconds |

## Case Analysis
### Query All Error Logs of the Day

In "Add Condition", select the lv field and add lv = error, as shown in the figure below

### View Request Logs

1. Use `msg`='access' to view all request logs, including HTTP and gRPC

2. View HTTP requests

3. View gRPC requests

