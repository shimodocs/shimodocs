# Transcoding Event Search

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## Function Overview
The transcoding event query function is used to quickly view recent transcoding events on the MDP backend, helping to locate and troubleshoot issues during the transcoding process.

By default, the list will display the most recent transcoding events.

## Task_ID Acquisition
A task_ID is generated during import and export tasks.

Open the browser's developer mode. During export, you can obtain the task_ID by checking the interface as shown in the figure below.

## Query by Task_ID
Enter the task_ID in the task ID input box to quickly filter transcoding events related to that task.

## View Links
As shown in the figure below, click the "View Link" icon in the row record to see all events related to that task ID. This helps analyze the complete process of the transcoding task from start to finish.

## Exception Location

### gRPC success, no callback received
If gRPC is sent successfully and a response is received successfully, it means the transcoding task has been assigned to the transcoding service. In this case, if the callback is not received in time due to the transcoding service timing out, the transcoding service needs to be investigated.

### Callback received
If you can see the callback_ID of the task, it is generally considered a transcoding failure, such as incompatible formats or other exceptions.
