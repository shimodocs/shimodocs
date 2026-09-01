# MongoDB Tools

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

> [!TIP]
>
> MongoDB is used to view MongoDB databases, collections, and document contents in the operations platform. It is suitable for troubleshooting document-based data, intermediate states, task records, and business data with flexible structures.
>
> This page supports searching by database or collection. After selecting a collection, MongoDB JSON can use conditional queries.

## 1. Access MongoDB

1. Log in to the **MDP Operations Platform**.
2. Select **System Services** in the left sidebar.
3. Expand **Middleware Tools**.
4. Select **MongoDB**.

The left side of the page displays the database and collection tree, while the right side shows query conditions and query results.

## 2. Search for Databases or Collections

1. Enter DATABASE_NAME Type the keyword of the database or collection name into the search box in the top left corner.
2. View the filtered tree list.
3. Clear the search box to restore the display of all databases.

## 3. Expand the database and select a collection

1. Locate the target database in the left tree.
2. Click the expand icon to the left of the database to load the collection list.
3. Select the target collection.
4. The right page will automatically perform a query once using the default conditions. `{}`.

> Selecting only the database will not execute a collection query; you need to select a specific collection first, then the query area will be displayed on the right.

## 4. Fill in the query conditions

1. Fill in MongoDB JSON Enter the query conditions in the query input box on the right.
2. Select the number of results to return, supporting `limit: 10`, `limit: 20`, `limit: 50`.
3. Click **Query**.

Query example: 

```json
{
  "_id": "task-123"
}
```

Example of querying by field:

```json
{
  "status": "running"
}
```

## 5. View Query Results

1. After the query is completed, view the returned documents in the results area on the right.
2. By default, results are displayed in JSON format.
3. Click **Expand** to expand the current document.
4. Click **Collapse** to collapse the current document.
5. Click **Copy** to copy the current document JSON.

## 6. Common Troubleshooting Scenarios

| Scenario | Suggested Action |
| --- | --- |
| Confirm if the document exists | After selecting the collection, query using `_id` or the business ID. |
| Check task status | Query using the task ID and check the status field and update time field. |
| Find a particular record | Query using a combination of fields such as status, type, and creation time. |
| No results | Check whether the correct database, collection, field names, and field types are selected. |
| Need to remove troubleshooting results | Click **Copy** to copy a single result. |
