# Brand Customization

[← ShimoDocs Suite Deployment Documentation](../../README.md)

> [!TIP]
>
> Brand customization is used to unify the brand identity and interface style of ShimoDocs Suite. Here, you can set the company logo, browser icon, tab brand extensions, theme colors, button corner radius, page entry points, and system watermark.
>
> When using this feature, it is recommended to first confirm the effective scope of the configuration, and then complete the settings in the order of brand identity, interface style, entry points, and watermark.

> When no tenant is selected, the configuration will take effect globally for ShimoDocs Suite. The priority of the same configuration item is: **Tenant Configuration > Global Configuration**.

## 1. Enter Brand Customization

1. Log in to the **MDP Operations Platform**.
2. At the top, select **ShimoDocs Suite**.
3. Select **Brand Customization** in the left navigation bar. 

## 2. Choose the Effective Scope of Configuration

Brand customization supports global, tenant, or user scopes. Before configuring, please choose the appropriate scope according to your actual needs.

| Configuration Scope | How to Choose | Effect |
| --- | --- | --- |
| Global Configuration | Do not select a tenant or user. | Takes effect globally in ShimoDocs Suite. |
| Tenant Configuration | Select a specific tenant. | Only takes effect for the selected tenant. |
| User Configuration | Select a specific user. | Only takes effect for the selected user. |

For example: If the global theme color is set to blue, while a certain tenant's theme color is set to green, that tenant will use green, while other tenants without separate configuration will still use blue.

## 3. Configure Brand Identity

### 1. Company Logo

By configuring 'Modify Main Site Company Logo,' you can control whether the option to change the company logo is displayed on the 'Enterprise Management > Basic Company Information' page. **Enterprise Management > Basic Company Information** page in ShimoDocs Suite.

Once enabled, administrators can modify the company logo on the Basic Company Information page.

### 2. Browser Icon

Through the 'Modify Page Browser Icon' setting, you can replace the icon displayed on the browser tab (Icon). ShimoDocs Suite browser tabs

After configuration, you can see the actual display effect on the browser tab.

### 3. Browser Tab Brand Suffix

Through the 'Browser Tab Brand Suffix' configuration, you can set the brand name suffix displayed in the ShimoDocs Suite browser tabs.

After configuration, you can check the effect in the browser tab title.

## 4. Configure Interface Style

### 1. Theme Color

Through the "Theme Color" configuration, you can uniformly adjust the color of main buttons, selected states, and highlighted content in ShimoDocs Suite.

After changing the color, you can preview the actual application of the theme color on the page.

### 2. Button Corner Radius

Adjust the button's corner effect in ShimoDocs Suite through the "Corner Radius Configuration".

After adjusting the value, the button's corner shape will change accordingly.

## 5. Configure Page Entry and Brand Information

### 1. Official Website Entry

By configuring "Enable Main Site Official Website Entry," you can control whether to display the ShimoDocs official website entry on your personal card.

Once enabled, users can see the official website entry in their profile.

### 2. "About" Entry

Through the "Enable Main Site About Entry" setting, you can control whether the "About" entry is displayed in the personal profile.

After enabling, users can view the 'About' entry in their profile.

### 3. Brand Information

Through the "Show Brand Information" setting, you can control whether brand information is displayed to users on relevant pages.

**Display effect:**

**Hidden effect:**

## 6. Configure System Watermark

### 1. Collaborator Watermark

By configuring "Enable Built-in System Collaborator Watermark," you can control the watermark content displayed when users edit or preview files.

The watermark content will vary depending on whether the visitor is anonymous and the choice of "Show User Information."

#### Non-anonymous Access

| Configuration Option | Watermark Display Content |
| --- | --- |
| Show/Hide | Displays the built-in system watermark, including basic authorization information and user information. |
| Custom | Displays the collaborator watermark content as set in the enterprise for ShimoDocs Suite Enterprise. |

#### Anonymous Access

| Configuration Options | Watermark Display Content |
| --- | --- |
| Show/Hide | Display the system built-in watermark, including basic licensing information and user information. |
| Custom | Display only the custom text configured for anonymous users. |

After enabling the built-in collaborator watermark, the corresponding watermark will be permanently displayed when users edit or preview the file.

### 2. Editor Bottom Bar Watermark

Through the "Modify System Built-in Editor Bottom Bar Watermark" configuration, you can adjust the system built-in watermark displayed at the bottom of the editor.

After configuration, you can view the actual display effect at the bottom of the editor.

## 7. Check Configuration Results

After completing the configuration, it is recommended to check in the following order:

1. Confirm whether the current selected configuration scope is global or tenant.
2. Open ShimoDocs Suite on a page within the corresponding scope.
3. Refresh the page and check the display status of logos, browser icons, tab names, theme colors, and entry points.
4. Open the file using both non-anonymous and anonymous methods, and confirm that the watermark content displays as expected.
5. If the actual result does not match the expectation, first check whether there is a higher-priority tenant configuration.
