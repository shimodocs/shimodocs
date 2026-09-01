# System Configuration

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## 1. Instructions

This manual introduces the "System Configuration" feature. ShimoDocs Suite is suitable for system administrators and implementers using this feature for the first time. You can follow the steps in this document to find configuration items, modify configurations, verify whether changes take effect, and restore the original settings when necessary.

> [!TIP]
>
> If you are unsure about the meaning or impact of a configuration item, please contact ShimoDocs technical support to confirm before making any changes.

**Most important scope rule**: When the Enterprise ID is left blank, queries and modifications apply to global configurations; when an Enterprise ID is selected, queries and modifications apply to the configuration of the selected enterprise. Modifying global configurations may affect multiple enterprises, so please reconfirm the configuration scope before saving.

### 1.1 Access Entry

Admin Backend > ShimoDocs Suite > Configuration Management > System Configuration

### 1.2 Preparations Before Use

- Confirm that the logged-in account has view and edit permissions for ShimoDocs Suite system configuration.
- First, confirm whether the target scope is global or a specific enterprise, and obtain the correct enterprise ID.
- Confirm the configuration key names according to the configuration requirements or Appendix A. Configuration key names are unique identifiers; do not guess based solely on the Chinese names.
- Record the source, status, and effective values before making modifications; for important configurations, also prepare rollback values.
- Major global configurations should be modified during business off-peak hours, and relevant personnel should be notified in advance.

## 2. Configuration Scope and Priority

System configuration supports both global and enterprise scopes. Before making changes, you must check the enterprise ID field and the scope prompt at the bottom of the page.

| **Functional Area** | **Global Scope** | **Enterprise Scope** | **Page Identification Signals** |
| --- | --- | --- | --- |
| System Configuration | Leave Enterprise ID empty | Select Enterprise ID | Bottom prompt shows “Global Version Override” or “Enterprise Final Effective Result” |

*Figure 1 Location to select Enterprise ID in System Configuration*

### 2.1 Global Configuration

- Keep the Enterprise ID unselected.
- The page indicates that the current query and modification is for the “Global Version Override.”
- Global values are the base values used when no enterprise override is set; modifying them may affect multiple enterprises.
- Before saving, please check at least once more to ensure that the Enterprise ID field is indeed empty.

### 2.2 Enterprise-Level Configuration

- Select the target enterprise from the Enterprise ID dropdown menu.
- The page displays the final effective result after merging the default values with the current enterprise custom configuration.
- Enterprise-level configuration only affects the selected enterprise and does not directly change the configuration of other enterprises.
- When the same item has an enterprise-level configuration, the enterprise's final effective value takes precedence over the global value.

### 2.3 Overrides, Inheritance, and Restoration

- When the current enterprise does not have an override, the global configuration or system default will be used.
- Actions such as "Restore System Default" or deleting the current override usually mean removing the override at the current scope and re-inheriting the value from the parent.
- Prompts on the page such as "System Default", "Global Version Override", and "Enterprise Final Effective Result" can be used to determine which layer the current value comes from.
- Before performing a restore or deletion, please record the current value and confirm that the inherited result meets expectations.

**Risk Tip** Do not save directly before confirming the enterprise scope. If the enterprise ID is empty, the operation may write to the global override and affect multiple enterprises.

## 3. System Configuration

System configuration is used to view and adjust common functions, quotas, and operation parameters in ShimoDocs Suite.

### 3.1 Search Method One: Exact Search

- Enterprise ID: Leave blank for global; select the enterprise ID for enterprise-level configuration.
- Search criteria: Select the type, value type, and valid end date as needed; if unsure, keep 'All'.
- Key Name: Enter the configuration key name; input one key per line, or use commas to separate multiple keys.
- Click 'Search' to confirm the name, key, source, status, and current value in the results.
- Click 'Edit' at the far right of the target row to open the modification popup.

*Figure 2 Precise search area for system configurations.*

*Figure 3 Single result after precise search by configuration key name.*

**Scope Tip.** When “When no company is selected, the current query and modification applies to global version override” appears at the bottom of the page, it indicates that the current scope is global. After selecting a company, the page displays the final effective result combining the default values with the current company's custom configuration.

### 3.2 Second Search Method: Locate directly in the list

- Keep the company scope and filter conditions correct, and use the page scroll to browse the list.
- Confirm the target configuration by "Name" or "Key Name", do not judge based solely on the current value. 
- View the type, valid end date, current value, source, and status in the same row. 
- Click "Edit" on the far right. If you cannot see the action column, scroll the table horizontally to the far right or enlarge the browser viewport.

### 3.3 Editing Different Types of System Configurations

#### 3.3.1 Key-Value Type

The top of the edit popup shows read-only metadata, including key name, name, description, type, and valid end date. After enabling it, fill in the value in input boxes such as "String Value" and save. If the string contains a path or list, the original format should be maintained. JSON, URL

*Figure 4 System Configuration Key-Value Type Edit Popup*

#### 3.3.2 Quota Type

Quota pop-up windows typically include status, minimum value, maximum value, and a 'no validation' switch. After enabling the configuration, fill in the range according to business requirements; turning on 'no validation' means the system will not perform restriction checks based on the entered range. The values must be consistent with the unit in the pop-up window, such as 'pcs', 'MB', etc.

*Figure 5 System Configuration Quota Type Edit Pop-up Window*

#### 3.3.3 Function Type

Function types are mainly based on status switching. Turning it on indicates that the configuration item is enabled within the current range; turning it off indicates it is disabled or not enabled. Some keys have reverse semantics and should be judged based on the configuration item name and description. For example, keys whose names contain 'unsupport' or 'disable' may indicate 'not supported' or 'disabled' when turned on.

### 3.4 Save, Delete, and Restore

- Before saving, please reconfirm the enterprise scope, key name, type, unit, and modified value.
- After saving, search for the same configuration item again to verify that the source, status, and effective value have changed.
- When there is an override in the current scope, the "Delete" operation can possibly be performed; after deleting the override, it will revert to the value inherited from the upper level. 
- When a rollback is needed, first write back the original record's value, or delete the current override after confirming the inheritance relationship.
- Do not interpret "Delete" as deleting the configuration item itself; deletion on the page usually only applies to the override record within the current scope.

## 4. Validity Verification and Rollback

### 4.1 Post-Save Verification

- On the system configuration page, query the same enterprise scope and the same configuration item again to confirm the source, status, and effective value.
- Visit the functional pages that actually use the configuration to check the feature's performance, not just the backend configuration.
- For global configuration, at least conduct spot checks for a company without enterprise-level configuration; enterprise-level configuration should only be verified for the target company.
- For account, permission, or cache-related settings, refresh the page, log in again, or wait for the cache to update if necessary.
- Record modification time, operator, enterprise scope, key name, values before and after modification, and verification results.  

### 4.2 Rollback

- Determined original value exists: re-edit and write back the original value.  
- Only need to remove the current scope override: use "Restore System Default" or delete the current override.  
- After rollback, query the source value and effective value again, and re-enter the business page for verification.  
- If global modifications cause widespread abnormalities, priority should be given to restoring global overrides, then investigating the differences in overrides among enterprises.  

**Important Note:** Deletions on the page usually apply to override records within the current scope; the configuration item itself still exists. It is necessary to confirm whether the inherited value meets expectations before deleting.  

## 5. Common Issues

| **Issue** | **Solution** |
| --- | --- |
| Cannot find edit or operation buttons | When the table is very wide, scroll horizontally to the far right; you can also enlarge the browser's visible area. |
| No results for exact search | Check the case and underscores of key names; confirm the enterprise ID range; clear overly strict type, value type, or valid end filter conditions. |
| After saving, the business page has no changes | Check if the wrong enterprise range is selected, if the source is overridden by the enterprise, whether a refresh or re-login is needed, and confirm whether the configuration item applies to the current function. |
| Restore system default button unavailable | The current range has no overrides, currently using inherited values or system defaults. |
| JSON or URL configuration error | Keep valid JSON or URL format, do not omit quotes, commas, or protocols; please verify in a test enterprise first. |
| Enterprise final effective value differs from global value | The current enterprise may have overrides. Please check the source record and override record to confirm whether to retain enterprise differences or revert to inheritance. |

## Appendix A: System Configuration Item Index

The following index only lists the system configuration items that can currently be queried and modified on this page; the specific visible range depends on the current deployment version and the actual page display.

| **Configuration Key** | **Configuration Item Name** | **Type/Rule** | **Page Support** |
| --- | --- | --- | --- |
| Allow_Team_Admin_Get_Invited_User_Password | Enterprise administrators obtain the initial PASSWORD of invited users | Empty string | Configurable on the page |
| Auto_Login_Enable_Or_Not_Permission_Page | Unauthorized anonymous access will be redirected to the login page | Empty string | Configurable on the page |
| Batch_Delete_File_Count_Limit | Maximum number of files for batch deletion | 0–500 | Configurable on the page |
| Batch_Download_File | Maximum number of files for a single batch download | 0–500 | Configurable on the page |
| Batch_Process_Download_Size | Maximum total size per batch download | 0–21474836480 | Configurable on the page |
| Batch_Process_Move_File_Count_Limit | Maximum number of files per batch move | 0–500 | Configurable on the page |
| Brand | Front-end brand name | Empty string | Configurable on the page |
| Change_Folder_Collaborators | Folder collaboration | Empty string | Configurable on the page |
| Classification_Tag_Config_Limit | Maximum number of downgrade approval policies | 0–30 | Configurable on the page |
| Classification_Tag_Limit | Maximum number of classification tags | 0–20 | Configurable on the page |
| Classification_Tag_Rule_Limit | Maximum number of classification tag rules | 0–30 | Configurable on the page |
| Cloud_Team_Space_Download_File_Size | Maximum size of a single downloaded file (MB) | 0–3072 | Configurable on the page |
| Cloud_Team_Space_Upload_File_Size | Team space file upload size limit | 0–300 | Configurable on the page |
| Daily_Unzip_File_Count_Limit | Maximum number of files to unzip per day | 0–2000 | Configurable on the page |
| Default_Avatar | Default avatar URL | Path | Configurable on the page |
| Default_Enterprise_Recycle_Bin_Quota | Default enterprise recycle bin quota | 0–0 | Configurable on the page |
| Default_Space_Quota | Default team space quota | 0–107374182400 | Configurable on the page |
| Default_Team_User_Quota | Default capacity limit for enterprise members | 0–0 | Configurable on the page |
| Default_User_File_Tag | Default tag for user files | JSON array | Configurable on the page |
| Default_User_Quota | Default personal space quota within the team (My Desktop) | 0–107374182400 | Configurable on the page |
| Department_Count_Limit | Maximum number of departments that can be created in the enterprise | 0–500 | Configurable on the page |
| Department_Depth_Limit | Maximum number of nested department levels | 0–20 | Configurable on the page |
| Disable_Bulk_Download | Disable bulk download | Empty string | Configurable on the page |
| Disable_Enterprise_Trash | Hide enterprise trash | Empty string | Configurable on the page |
| Show_IP_Location | Show IP location | Empty string | Configurable on the page |
| Driver_Editor_About_Brand_Visible | Display brand information on the About page of ShimoDocs Suite Editor | Empty string | Configurable on page |
| Driver_Editor_About_Entry_Visible | Show 'About' entry in ShimoDocs Suite Editor | Empty string | Configurable on page |
| Driver_Editor_Official_Website_Entry_Visible | ShimoDocs Suite Editor official website entry display | Empty string | Configurable on page |
| Enable_Link_Report | External link report | Empty string | Configurable on page |
| Enable_External_Person | External collaborators | Empty string | Configurable on page |
| Enable_Computer_System_Theme | Enable computer system theme | Empty string | Configurable on page |
| Enable_rdoc_Markdown_Mirror_Export_Option | Enable_rdoc_Markdown_Mirror_Export_Option | Empty string | Configurable on page |
| Enable_Risk | Risk Identification | Empty string | Configurable on page |
| Enable_Share_Expiration_Time | Share Link Expiration Time | Empty string | Configurable on page |
| Enable_Share_Password | Share Password | Empty string | Configurable on page |
| File_Collaborator_Limit | Maximum Number of Collaborators per File | 0–100 | Configurable on page |
| Folder_Subitem_Count_Limit | Maximum Number of Files at the Same Level | 0–2000 | Configurable on page |
| Free_User_Creation_Limit | Limit on the Number of Templates Free Users Can Create | 0–5 | Configurable on page |
| Frontend_Runtime_Feature | List of Frontend Runtime Configuration Items | JSON Array | Configurable on Page |
| Import_User_Row_Limit | Maximum number of users that can be imported at one time | 0–500 | Configurable on Page |
| Invite_Phone_Limit_Expired | Valid time window for file collaboration invitations sent via phone | 0–3600 | Configurable on Page |
| Invite_Phone_Limit_Max | Limit on the number of file collaboration invitations sent via phone | 0–20 | Configurable on Page |
| Is_Enable_Role_Application | File permission application | Empty string | Configurable on Page |
| Login_Device_Limit | Maximum number of devices that can be logged in simultaneously per account | 0–0 | Configurable on Page |
| Max_Creator_Teams_Per_Account | Maximum number of enterprises that can be created per account | 0–3 | Configurable on Page |
| Max_Folder_Depth | Maximum folder nesting depth | 0–50 | Configurable on the page |
| Max_Joined_Teams_Per_Account | Maximum number of enterprises an account can join | 0–100 | Configurable on the page |
| Max_Trash_List_Size | Number of records returned by the trash list interface | 0–500 | Configurable on the page |
| Chunked_Upload_Enable | Chunked upload | Numeric string | Configurable on the page |
| Single_Extraction_File_Limit | Maximum number of files extracted at one time | 0–500 | Configurable on the page |
| Owner_Only_Delete | Only the owner can delete | Empty string | Configurable on the page |
| Advanced_User_Creation_Limit | Maximum number of templates a user can create | 0–50 | Configurable on the page |
| Private_Deployment_Page_Icon | Page Icon Configuration | Empty String | Configurable on Page |
| Public_Share | Public Share | Empty String | Configurable on Page |
| Rag_Search_Rules | RAG Search Rules | JSON Object | Configurable on Page |
| SDK_Checkpoint_Cache_TTL | Editor Configuration Cache Duration | 0–600 | Configurable on Page |
| SDK_Checkpoint_Whitelist | Editor Configuration Whitelist | JSON Object | Configurable on Page |
| Search_AI_Enable | Search AI Enable | Empty String | Configurable on Page |
| Share_Password_Length | Share Password Length | 0–6 | Configurable on Page |
| Single_File_Upload_Size_Limit | Maximum Size for Single Uploaded File (GB) | 0–1 | Configurable on Page |
| Single_Upload_File_Count_Limit | Batch Upload | Empty String | Configurable on Page |
| Team_Change | Team Change | Empty String | Configurable on Page |
| Team_Role_Management | Role Management | Empty String | Configurable on Page |
| Theme_Color | Frontend Theme Color | Empty String | Configurable on Page |
| Theme_Color_Button | Button Theme Color | HEX Color Value | Configurable on Page |
| UI_Border_Radius_Config | Frontend Border Radius Config | Empty String | Configurable on Page |
| Upload_Batch_Max | Maximum Number of Files per Upload | 0–500 | Configurable on Page |

## Appendix B: Correspondence Between Terms and Page Fields

| **Term** | **Meaning** |
| --- | --- |
| Configuration Key / Key Name | The unique key of the configuration item, for example, batch_download_file_count. |
| Enterprise ID | Enterprise identifier. Selecting it enters the enterprise-level configuration scope. |
| Global Configuration | The default scope for query and modification when the enterprise ID is left blank. |
| Enterprise-Level Configuration | Overrides that take effect only for the selected enterprise. |
| System Default Value | The built-in default value used if there is no custom override in the current scope. |
| Global Version Override | The current configuration item has a custom setting at the global level. |
| Enterprise Effective Result | The actual effective result after merging enterprise default values with enterprise overrides. |
| Key-Value | Single-value parameter stored as a string, which may contain text, URL paths, or JSON. |
| Quota | Includes numerical ranges for minimum, maximum, or limit switching. |
| Feature | Switch or status-type parameter. |
| No validation | Does not perform validation checks according to the specified limit. |
