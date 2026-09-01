# Document Repair

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

Symptoms of file corruption include documents failing to open properly, error pop-ups during loading, and inability to display content.

When a document cannot be opened, use this feature to repair the file.

# Usage Example

There are 2 repair methods.

## Preparation

### Quick Reference for File Types

|**File Type**|**File URL Function**|**Remarks**|
|:----|:----|:----|
|rdoc (Rich Document)|/**document**/{file GUID}|Lightweight Document|
|mosheet (modoc)|/**spreadsheet**/{file GUID}|Spreadsheet|
|modoc (modoc)|/**docx**/{file GUID}|Professional Document|

### Operation Risk Notice

No risk if the repair fails.

## Recovery from Encrypted Data

Only tabular files are supported for repair. For other types of files, please choose [Recovery from Historical Data]

This is the preferred method. You can directly input the file GUID to perform the repair. This GUID is the ShimoDocs file GUID.

The principle of repair is to convert the encrypted file data in object storage into non-encrypted file content data, applicable to most scenarios.

If this method fails, choose another method.

### File GUID

1. Open the browser developer tools

2. Filter pull requests

3. In the request, part of rp3OMYnMrdcQJZkm, this 16-character string, is the GUID

## Recovery from Historical Data

Recover from history

1. Customer File ID

2. File Type

   1. For traditional documents/spreadsheets/presentations, select modoc

   2. For lightweight documents, select richdoc

1. Select Data Source

### Customer File ID

If the customer uses it across the entire ShimoDocs site, it refers to the file address of the document in the browser, for example, the following: m8AZMoYMrRsYbOkb

### How to Determine the Data Source

Check the configuration of the svc-edit service

Configuration item: history.driver

If it is mysql, the "Use Mongo Data Source" switch is off

If it is mongo, the "Use Mongo Data Source" switch is on

