# File Information Search

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## Function Overview

File information query is used to search for basic records of files in the system based on internal file GUID or client-side file ID, making it easy to verify file identification, associated applications, file type, status, and content size.

This page is read-only and will not modify file content or status.

## Access Page

After logging into the management backend, select **File Information Query** in the left navigation to access this page.

## Query Files

The page supports the following query criteria:

- **Internal File GUID**: the file's `history_guid`.
- **Client File Provider GUID**: the client-side `provider_file_id`.
- **Application ID**: optional, it is recommended to fill in the GUID with the provider to specify the associated application.

At least one internal file GUID or client file provider GUID is required, then click **Query**.

If only the provider GUID is filled in and the application ID is not filled in, the system will return all record GUIDs matching the provider, so multiple results may appear.

### Obtain File GUID
1. In the case of ShimoDocs Suite, you only need to use the suite's address in the browser as the **Client File Provider GUID**.

## Query Results

The query results mainly include:

- **ID**: Primary key ID of the file record.
- **Application_ID**: ID of the associated application.
- **Provider_File_ID**: Client file ID.
- **History_Global_Unique_Identifier**: GUID of the historical file in the internal system.
- **Creation_Time**: Record creation time.
- **Type**: The file type, such as document, spreadsheet, presentation, PDF, image, or video.
- **Created_By**: The user ID of the creator.
- **Status**: The current status value of the file.
- **File_Content_Size**: The size of the file content in bytes.

The file type in the results will display both the type number and the corresponding name for easier identification.

## Common Situations

- **Tip: Query criteria must be entered**: Please fill in at least the internal file GUID or the provider GUID.
- **File not found**: Please check if the identifier is complete, or confirm whether the file belongs to the current environment.
- **Multiple records returned**: The provider GUID may be duplicated across multiple applications; please add the application ID and search again.
- **File type shown as unknown**: The type number of the current record may not yet have a corresponding name configured. You can provide the number to technical support for confirmation.
- **Unable to determine status value**: The status field is a low-level recorded value and requires further analysis in conjunction with specific business phenomena and logs.

> File identifiers belong to business data; please avoid sharing them directly in public chats or external tickets.
