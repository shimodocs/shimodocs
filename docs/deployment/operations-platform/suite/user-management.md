# Suite User Management

[← ShimoDocs Suite Deployment Documentation](../../README.md)

> [!TIP]
>
> User management is used to view users in each tenant of ShimoDocs Suite and to enable or disable user accounts in bulk.
>
> To use this feature, you need to first select the tenant the user belongs to, and then go to the corresponding user list for management.

> The user management page displays the tenant scope of users using the "Team" name.

## 1. Access User Management

1. Log in to the **MDP Operations Platform**.
2. At the top, select **ShimoDocs Suite**.
3. In the left navigation bar, select **User Management**.

## 2. Select the Team (Tenant) to Manage

After entering the user management page, the system first displays the team list. Please select the team (tenant) to which the user belongs from the list.

### Find Teams (Tenants)

1. Enter the team name, creator, or ID in the search box at the top of the page.
2. In the search results, confirm the team name, creator, number of active users, capacity, and expiration date.
3. Click the team name to enter the user list of that team.

### Team List Field Descriptions

| Field | Description |
| --- | --- |
| ID | The unique identifier of the team in the system. |
| Team Name | The name of the team. Click to enter the user list of the team. |
| Creator | The account that created this team. |
| Active / Capacity | The number of currently active users in the team and the available slots. |
| Expiration Date | The expiration date of the current service for this team. |

> If the list content is not updated in time, you can click the "Refresh" button to the right of the search box.

## 3. View Tenant Users

After clicking the tenant name, the page will display the list of users within that tenant.

### User List Field Description

| Field | Description |
| --- | --- |
| ID | The unique identifier of the user in the system. |
| USERNAME | The name displayed for the user in ShimoDocs Suite. |
| Email | The email bound to the user account. |
| Role | The user's role in the current tenant, such as admin or member. |
| Status | Whether the user account is currently enabled. |

## 4. Search Users

When there are many users in the tenant, you can use the search function to quickly find the target user.

1. Enter USERNAME: input the name, email, or user ID in the search box above the user list.
2. Verify the USERNAME, email, and role in the search results to ensure you have found the target user.
3. To reload the list, click the "Refresh" button to the right of the search box.

## 5. Batch Enable Users

When a user account is disabled and needs to be restored, you can enable users in bulk.

### Steps

1. Make sure the current page displays the target tenant.
2. Check one or more users you want to enable on the left side of the user list.
3. Confirm whether the number shown as "Selected" at the top right of the page is correct.
4. Click "Enable in Bulk".
5. Follow the page prompts to confirm the operation.
6. After the operation is complete, refresh the list and confirm that the user status has changed to "Enabled".

## 6. Disable Users in Bulk

When a user temporarily does not need to use ShimoDocs Suite, you can disable their accounts in bulk.

### Steps

1. Make sure the current page displays the target tenant.
2. Check one or more users you want to disable on the left side of the user list.
3. Confirm whether the number shown as "Selected" at the top right of the page is correct.
4. Click "Disable in Bulk".
5. Follow the page prompts to confirm the operation.
6. After the operation is complete, refresh the list and confirm that the user status has been updated.

> Disabling a user will affect the normal use of the account. ShimoDocs Suite, please check the tenant, USERNAME email, and role before performing the operation to avoid mistakenly affecting other users.

## 7. Returning to the Tenant List

If you need to switch to another tenant:

1. Click "Return to Team List" in the upper left corner of the page.
2. Search for and select the target tenant again in the tenant list.
