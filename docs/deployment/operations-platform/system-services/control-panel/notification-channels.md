# Notification Channels

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## Overview

Notification channels are used to centrally manage how system alert messages are received, allowing middleware checks and other features to send failure and recovery notifications.

Currently supported channels include WeCom, DingTalk, Feishu, and custom Webhooks.

## Access Page

After logging into the management console, select **Notification Channels** in the left navigation to access this page.

Notification channels are only available to administrators. If you do not see this menu, please contact your system administrator to confirm your account permissions.

## Create a New Notification Channel

Click **Create Channel**, enter a channel name, and select the channel type:

- **WeCom**: Enter the robot Webhook key.
- **DingTalk**: Enter the full Webhook URL and optionally enter the signature key according to the robot configuration.
- **Feishu**: Enter the complete Webhook URL, and optionally enter a signing key according to the bot configuration.
- **Custom Webhook**: Enter the request URL, HTTP method, and body template.

Confirm whether to enable this channel, then click **Save**.

## Custom Webhook

The body template for custom Webhook supports the following variables:

```text
{{title}}
{{body}}
{{level}}
```

Default template example: 

```json
{"title":"{{title}}","body":"{{body}}","level":"{{level}}"}
```

When the system sends a notification, it will replace variables with the actual title, content, and alert level. 

## Test Channel

After saving, click **Test** on the right side of the channel. The system will send a test message to verify that the Webhook address, signature, and network connection are correct.

It is recommended to perform a test immediately after creating or modifying a channel, before binding it to middleware checks or other business functions.

## Enable, Edit, and Delete

- **Enable/Disable**: Adjust the enabled status when editing the channel. Once disabled, the channel will not receive business notifications.
- **Edit**: You can modify the channel name, type, and Webhook configuration.
- **Delete**: Remove channels that are no longer in use. Channels referenced by middleware checks must be unbound before they can be deleted.

## Common Situations

- **Test Sending Failed**: Please check the Webhook address, Key, Secret, HTTP method, and network access permissions.
- **Save Failed**: Please ensure all required fields are completed and the Webhook URL format is correct.
- **Business Notification Not Received**: Please confirm that the channel is enabled and selected on the corresponding business page.
- **Unable to Delete Channel**: The channel may still be used by middleware checks. Please remove the association and save the check configuration first.
- **Custom Webhook Received Content Format Incorrect**: Please check whether the Body template meets the requirements of the target system.

> Webhook addresses and signature Secrets are sensitive information. Please limit access and avoid sharing them publicly via screenshots, logs, or chat tools.

## Example Operation Interface

The image below shows the channel types and configuration form when creating a new notification channel.

