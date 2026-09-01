# Operations Platform Overview

[← ShimoDocs Suite Deployment Documentation](../README.md)

## Feature Overview

- **ShimoDocs Suite**: Used for managing ShimoDocs Suite related to authorization, tenants, users, brands, and AI configurations.
- **System Services**: Used for general operations and maintenance tasks, such as global configuration, cluster management, log viewing, feature checks, issue queries, document repair, and **system upgrades**.

> **Note**: The specific features displayed depend on the current deployment version and the features that have been enabled.

## Logging into the Operations Platform

Access the following address in your browser:
> **Browser Requirements**: Please use Google Chrome version 111 or above to access the operations platform. It is recommended to upgrade to the latest stable version first.

```text
http(s)://<OPERATIONS_PLATFORM IP OR_DOMAIN_NAME>/mdp/user/login
```

Enter the administrator account and PASSWORD, then click "Login".

## Understanding the Operations Platform Home Page

After logging in, you can access the corresponding management functions through the menu on the left side of the page. The displayed menu depends on the products and versions deployed and authorized in the current environment.

## Resetting the Administrator PASSWORD When Forgotten

If you forget the operations platform administrator PASSWORD, you can log in to the deployment node and execute the following command to reset it.

```bash
kubectl exec -it $(kubectl get pods -l app=mdp -o jsonpath='{.items[0].metadata.name}') -- reset-admin-password Aa1234567.
```

The above example resets the PASSWORD to `Aa1234567.`. In actual operation, please replace the example PASSWORD at the end of the command with a new PASSWORD that meets security requirements.

After resetting, return to the login page, log in with the new PASSWORD, and confirm that the menu can be accessed normally.
