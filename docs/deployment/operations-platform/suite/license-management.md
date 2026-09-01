# License Management

[← ShimoDocs Suite Deployment Documentation](../../README.md)

> [!TIP]
>
> License management is used to obtain the server machine code for ShimoDocs Suite, create and issue licenses, and view the current license status, license scope, and running machine information. When activating for the first time, the license can be updated on this page upon expiration or when the server node changes. After ShimoDocs Suite writes the license, you must check the verification results before issuing the license; simply writing or temporarily saving it does not make the license officially effective.

> If you are a paying customer, please send the copied machine code to
- the technical support team. We will generate the corresponding license based on the machine code and send the license information to you. If you want to try ShimoDocs for free, please visit
- Official Website License Application **ShimoDocs fill in the application information and paste the machine code.**: https://shimo.net/license After submission, the system will automatically generate a trial license. Please copy and complete the writing within 10 minutes of generation. If it expires, please reapply.
- 1. Visit the license management at the top
## .

1. Log in to **MDP Operation & Maintenance Platform**.
2. Select **ShimoDocs Suite** in the left navigation bar.
3. Select **License Management**.

This page includes license activation, license information, machine code comparison, and machine information sections.

## 2. Apply for ShimoDocs Suite License

Before applying for authorization, please ensure that the ShimoDocs Suite server nodes are stable and that no nodes will be added, removed, or replaced in the short term.

### 1. Copy all machine codes

1. In the "Authorization Activation" area, click "One-Click Copy All Machine Codes".
2. Confirm that the page displays the copy success message.
3. Save the machine codes in the authorization application materials.

### 2. Submit Authorization Application Information

Submit all machine codes and authorization requirements to the ShimoDocs service staff to generate the ShimoDocs Suite license.

It is recommended to confirm the following information when applying:

| Information | Description |
| --- | --- |
| Authorized Product | ShimoDocs Suite. |
| License Name and Company | The license name and company that will be displayed on the license. |
| Number of Seats | The maximum number of seats that can use the license. |
| Usage Period | The start and expiry time of the product license. |
| After-Sales Service Period | The start and expiry time of after-sales service. |
| Available Suites | The document types or feature suites that need to be enabled. |
| AI Function | Whether to enable AI functionality. |
| Machine Code | The machine codes of all servers currently running ShimoDocs Suite. |

## 3. Write and Verify Authorization

After receiving the new license, please follow the steps below to write it into the system.

### 1. Write the License

1. Click "Write Authorization" in the "Authorization Activation" area.
2. Paste the full license content you received into the input box.
3. Ensure that the license content is complete, without missing characters or extra spaces.
4. Click "Temporary Save".

### 2. Check License Verification Results

After temporary save, the system will verify the new license and display the following results:

| Verification Result | Description | Recommended Action |
| --- | --- | --- |
| Mismatch | The key information of the new license does not match the current environment. | Check the machine code, application type, version, and authorization scope. Once confirmed correct, acquire the license again. |
| Changed | Some information in the new license differs from the current license. | Please check the original values, new values, and judgment results on the page to confirm that the changes are consistent with this application. |
| Passed | The corresponding verification item has been passed. | Continue to check other authorization information. |

If there are any 'Changed' items on the page, and these changes are consistent with this authorization application, please click 'Confirm Temporary Save'.

> If an unexpected 'Mismatch' or 'Changed' occurs, stop publishing and recheck the application information and the current operating environment first.

## 4. Publish Authorization

After verification and temporary save are completed, the page will display the license information pending publication.

### Pre-publishing Check

Carefully verify the following items:

- License name.
- Quantity limit.
- Start time and expiration time.
- Application ID. 
- Server machine code.

After confirming the information is correct, click "Publish License."

After publishing, refresh the page to view the updated license status and license information.

## 5. View License Information

The "License Information" section displays the currently valid license content.

### License Information Field Description

| Field | Description |
| --- | --- |
| License Name | The name of the current license. |
| Licensed Company | The company that owns the license. |
| Product Version | The product version applicable to the current license. |
| Release Version | The release type of the current license. |
| Application Type | The product type applicable to the current license. |
| Number of Tenants | The maximum number of tenants allowed by the current license. |
| Number of Seats | The maximum number of seats allowed by the current license. |
| Product Usage Period | The start and expiration dates of the product license. |
| After-sales Service Period | The start and expiration dates of the after-sales service. |
| Number of Machines | The number of servers the license is bound to. |
| AI Function | Whether the AI function is enabled in the current license. |
| Available Suites | The types of documents or suites allowed by the current license. |

## 6. Check the Machine Code Comparison Results

Below the authorization information, the comparison results between the machine codes in the license and the currently running server will be displayed.

| Result | Description |
| --- | --- |
| Machines in Certificate | The number of servers bound in the current license. |
| Actual Machines | The number of servers currently detected running in the system. |
| Match | The machine codes in the license match the current server. |
| Mismatch | There are servers that are not bound to the license, or the machines in the license have changed. |
| Missing | The machines bound in the license are not currently detected by the system. |

If the number of machines in the certificate does not match the actual number of machines, or if the page shows 'Unmatched' or 'Missing', please click 'Copy Machine Code' and reapply for the license using the current machine code.

> If a countdown appears at the top of the page, indicating that the machine code does not match, please complete server information verification or license replacement within the indicated time to ensure continuous authorization.

## 7. View Machine Information

The 'Machine Information' section shows the currently running server nodes.

### Machine Information Field Description

| Field | Description |
| --- | --- |
| Server | The name or address of the current server node. |
| Operating System | The operating system currently used by the server. |
| CPU | The model of the server's processor. |
| Number of Cores | The number of cores in the server's CPU. |
| Memory | The memory capacity currently recognized by the server. |
| Architecture | The system architecture of the server. |
| Machine Code | The server's unique identifier used for generating and binding licenses. |

## 8. Check License Update Results

After completing the license release, it is recommended to check in the following order:

1. Refresh the license management page to confirm whether the license status is normal.
2. Verify the license name, number of seats, usage period, available suites, and AI features.
3. Confirm that the number of machines in the certificate matches the actual number of machines.
4. Ensure that there are no unexpected "mismatch" or "missing" results in the machine code comparison.
5. Log in to ShimoDocs Suite to verify whether the licensed document types and functions can be used normally.

> After replacing, adding, or removing server nodes, it is recommended to return to this page promptly to check the machine code comparison results. If the machine code changes, all new machine codes need to be used to reapply for and release the license.
