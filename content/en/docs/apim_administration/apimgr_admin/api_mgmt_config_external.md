{
"title": "Configure external LDAP identity providers",
  "linkTitle": "Configure external LDAP identity providers",
  "weight": "160",
  "date": "2019-09-17",
  "description": "Configure policy-based integration with external identity providers using Lightweight Directory Access Protocol (LDAP)."
}
API Manager provides policy-based integration with external identity providers using Lightweight Directory Access Protocol (LDAP). For example, this enables internal API Manager users such as API administrators to use their existing LDAP user account to log into API Manager. However, they must still use their LDAP identity provider to perform tasks such as changing their LDAP password.

API Manager supports a hybrid mix of external LDAP and API Manager identity providers. For example, you can configure an external LDAP identity provider such as Apache Directory or Microsoft Active Directory, and also create external users in API Manager. Users created in API Manager are authenticated against the Key Property Store (KPS) and stored in the Apache Cassandra database.

Typically, in this hybrid setup, users are managed as follows:

* External LDAP identity provider stores existing internal corporate users and administrators
* API Manager uses the Cassandra database to store external users and developers using the API Manager on-boarding process

You can also use your existing external LDAP identity provider to store external users and developers.

## User registration and login

When using both Cassandra users and external LDAP users with API Manager:

* Users managed in the external identity provider do not need to be created in API Manager, and are automatically created on first user login. API Manager does not store passwords for these users.
* Users not managed in the external identity provider are created in API Manager by the API administrator or by self-registration. API Manager stores the full user profiles and passwords for these users in Cassandra.

When a user logs into API Manager:

* If the user exists in Cassandra, API Manager checks the password for the user stored in Cassandra, and grants access.
* If the user does not exist in Cassandra, API Manager checks if the user exists in the configured external identity store. If the user exists, the behavior is as follows:

  1. At first login, API Manager automatically creates the user in Cassandra, stores the login as a read only field (the credentials remain in the external identity store), and a role is automatically assigned using the configured policy. Roles can also be changed by the API administrator in API Manager.
  2. For subsequent logins, API Manager will have the user login in Cassandra, and will check the user credentials in the external identity store.

## Sample configuration

API Manager provides sample external identity providers for LDAP based on Apache Directory and Microsoft Active Directory. This topic explains how to configure these sample providers using the Policy Studio and API Manager tools.

The following sample external identity provider configuration is available in the Policy Studio tree:

* Apache Directory LDAP authentication and account retrieval policies in **Policies** > **Sample Policies** > **API Management Identity Provider** > **LDAP**
* Active Directory authentication and account retrieval policies in **Policies** >**Sample Policies** > **API Management Identity Provider** > **Active Directory**
* Common account creation success and failure policies in **Policies** > **Sample Policies** > **API Management Identity Provider**
* Sample LDAP repositories in **Environment Configuration** > **External Connections** > **Authentication Repositories** > **LDAP Repositories**
* Sample LDAP connections in **Environment Configuration** > **External Connections** > **LDAP Connections**

## Configure an Apache Directory LDAP external identity provider

This section explains how to configure an Apache Directory LDAP external identity provider.

### Prerequisites

The sample LDAP configuration assumes that an Apache Directory LDAP server is running locally (on `localhost:10389`), and configured with a sample partition (Seven Seas). This sample partition is available from:

<http://directory.apache.org/apacheds/basic-ug/1.4.3-adding-partition.html>

When the partition has been configured, you must import the sample LDAP Data Interchange Format (LDIF) data to populate the directory with users. The sample LDIF data is available from:

<http://directory.apache.org/apacheds/basic-ug/1.5-sample-configuration.html#the-sample-data-sailors-of-the-seven-seas>

All user passwords are set to `pass`.

For more details, see the *Apache Directory Studio User Guide*.

### Configuration steps

To set up LDAP as an external identity provider, perform the following steps:

1. In the Policy Studio tree, select **Server Settings** > **API Manager** > **Identity Provider** > **Use external identity provider**.
2. Ensure that the sample LDAP account policies are configured. These policies are selected by default. For example:
   ![Sample LDAP policies](/Images/docbook/images/api_mgmt/api_mgmt_sample_ldap_policies.png)
3. Click **Apply Changes** at the bottom right.
4. Optionally, if the community organization is not named **Community**, or if you wish to on-board users to a specific organization, edit the **Set.extidentity.organization** filter in the **Read LDAP Account Information** policy. For example:
   ![Retrieve LDAP Account Information policy](/Images/docbook/images/api_mgmt/api_mgmt_sample_ldap_policies_retrieve.png)
5. Enter the appropriate value in the **Organization selector** field (for example, `Community`).
6. Click **Deploy** in the toolbar to deploy the updated configuration.
7. Connect to API Manager in your browser (`https://localhost:8075/`).
8. On-board a user from the Apache Directory LDAP server by logging in with the appropriate user credentials (for example, the `wbligh` user).
9. Select **Settings > Account** to view the on-boarded account details. For example:
   ![Onboarded LDAP user](/Images/docbook/images/api_mgmt/api_mgmt_sample_ldap_onboard.png)

{{< alert title="Note" color="primary" >}}The **Login name** for an external user (provisioned by an external identity provider) is read-only and cannot be changed.{{< /alert >}}

## Configure a Microsoft Active Directory external identity provider

This section explains how to configure a Microsoft Active Directory external identity provider.

To set up Active Directory as an external identity provider, perform the following steps:

1. In the Policy Studio tree, select **Server Settings** >**API Manager** > **Identity Provider** > **Use external identity provider**.
2. Ensure that the sample Active Directory account policies are configured. For example:
   ![Sample Active Directory policies](/Images/docbook/images/api_mgmt/api_mgmt_sample_ad_policies.png)
3. Click **Apply Changes** at the bottom right.
4. In the Policy Studio tree, select **Environment Configuration** > **External Connections** >**LDAP Connections** > **API Management Sample Active Directory Connection**.
5. Right-click, select **Edit,** and enter the following settings:

   * **URL**: Enter the URL for your LDAP server (for example, `ldap://127.0.0.1:389`).
   * **User Name**: Enter the distinguished name of the user to connect to the Active Directory (for example, `CN=Joe Bloggs,OU=DUBL,OU=IE,OU=Employees,DC=company,DC=com`). This user must have `Read MemberOf` (search) privileges.
   * **Password**: Enter the user password.

   ![Sample Active Directory connection](/Images/docbook/images/api_mgmt/api_mgmt_sample_ad_cxn.png)
6. Click **Test Connection** to verify that the configuration details are correct.
7. Select **Environment Configuration** > **External Connections** > **Authentication Repositories** > **LDAP Repositories** > **API Management Sample Active Directory Repository**.
8. Right-click, select **Edit Repository,** and enter the **Base Criteria** (for example, `OU=Employees,DC=company,DC=com`). This is the starting point in the Active Directory hierarchy at which the search for users will begin.
9. Optionally, if the community organization is not named **Community**, or if you wish to on-board users to a specific organization, edit the **Set.extidentity.organization** filter in the **Read Active Directory Account Information** policy.
10. Enter the appropriate value in the **Organization selector** field (for example, `Community`).
11. Click **Deploy** in the toolbar to deploy the updated configuration.  
12. Connect to API Manager in your browser (`https://localhost:8075/`).
13. On-board a user from the Active Directory server by logging in with the appropriate user credentials (for example, a `jbloggs` user).
14. Select **Settings > Account** to view the on-boarded account details. For example:
    ![Onboarded Active Directory user](/Images/docbook/images/api_mgmt/api_mgmt_sample_ad_onboard.png)

{{< alert title="Note" color="primary" >}}The **Login name** for an external user (provisioned by an external identity provider) is read-only and cannot be changed.{{< /alert >}}

## Account information policy

You can configure the **Account information policy** in Policy Studio in **Environment Configuration > Server Settings > API Manager > Identity Provider**. This policy returns the user information to API Manager using the following attributes:

| Attribute                     | Description                                                                                                                                                                                                                                                                                                            |
| ----------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `extidentity.organization.id` | The organization ID (required).                                                                                                                                                                                                                                                                                        |
| `extidentity.role`            | The user’s role (required). This is one of `user` (client appplication developer), `oadmin` (organization administrator), or `admin` (API administrator).                                                                                                                                                              |
| `extidentity.enabled`         | User is enabled only if the selector evaluates to `1` or `true`.                                                                                                                                                                                                                                                       |
| `extidentity.name`            | The user’s name (required).                                                                                                                                                                                                                                                                                            |
| `extidentity.description`     | A description of the user.                                                                                                                                                                                                                                                                                             |
| `extidentity.email`           | The user’s email address.                                                                                                                                                                                                                                                                                              |
| `extidentity.phone`           | The user’s phone number.                                                                                                                                                                                                                                                                                               |
| `extidentity.orgs2Role`       | This optional attribute associates the user with multiple organizations. The value must be a JSON array of organization name and user role pairs. The organization must exist before configuring it. Possible roles are `oadmin` and `user`. For example: `[{"organizationName1": "oadmin"},{"organizationName2": "user"}]` |
