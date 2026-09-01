# Editor Configuration

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## 1. Manual Instructions

This manual introduces the "Editor Configuration" feature of ShimoDocs Suite and is suitable for system administrators and implementers using this feature for the first time. You can follow the steps in this document to locate configuration items, modify feature switches or quotas, verify whether they take effect, and restore the original settings if necessary.

**Most Important Scope Rule**: When the Team ID is left blank, the default application configuration will be queried and modified; when a Team ID is entered, the configuration of the corresponding team will be queried and modified. Modifying the default application configuration may affect multiple teams, so please confirm the configuration scope again before saving.

### 1.1 Access Entry

Admin Backend > ShimoDocs Suite > Configuration Management > Editor Configuration

### 1.2 Preparations Before Use

- Confirm that your login account has permissions to view and modify ShimoDocs Suite editor configurations. 
- First, confirm whether the target scope is the default application or a specific team, and obtain the accurate team ID. 
- Please confirm the configuration item names according to the configuration requirements or Appendix A. The configuration item name is a unique identifier; do not guess based only on the Chinese function name. 
- Record the source, valid values, and restriction status before making changes; for important configurations, also prepare rollback values. 
- Default application configurations have a wide impact; it is recommended to make modifications during business off-peak hours and notify the relevant responsible persons in advance.

## 2. Configuration Scope and Priority

Editor configurations support two scopes: application default and team. Before making changes, you must check the team ID and "current dimension" at the top of the page.

| **Functional Area** | **Application Default Scope** | **Team Scope** | **Page Identification Indicator** |
| --- | --- | --- | --- |
| Editor Configuration | Leave Team ID Empty | Enter Positive Integer Team ID | Current Dimension Shows “Application Default” or “Team” |

*Figure 1 Correspondence Between Team ID and Current Dimension*

### 2.1 Application Default Scope

- Keep the Team ID empty.
- The top of the page displays “Current Dimension: Application Default”.
- The application default value is the base value when no team override is set; modifying it may affect multiple teams.
- Before saving, please reconfirm that the Team ID is indeed empty to avoid mistakenly writing team requirements into the application default settings.

### 2.2 Team Scope

- Enter the target team’s positive integer ID in the Team ID field, then click “Query”.
- The top of the page displays “Current Dimension: Team”.
- Team-level configurations only affect the team corresponding to the entered Team ID and will not directly change the configuration of other teams.
- When a configuration item has a team-level setting, the team effective value takes precedence over the application default value.

### 2.3 Override, Inheritance, and Restoration

- If the current team has no custom override, the application default configuration or system default value will be used.
- The "Source" in the list can help determine whether the current value comes from the system default, application override, or team override.
- After deleting the current layer's override, the configuration will usually inherit the upper-level value again; please confirm the inheritance result before deletion.
- After modification or restoration, please query the same team ID and configuration items again to confirm that the source and effective value meet expectations.

**Risk Warning** Do not save directly without confirming the current dimension. When the team ID is empty, the operation will write to the application default scope, which may affect multiple teams.

## 3. Editor Configuration

The editor configuration is used to view and adjust feature switches, usage quotas, and the structured configuration of the editor. In ShimoDocs Suite, you can filter by type, or expand "Advanced Filter" and enter the configuration item name in the "Name Whitelist" for precise search. 

### 3.1 Page Fields 

| **Field** | **Description** | 
| --- | --- | 
| Application ID | The identifier of the current ShimoDocs Suite application, used only to confirm the context. | 
| Current Dimension | "Application Default" when the Team ID is empty; "Team" after entering the Team ID. | 
| Team ID | The team identifier; only positive integers are accepted. | 
| Type | Options include All, Feature, Single Value Quota, Range Quota, or JSON Configuration. | 
| Advanced Filter | Expand the input box to enter the configuration item name. |
| Name Whitelist | Enter the configuration item name; support one per line or separated by commas. | 
| Source | Indicates whether the current value comes from the system default, application override, or team override. | 
| Effective Value | The actual switch, quota, or structured configuration used within the current scope. | 
| Actions | Pencil icon is for editing; delete icon is for removing the current layer override. | 

*Figure 2 Editor configuration query area, result list, and actions column*

### 3.2 Exact Search

1. Decide whether to fill in the team ID based on the configuration scope: leave it blank to use the default application scope, or fill in the corresponding team.
2. Select “Type” as needed; if unsure, keep it as “All.”
3. Click “Advanced Filter” to expand the “Name Whitelist.”
4. Enter the full configuration item name. For multiple names, enter one per line or separate them with commas.
5. Click “Query” to verify the name, type, source, and effective value in the results.
6. Click the pencil icon on the far right of the target row to open the edit popup.

*Figure 3 Fill in the name whitelist after clicking "Advanced Filter"*

*Figure 4 After an exact search, use a single result for editing _limit_mosheet_ size*

**Operation Tip** If you cannot see the action icon, please scroll the list horizontally to the far right or expand the visible area of the browser.

### 3.3 Direct Search in the List

- First, confirm whether the team ID and type filters are correct, then scroll through the query results.
- Use "Name" and "Type" to identify the target configuration; do not rely solely on the current value.
- The number of records displayed on the page may vary depending on the deployment version and the configuration items currently supported by the application.
- After finding the target row, click the pencil icon on the far right to enter edit mode.

### 3.4 Edit Configuration

#### 3.4.1 Function

Function types are used to control whether a certain function is available. After opening the edit popup, select the status provided by the page from the "Valid Values" dropdown, such as "Supported" or "Hidden", and then click "Save." Some configuration item names contain reverse semantics, such as not supported or disabled. Please determine the actual meaning based on the item name and description.

*Figure 5 Valid Value Settings for Function Type Configuration Items*

#### 3.4.2 Single Value Quota

A single value quota usually includes a "Limit Validation" switch and a "Maximum Value." When limit validation is enabled, the system will validate according to the maximum value; when disabled, it is usually displayed as "Unlimited." The maximum value must be within the allowed range of the parameter and consistent with the unit in the parameter name, such as MB, GB, pages, items, or characters.

*Figure 6 Single Value Quota Validation and Maximum Value Settings*

#### 3.4.3 Range Quota

- A range quota typically provides a minimum value and a maximum value.
- The minimum value cannot be greater than the maximum value, and the entered value should be within the allowed range provided by the page or appendix.
- If the page offers "No Verification" or "Unlimited" options, first confirm whether the current function supports this setting. 
- After saving, verify boundary values in actual business functions to avoid only checking the display in the configuration backend.

#### 3.4.4 JSON Configuration

- JSON configuration must maintain a valid structure, including paired quotes, commas, brackets, and correct data types.
- Save the complete original values before making changes; do not only record individual fields.
- When the meaning of a field is unclear, do not arbitrarily add, delete, or rename fields.

### 3.5 Save and Delete

- Before saving, reconfirm the current dimension, team ID, configuration item name, type, unit, and new value.
- After saving, query the same scope and the same configuration item again to confirm that the source value and the effective value have been updated.
- The delete icon is usually used to remove the override record of the current scope, not to delete the configuration item itself.
- This manual only lists the configurable items that can be queried and modified on the current page; the actual items displayed may vary depending on the deployment version and the current application's support capabilities.

### 3.6 Description of Configuration Items

Appendix A only includes the editor configuration items that can be queried and modified on the current page; the specific visible scope depends on the current deployment version and the actual display of the page.

## 4. Effect Verification and Rollback

### 4.1 Verification After Saving

- On the editor configuration page, query the same team ID and the same configuration items again to confirm the source, effective value, and restriction status.
- Enter the editor or feature page that actually uses this configuration to verify whether the feature is visible, the quota is effective, or the restriction is lifted.
- When applying default configurations, at least verify one team that has no team-level configuration set; for team-level configurations, only verify the target team ID.
- If necessary, refresh the page, re-enter the editor, log in again, or wait for cache updates.
- Record modification time, operator, configuration scope, team ID, configuration item name, values before and after the change, and verification results.

### 4.2 Rollback

- If the original value has been recorded, re-edit it and write back the original value.
- If you only need to remove the override for the current scope, use the delete icon, and after deletion, confirm the inherited value from the higher level.
- After rollback, query the source value and effective value again, and go to the business page to verify once more.
- If applying default configuration changes causes anomalies, first restore the default value, then check if any team has independent overrides.

**Important Tip** After deleting the current override, the inherited value may immediately display on the page. Before deletion, you must confirm that the higher-level configuration meets expectations and keep a record of the state before the modification.

## 5. Common Issues

| **Issue** | **Solution** |
| --- | --- |
| Cannot find the configuration item name input box | Click "Advanced Filter" to expand the "Name Whitelist". |
| Cannot find edit or delete icon | Scroll the horizontal list all the way to the right, or enlarge the browser viewport. |
| Exact search returned no results | Check the case, underscores, team ID, and type filters in the name; clear overly strict filters and try searching again. |
| After entering the team ID, it still doesn’t appear in the team dimension | The team ID must be a valid positive integer; after entering it, click "Query" again and check the "Current Dimension" at the top of the page. |
| After saving, the business page hasn’t changed | Check whether the wrong scope was selected, whether it’s overridden by the team, whether a refresh or re-login is needed, and whether the configuration applies to the current feature. |
| Delete icon unavailable | The current scope may not have a custom override, using the system default, or inheriting values from a higher level. |
| Quota save failed | Check the value range, unit, relationship between minimum and maximum, and confirm whether "Unlimited" is allowed. |
| JSON configuration save failed | Use valid JSON; check quotes, commas, brackets, and field types; if unsure, restore the full original value before modifying. |

## Appendix A: Editor Configuration Item Index

The following index only lists editor configuration items that can be queried and modified on the current page; the specific visible range depends on the currently deployed version.

| **Configuration Item Name** | **Category / Function Description** | **Type** | **Default Value / Optional Range** | **Configuration Method** |
| --- | --- | --- | --- | --- |
| Export_modoc_docx | Export | Function switch | On | Configurable on page |
| Export_modoc_image | Export | Function switch | On | Configurable on page |
| Export_modoc_pdf | Export | Feature Switch | On | Configurable on Page |
| Export_modoc_pdf_Image | Export | Feature Switch | On | Configurable on Page |
| Export_modoc_wps | Export | Feature Switch | On | Configurable on Page |
| Export_mosheet_Image | Export | Feature Switch | On | Configurable on Page |
| Export_mosheet_pdf_Image | Export | Feature Switch | On | Configurable on Page |
| Export_mosheet_Single_Form_csv | Export | Feature Switch | On | Configurable on Page |
| Export_mosheet_Single_Form_pdf_Image | Export | Feature Switch | On | Configurable on Page |
| Export_mosheet_single_form_xlsx | Export | Feature Switch | On | Configurable on Page |
| Export_mosheet_xlsx | Export / Table | Feature Switch | On | Configurable on Page |
| Export_mosheet_archive | Export | Feature Switch | On | Configurable on Page |
| Export_presentation_image | Export | Feature Switch | On | Configurable on Page |
| Export_presentation_pdf | Export | Feature Switch | On | Configurable on Page |
| Export_presentation_pdf_image | Export | Feature Switch | On | Configurable on Page |
| Export_presentation_pptx | Export | Feature Switch | On | Configurable on Page |
| Export_rdoc_docx | Export | Feature Switch | On | Configurable on Page |
| Export_rdoc_image | Export | Feature Switch | On | Configurable on Page |
| Export_rdoc_md | Export | Feature Switch | On | Configurable on Page |
| Export_rdoc_pdf | Export | Feature Switch | On | Configurable on Page |
| Export_table_xlsx | Export / Application Table | Feature Switch | On | Configurable on Page |
| Form_Notification | Form Editing / Set Notification Reminders (Reply Reminder, Subscription Updates) | Feature Switch | On | Configurable on Page |
| Import_Convert_svg | Import / Upload / Force Convert Attachment Format Type | Feature Switch | On | Configurable on Page |
| Import_MindMap_xmind | Import/Upload / Mind Map | Feature Switch | Enable | Page Configurable |
| Import_modoc_doc | Import/Upload | Feature Switch | Enable | Page Configurable |
| Import_modoc_docx | Import/Upload | Feature Switch | Enable | Page Configurable |
| Import_modoc_wps | Import/Upload | Feature Switch | Enable | Page Configurable |
| Import_modoc_wpt | Import/Upload | Feature Switch | Enable | Page Configurable |
| Import_mosheet_csv | Import/Upload | Feature Switch | Enable | Page Configurable |
| Import_mosheet_xls | Import/Upload | Feature Switch | Enable | Page Configurable |
| Import_mosheet_xlsm | Import/Upload | Feature Switch | On | Configurable on Page |
| Import_mosheet_xlsx | Import/Upload | Feature Switch | On | Configurable on Page |
| Import_Presentation_ppt | Import/Upload | Feature Switch | On | Configurable on Page |
| Import_Presentation_pptx | Import/Upload | Feature Switch | On | Configurable on Page |
| Import_rdoc_doc | Import/Upload | Feature Switch | On | Configurable on Page |
| Import_rdoc_docx | Import/Upload | Feature Switch | On | Configurable on Page |
| Import_rdoc_md | Import/Upload | Feature Switch | On | Configurable on Page |
| Import_rdoc_txt | Import/Upload | Feature Switch | On | Configurable on Page |
| Import_Table_csv | Import/Upload | Feature Switch | On | Configurable on Page |
| Import_Table_xls | Import/Upload | Feature Switch | On | Configurable on Page |
| Import_Table_xlsx | Import/Upload | Feature Switch | On | Configurable on Page |
| Import_Unsupported_Attachment_svg | Import/Upload | Feature Switch | On | Configurable on Page |
| Import_Unsupported_Attachment_XML | Import/Upload | Feature Switch | On | Configurable on Page |
| Worksheet_Merge_Worksheet | Spreadsheet Editing / Merge Worksheet | Feature Switch | Hidden/Off | Configurable on Page |
| Worksheet_Date_Mention | Spreadsheet Editing / Date Reminder | Feature Switch | On | Page Configurable |
| Worksheet_Follow_Mode | Spreadsheet Editing / Follow Mode | Feature Switch | On | Page Configurable |
| Worksheet_Follow_Selection | Spreadsheet Editing / Follow Selection | Feature Switch | Hidden/Off | Page Configurable |
| Worksheet_Import_Range | Spreadsheet Editing / Cross-Sheet Reference | Feature Switch | Hidden/Off | Page Configurable |
| Worksheet_Independent_Viewport | Spreadsheet Editing / Independent View | Feature Switch | Hidden/Off | Page Configurable |
| Presentation_Remote_Presentation | Slide Editing / Remote Presentation | Feature Switch | Hidden/Off | Page Configurable |
| Preview_Not_Supported_OFD | Preview | Feature Switch | Hide/Close | Page Configurable |
| Preview_Not_Supported_pdf | Preview | Feature Switch | Hide/Close | Page Configurable |
| Preview_Not_Supported_RTF | Preview / Text (Preview does not support RTF) | Feature Switch | Hide/Close | Page Configurable |
| RDOC_Focus_Mode | Document Editing / Follow Mode | Feature Switch | On | Page Configurable |
| RDOC_Notification | Document Editing / Notification Alert | Feature Switch | On | Page Configurable |
| RDOC_Wide_Paper | Document Editing / Wide Paper | Feature Switch | On | Page Configurable |
| SDK_Editor_About_Brand_Visible | Editor Brand Entry | Feature Switch | On | Page Configurable |
| SDK_Editor_About_Entry_Visible | Editor Brand Entry | Feature Switch | On | Page Configurable |
| SDK_Editor_Official_Website_Entry_Visible | Editor Brand Entry | Feature Switch | On | Page Configurable |
| Table_Association_Reference_or_Formula | Application Table Editing / Field - Association Reference & Association Formula | Feature Switch | Hidden/Off | Page Configurable |
| Table_Notification | Application Table Editing / Date Reminder | Feature Switch | On | Page Configurable |
| Table_Reference_Data | Application Table Editing / Reference Data Table (Merged Sheet) | Feature Switch | Hidden/Off | Page Configurable |
| Upload_Image_GIF | Upload Image Format | Feature Switch | On | Page Configurable |
| Upload_Image_JPEG | Upload Image Format | Feature Switch | On | Configurable on Page |
| Upload_Image_PNG | Upload Image Format | Feature Switch | On | Configurable on Page |
| Upload_Image_TIFF | Upload Image Format | Feature Switch | On | Configurable on Page |
| Upload_Image_WEBP | Upload Image Format | Feature Switch | On | Configurable on Page |
| Attachment_Limit_All_Image_Size | Attachment Parameter / Maximum Upload Image Size (MB) | Quota | Default 512; 0–512 | Configurable on Page |
| Attachment_Limit_All_Size | Attachment Parameter / Maximum Upload Attachment Size (GB) | Quota | Default 2048; 0–2048 | Configurable on Page |
| Edit_Limit_Form_Size | Edit Parameter / Maximum Editable Data Size (MB) | Quota | Default 100; 0–100 | Configurable on Page |
| Edit_Limit_Form_Submissions | Edit Parameter / Maximum Submissions per Form | Quota | Default 50000; 0–50000 | Configurable on Page |
| Edit_Limit_modoc_Size | Edit Parameter / Maximum Editable Data Size (MB) | Quota | Default 100; 0–100 | Configurable on Page |
| Edit_Limit_mosheet_Calculation_Cells | Edit Parameter / Formula - Cross-Sheet Reference - Maximum Referenced Cells | Quota | Default 1500000; 0–1500000; Unverified | Configurable on Page |
| Edit_Limit_mosheet_Calculation_Complexity | Edit Parameters / Formulas - Cross-Sheet References - Formula Complexity | Quota | Default 6000000; 0–6000000; Not Verified | Configurable on Page |
| Edit_Limit_mosheet_Feature_Reference | Edit Parameters / Formulas - Maximum Number of Cross-Sheet Reference Functions that Can Be Entered (Unit) | Quota | Default 4000; 0–4000 | Configurable on Page |
| Edit_Limit_mosheet_Form_Cells | Edit Parameters / Maximum Number of Cells in a Single Worksheet | Quota | Default 0; 0–0; Not Verified | Configurable on Page |
| Edit_Limit_mosheet_Form_FC | Edit Parameters / Maximum Number of Formulas that Can Be Entered in a Single Worksheet | Quota | Default 0; 0–0; Not Verified | Configurable on Page |
| Edit_Limit_mosheet_Size | Editing Parameter / Maximum Editable Data Volume (MB) | Quota | Default 100; 0–100 | Configurable on the page |
| Edit_Limit_mosheet_View | Editing Parameter / Maximum Number of Independent Views a User Can Create in a Single Worksheet (Units) | Quota | Default 100; 0–100 | Configurable on the page |
| Edit_Limit_Presentation_Page | Editing Parameter / Number of Slides | Quota | Default 2000; 0–2000 | Configurable on the page |
| Edit_Limit_Presentation_Size | Editing Parameter / Maximum Editable Data Volume (MB) | Quota | Default 100; 0–100 | Configurable on the page |
| Edit_Limit_rdoc_Size | Edit Parameter / Maximum Editable Data Amount (MB) | Quota | Default 100; 0–100 | Configurable on the page |
| Edit_Limit_Table_Calendar_View | Edit Parameter / Maximum Number of Calendar Views per File | Quota | Default 200; 0–200 | Configurable on the page |
| Edit_Limit_Table_Count | Edit Parameter / Maximum Number of Data Tables | Quota | Default 200; 0–200 | Configurable on the page |
| Edit_Limit_Table_Gantt_View | Edit Parameter / Maximum Number of Gantt Views per File | Quota | Default 200; 0–200 | Configurable on the page |
| Edit_Limit_Table_Lock_View | Edit Parameters / Maximum number of locked views per single data table | Quota | Default 50; 0–50 | Configurable on the page | 
| Edit_Limit_Table_Manual_Version | Edit Parameters / Number of manually saved versions | Quota | Default 10000; 0–10000 | Configurable on the page | 
| Edit_Limit_Table_Merge_Table_Reference | Edit Parameters / Maximum number of data tables that a single merged worksheet can reference | Quota | Default 20; 0–20 | Configurable on the page | 
| Edit_Limit_Table_Merge_Table_Summary | Edit Parameters / Maximum number of merged worksheets | Quota | Default 20; 0–20 | Configurable on the page |
| Edit_Limit_Table_Personal_View | Edit parameter / Maximum number of personal views for a single data table | Quota | Default 50; 0–50 | Configurable on the page |
| Edit_Limit_Table_Single_Column | Edit parameter / Total number of columns in a single data table | Quota | Default 50; 0–50 | Configurable on the page |
| Edit_Limit_Table_Single_Row | Edit parameter / Total number of rows in a single data table | Quota | Default 20000; 0–20000 | Configurable on the page |
| Edit_Limit_Table_Single_View | Edit parameter / Maximum number of views for a single data table | Quota | Default 200; 0–200 | Configurable on the page |
| Edit_Limit_Table_Size | Edit parameter | Quota | Default 100; 0–100 | Configurable on the page |
| Export_Limit_rdoc_Pixel_Height | Export Parameter / Maximum height of exported image (pixels) | Quota | Default 66000; 0–66000 | Page configurable |
| Export_Size_Limit | Export Parameter / Maximum size of exported file (GB) | Quota | Default 3072; 0–3072 | Page configurable |
| History_Limit_All_Time | History Parameter / Number of days file history is retained | Quota | Default 10000000000000000; 0–10000000000000000; unverified | Page configurable |
| History_Limit_MoSheet_Cell_Time | History Parameter / Number of days spreadsheet cell history is retained | Quota | Default 10000000000000000; 0–10000000000000000; unverified | Page configurable |
| History_Limit_Restore_Count | History parameter / Number of recent history entries that can be restored per file | Quota | Default 2000; 0–2000 | Configurable on the page |
| History_Limit_Table_Cell_Time | History parameter / Number of days to retain history for table cells in an application | Quota | Default 10000000000000000; 0–10000000000000000; unverified | Configurable on the page |
| History_Limit_Table_Row_Time | History parameter / Number of days to retain dynamic history for table rows in an application | Quota | Default 10000000000000000; 0–10000000000000000; unverified | Configurable on the page |
| History_Limit_Version_Quantity | History parameter / Number of versions (snapshots) that can be saved/restored per file | Quota | Default 100; 0–100 | Configurable on the page |
| Import_Export_Timeout | Import parameter / Maximum import time (minutes) | Quota | Default 10; 0–10 | Configurable on the page |
| Import_Limit_modoc_Size | Import parameter / Maximum file size (MB) | Quota | Default 300; 0–300 | Configurable on the page |
| Import_Limit_modoc_Characters | Import parameter / Maximum number of characters | Quota | Default 2,000,000; 0–2,000,000 | Configurable on the page |
| Import_Limit_mosheet_All_Form_Cells | Import parameters / Maximum valid cells in a single sheet | Quota | Default 5,000,000; 0–5,000,000 | Configurable on page |
| Import_Limit_mosheet_All_XML_Size | Import parameters / Total size of all files in the spreadsheet (MB) XML | Quota | Default 300; 0–300 | Configurable on page |
| Import_Limit_mosheet_Converted_Size | Import parameters / ShimoDocs data volume (MB) | Quota | Default 100; 0–100 | Configurable on page |
| Import_Limit_mosheet_Single_Form_Cells | Import parameters / Maximum valid cells in a single worksheet | Quota | Default 2,000,000; 0–2,000,000 | Configurable on page |
| Import_Limit_mosheet_Single_XML_Size | Import parameter / Maximum size per worksheet file (MB) XML import parameter / Maximum file size (MB) | Quota | Default 20; 0–20 | Configurable on page |
| Import_Limit_mosheet_Size | Import parameter / Maximum number of slides (pages) | Quota | Default 300; 0–300 | Configurable on page |
| Import_Limit_Presentation_Page | Import parameter / Maximum number of characters (characters) | Quota | Default 2000; 0–2000 | Configurable on page |
| Import_Limit_Presentation_Size | Import parameter / Maximum number of slides (pages) | Quota | Default 100; 0–100 | Configurable on page |
| Import_Limit_rdoc_Size | Import Parameter / Maximum Number of Slides (pages) | Quota | Default 50; 0–50 | Configurable on the page |
| Import_Limit_rdoc_Characters | Import Parameter / Maximum Number of Characters (characters) | Quota | Default 300000; 0–300000 | Configurable on the page |
| Import_Limit_Table_Single_Column | Import Parameter / Maximum Number of Valid Columns per Worksheet (columns) | Quota | Default 50; 0–50 | Configurable on the page |
| Import_Limit_Table_Single_Row | Import Parameter / Maximum Number of Valid Rows per Worksheet (rows) | Quota | Default 20000; 0–20000 | Configurable on the page |
| Paste_Limit | Paste Parameter / Maximum Data Amount per Paste (MB) | Quota | Default 9; 0–9 | Configurable on the page |
| Paste_Limit_modoc | Paste parameter / Maximum number of characters per paste (characters) | Quota | Default 200000; 0–200000 | Configurable on the page |
| Paste_Limit_mosheet | Paste parameter / Maximum number of cells per paste (units) | Quota | Default 2000000; 0–2000000 | Configurable on the page |
| Paste_Limit_presentation | Paste parameter / Maximum number of slides per paste | Quota | Default 200; 0–200 | Configurable on the page |
| Paste_Limit_rdoc | Paste parameter / Maximum number of characters per paste | Quota | Default 200000; 0–200000 | Configurable on the page |
| Paste_Limit_table | Paste parameters / Maximum number of rows that can be pasted each time | Quota | Default 2000; 0–2000 | Configurable on the page |
| Preview_Timeout | Preview parameters / Maximum preview time (minutes) | Quota | Default 10; 0–10 | Configurable on the page |

## Appendix B: Correspondence between Terms and Page Fields

| **Term** | **Meaning** |
| --- | --- |
| Configuration item name / Name whitelist | The unique name of the configuration item, for example rdoc_notification, edit_limit_mosheet_size |
| Team ID | Team identifier; enter a positive integer to apply team-level configuration. |
| Application default value | Configuration scope when team ID is not filled in; referred to as global configuration in this manual. |
| Team-level configuration | Override configuration effective only for the specified team ID. |
| System Default | Use the built-in default value when this level is not overridden. |
| Application Override / Team Override | Custom configuration exists at the current level, taking priority over upper-level values. |
| Feature Toggle | Parameter of switch or status type. |
| Single Value Quota | Maximum value and optional limit validation switch. |
| Range Quota | Parameter with a range including minimum and maximum values. |
| JSON Configuration | Must maintain a valid structured parameter JSON; some configuration items are not displayed on the current page. |
| No Validation / Unlimited | Do not perform limit validation based on the input maximum value. |
