---
# required metadata

title: Upgrade the SQL Server instance of Microsoft Dynamics 365 Finance + Operations (on-premises) environments
description: This topic explains how to upgrade the Microsoft SQL Server instance or cluster that your environment is using.
author: faix
ms.date: 11/29/2021
ms.topic: article
audience: IT Pro
ms.reviewer: sericks
ms.search.region: Global
ms.author: osfaixat
ms.search.validFrom: 2021-11-29

---

# Upgrade the SQL Server instance of Microsoft Dynamics 365 Finance + Operations (on-premises) environments

This topic explains how to upgrade the Microsoft SQL Server instance or cluster that your environment is using. You must complete this process if you want to upgrade from one major version of SQL Server to another but don't want to do an [in-place upgrade](/sql/database-engine/install-windows/choose-a-database-engine-upgrade-method.md).

## Prerequisites for upgrading the SQL Server version

### Upgrade from SQL Server 2016 to SQL Server 2019

- The environment must be on application version 10.0.21 or later.
- The local agent must be on version 2.7.0 or later.

## Preparation

1. Deploy your new SQL Server instance on a virtual machine (VM), or create a new SQL cluster.
1. Configure the new instance or cluster as described in [Set up SQL Server](./setup-deploy-on-premises-pu41.md#setupsql).
1. If you're using self-signed certificates, make sure that they have been imported into the Application Object Server (AOS), Management Reporter (MR), and Orchestrator nodes in your Azure Service Fabric cluster.

## Database operations

1. Make sure that users aren't connected, because your downtime will begin.
1. Back up all your databases from your current SQL Server instance or cluster.
1. Restore the database backups into the new SQL Server instance or cluster.
1. After your databases are restored, run the scripts in [Configure the databases](./setup-deploy-on-premises-pu41.md#configuredb).

## Upgrade other SQL Server components

All your SQL Server components across an environment must be on the same version. For example, if you're upgrading to SQL Server 2019, SQL Server Reporting Services (SSRS), SQL Server Integration Services (SSIS), and the database engines must all be on the same version.

1. On your AOS nodes, upgrade the SSIS component.
1. On your Business Intelligence (BI) nodes, upgrade the SSRS and database engine components.

## Update the local agent

1. Clean up the local agent by running the following command from an Orchestrator node.

    ```powershell
    LocalAgentCLI.exe Cleanup <path of localagent-config.json>
    ```

1. In [Microsoft Dynamics Lifecycle Services (LCS)](https://lcs.dynamics.com), update your connector configuration with the new fully qualified domain name (FQDN) of the SQL Server instance or cluster.

    ![Updating the connector database settings.](media/ConnectorSettingsDB.png)

1. Download the new configuration file.
1. In the configuration file, update the SQL Server version. The version that you specify must match the version of SQL Server that is present in the environment. For more information, see [Deployment configurations for the local agent](./onprem-localagent-options.md).

    ```json
    "deploymentOptions": {
        ...
        "sqlServerVersion" : {
            "value": "2019"
        },
        ...
    }
    ```

1. Install the local agent with the new configuration file.

    ```powershell
    LocalAgentCLI.exe Install <path of the new localagent-config.json>
    ```

## Update your environment settings

1. In [LCS](https://lcs.dynamics.com), select the **Full Details** link for the environment where you want to update the SQL Server.
1. Select **Maintain**, and then select **Update Settings**.
1. When you update the settings, update the FQDN of the new SQL Server instance or cluster.

    ![Updating the environment database settings.](media/EnvironmentSettingsDB.png)

1. Select **Prepare**.

    After the download and preparation are completed, the **Update environment** button appears.

	![Update environment button.](media/0a9d43044593450f1a828c0dd7698024.png)

1. Select **Update environment** to start to update your environment.

    The environment is redeployed and configured to interact with the new version of SQL Server.
