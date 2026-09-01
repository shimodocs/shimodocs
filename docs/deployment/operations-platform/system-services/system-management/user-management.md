# Platform User Management

[← ShimoDocs Suite Deployment Documentation](../../../README.md)

## Overview

System user management is used to maintain user accounts in the admin backend, including creating users, editing basic information, resetting passwords, managing two-factor authentication, and deleting users.

## Access Page

After logging in with a system administrator account, select **System User Management** in the left navigation to access this page.

This menu is only available to specified system administrator accounts. If you cannot see this menu, please contact your system administrator.

## View Users

The page displays user nicknames, usernames, roles, email addresses, contact information, last login time, and registration time, and provides overview information such as total users, recently active users, and administrator accounts.

You can view all users through list pagination.

## Create a New User

Click **Create New User** and fill in the following information:

- **Nickname**: Required, used for display on the page.
- **USERNAME**: Required, used to log into the system.
- **Email**: Required, a valid email address must be provided.
- **Contact Information**: Optional.
- **Role**: Choose between regular user or administrator.

After creation, the system will generate an initial password, PASSWORD. Please copy it immediately and provide it to the user securely, as PASSWORD may not be viewable again once this window is closed.

### User Role Description

- Administrator
  - Can use all pages according to global permissions
    - ShimoDocs Suite
    - Document Center
    - System Services
- Regular User
  - Page usage range is based on global permissions
    - ShimoDocs Suite
    - Document Center
    - System Services (Hidden)

## Edit User Information

Click the edit button on the right side of the user to modify their nickname, email, and contact information. USERNAME cannot be modified on this page after creation.

## Reset PASSWORD

Click the reset PASSWORD button and confirm the action, and the system will generate a new PASSWORD. The original PASSWORD will immediately become invalid.

Please copy and securely save the new PASSWORD, deliver it to the respective user through a trusted channel, and remind the user to log in and change the PASSWORD as soon as possible.

## Manage Two-Factor Authentication

- **Enable or Disable 2FA**: Use the switch in the user's row and continue in the confirmation window.
- **Reset 2FA**: The system will generate a new QR code and key, and the original verification information will become invalid.

After resetting, the user should rescans and binds using an authenticator such as Authenticator. The QR code and key are sensitive credentials and should not be transmitted via public channels.

Bind 2FA

Add by scanning with Authenticator and use a dynamic 6-digit 2FA for subsequent logins.

## Delete User

Click the delete button and confirm, and the user account will be removed. Deletion cannot be undone, so please ensure the account is no longer in use and complete the necessary data and permission handover before performing this action.

## Common Situations

- **Unable to create user**: Please check whether the USERNAME is already taken, the email format is correct, and all required fields are complete.
- **User cannot log in**: Please verify the credentials; if necessary, reset the USERNAME and PASSWORD.
- **User cannot complete two-factor authentication (2FA) verification**: Ensure the system time is correct, or reset 2FA for the user and rebind it.
- **User management menu is not visible**: The current account may not be the designated system administrator account.
- **Accidentally Deleted User**: Deletion cannot be undone directly; you need to recreate the account and reconfigure the relevant permissions.

> Credentials generated when creating, resetting the PASSWORD, and resetting 2FA should be saved promptly and provided only to the account holder.
