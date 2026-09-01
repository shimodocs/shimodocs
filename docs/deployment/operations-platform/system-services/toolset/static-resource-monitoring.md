# Static Resource Monitoring

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

Static resource monitoring is used to check the JS and CSS resources referenced by web pages, helping you understand the access status of resources, transmission protocols, cache configuration, and CDN usage.

> The feature name on the system page is "Static Resource Detection."

## 1. How to Use

Log in to the management platform, select **System Services** at the top, then choose **Tool Collection > Static Resource Detection**.

This feature is only available to administrators. If you do not see this entry, please check your account permissions and the current product version.

1. Enter the complete page URL, for example `https://example.com/recent`.
2. If the page requires login, expand "Custom Request Headers" and fill in the necessary information, such as `Cookie` and `Authorization`.
3. Click "Start Detection" and then wait for the results.

> The request headers will also be used to access static resources referenced by the page. Please use temporary credentials only, and ensure that the domains of cross-origin resources are trusted. The page address, request headers, and recent detection results will be saved in the current browser.

## 2. Detection Scope

The system will identify HTML that directly references JS and CSS on the page, but it will not detect images, fonts, inline code, or resources dynamically loaded after script execution.

- Up to 3 same-domain JS and CSS files per domain can be detected;
- Up to 50 cross-domain resources can be detected at one time;
- Duplicate URLs are counted only once.

Same-domain resources that are not detected will be marked as "Skip Same-Domain Sampling," which does not indicate a resource error.

## 3. View Results

After the inspection is completed, the page will display:

- **Summary Information**: Number of HTML resources, number detected, number of issues, cache usage, CDN, and HTTP/2;
- **Page Response**: Status code, protocol, and cache information of the target page;
- **Resource List**: URL, status code, protocol, cache, CDN, issues for each resource, and response headers.

The resource list supports filtering by "Inspected", "All", and "With Issues".

The system mainly highlights the following issues:

- HTTP 4xx/5xx;
- No valid cache detected;
- HTTP not using /2;
- Cross-origin resources not showing CDN features;
- Request timeout or domain name resolution failure.

## 4. Common Issues

### Page Inspection Failed

Please check the page URL, network connection, HTTPS certificate, and login status. The detection service does not automatically reuse login information from the browser, so if needed, please provide custom request headers. 

### Unrecognized Resources

Please ensure the page returns normal HTML. Resources loaded dynamically through scripts will not be recognized.

### CDN Shows 'Not Detected'

This result only indicates that no CDN characteristics were detected in the response; it does not mean that the resource is definitely not using a CDN. Please verify through the CDN console and network architecture.

## 5. Notes

- The detection results reflect what the management platform observed from the network during this request and may differ from the actual user experience.
- CDN caching and issue statuses are automatically determined results, intended only to assist in diagnosis.
- 'No issues found' does not mean that the page has passed a complete performance, security, or availability assessment.
- After the page is published, if the CDN is refreshed or the network environment changes, it is recommended to test again.
