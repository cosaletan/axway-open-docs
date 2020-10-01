---
title: API Gateway and API Manager 7.7 September 2020 Release Notes
linkTitle: API Gateway and API Manager September 2020
weight: 60
date: 2020-08-26T00:00:00.000Z
---

## Summary

API Gateway is available as a software installation or a virtualized deployment in Docker containers. API Manager is a licensed product running on top of API Gateway, and has the same deployment options as API Gateway.

The software installation is available on Linux. For more details on supported platforms for software installation, see [System requirements](/docs/apim_installation/apigtw_install/system_requirements/).

Docker deployment is supported on Linux. For a summary of the system requirements for a Docker deployment, see [Set up Docker environment](/docs/apim_installation/apigw_containers/docker_scripts_prereqs/).

## New features and enhancements

The following new features and enhancements are available in this update.

### API Manager Remote host updates

The Remote host capability in API Manager has been enhanced to provide the same powerful configuration options that can be found in Policy Studio. Take advantage of creating aliases for back-ends, and apply specific changes to control a connection without having to deploy the change via Policy Studio. To learn more, see [Remote hosts](/docs/apim_reference/api_mgmt_config_web/#remote-hosts).

### YAML-based configuration store

This work is an initiative born from collaborative customer hackathons to make the API Gateway configuration more CI/CD/DevOps and developer-friendly.​ It involved transforming the federated configuration into YAML fragments, which can be managed using standard DevOps tools, moving away from a propriety TeamDev approach to encourage the use of standard tools, source control, and DevOps tools that could be used to facilitate and encourage a better experience for collaboration.​

This initiative focuses on addressing:​

* Fine-grained configuration for an improved collaborative development experience​.
* Source code which is developer-friendly​.
* Designed for improved DevOps capability.

{{% alert title="Note" %}}The XML configuration store is still supported and can be used as normal.{{% /alert %}}

We strongly encourage our customers to take a look and explore the possibilities of the new configuration format, and provide feedback to us on this experience. To learn more, see [Axway API Management: DevOps friendly configuration capability](https://community.axway.com/s/article/Axway-API-Management-Upcoming-DevOps-friendly-capability).

To follow-up on what's to come based on this capability, see [API Management Roadmap](https://community.axway.com/s/api-management).

### Database integration updates

Validated support for the following versions of Oracle Databases:​

* 12c Release 2 (Oracle Database 12c Release 2 (12.2.0.1.0) - Standard Edition 2 and Enterprise Edition)​
* 18c (18.3)​
* 19c (19.3)​

### User membership in multiple organizations

API Manager now enables creating user membership in multiple organizations (multi-orgs). User accounts will have different roles in each organization the user is a member of. The following are some important changes implemented to enable this new feature.

#### API version update

The multi-orgs feature is available with the 1.4 version of the API only, meaning that any third-party portals or integrations (for example, SSO) must be updated to use version 1.4. The API Manager UI uses the API 1.4 by default now.

This feature is forward compatible, the API 1.4 will work with single-org users, but not backward compatible, you cannot configure multi-orgs in the API 1.4, then revert it to the API 1.3.

The new **API Manager 7.7 API 1.4** is available in OAS3 format on the [Axway Documentation portal](https://docs.axway.com/category/api).

#### Managing multi-orgs users

The following are new changes implemented to facilitate the creation and maintenance of multi-orgs users in API Manager:

* The API Administrator can now assign a user to more than one organization using the [User API](http://apidocs.axway.com/swagger-ui-NEW/index.html?productname=apimanager&productversion=7.7.0&filename=api-manager-V_1_4-oas3.json#/Users) or the Application Developer's **Create** and **Edit** user interface. For more information, see [Manage users](/docs/apim_administration/apimgr_admin/api_mgmt_admin/#manage-users).

* The ability for the Organization Administrator to manage users within its own organization has changed to avoid privileged escalation concerns. OrgAdmins can only edit or delete multi-orgs users from organizations where they are OrgAdmins. To learn more about these restrictions, see [Organizations and user roles in API Manager](/docs/api_mgmt_overview/key_concepts/api_mgmt_orgs_roles/#organizations-and-user-roles-in-api-manager).

* Integration via SSO has changed to facilitate configuring users from an external IDP to multi-orgs. You can use the new setting, `orgs2Role`, to assign a user to multi-orgs. This attribute can be populated via:
    * A direct attribute in the IDP.
    * An LDAP mapping in the service-provider.xml file.
    * A filter configured in Policy Studio, which allows the `orgs2Role` setting to be overridden.

For more information on configuring multi-orgs users, see [Configure API Manager Single Sign On](/docs/apim_administration/apimgr_sso/saml_sso_config/).

#### Account changes

When a user account is configured to be a member of multi-orgs, it is automatically authenticated and associated with its primary organization, the first organization in their membership list. Using the drop-down menu, users can navigate between their organizations and the UI will display the options based on their role, which can be either `orgAdmin` or `User`.

When you delete the primary organization of a user, the user’s account is also deleted. For details, see [Manage users](/docs/apim_administration/apimgr_admin/api_mgmt_admin/#manage-users).

#### Organization administrators can publish APIs

By default, Organization administrators require the approval of an API administrator to publish and unpublish APIs that were created in their organization without approval from an API Administrator. By setting the `api.manager.orgadmin.selfservice.enabled` system property to `true`, the OrgAdmin will no longer require approval, and will be able to directly publish and unpublish APIs.

## Important changes

It is important, especially when upgrading from an earlier version, to be aware of the following changes in the behavior or operation of the product in this update.

<!-- Use this section to describe any changes in the behavior of the product (as a result of features or fixes), for example, new Java system properties in the jvm.xml file. This section could also be used for any important information that doesn't fit elsewhere. -->

### OpenJDK JRE update to v8u265

API Gateway 7.7 and API Manager 7.7 support OpenJDK JRE, and this release includes Zulu OpenJDK v8u265.

The Zulu OpenJDK v8u265 changes the way API Gateway verifies wildcarded certificates. If the wildcarded domain is a top-level domain under which names can be registered, then a wildcard is not allowed. Matching approach has not changed for the second-level and lower-level domain names. In case you have certificates with wildcards characters in top-level domain you must recreate them with proper top-level domain names.

Examples of matching domain names:

```javascript
SubjectAlternativeName [
 DNSName: *.com     // will match e.g. 'google.com'
 DNSName: *.*.com   // will match e.g. 'mail.google.com'
 DNSName: *         // no longer match 'abc'
 DNSName: abc.*     // no longer match 'abc.com'
 ]
```

### Removed broken and risky algorithms from sFTP server

Broken and risky algorithms utilized in sFTP protocols exposed in all previous versions of API Gateway have been removed to mitigate possible attacks against those services. This means that any FTP client that uses any of the removed algorithms **cannot connect** to the sFTP server after this release.

The list of removed broken and risky algorithms is:

* **Ciphers**: `arcfour256`, `arcfour128`, `aes128-cbc`, `3des-cbc`, `blowfish-cbc`, `aes192-cbc`, `aes256-cbc`
* **Mac Algorithms**: `hmac-md5`, `hmac-sha1`, `hmac-sha1-96`, `hmac-md5-96`
* **Key exchange**: `diffie-hellman-group-exchange-sha1`, `diffie-hellman-group18-sha512`, `diffie-hellman-group17-sha512`, `diffie-hellman-group16-sha512`,
  `diffie-hellman-group15-sha512`, `diffie-hellman-group14-sha256`, `diffie-hellman-group14-sha1`, `diffie-hellman-group1-sha1`

The sFTP server currently supports:

* **Authentication Methods**: `Username/Password`, `Public Key`
* **Ciphers**: `aes128-ctr`, `aes192-ctr`, `aes256-ctr`
* **Mac Algorithms**: `hmac-sha2-512`, `hmac-sha2-256`
* **Signatures**: `ecdsa-sha2-nistp256`, `ecdsa-sha2-nistp384`, `ecdsa-sha2-nistp521`, `ssh-rsa, ssh-dss`
* **Key exchange**: `ecdh-sha2-nistp256`, `ecdh-sha2-nistp384`, `ecdh-sha2-nistp521`, `diffie-hellman-group-exchange-sha256`
* **Compressions**: `none`, `zlib`, `zlib@openssh.com`

### Groovy v2.5.13

The Groovy version used by the Scripting Filter is updated to 2.5.13.

### Filebeat v6.2.2

This update uses Filebeat v6.2.2. Before installing this update, you must delete the Filebeat folder  `/apigateway/tools/filebeat-5.2.0`. When using Filebeat, follow the [official Filebeat documentation](https://www.elastic.co/guide/en/beats/filebeat/6.6/index.html).

### OpenSSL and FIPS support

Running API Gateway in FIPS mode is not supported in API Gateway since the 7.7 March 2020 update, when OpenSSL was upgraded to OpenSSL 1.1.1, as OpenSSL 1.0.2 is EOL. OpenSSL 1.1.1 does not support FIPS. OpenSSL 3.0 (when available) will support FIPS.

### Increased validation of WSDLs

The Xerces library has been updated to `xerces 2.12.0`. This library enforces stricter rules when validating malformed schemas and, in consequence, some WSDLs that were previously imported successfully by API Manager might not import successfully in this version.

To suppress schema validation errors and relax the stricter validation of XML files, set the flag `-DwsdlImport.suppressSchemaValidationErrors` to `true` in the `policystudio.ini` file. The default value is `false`.

### API Gateway behavior

* **Anonymous cipher suites**: The JRE included in API Gateway disables undesirable cipher suites when using SSL/TLS by default. Users using RSA Access Manager (formerly known as RSA ClearTrust) with API Gateway might experience SSL/TLS handshake issues where no common cipher suites can be found. In this case, you should reconfigure SSL/TLS of the RSA Access Manager to support stronger cipher suites.

  Alternatively, to re-enable the anonymous cipher suites in JRE for successful SSL/TLS connections with the RSA Access Manager, remove `anon` from the `jdk.tls.disabledAlgorithms` Java security property in the `INSTALL_DIR/Linux.x86_64/jre/lib/security/java.security` file.

* **Endpoint identification property**: The JRE included in API Gateway enables endpoint identification algorithms for LDAPs (secure LDAP over TLS) by default to improve the robustness of the connections. This might cause API Gateway LDAP filters to fail to connect to an LDAP server. To disable endpoint identification add the `<VMArg  name="-Dcom.sun.jndi.ldap.object.disableEndpointIdentification=true"/>` line to the `INSTALL_DIR/system/conf/jvm.xml` file.

* **Connect to URL Filter**: If the `Request Protocol Headers` field in the **Connect to URL** filter is not configured correctly, a runtime exception (`unexpected IO error`) is thrown when the filter is executed. In earlier versions, this error did not appear in the logs.

### API Manager behavior

* **Increased validation of `/users` endpoint**: In earlier versions of API Manager, the `/users` API returns a list of all the users in an organization. This endpoint allowed a user to share an application with other users in their organization. This was identified as a security risk, as only Organization administrators and API administrators should have access to a list of users, and not user roles. The ability for user roles to view all other user names in the organization has been removed.

  To reduce the impact of this change, you can relax this restriction using a configuration flag. Set the flag in the `jvm.xml` file (it does not exist by default) under `groups/group-x/  instance-y/conf`.
  
  ```xml
  <ConfigurationFragment>
      <VMArg name="-DAPIGW_TOGGLE_FEATURE_GET_ALL_USERS=true" />
  </ConfigurationFragment>
  ```

* **Trailing slash property**: An inbound API request with a trailing slash can match an API path with no trailing slash. To activate this feature set the Java property `com.vordel.apimanager.uri.path.trailingSlash.preserve` to `true`. The default value is `false`.

* **Content type property**: An API method's Content-Type is checked against the API method's defined MIME type when performing path matching. To allow legacy API method matching and disable this check, set the Java property `com.coreapireg.apimethod.contenttype.legacy` to `true`. The default value is `false`.

* **Caching property**: Enable caching to improve general system performance and speed. Set the `com.axway.apimanager.api.data.cache` Java system property to `true`. External clients, API keys, and OAuth credentials cache are optimized so that updates to the cache no longer block API Manager runtime traffic, resulting in performance improvements for corresponding API Manager APIs. As a result of the non-blocking cache updates, API Manager memory consumption will increase, particularly in systems with large numbers of external clients, API keys, or OAuth credentials.

* **No match property**: To configure the status code of an unsuccessful match of an API to `404` when authentication is successful, set the Java property `com.axway.apimanager.use404AuthSuccessNoMatch`  to `true`. The default value is `false`.

* **MIME types**: To import API Gateway Management API Swagger into API Manager API Catalog, you must add the `application/x-download` MIME type to the default list of MIME types in API Gateway. Select **Server Settings > General > Mime configuration** in the Policy Studio tree and add `application/x-download` to the MIME list. After the configuration is deployed to API Gateway, you can import the API Gateway Manager API Swagger into API Manager API Catalog.

* **Confidential fields property**: Fields that contain confidential information are no longer returned in some API calls. For example, a call to `GET /api/portal/v1.3/proxies/` does not return the password in the `AuthenticationProfile.parameters\["password"]` field. For compatibility with earlier versions, you can continue to return confidential fields. Set the system property `com.axway.apimanager.api.model.disable.confidential.fields` to `false` in the `jvm.xml` file (it does not exist by default) under `groups/group-x/instance-y/conf`.

  ```xml
  <ConfigurationFragment>
      <VMArg name="-Dcom.axway.apimanager.api.model.disable.confidential.fields=false"/>
  </ConfigurationFragment>
  ```
  
  {{< alert title="Note" color="primary" >}}Setting this property to `false` is not recommended as it could pose a security risk to your API Gateway installation.{{< /alert >}}

### Security

* **`nosniff` header**: The X-Content-Type-Options HTTP header with value `nosniff` is not included in a HTTP response serving static content from the API Gateway or API Manager.Static contents are served from the API Gateway or API Manager `webapps` directory, which does not serve dynamic contents to avoid the risk of the browser making an incorrect assumption of the content type and exposing a security vulnerability. The X-Content-Type-Options response header with the value `nosniff` is included with HTTP responses serving dynamic content by default.

* **CSRF check property**: If you are using the API Manager Management APIs, Client Application Registry APIs, and API Gateway APIs you might need to disable the CSRF token check implemented in v7.5.3 SP9 and later. To disable this check, set the Java system property `com.axway.apimanager.csrf` to `false`. The default is `true`.

* **Custom filters**: If you have written a custom filter using the extension kit, you might need to update your custom code as a result of changes in the classes. The following interfaces deprecate `_()` and `__()` in favor of a new `resolve()` method:

    * `com.vordel.client.manager.ResourceResolver`
    * `com.vordel.client.manager.attr.ScreenAttribute`
    * `com.vordel.client.manager.wizard.EntityContext`

    Both `DefaultGUIFilter` and `VordelWizard` classes do not implement the `ResourceResolver` interface. As a result, any classes extending either of these must replace `_()` and `__()` method calls with `resolve()`.

## Deprecated features

<!-- Add features that are deprecated here -->

As part of our software development life cycle we constantly review our API Management offering.

The following capabilities have been deprecated.

### Antivirus scanners

API Gateway already supports the industry standard Internet Content Adaption Protocol (ICAP). From the November 2020 update the following embedded antivirus scanners will be removed:

* McAfee
* Sophos
* Clam AV

Content scanning is still supported using the ICAP filter, which provides out-of-the-box integration with ICAP-capable servers provided by Symantec, McAfee, OPSWAT and others, promoting ease of deployment and operational control.

## Removed features

<!-- Add features that are removed here -->

To stay current and align our offerings with customer demand and best practices, Axway might discontinue support for some capabilities. As part of this review, the following features have been removed:

### Run `update-apimanager.py` script to update API Manager

The requirement to run `update-apimanager.py` has been removed from the [Upgrade steps - Single-node upgrade example](/docs/apim_installation/apigw_upgrade/upgrade_steps_extcass/).

Updating API Manager is now carried out through any of the following procedures:

* Applying the latest API Gateway update to an existing installation will update the API Manager `config` script. This running `config` can then be pulled into a Policy Studio project.
* Policy Studio project upgrades. Importing an existing API Manager Policy Studio project will upgrade API Manager. The upgrade is also applied when creating a new project from an existing `fed` file.
* API Manager `.fed` files can be upgraded using the [upgradeconfig](/docs/apim_installation/apigw_upgrade/upgrade_analytics#upgradeconfig-options) script.
* The [projupgrade](/docs/apim_reference/devopstools_ref#projupgrade-command-options) script will apply API Manager updates to any existing projects.

{{< alert title="Note" color="" >}} If deploying an earlier update, prior to September 20, it is still required to use the `update-apimanager.py` script.{{< /alert >}}

## Fixed issues

This version of API Gateway and API Manager includes:

* Fixes from all 7.5.3, 7.6.2, and 7.7 service packs released prior to this version. For details of all the service pack fixes included, see the corresponding *SP Readme* attached to each service pack on [Axway Support](https://support.axway.com).
* Fixes from all 7.7 updates released prior to this version. For details of all the update fixes included, see the corresponding [release note](/docs/apim_relnotes/) for each 7.7 update.

### Fixed security vulnerabilities

<!-- Add  here -->

| Internal ID | Case ID                      | Cve Identifier                               | Description                                                                                                                                                                                                                                                                                                                                                  |
| ----------- | ---------------------------- | -------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| RDAPI-20951 |                              | CVE-2020-14621 CVE-2020-14556 CVE-2019-17359 | **Issue**:  API Gateway included Zulu OpenJDK v8u242, which has a number of vulnerabilities including CVE-2020-14621. API Gateway included Bouncy Castle library version 1.60 which contained CVE-2019-17359 vulnerability. **Resolution**: API Gateway now includes Zulu OpenJDK v8u265 and Bouncy Castle library version 1.66 and is no longer vulnerable. |
| RDAPI-21314 | 01195064, 01187608, 01183015 |                                              | **Issue**: API Manager uses JQuery version 3.4.1, which has a XSS vulnerability. **Resolution**: API Manager now uses JQuery version 3.5.1, which XSS vulnerability is resolved.                                                                                                                                                                             |
| RDAPI-21699 | 01184311, 01184328           | CVE-2019-17359                               | **Issue**: Aquascan reports security vulnerability CVE-2019-17359 due to bcprov-jdk15on-1.60.jar **Resolution**: bcprov-jdk15on-1.60.jar with security vulnerability CVE-2019-17359 is replaced by bcprov-jdk15on-1.60.jar that does not have the vulnerability.                                                                                             |

### Other fixed issues

| Internal ID | Case ID                                                                                                                | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
| ----------- | ---------------------------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| RDAPI-17431 | 01090329  01092098                                                                                                     | **Issue**: API Gateway Policy Studio and projdeploy script attempt to re-deploy configuration on error response from Admin Node Manager. **Resolution**: Policy Studio and projdeploy script attempt to deploy configuration only once as expected.                                                                                                                                                                                                                     |
| RDAPI-18128 | 01176343  01106018  01183936  01184266  01182781  01169424  01099988  01107786  01162018  01129468  01168438  01104274 | **Issue**: In API Manager, updates of trusted certs for a virtualized API require restart of API Manager instance in order for changes to take effect **Resolution**: Fixed a race condition in API Manager configuration when APIs are configured with outbound policies.                                                                                                                                                                                              |
| RDAPI-18431 | 01105091                                                                                                               | **Issue**: In API Manager 7.7, External Client IDs must be different to API Keys and OAuth Client IDs. In 7.5.3 they were allowed to be the same. This causes issues with some customers when migrating from 7.5.3. **Resolution**: External Client IDs can be the same to API Keys and OAuth Client IDs. Root cause that forced the decision to make them different has been solved.                                                                                   |
| RDAPI-19150 | 01136673                                                                                                               | **Issue**: In API Manager, the Try It Dialog only showed a maximum of 10 API Keys as the store used to retrieve API Keys was a paginated store .Resoution: Updated the store used when retrieving API Keys to be a non paginated store so that all API Keys are retrieved.                                                                                                                                                                                              |
| RDAPI-19262 | 01138506  01095419                                                                                                     | **Issue**: In API Manager in a multiple gateway environment, when a quota is defined for an API, the X-Rate-Limit header, present in the API responses, shows inconsistent values across the different nodes. **Resolution**: When defining a quota for an API in a multiple gateway environment, the X-Rate-Limit header behaves as expected.                                                                                                                          |
| RDAPI-20023 | 01156912                                                                                                               | **Issue**: Use of TLS Server Name Indication (SNI) was only enabled when the option "verification of server's certificate name match against server name" was enabled. **Resolution**: SNI and Server Certificate name check are now separated features. This separation also applies to SMTP protocol.                                                                                                                                                                 |
| RDAPI-20526 | 01164755                                                                                                               | **Issue**: Groovy 2.4.8 provided with API Gateway does not provide an option to deactivate escaping in JSON serialization. See <https://issues.apache.org/jira/browse/GROOVY-6975> for details. **Resolution**: Groovy has been updated to version 2.5.13 that provides such option.                                                                                                                                                                                      |
| RDAPI-20958 | 01122859  01132092  01133854  01064716                                                                                 | **Issue**: Changes to an Organization Name or Organization that an Application is associated with causes duplication in the API Manager Metrics reports. **Resolution**: API Manager Metrics reports now restrict report totals using the Organization name the Application is associated with.                                                                                                                                                                         |
| RDAPI-21011 | 01174542  01181811                                                                                                     | **Issue**: API Gateway Manager does not display KPS entries correctly. **Resolution**: Tables with long column names and entries are now rendered correctly in API Gateway Manager.                                                                                                                                                                                                                                                                                     |
| RDAPI-21060 | 01189348  01194083  01171842                                                                                           | **Issue**: An SQLException occurs for any query against a read-only KPS store backed by an RDBMS. **Resolution**: Fixed the broken query syntax, allowing SQL queries to be run.                                                                                                                                                                                                                                                                                        |
| RDAPI-21068 | 01169260  01168656                                                                                                     | **Issue**: JSON redaction is slow when configured with a large number of paths to be redacted. **Resolution**: JSON redaction has been refactored to parse inbound document more efficiently and reduce the number of path matching performed.                                                                                                                                                                                                                          |
| RDAPI-21085 | 01160271  01143897  01153011  01160321  01160146                                                                       | **Issue**: API Manager was creating sessions incorrectly resulting in possible authentication issues during high traffic. **Resolution**: API Manager now only creates a session upon successful login.                                                                                                                                                                                                                                                                 |
| RDAPI-21184 | 01174950  01172806                                                                                                     | **Issue**: Custom Properties are not shown in the Swagger definition of an API. **Resolution**: Custom Properties are now included in the API Swagger definition.                                                                                                                                                                                                                                                                                                       |
| RDAPI-21221 | 01185235  01184668  01177860  01192587                                                                                 | **Issue**: LDAP connection test fails when using a node manager configuration in Policy Studio, due to absence of an environmentalisation configuration. **Resolution**:  Updated the connection test to only check for environmentalised fields if environmentalisation is supported. Added a warning dialog when the 'Allow environmentalization of fields' option is tuned on, but currently open configuration does not support it.                                 |
| RDAPI-21234 | 01179001                                                                                                               | **Issue**: API Manager Metrics Date interval behaviour is unclear. **Resolution**: Added additional information about the [Date interval](/docs/apim_administration/apimgr_admin/api_mgmt_monitor/#date-interval) behavior to the documentation.                                                                                                                                                                                                                        |
| RDAPI-21291 | 01170263  01182236                                                                                                     | **Issue**: When a CORS Profile is configured in Policy Studio and applied to nested paths, CORS response headers appear duplicated as the gateway processes CORS for each path in the tree. **Resolution**: When a CORS Profile is configured in Policy Studio and applied to nested paths, API Gateway processes CORS only for the relevant path.                                                                                                                      |
| RDAPI-21300 | 01182381                                                                                                               | **Issue**: Use of "%b" or "%B" in Transaction Access Log format can generate errors that exhaust connection pool. **Resolution**: The use of "%b" or "%B" no longer requires response to be buffered. Enforcement has been made on HTTP transactions to ensure resources are released.                                                                                                                                                                                  |
| RDAPI-21366 | 01180384                                                                                                               | **Issue**: Incorrect "productname" option documented for the update-apimanager script. **Resolution**: Updated [documentation](/docs/apim_installation/apigw_upgrade/upgrade_steps_extcass/#step-5---run-update-apimanager) to refer to the correct "product" option.                                                                                                                                                                                                   |
| RDAPI-21403 | 01184844  01182236  01184347  01186338                                                                                 | **Issue**: When a CORS Profile is configured in Policy Studio, CORS request headers (Origin, Access-Control-Request-Method, etc.) are passed to back-end servers, leading to duplication of the CORS response headers. **Resolution**: When a CORS Profile is configured in Policy Studio, CORS request headers are saved in the message attribute ${http.cors.headers}, then removed from the input headers so they don't trigger CORS processing in back-end servers. |
| RDAPI-21418 | 01183534                                                                                                               | **Issue**: crash occurs when importing PKCS12 (or PFX) file not containing private key. **Resolution**: PKCS12 decryption has been corrected to handle those files.                                                                                                                                                                                                                                                                                                     |
| RDAPI-21425 | 01193196  01184844  01184845  01185638  01184347  01186338                                                             | **Issue**: OPTIONS requests are automatically processed and returned by API Gateway without executing any policy, even if no CORS profile has been configured, preventing the call to other back-end servers. **Resolution**: If no CORS profile has been configured, OPTIONS requests will not trigger the CORS processing in the Gateway, so requests can be forwarded to back-end servers.                                                                           |
| RDAPI-21433 | 01189832  01189522  01189274  01189261  01185424  01184347                                                             | **Issue**: API Manager duplicates HTTP outbound headers, which are defined in the Swagger of an API. **Resolution**: API Manager correctly passes HTTP outbound headers to the back-end system.                                                                                                                                                                                                                                                                         |
| RDAPI-21482 | 01187410                                                                                                               | **Issue**: Empty JSON Body due to JSON Remove Node removing the only node causes Exception. **Resolution**: Successfully parsed JSON body no longer throws Exception when writing content.                                                                                                                                                                                                                                                                              |
| RDAPI-21483 | 01187661  01187906  01188834                                                                                           | **Issue**: Compare Attribute filter fix RDAPI-20017 causes breaking changes for existing customers **Resolution**: RDAPI-20017 is reverted to remove breaking changes and will be analyzed further.                                                                                                                                                                                                                                                                     |
| RDAPI-21505 | 01187335                                                                                                               | **Issue**: In a multi-node setup, API Manager does not notify all instances of remote host configuration changes **Resolution**: Remote host configuration changes are now broadcasted across all instances so that configuration changes can take effect.                                                                                                                                                                                                              |

## Known issues

The following are known issues for this update.

| Internal ID | Description                                                                                                                                                        |
| ----------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| RDAPI-15981 | Scopes fields for API Key remain visible even if Application Scopes are disabled                                                                                   |
| RDAPI-16486 | Changes in the mapper always require a reload in the Execute Data Maps filter and once reloaded then providing values for the required parameters must be repeated |
| RDAPI-16778 | Doc update, recommendations for Cassandra storage                                                                                                                 |
| RDAPI-17282 | Connector for Salesforce APIs in API Manager doesn't work or is impossible to configure                                                                            |
| RDAPI-18198 | CORS preflight fails for WSDL based API Manager APIs, thus Try-It fails                                                                                           |
| RDAPI-18332 | "Try-it" for API-Method is not working                                                                                                                             |
| RDAPI-18523 | Inconsistent application search behaviour relating to application sharing                                                                                          |
| RDAPI-18674 | Insufficient data validation when importing an Application                                                                                                         |
| RDAPI-18986 | projpack is unable to merge projects after projupgrade; likely due to Default APIManager policies                                                                 |
| RDAPI-18990 | Failed to delete undefined" pop-up pops up unexpectedly when attempting to delete application                                                                     |
| RDAPI-19006 | Delete API "not found" after changing Application Org                                                                                                             |
| RDAPI-19132 | Issue with selection of Retirement date when deprecating API                                                                                                       |
| RDAPI-19240 | Users in "pending approval" state are visible in the Sharing tab                                                                                                  |
| RDAPI-19278 | API access removed from app during org migration                                                                                                                   |
| RDAPI-19292 | When an APIM admin user's login name is changed, the user is directed to a blank page                                                                             |
| RDAPI-19293 | API Catalog Try It shows only the first security device of a security profile                                                                                      |
| RDAPI-19333 | Grant API access removed from API Access list in application when FE unpublished                                                                                   |
| RDAPI-19354 | Issue with backend API import regarding Duplicate parameters value                                                                                                 |
| RDAPI-19433 | Line breaks in outbound parameter (type header) value not escaped                                                                                                  |
| RDAPI-19442 | Saving Mode Stuck for the Application Creation in different session                                                                                                |
| RDAPI-19453 | Uploading an invalid image type when creating an Application leads to an internal server error                                                                     |
| RDAPI-19580 | Trial option in the Organization does not work                                                                                                                     |
| RDAPI-19586 | "Retired API appears as "published" in Catalog                                                                                                                     |
| RDAPI-19601 | Sharing section of Application issue when Organization of application changed                                                                                      |
| RDAPI-19788 | Issue with pagination across multiple sections                                                                                                                     |
| RDAPI-19971 | nodemanager pid file is created with permissions that are too restrictive                                                                                          |
| RDAPI-20255 | Application Quota not promoted by apimanger promote                                                                                                                |
| RDAPI-20428 | Adding a routing policy to a Studio-defined API produces an EntityStoreException                                                                                   |
| RDAPI-20480 | swagger enum query parameters not validated                                                                                                                        |
| RDAPI-20550 | renaming existing KPS collection in PS breaks ES store                                                                                                             |
| RDAPI-20594 | When a token is revoked with an incorrect Authorization header, the response is 400 instead of 401                                                                |
| RDAPI-20726 | How to get attribute value for apimgmt.application.id                                                                                                              |
| RDAPI-20740 | Unable to environmentalize format in Transaction Access Log                                                                                                        |
| RDAPI-20793 | Resource Path is duplicated in API Manager for Swagger 1.2                                                                                                         |
| RDAPI-20829 | API deployment : restrict grant only to specified organizations                                                                                                    |
| RDAPI-20919 | Add timeout type/origin in trace messages                                                                                                                          |
| RDAPI-20924 | OAS Defect: APIm doesn't support empty value params                                                                                                                |
| RDAPI-20928 | Parameter types differ in Backend API and In TryIt                                                                                                                 |
| RDAPI-21009 | Issue after updating API Manager settings through rest api if lockUserAccount is missing                                                                           |
| RDAPI-21030 | API Manager ignores dont.expect.100.continue flag if the Outbound Security is HTTP Basic                                                                           |
| RDAPI-21061 | Updated error message, OAS3 file import without servers section url                                                                                               |
| RDAPI-21113 | "URLDecoder: Incomplete trailing escape (%) pattern" only when virtualized API uses Outbound authentication                                                        |
| RDAPI-21171 | Minor UI issue affecting pagination in API keys and Oauth client credentials                                                                                       |
| RDAPI-21211 | APIM doesn't support SOAP 1.1 Binding for MTOM 1.0                                                                                                                 |
| RDAPI-21263 | "Import from topology" prefers non-SSL ports of Studio-created APIs                                                                                                |
| RDAPI-21295 | XSD files are not downloaded when Use client registry is enabled                                                                                                   |
| RDAPI-21332 | Cassandra 2.2.12 Vulnerabilities from latest scan                                                                                                                  |
| RDAPI-21384 | Webservice (WSDL-based) responds with 500 instead of 405 when an invalid HTTP method is used                                                                       |
| RDAPI-21411 | Inconsistent treatment of multiple scopes in Oauth request                                                                                                         |
| RDAPI-21423 | Quota values getting re-initialized just after clicking quota text box                                                                                             |
| RDAPI-21434 | "Expect to Retrieve X rows" setting on database query filter                                                                                                       |
| RDAPI-21436 | long deployment time when lot of APIs are virtualized in API Manager                                                                                               |
| RDAPI-21444 | Policy Studio can't delete or edit scripts                                                                                                                         |
| RDAPI-21456 | Application export doesn't include external credential                                                                                                             |
| RDAPI-21469 | kpsadmin returns inconsistent status codes, making it hard to script                                                                                              |
| RDAPI-21506 | APIM cannot import Swagger with externalDocs (NPE)                                                                                                                 |
| RDAPI-21557 | API gateway sends 100 continue when backend fails to respond in time                                                                                               |
| RDAPI-21619 | API Manager remote hosts not synchronized between instances (issue not fully resolved)                                                                             |
| RDAPI-21679 | Amazon SQS Poller - visibility setting lacks units in Policy Studio                                                                                                |
| RDAPI-21681 | Swagger API blob not updated after updated API import                                                                                                              |
| RDAPI-21689 | [ELI] HTTP 500 when accessing user profile                                                                                                                        |
| RDAPI-21697 | problems with recent change to multipart form-data RDAPI-20461                                                                                                     |
| RDAPI-21738 | Issue in sysupgarde apply - Import failed for table                                                                                                                |
| RDAPI-21784 | Redaction not working when Content-Type is multipart/form-data and form-field is a file                                                                            |
| RDAPI-21801 | Spaces in query-string parameters which are encoded as '%20' on the inbound request, are changed to be encoded with '+' during the transaction                    |

### Policy Studio help documentation

This update includes changes to the Policy Studio documentation, but the version of the documentation (Eclipse Help) shipped with Policy Studio is out of sync with the online documentation and does not include these changes.

We are working to rectify this issue in a future update. In the meantime, you can find the up to date documentation online at [Develop in Policy Studio](/docs/apim_policydev/).

## Update a classic (non-container) deployment

These instructions apply to API Gateway and API Manager classic deployments only. For container deployments, see [Update a container deployment](#update-a-container-deployment).

### Prerequisites

This update has the following prerequisites in addition to the [System requirements](/docs/apim_installation/apigtw_install/system_requirements/).

1. Shut down any Node Manager or API Gateway instances on your existing installation.
2. Back up your existing installation. You can use the `update_apigw.sh` script to take a backup of your entire API Gateway installation directory as detailed in the [API Gateway server install steps](#install-the-api-gateway-server-update), or you can manage your own backups as detailed in [API Gateway backup and disaster recovery](/docs/apim_administration/apigtw_admin/manage_operations/#api-gateway-backup-and-disaster-recovery). Ensure that you back up any customized files. You should merge updated files instead of copying them back directly to avoid any regex matching issues, whether you manage your own backups or not. For example, the following directories might contain customized files:

   ```
   webapps/apiportal/vordel/apiportal
   webapps/emc/vordel/manager/app
   webapps/emc
   system/conf/apiportal/email
   system/conf
   samples/scripts/
   tools/filebeat-VERSION-PLATFORM
   ```
3. If you have an existing Apache Cassandra installation, ensure that you back up your data (Cassandra and `kpsadmin`), and that the `JAVA_HOME` variable is set correctly in `cassandra.in.sh` and `cassandra.in.bat`.
4. Remove the old Filebeat folder `/apigateway/tools/filebeat-5.2.0`. Check any customized files to see if they are compatible with the new version. See [Filebeat](#filebeat-v6-2-2) for more information.
5. On Linux, remove existing capabilities on product binaries (which might prevent overwriting files):

   ```
   setcap -r INSTALL_DIR/apigateway/platform/bin/vshell
   ```

### FIPS mode only

If FIPS mode is enabled, you must also perform the following steps to install the update:

1. Run `togglefips --disable` to turn FIPS mode off.
2. Start the Node Manager to move the JARs.
3. Stop the Node Manager.
4. Install the API Gateway update as described in the [Installation](#installation) section.
5. Start the Node Manager.

In Policy Studio, If FIPS mode is enabled, you must also perform the following steps to install the update:

1. Open Policy Studio and select **Window > Preferences**.
2. Select the **FIPS Mode** setting.
3. Deselect the **Enable FIPS Mode in Axway Policy Studio** option and click **OK**.
4. Restart Policy Studio using the `policystudio -clean` command.

In Configuration Studio, If FIPS mode is enabled, you must also perform the following steps to install the update:

1. Open Configuration Studio and select **Window > Preferences**.
2. Select the **FIPS Mode** setting.
3. Deselect the **Enable FIPS Mode in Axway Configuration Studio** option and click **OK**.
4. Restart Configuration Studio using the `configurationstudio -clean` command.

### Installation

This section describes how to install the update on existing 7.7 installations of API Gateway or API Manager.

* If you have installed an existing version of API Manager, installing the API Gateway server update automatically also installs the updates and fixes for API Manager.
* If you have installed a licensed version of API Gateway or API Manager 7.7, you do not require a new license to install updates.

#### Install the API Gateway server update

To install the update on your existing API Gateway 7.7 server installation, perform the following steps:

1. Ensure that your existing API Gateway instance and Node Manager have been stopped.
2. Remove any previous patches from your `INSTALL_DIR/ext/lib` and `INSTALL_DIR/META-INF` directories (or the `ext/lib` directory in an API Gateway instance). These patches have already been included in this update. You do not need to copy patches from a previous version.
3. Download and unpack the API Gateway 7.7 server Update file into a new directory. For example:

   ```
   mkdir 77update
   tar -xzvf APIGateway_7.7.YYYYMMDD_Core_linux-x86-64_BNnn.tar.gz -C 77update
   ```

    {{< alert title="Note" color="primary" >}}You must extract the file into a new directory and not into the existing API Gateway installation directory.{{< /alert >}}
4. Run the [`update_apigw.sh` script](#update-apigw-sh-script) from the directory into which you extracted the Update file (for example, `77update`) and specify  your API Gateway installation directory using the `--install_dir` option. For example:

   ```
   ./update_apigw.sh --install_dir /opt/Axway-7.7/
   ```
5. Restart your Node Manager and API Gateway instances on the local machine.

##### `update_apigw.sh` script

Run the `update_apigw.sh` script with the `--help` option to see the available options:

```
./update_apigw.sh --help
```

The script generates a trace file in the `update-output/trace` directory. Use the `--tracelevel` option to change the level of tracing.

The script takes a backup of your entire API Gateway installation directory and places it in a `tar` file in the `update-output/backups` directory. Specify a different directory using the `--backup_dir` option. To manage your own backups, use the `--no_backup` option.

To run the script without user interaction, specify `--mode unattended` option.

Running the `update_apigw.sh` script performs the following steps:

1. Check that the installation directory is valid.
2. Check that the user who owns the API Gateway binaries is the same user running the `update_apigw.sh` script.
3. Check that the Node Manager and API Gateway instances that run on the local machine are not running.
4. Take a backup unless `--no_backup` has been specified.
5. Install the update content into the API Gateway installation directory.
6. Perform all of the steps that were performed by a post installation script in earlier updates. This includes JRE cleanup, `system/lib/modules` cleanup, third-party and Axway JAR cleanup, apply `acl.json` fix, apply passphrase obfuscation fix, and apply modifications to Node Manager entity store configuration. Fixes are only applied if they have not previously been applied.

#### Install the Policy Studio update

To install the update on your existing Policy Studio installation, an update script is provided. The update script is located inside the API Gateway 7.7 Policy Studio Update pack (for example, `APIGateway_7.7.YYYYMMDD_PolicyStudio_linux-x86-64_BNnn.tar.gz`).

Download and unpack the API Gateway 7.7 Policy Studio Update file into a new directory. For example:

```
mkdir 77update
tar -xzvf APIGateway_7.7.YYYYMMDD_PolicyStudio_linux-x86-64_BNnn.tar.gz -C 77update
```

{{< alert title="Note" color="primary" >}}
You must extract the file into a new directory and not into the existing API Gateway installation directory.

You must also remove the files `libeay32.dll` and `ssleay32.dll` if they exist in the directory `INSTALL_DIR/policystudio`.

For installations running on Windows 7, you must manually unzip the Policy Studio update.
{{< /alert >}}

Run the `update_policy_studio.sh` script from the directory into which you extracted the Update file (for example, `77update`) and specify your API Gateway installation directory as an argument:

```
./update_policy_studio.sh INSTALL_DIR
```

`INSTALL_DIR` is the base API Gateway 7.7 installation directory that contains the `policystudio` directory.

For example:

```
./update_policy_studio.sh /opt/Axway-7.7/
```

If you had applied any modifications to the `policystudio.ini` file, you must reapply them after upgrade.

{{< alert title="Note" color="primary" >}}You must execute the update script using the same user who installed Policy Studio.

An update script is also available for Windows. It is called `update_policy_studio.bat` and it is located in the API Gateway 7.7 Policy Studio Update pack for Windows (`.zip`).{{< /alert >}}

Running this script performs the following steps:

1. Back up your existing `INSTALL_DIR/policystudio` directory.
2. Remove old JRE versions by deleting the `INSTALL_DIR/policystudio/jre` directory.
3. Unzip and extract API Gateway 7.7 Policy Studio Update over the `policystudio` directory in your existing API Gateway 7.7 installation directory.
4. Start Policy Studio with `policystudio -clean`.

A backup of the installation is created at `INSTALL_DIR/backups/policystudio/<date_time>`.

#### Install the Configuration Studio update

To install the update on your existing Configuration Studio installation, an update script is provided. The update script is located inside the API Gateway 7.7 Configuration Studio Update pack (for example, `APIGateway_7.7.YYYYMMDD_ConfigurationStudio_linux-x86-64_BNnn.tar.gz`).

Download and unpack the API Gateway 7.7 Configuration Studio Update file into a new directory. For example:

```
mkdir 77update
tar -xzvf APIGateway_7.7.YYYYMMDD_ConfigurationStudio_linux-x86-64_BNnn.tar.gz -C 77update
```

{{< alert title="Note" color="primary" >}}
You must extract the file into a new directory and not into the existing API Gateway installation directory.

You must also remove the files `libeay32.dll` and `ssleay32.dll` if they exist in the directory `INSTALL_DIR/configurationstudio`.

For installations running on Windows 7, you must manually unzip the Configuration Studio update.
{{< /alert >}}

Run the `update_configuration_studio.sh` script from the directory into which you extracted the Update file (for example, `77update`) and specify your API Gateway installation directory as an argument:

```
./update_configuration_studio.sh INSTALL_DIR
```

`INSTALL_DIR` is the base API Gateway 7.7 installation directory that contains the `configurationstudio` directory.

For example:

```
./update_configuration_studio.sh /opt/Axway-7.7/
```

If you had applied any modifications to the `configurationstudio.ini` file, you must reapply them after upgrade.

{{< alert title="Note" color="primary" >}}You must execute the update script using the same user who installed Configuration Studio.

An update script is also available for Windows. It is called `update_configuration_studio.bat` and it is located in the API Gateway 7.7 Configuration Studio Update pack for Windows (`.zip`).{{< /alert >}}

Running this script performs the following steps:

1. Back up your existing `INSTALL_DIR/configurationstudio` directory.
2. Remove old JRE versions by deleting the `INSTALL_DIR/configurationstudio/jre` directory.
3. Unzip and extract API Gateway 7.7 Configuration Studio Update over the `configurationstudio` directory in your existing API Gateway 7.7 installation directory.
4. Start Configuration Studio with `configurationstudio -clean`

A backup of the installation is created at `INSTALL_DIR/backups/configurationstudio/<date_time>`.

#### Install the API Gateway Analytics update

To install the update on your existing API Gateway Analytics 7.7 installation, perform the following steps:

1. Ensure that your existing API Gateway Analytics instance and Node Manager have been stopped.
2. Remove old third-party libraries by deleting the following directories:

   ```
   INSTALL_DIR/analytics/system/lib/modules
   ```
3. Remove old JRE versions by deleting the following directories:

   ```
   INSTALL_DIR/analytics/platform/jre
   ```
4. Verify the owners of API Gateway binaries before extracting the update.

   ```
   ls -l INSTALL_DIR/analytics/posix/bin
   ```
5. Using the same user who owns the API Gateway Analytics binaries, unzip and extract API Gateway 7.7 Analytics Update over the `analytics` directory in your existing API Gateway 7.7 installation directory. For example:

   ```
   tar -xzvf APIGateway_7.7.YYYYMMDD_Analytics_linux-x86-64_BNnn.tar.gz -C /opt/Axway-7.7/analytics/
   ```
6. Change to the `analytics` directory in your installation:

   ```
   cd INSTALL_DIR/analytics
   ```
7. Run the post-install script for API Gateway Analytics.

   ```
   apigw_analytics_sp_post_install.sh
   ```

You must also install an update for your existing API Gateway 7.7 server.

### After installation

The following steps apply after installing the update.

#### API Gateway

To allow an unprivileged user to run the API Gateway on a Linux system, perform the following steps:

1. Add the following line to the `INSTALL_DIR/system/conf/jvm.xml` file:

   ```
   <VMArg name="-Djava.library.path=$VDISTDIR/$DISTRIBUTION/jre/lib/amd64/server:$VDISTDIR/$DISTRIBUTION/jre/lib/amd64:$VDISTDIR/$DISTRIBUTION/lib/engines:$VDISTDIR/ext/$DISTRIBUTION/lib:$VDISTDIR/ext/lib:$VDISTDIR/$DISTRIBUTION/jre/lib:system/lib:$VDISTDIR/$DISTRIBUTION/lib"/>
   ```
2. Run the command `setcap 'cap_net_bind_service=+ep cap_sys_rawio=+ep' INSTALL_DIR/platform/bin/vshell` to allow the API Gateway to listen on privileged ports.

#### API Manager

When API Manager is installed, you must run the `update-apimanager` script after the API Gateway post-install script to ensure that all paths are up-to-date. For details, see [Run update-apimanager](/docs/apim_installation/apigw_upgrade/upgrade_steps_extcass/#run-update-apimanager).

{{< alert title="Caution" color="warning" >}} Before executing the `update-apimanager` script:

* Apply the update to all API Gateways.
* Ensure that all Node Managers and API Gateway instances are running.

{{< /alert >}}

## Update a container deployment

If a `fed` file is provided as part of building the API Manager container, you must follow these steps to update the `fed` with the configuration changes:

1. Install the update on a installation of the API Gateway.
2. Run the following command:

   ```
   /opt/Axway-7.7/apigateway/posix/bin/update-apimanager --fed <path to old file>.fed --oa <path to update file>.fed
   ```

You do not need to run any API Manager instances.

The `fed` now contains the updates for the API Manager configuration and can be used to build containers.

## Documentation

This section describes documentation enhancements and related documentation.

### Documentation enhancements

The latest version of API Gateway, API Manager, and API Portal documentation has been migrated to Markdown format and is available in a [public GitHub repository](https://github.com/Axway/axway-open-docs) to prepare for future collaboration using an open source model. As part of this migration, the documentation has been restructured to help users navigate the content and find the information they are looking for more easily.

Documentation change history is now stored in GitHub. To see details of changes on any page, click the link in the **Last modified** section at the bottom of the page.

### Related documentation

To find all available documentation for this product version:

1. Go to [Manuals on the Axway Documentation portal](https://docs.axway.com/bundle).
2. In the left pane Filters list, select your product or product version.

Customers with active support contracts need to log in to access restricted content.

The following reference documents are also available:

* [Supported Platforms](https://docs.axway.com/bundle/Axway_Products_SupportedPlatforms_allOS_en) - Lists the different operating systems, databases, browsers, and thick client platforms supported by each Axway product.
* [Interoperability Matrix](https://docs.axway.com/bundle/Axway_Products_InteroperabilityMatrix_allOS_en) - Provides product version and interoperability information for Axway products.

## Support services

The Axway Global Support team provides worldwide 24 x 7 support for customers with active support agreements.

Email [support@axway.com](mailto:support@axway.com) or visit [Axway Support](https://support.axway.com/).

See [Get help with API Gateway](/docs/apim_administration/apigtw_admin/trblshoot_get_help/) for the information that you should be prepared to provide when you contact Axway Support.
