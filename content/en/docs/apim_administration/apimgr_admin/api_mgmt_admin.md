{
"title": "Manage access to APIs",
  "linkTitle": "Manage access to APIs",
  "weight": "70",
  "date": "2019-09-17",
  "description": "Manage quotas, OAuth authorizations, organizations, and users in API Manager."
}
Before you begin using API Manager as an API administrator, you must ensure that API Manager has been enabled and configured correctly for your environment. For example, this includes configuring API Manager settings such as the following:

* Monitoring metrics
* Identity provider
* Quota storage
* SMTP server - You must ensure that API Manager is configured with the SMTP server used by your organization. For example, this enables you to generate emails for user registration or client application approval.

For more details, see [Configure API Manager settings in Policy Studio](/docs/apim_administration/apimgr_admin/api_mgmt_config_ps/).

## Manage quotas

API administrators can use the **Clients** > **Default Quotas** tab to manage the maximum message traffic rate sent by applications to APIs using application-default or system-level quotas. Alternatively, API administrators can set application-specific quotas in the **Clients** > **Applications** > **Quota** tab.

API administrators can set system and application-level quotas only in API Manager. Policy developers can create custom throttling policies for user or organization-level quotas in Policy Studio.

### System and application-default quotas

To create a system or application-default quota, perform the following steps:

1. On the **Default Quotas** tab, click **Application Default** or **System**, depending on the quota type you wish to create.
2. Click **Add API**, and select an API from the list (for example, a Swagger-based Petstore API). Alternatively, select **All API**.
3. If you selected a specific API, you can select whether the quota applies to **All Methods** or to a specific method (for example, **updatePet**).
4. Select **Throttle** and enter a number of messages, or select **Throttle MB** and enter a number of megabytes.
5. Enter the amount of time, and select the time unit (for example 5 seconds).

If an application-specific quota is defined, this completely overrides the application-default quota and its associated rules. The **APIs** > **API Catalog** view in the API Manager console only shows application-default quotas.

### Application-specific quotas

{{< alert title="Note" color="primary" >}}If your front-end API uses pass-through authentication for the inbound request, there is no client application context so application quotas cannot be enforced.{{< /alert >}}

To create an application-specific quota, perform the following steps:

1. In the API Manager menu, click **Clients** > **Applications**.
2. Click the application name (for example, **Test Application**), and click the **Quota** tab.
3. Select **Override default application quota**.
4. Click **Add API**, and select an API from the list (for example, a Swagger-based Petstore API). Alternatively, select **All API**.
5. If you selected a specific API, you can select whether the quota applies to **All Methods** or to a specific method (for example, **deletePet**).
6. Select **Throttle** and enter a number of messages, or **Throttle MB** and enter a number of megabytes.
7. Enter the amount of time, and select the time unit (for example 5 seconds).

### Quota time windows

When specifying time windows in quota rules, the quota opens when the API is called at the current second, minute, day, or week, depending on the time unit specified in the quota rule.

For example, you have defined a quota rule on API A and method B that throttles the message count to N messages per hour. Then assume API A and method B was invoked at 14:33 for the first time. The specified rule is activated at the time of the first API call, setting the time window to start at the hour (14:00:00.000). If you get another call at 14:35, the counter is incremented, and its value is validated against the limit (N). If you get another call at 17:33, the new time window start will start at the hour (17:00:00.000), and the counter is reset to 0 before reflecting the API call from 17:33.

### Multiple quota rules per method

You can also specify quotas with multiple rules for the same API methods for all quota types (system, application default, and application specific). For example, a system-level quota for a pet store API is specified with the following rules for the `addPet`
method:

* 10 messages every 5 seconds
* 1000 messages every 1 day

Both quota rules apply to the same API method.

### Configure quota storage settings

You can configure how quota information is stored using Policy Studio in **Server Settings** > **API Manager** > **Quota Settings**. For more details, see [Quota Settings](/docs/apim_administration/apimgr_admin/api_mgmt_config_ps/#quota-settings).

## Manage OAuth authorizations

API Manager enables API administrators to view and revoke OAuth authorizations made by protected resource owners. This enables you to manage all client application authorizations to access OAuth-protected APIs. This also means that resource owners do not need to re-authorize application requests.

When client applications are authorized to access OAuth-protected APIs, they are issued with an access token and optionally a refresh token. API Manager displays the authorizations granted to each client application, including the scope. Revoking an OAuth authorization means that the access and refresh tokens that the client application has are no longer valid.

The **Clients** > **OAuth Authorizations** tab enables you to manage the stored OAuth authorizations made by protected resource owners.

The following details are displayed:

* **SUBJECT**: The name of the OAuth resource owner (for example, **sample_user**).
* **SCOPES**: The OAuth scopes used to managed access to the protected resource (for example, **resource.Write**, **openid**).
* **CREATED**: When the authorization was first made.

To revoke a stored authorization, and block further requests from the client application, select the resource owner name under **SUBJECT**, and click **Remove**.

## Manage organizations

API administrators can use the **Clients > Organizations** tab to create and edit organizations.

### What is an organization

Organizations are used in API Manager by different entities to group or manage entities such as users, applications, and APIs. There are two major types of organizations:

* **Development organization**: Typically represents an internal unit of an enterprise. You can create a development organization for every business unit. This allows internal users who belong to a development organization and have the role organization administrator to register APIs in a self-service fashion up to the Unpublished state. These users are API service providers.  
* **Standard organizations**: These organizations are for consumption only, as they cannot own APIs. Users who belong to standard organizations are typically external users that discover and consume APIs. These users are API service consumers.

### Grant access to APIs

Both organization types can be used to manage access to APIs. Organization administrators grant access to APIs for one, multiple, or all organizations. As a result, a user sees only the APIs that their organization has access to.

For more details, see [Organizations and user roles in API Manager](/docs/api_mgmt_overview/key_concepts/api_mgmt_orgs_roles/#organizations-and-user-roles-in-api-manager).

### Create an organization

To create an organization, perform the following steps:

1. Click **New organization** in the toolbar.
2. Configure the following general fields:
   * **Image**: Click to add a graphical image for the organization (for example, .png, `.gif`, or `.jpeg` file).
   * **Organization name**: Enter a name for the organization. This field is required.
   * **Email**: Enter an email address for the organization.
   * **Enabled**: Select whether the organization is enabled. The organization is enabled by default.  When disabled, all API calls secured by applications in that organization will be rejected.
   * **API Development**: Select whether the organization is enabled for API development. This setting is disabled by default. You must first enable an organization for API development before you can begin registering REST APIs for that organization. For more details, see [Register REST APIs in API Manager](/docs/apim_administration/apimgr_admin/api_mgmt_register_web/). When the organization has registered APIs, you cannot disable this setting.
   * **Virtual host**: Enter the virtual host and port on which unpublished APIs belonging to this organization are available. The host name should be DNS resolvable.
3. If **Trial mode** is enabled on the **Settings** > **API Manager Settings** page, the following settings are displayed to enable you to manage the lifespan of the organization:
   * **Trial Status**: Select one of the following:
     * **No Trial**: The organization is not in trial mode.
     * **In Trial**: The organization is in trial mode.
     * **Trial Ended**: The trial for this organization has ended, the organization expires, and users in the organization can no longer log in.
   * **Trial Start**: When the trial started. The trial starts when a member of the organization logs in.
   * **Trial End**: When the trial will end.
   * **Trial Duration**: Duration of the trial in days. Defaults to 30 days.
   * **Extend Trial**: Click to extend the duration of the trial.
   * **Restart Trial**: Click to reset a trial that has ended. The trial restarts when a member of the organization logs in.
4. Configure the following additional attributes:
   * **Phone**: Enter a phone number for the organization if available.
   * **Description**: Enter a short description of the organization.
5. Click **Add API** to select the APIs that the organization can access.
6. Click **Generate code** to generate optional registration codes used to simplify onboarding of new users into the organization. You can specify the **Maximum number of users per code** and **The code is valid until**. These codes are provided to new users who can input them when self-registering in API Manager. These users are then automatically registered in the organization.
7. Click **Create** in the toolbar.

### Edit an organization

When organizations have been created, you can click an organization name in the **Managing organizations** screen to edit its settings. You can also perform the following tasks:

* Click **Add API** to select the APIs that the organization can access.
* Click **Generate code** to generate optional registration codes used to onboard new users into the organization. You can specify the **Maximum number of users per code** and when **The code is valid until**. You can provide these registration codes to new users who can input them when self-registering in API Manager.
* Click to view the **Users** and **Applications** in that organization.

## Manage users

API administrators and organization administrators can use the **Clients** > **Application Developers** tab to create and edit the administrator users and the API consumers that use the APIs virtualized in **APIs** > **API Catalog**.

### Create a user

To create a user, perform the following steps:

1. Click **New user** in the toolbar.
2. Configure the following general fields:
   * **Image**: Click to add a graphical image for the user (for example, .png, `.gif`, or `.jpeg` file).
   * **Login Name**: Enter a globally unique name to identify the user, which is entered by the user when logging in to API Manager. This can be changed only by an API administrator, and is read-only for all other users. This field is required. Changing a user's login name prevents that user from logging in. You must ensure that the user is notified of any change.
   * **Name**: Enter the user's first name and surname to be used as a display name. This field is required.
   * **Email**: Enter an email address for the user. This field is required. This must be globally unique when the **Login Name** is set to the email address.
   * **Enabled**: Select whether the user is enabled. The user is enabled by default.
3. Configure the following membership fields. You can use the **+** button to add multiple memberships:
   * **Organization**: Select the organization that the user belongs to. The default list includes the API Development organization only.

        {{< alert title="Note">}}The first organization assigned to the user will be its **Primary Org**. This means that the user is logged into and associated with this organization by default, and if this organization is deleted the user is deleted together with it.{{< /alert >}}

   * **Role**: Select one of the following required roles for the user:
     * **API Manager Administrator**: This is the API administrator with full access rights.
     * **Organization Administrator**: This administrator has a subset of access rights within an organization.
     * **User**: This is the client application developer user (API consumer).

4. Configure the following additional attributes:
   * **Phone**: Enter a phone number for the user.
   * **Description**: Enter a short description of the user.
5. Click **Create** in the toolbar.

### Edit a user

When users have been created, you can click a user name in the **Managing users** screen to edit its settings. You can also perform the following:

* Click to view the user's **Organization** and **Applications**.
* Click **Reset password** to generate a random password and send it to the user's email address.
* Click **Change password** to enter a new user password in the dialog.

When you delete a user, their applications are reassigned to the API administrator.

### Enforce password changes

You can configure API Manager to ask users to change their password at first login, as well as after an expired interval. These settings are enabled by default. To disable the **Enable password expiry** and **Days before passwords expire** settings, click **Settings** > **API Manager settings**.

If you disable **Enable password expiry**, which forces users to change password at first login, and a new user logs in after that, then this user will not be asked to change password if you decide to enable this setting again.

## Manage applications

API administrators, organization administrators, and application developers (API consumers) can use the **Clients** > **Applications** tab. This enables you to create and edit the client applications that use the APIs virtualized in **APIs** > **API Catalog**.

For details on managing applications, see [Manage client applications](/docs/apim_administration/apimgr_admin/api_mgmt_consume/#manage-client-applications).
