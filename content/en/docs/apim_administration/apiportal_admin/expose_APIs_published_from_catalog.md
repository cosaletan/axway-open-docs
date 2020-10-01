---
title: Expose APIs published from the Unified Catalog
linkTitle: Expose APIs published from the Unified Catalog
weight: 80
date: 2020-05-29T00:00:00.000Z
description: Configure API Portal to expose APIs, Applications, and
  Subscriptions from the AMPLIFY Unified Catalog (cloud platform), alongside
  resources coming from API Manager (on-premise platform).
---
{{< alert title="Public beta" color="warning" >}}This feature is currently **experimental**, and it is available for a technical preview only by way of a Docker image.{{< /alert >}}

Before you start, you must create a Service account in AMPLIFY Central to use this feature. To learn more see, [Create a service account](/docs/central/cli_central/cli_install/#create-a-service-account).

## Set up the connection to AMPLIFY

1. Log in to the Joomla! Administrator Interface (JAI), and click **Components** > **API Portal** > **AMPLIFY**.
2. Enter your `Client ID`, from your AMPLIFY Central Service (DOSA) account, in the **Client ID** field.
3. Enter the content of your private key (from the key pair generated for the DOSA Account) in the **Private key** field.
4. Click **Test connection**.

   Message **Connection established successfully!** is displayed.
5. Click **Save**.

## Configure menu items to display the catalog resources

You must create menu items to display APIs and Applications from the Unified Catalog.

### Create API Catalog menu items

Configure the menu item to display APIs from the Unified Catalog.

1. In JAI, click **Menus** > **Main Menu**.
2. Click **New** to add a new Menu item.
3. Enter a title, and from **Menu Item Type** select **API Portal** > **API Catalog Page**.
4. Click the **API Catalog** tab and change **Data Source** from **API Manager** to **Unified Catalog**.
5. Click **Save**.

### Create Applications Lists menu items

Configure the menu item to display Applications from the Unified Catalog.

1. In JAI, click **Menus** > **Main Menu**.
2. Click **New** to add a new Menu item.
3. Enter a title, and from **Menu Item Type** select **API Portal** > **Applications List Page**.
4. Click the **Applications** tab and change **Data Source** from **API Manager** to **Unified Catalog**.
5. Click **Save**.

Your API Portal is now configured to serve APIs and Applications from the AMPLIFY Unified Catalog, as well as from API Manager.