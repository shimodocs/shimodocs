# Compatibility Testing

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## 1. Page Overview

The compatibility testing page is used to check object storage configuration, connectivity, upload compatibility, and upload performance. The page is divided into:

1. Storage Configuration;
2. Compatibility Testing;
3. Performance Testing.

## 2. Storage Configuration 

### 2.1 Configuration Description 

| Configuration Item | Function |
| --- | --- |
| Access Key | Object storage access identifier, i.e., AK |
| Secret Key | Access key paired with AK, i.e., SK |
| Endpoint | Object storage service address |
| Bucket Name | The target bucket to be checked |
| Region | Region where the bucket is located |
| Public Endpoint | Public domain name for browser access to object storage, optional |
| Path Style | Use the "endpoint/bucket/object" format to access objects. Similar services usually require enabling MinIO.  |

### 2.2 Fill in Configuration
1. Click "Fill Attachment Bucket" or "Fill Content Bucket" as needed.
2. The system will automatically fill in configurations such as AK, SK, Endpoint, Bucket, Region based on the current environment.
3. If you do not use auto-fill, you can also manually fill in all configurations.
4. Check whether the endpoint includes the correct protocol and port.
5. Check whether the Bucket name is the one to be tested this time.
6. Check whether the Region matches the actual region of the object storage.
7. If you need to access object storage directly from a browser, fill in the public endpoint.
8. Decide whether to enable path style based on the type of object storage.

After automatically filling in the attachment bucket this time:

- Bucket: `shimo-attachments`;
- Endpoint: auto-filled by the system;
- Region: `cn-north-1`;
- Public endpoint: auto-filled by the system;
- Path style: enabled.

After clicking "Fill Content Bucket," the bucket can automatically switch to `file-contents`.

> Keys are sensitive information; do not display them in plain text in screenshots, chats, or tickets.

## 3. Compatibility Test

The compatibility test will sequentially check backend upload, browser direct upload, and multipart upload.

### 3.1 Start the Test

1. Complete the storage configuration.
2. Click the "Compatibility Test" tab.
3. Confirm again that the bucket, endpoint, region, public endpoint, and path style are correctly configured.
4. Click "Start Compatibility Test."
5. Wait for the page to show "Compatibility Test Completed."
6. Check if the summary status on the page is "All Passed."
7. Check the status, duration, and result information of the three tests separately.

### 3.2 Backend Upload Test

This test is used to verify the network connectivity and write permissions from the backend service to object storage.

1. The backend generates a test text file.
2. The backend writes the file to the target storage bucket.
3. The page displays the upload duration and the path of the test object.
4. A green success status indicates that the backend network and write permissions are functioning properly.

Current result: Upload successful, duration `157 ms`.

### 3.3 Browser Direct Upload Test

This test is used to verify the browser's ability to upload directly to object storage via a PostPolicy link.

1. The page requests the PostPolicy required for direct upload from the backend.
2. The browser directly uploads the file to object storage using the public endpoint.
3. The page checks the HTTP status code returned by the object storage.
4. HTTP 204 indicates that the browser direct upload was successful.

This result: The browser upload was successful, taking `61 ms`, status code `204`.

### 3.4 Multipart Upload Test

This test is used to verify the complete process of large file multipart upload.

1. Initialize the multipart upload task.
2. Split the test file into multiple parts.
3. Upload each part sequentially.
4. Call the merge interface to complete the object creation.
5. The page displays `multipart upload succeeded`, indicating that the entire process was successful.

This result: Multipart upload was successful, taking `1746 ms`.

### 3.5 Description of Test Objects

The compatibility test will perform actual writes to the target Bucket. The path of the background test objects usually looks like:

```text
compatibility-tests/<RANDOM UUID>.txt
```

1. Confirm whether the target Bucket is correct before running tests.
2. Do not run tests casually in the wrong production Bucket.
3. After the test is completed, you can check and clean up test objects according to the environment's cleanup strategy.

## 4. Performance Testing

Performance testing is used to measure the upload time and throughput for different file sizes.

### 4.1 Configure Performance Test

1. Click on the "Performance Test" tab.
2. Select the test mode; the default on the page is "Browser Upload".
3. Select the content type; you can use `application/octet-stream` as the default.
4. Select the file size to test.
5. The page supports 1, 5, 8, 10, 12, 15, 20, and 25 MB.
6. In a test environment, you can first choose 1 MB for quick verification.
7. For formal performance comparison, choose multiple file sizes for testing.

### 4.2 Start Performance Test

1. Confirm that the storage configuration is correct.
2. Confirm that at least one file size has been selected.
3. Click "Start Performance Test".
4. Wait for all files to finish uploading.
5. Check the average throughput, shortest time, and longest time.
6. Check the status, time spent, throughput, and error information for each file size.
7. If a failure occurs, first check the browser network, public endpoint, cross-domain configuration, and object storage status.

### 4.3 Test Results

This test only used 1 MB files for a lightweight front-end direct upload test:

| Metric | Result |
| --- | ---: |
| File Size | 1.0 MB |
| Status | Success |
| Time | 874 milliseconds |
| Throughput | 1.14 MB/s |
| Error Information | None |

Actual results: Upload succeeded, and the page was able to correctly generate the time spent and throughput metrics.

> Performance results may be affected by browser network, cluster load, proxy links, and object storage load. A single test can only verify functional availability; formal performance acceptance should be tested repeatedly in the same environment with statistics recorded, including P50, P95, and failure rates.

## 5. Precautions
1. Confirm the target storage bucket before testing to avoid writing test files to the wrong bucket.
2. Do not display keys in plain text in documents or screenshots.
3. Direct browser uploads rely on public endpoints and cross-origin configuration.
4. S3-compatible services like MinIO usually require enabling path-style access.
5. Performance testing generates real network traffic and storage writes; evaluate the environmental impact before testing large files.
6. Formal performance acceptance should involve multiple rounds of testing; results from a single browser test are not sufficient as a basis.
