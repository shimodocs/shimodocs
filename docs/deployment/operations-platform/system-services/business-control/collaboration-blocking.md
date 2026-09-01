# Collaboration Blocking

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## Function Description

When Kafka experiences a backlog, and it is confirmed that the abnormal backlog is caused by a certain file, you can use this disable feature to prohibit editing that file, thereby resolving the Kafka backlog issue.

## Usage Example

1. Select Collaboration Disable

2. Enter the file GUID. Note: this refers to the GUID within ShimoDocs, not the customer's file ID.

Enter the ShimoDocs file GUID and then click "Add to Block"; the file will be prohibited from editing within 3 minutes.

Click the "Unblock" button to restore the file's editing capability.

### How to Obtain GUID

1. Open the browser developer tools

2. Filter the pull requests

3. In the request, the 16-character string from rp3OMYnMrdcQJZkm is a GUID

### How to determine the effect of the ban

The document cannot be saved successfully; after editing the file, an offline popup appears 2 minutes later, and the data is lost when the page refreshes.

### When to lift the ban

It is not recommended to lift the ban. Usually, this is because the file is too large, and the server cannot support editing. After being banned, the file becomes read-only. It is recommended to manually copy the file contents into a new file.
