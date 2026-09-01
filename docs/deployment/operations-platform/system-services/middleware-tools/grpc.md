# gRPC Tool

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

> [!TIP]
>
> This tool is used to connect to internal gRPC services, view services and methods, and initiate unary method debug calls.
>
> This page supports three ways to select a target: manually entering the address, selecting a Kubernetes service, or selecting by Pod.

## 1. Access gRPC

1. Log in to **MDP Operations Platform**.
2. Select **System Services** in the left navigation bar.
3. Expand **Middleware Tools**.
4. Select **gRPC**.

The page first displays the "Target" area for selecting the service to connect to.

## 2. Target Selection Methods

The page provides three target modes:

| Mode | Description |
| --- | --- |
| Manual | Manually enter the gRPC address, for example, `svc-user:50051`. |
| Service | Select the target by cluster, namespace, service, and port. |
| Pod | Select the target by cluster, namespace, Pod, and port. |

## 3. Manual Connection

1. Select **Manual**.
2. Enter the service address in the gRPC address input box. **Address** input box.
3. Click **Connect**.
4. After a successful connection, the page will enter the service/method debugging workspace.

## 4. Connect by Service

1. Select **Service**.
2. Choose the target cluster and namespace.
3. In the **Service/Port** dropdown, select the target service and port.
4. If the service list is not updated, click **Refresh**.
5. Click **Connect**.

## 5. Connect by Pod

1. Select **Pod**.
2. Choose the target cluster and namespace.
3. In the **Pod/Port** dropdown menu, select the target Pod and port.
4. If the Pod list is not updated, click **Refresh**.
5. Click **Connect**.

## 6. Select Service and Method

After a successful connection, the page is divided into service list, method list, request area, and response area.

1. Select the target service from the service list on the left.
2. You can use the service search box to filter services.
3. Select the target method from the method list.
4. Switchable method filter options: `Unary`, `Streaming`, `All`.
5. You can use the method search box to filter methods.

> The current page only supports calling a method once. Streaming methods will appear as unavailable.

## 7. Fill in Request Parameters

The request area supports two ways of filling in:

| Method | Description |
| --- | --- |
| Form Mode | The page generates a form based on the method input fields. |
| JSON Mode | When **JSON Mode** is enabled, you can directly edit the complete JSON request body. |

Steps for using Form Mode:

1. Select the target method.
2. Fill in the request parameters field by field.
3. Use the drop-down menu to select enumeration fields.
4. Choose `true` or `false` for boolean fields.
5. For repeated fields, use commas as indicated on the page.

Steps for using JSON Mode:

1. Turn on the **JSON Mode** switch.
2. Fill in the complete JSON in the text box.
3. Make sure the JSON format is valid.

## 8. Filling in Metadata

1. Expand **Metadata** in the request area.
2. Fill in the key and value.
3. To add multiple metadata entries, click **Add**.
4. To delete a row, click the delete icon.

Metadata is usually used to pass authentication information, request ID, or business context.

## 9. Initiate a call and view the response

1. Confirm the target, service, method, request body, and metadata.
2. Click **Call** at the top right of the page.
3. View the status, duration, response metadata, and response JSON in the response area.
4. If the call fails, the response area will display the error status and error content.

## 10. Switch or reconnect the target

1. Click **Connect** at the top to reload the service definition of the current target.
2. Click **Change Target** to go back to the target selection page.
3. After switching the target, you need to reconnect and select the service/method again.

## 11. Common troubleshooting scenarios

| Scenario | Recommended Actions |
| --- | --- |
| Check if the service is connectable | Select the target and click **Connect** to see if the service list can be loaded. |
| Find interface methods | Use service search and method search for filtering. |
| Debug query interface | Select a single-point method, fill in the request parameters, and click **Invoke**. |
| Need to pass context | Expand the metadata and fill in the corresponding key and value. |
| Response is empty or failed | Check the response status, error content, and metadata. |
