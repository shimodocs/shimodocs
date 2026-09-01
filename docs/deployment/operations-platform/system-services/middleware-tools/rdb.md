# RDB Tool

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

> [!TIP]
>
> The RDB tool is used to view and troubleshoot relational database data on the Operations Platform. It is often used to confirm business records, configuration records, task statuses, operation logs, and other structured data.
>
> Before using, make sure your current account has middleware tool permissions and that you have selected the correct deployment environment.

> Users can directly access database data through the RDB tool. Before querying, please confirm the tables and filters to avoid executing high-cost queries or accidentally manipulating production data.

## 1. Accessing RDB

1. Log in to the **MDP Operations Platform**.
2. Select **System Services** in the left navigation bar.
3. Expand **Middleware Tools** in the left navigation panel.
4. Select **RDB**.

Pages typically include database connections, table selection, SQL input, and a query results area.

## 2. Select Database Connection

1. On the RDB page, select the database instance you need to access.
2. Check whether the instance name, database address, or environment identifier matches your current troubleshooting target.
3. Select the target database.
4. Expand the table list to confirm whether the target table exists.

> If the database instance is empty or the connection fails, first check the middleware configuration, account permissions, and network connection.

## 3. View Table Structure

1. Select the target table from the table list on the left.
2. Check the field names, field types, primary keys, and index information.
3. Confirm subsequent query conditions based on the meaning of the fields.

It is recommended to pay attention to the following information:

| Information | Description |
| --- | --- |
| Primary Key | Used to accurately query a single record. |
| Business ID | For example, tenant ID, user ID, task ID, file ID. |
| Status Field | Used to confirm the current process status of the business. |
| Time Field | Used to limit the time range of queries. |
| Index Field | Preferably used as query filter conditions to reduce full table scans. |

## 4. Using Common Functions SQL

'Common' SQL 'Common' is used to quickly execute preset queries, suitable for high-frequency check scenarios such as certificates, applications, documents, and users.

1. Click **Common SQL** located above the SQL editor.
2. Select the SQL you need to use from the dropdown list.
3. If you need to check the statement content first, click **Preview** next to the corresponding SQL.
4. In the preview window, verify the description, database, table name, and SQL content.
5. After confirming that everything is correct, click **Execute**.

Common SQL may contain placeholders:

| Placeholder | Parameter Type | Example |
| `%s` | String | Application ID, provider file ID, historical GUID, provider user ID |
| `%d` | Number | Internal user ID |

If the SQL contains placeholders, a **Fill SQL Parameters** window will pop up during execution.

1. Fill in each parameter according to the prompts.
2. For string parameters, fill in the complete ID without adding extra quotes.
3. For numeric parameters, fill in pure numbers.
4. Click **Execute Query**.

Common SQL currently mainly includes the following scenarios:

| Scenario | Description |
| --- | --- |
| Certificate Query | Query application certificates and application IDs. |
| Query by specified appid | Query application details using the application ID. |
| Query by specified client file guid | Query file details `provider_file_id`. |
| Query by specified internal file guid | Query file details `history_guid`. |
| Query by specified internal user id | Query user details by internal user ID. |
| Query by specified customer user id | Query user details by `provider_user_id`. |

> Even before using common SQL, it is necessary to confirm the target environment and parameter values. Common SQL is only used to reduce the cost of manual writing and does not guarantee that the query results will meet the objectives of this investigation.

## 5. Execute Query

1. Fill in the query statement in the SQL input area.
2. Prefer using `SELECT` for queries; do not execute insert, update, or delete statements.
3. The default query LIMIT is 10 and can be adjusted manually.
4. Click **Execute Query**.
5. Prefer to execute EXPLAIN and **confirm execution** before starting the query.

Example:

```sql
SELECT *
FROM example_table
WHERE id = 'example-id';
```

## 6. Viewing Query Results

1. View the returned records in the results area.
2. Check whether the key fields meet expectations.
3. If the result is empty, please check the database, table name, query conditions, and time range.
4. If there are too many results, add more precise filter conditions and query again.

## 7. Using Query History

"Query History" is used to view SQL statements executed on the current page, making it convenient to reuse troubleshooting statements, verify execution results, and copy SQL.

> [!NOTE]
>
> Query history is stored locally in the current browser and is not permanently saved. Each database/table dimension retains up to the most recent 100 records. Currently, there is no automatic time-based elimination mechanism; clearing browser site data, switching browsers, changing devices, or switching to a different database/table will display different history records.

1. Switch to **Query History** to view it in the results area.
2. View the execution status, time, database, table, SQL, number of rows returned, and the time consumed in the history.
3. To execute an SQL statement again, click **Insert into Editor and Execute** in the operation column of that record.
4. If you just need to reuse the statement, click **Copy SQL**.

Query history field descriptions:

| Field | Description |
| --- | --- |
| Status | Whether the SQL executed successfully; if it failed, troubleshoot based on the error message. |
| Time | The execution time of the current query. |
| Database | The database selected for SQL execution. |
| Table | The table associated with the SQL execution. |
| SQL | The actual query statement executed. |
| Number of Rows Returned | The number of rows of data returned by this query. |
| Time Consumed | The time spent executing the SQL, which can be used to assess whether there is a risk of slow queries. |
| Operation | Supports re-inserting for execution or copying SQL. |

When troubleshooting query history issues, it is recommended to focus on:

| Situation | Suggested handling |
| --- | --- |
| Status Failed | First, check whether there are SQL syntax errors, whether the database table exists, and whether the fields are correct. |
| Long execution time | Add filtering conditions, or first check the table structure and indexed fields. |
| Too many returned rows | Add 'WHERE' conditions and 'LIMIT'. |
| Inconsistent results across multiple queries | Confirm whether the database, table, or environment has been switched. |

> Query history is used to assist in reviewing the current troubleshooting process. Before re-executing historical SQL, it is still necessary to confirm the SQL content, target database, and current environment.

## 8. Common troubleshooting scenarios

| Scenario | Operation suggestions |
| --- | --- |
| Confirm whether the business record exists | Use the business ID for precise query. |
| Check task status | Query the status field and update time through the task ID. |
| Troubleshoot invalid configurations | Query the current value and update time in the configuration table. |
| Check recent changes | Query in descending order of the time field and limit the number of returned entries. |
| Query application or certificate information | Prefer using certificate query or appid query SQL in “Frequently Used”. |
| Reuse troubleshooting statements | Copy SQL from “Query History”, verify parameters, and execute again. |

## 9. Precautions

1. Unconditional queries on large tables are prohibited in the production environment.
2. If the impact is uncertain, first verify in a low-risk environment. SQL
3. Do not directly modify business data through tools unless there is a clear change plan and approval. RDB
4. Common parameter SQL must be filled in with the actual values of the current environment to avoid erroneous queries across environments. 5. The content in SQL query history may contain sensitive IDs, please confirm the scope before copying or forwarding. 6. When query results involve sensitive information, do not externally share complete screenshots or plaintext data.
