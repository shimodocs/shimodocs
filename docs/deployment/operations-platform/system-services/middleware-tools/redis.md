# Redis Tool

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

> [!TIP]
>
> Redis is used to view Redis instances, databases, a set of Keys, and Key details on the operations platform. It is usually used to troubleshoot cache, sessions, distributed locks, rate limit counters, and short-term states.
>
> The page supports searching by Key or Key prefix and displays Key type, TTL, and current value.

## 1. Access Redis

1. Log in to the **MDP Operations Platform**.
2. Select **System Services** in the left navigation bar.
3. Expand **Middleware Tools**.
4. Select **Redis**.

The left side of the page is the Key query area, and the right side is the Key details area.

## 2. Select Redis Instance and Database

1. In the first dropdown menu at the top left corner, select a Redis instance.
2. In the second dropdown menu, select a database, for example `db0`.
3. The page will load the key list based on the current instance and database.

If the database list is empty or the page reports an error, first check whether the Redis instance is configured properly.

## 3. Search for Keys

1. Enter the key name or key prefix in the search box.
2. Click the search button or press Enter to execute the query.
3. View the key list on the left.
4. If you need to reload the list under the current conditions, click the refresh icon.

The search box prompt is "Please enter a key name, fuzzy search is supported." The page will display the matching key types and TTL.

## 4. View Key List

The key list contains the following information:

| Information | Description |
| --- | --- |
| Type | The type of this Redis key, for example `string`, `hash`, `list`, `set`, `zset`. |
| Key Name | The full key currently matched. |
| TTL | The remaining expiration time of the key; if the current key does not expire, the page shows "permanent". |

## 5. View Key Details

1. Click the target key in the key list on the left.
2. The details area on the right shows the key name, type, TTL, and specific value.
3. To refresh the current key's details, click the refresh button in the details header area.

The display methods for different types are as follows:

| Type | Display Method |
| --- | --- |
| `string` | Display the full value in a text box. |
| `hash` | Display field keys and values in a table. |
| `list` / `set` | Display the list of elements in a table. |
| `zset` | Display scores and members in the table. |

## 6. Copy Field Values

1. Locate the field or value you want to copy in the key details table.
2. Click on the corresponding content.
3. The page will copy the content to the clipboard.

> The `string` type is displayed in a text box and can be copied directly by selecting the text; table types support clicking the value to copy.

## 7. Common Troubleshooting Scenarios

| Scenario | Recommended Action |
| --- | --- |
| Check if the cache exists | After selecting an instance and database, search by full key or prefix. |
| Check if the cache has expired | Check the TTL in the key list or the details. |
| View hash fields | Click the key to see fields and values in the right-side table. |
| View ZSet sorted data | Click the `zset` key to view scores and members. |
| View the latest status of the same key | Click the refresh button in the details area. |
