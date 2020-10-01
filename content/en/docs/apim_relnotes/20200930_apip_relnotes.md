---
title: API Portal 7.7 September 2020 Release Notes
linkTitle: API Portal September 2020
weight: 60
date: 2020-08-26T00:00:00.000Z
---
## Summary

API Portal provides an API consumer-facing interface that you can customize to match your corporate brand. API Portal is a layered product linked to API Manager, and requires both API Manager and API Gateway. For more information, see the API Gateway and API Manager documentation.

## Installation

API Portal is available as a software installation or a virtualized deployment in a Docker container. For more information, see the following options:

* If you are installing API Portal for the first time using this update, see [Install API Portal](/docs/apim_installation/apiportal_install/)
* If you are already using API Portal (7.5.x, 7.6.x, 7.7.x) and want to install this update, see [Upgrade API Portal](/docs/apim_installation/apiportal_install/upgrade_automatic/)
* If you want to deploy API Portal in Docker containers, see [Deploy API Portal in containers](/docs/apim_installation/apiportal_docker/)

## New features and enhancements

### Multiple organizations membership

Building on the support now added in API Manager for a user being a member of multiple organizations, the user profile in API Portal will reflect all of their organization memberships, and the user will be able to see all APIs and all applications from all organizations that they are a member of. For more information, see [Users membership to multiple organizations](/docs/apim_relnotes/20200930_apimgr_relnotes/).

### GDPR compliance

Users can request a copy of their API Portal and Joomla! data to be sent to a nominated email address. They can also request that their data is deleted from API Portal and Joomla!, including data recorded in audit logs. For more information, see [Manage privacy and personal data](/docs/apim_administration/apiportal_admin/manage_privacy_personal_data/).

### Security

Users can enable [two-factor authentication](/docs/apim_installation/apiportal_install/secure_harden_portal/#enable-two-factor-authentication) (2FA) for both API Portal website and Joomla! Administrator Interface.

### UI/UX improvements

User interface (UI) and user experience (UX) improvements in this release:

* You can search APIs by **custom properties** in the API catalog.​
* Date and time are displayed in the users time zone.

## Limitations of this update

This update has the following limitations:

* API Portal 7.7.20200930 is compatible with API Gateway and API Manager 7.7.20200930 only.
* Upgrade directly to API Portal 7.7.20200930 is supported from [API Portal 7.7](/docs/apim_relnotes/201904_release/apip_relnotes/) only.
* To upgrade from earlier versions (for example, 7.5.5, 7.6.2) you must first upgrade to API Portal 7.7. For more information see [API Portal single version upgrade](/docs/apim_installation/apiportal_install/upgrade_automatic/#upgrade-from-api-portal-7-6-2) to upgrade versions incrementally.
* You can use the [cumulative upgrade script](/docs/apim_installation/apiportal_install/upgrade_automatic/#upgrade-api-portal-using-the-cumulative-upgrade-script) to upgrade directly from earlier versions (for example, 7.5.5, 7.6.2) to API Portal [7.7 July](/docs/apim_relnotes/20200730_apip_relnotes/), then apply this upgrade package to update your API Portal to the September release.
* The ready-made API Portal Docker image 7.7.20200730 is strictly for development environments only, and it is not recommended for use in production environments.

    It is not recommended to use the image in production environments because the image is built with CentOS as a base OS, and our Axway security scans have detected multiple security concerns with this OS. We continue to monitor the latest versions of this base OS to determine if these issues have been resolved, but until we can ship a hardened image that passes our security concerns, we cannot advise customers to use this image in a production environment. A Docker image for production use is already planned in the [API Portal 2020 roadmap](https://community.axway.com/s/api-portal).
* Upgrading from previous API Portal Docker image is not supported.
* This update is not available as a virtual appliance or as a managed service on Axway Cloud.

## Important changes

It is important, especially when upgrading from an earlier version, to be aware of the following changes in the behavior or operation of the product in this update.

### API version update

To support [User membership in multiple organizations](/docs/apim_relnotes/20200930_apimgr_relnotes/#user-membership-in-multiple-organizations) (multi-orgs), a new version of the API v1.4 was introduced. The multi-orgs feature is available with the 1.4 version of the API only, meaning that any third-party portals or integrations (for example, SSO) must be updated to use version 1.4. The API Manager UI uses the API 1.4 by default.

This feature is forward compatible, the API 1.4 will work with single-org users, but not backward compatible, you cannot configure multi-orgs in the API 1.4, then revert it to the API 1.3.

The new **API Manager 7.7 API 1.4** is available in OAS3 format on the [Axway Documentation portal](https://docs.axway.com/category/api).

## Deprecated features

<!-- Add features that are deprecated here -->

As part of our software development life cycle we constantly review our API Management offering. As part of this review, no capabilities have been deprecated.

## Removed features

<!-- Add features that are removed here -->

To stay current and align our offerings with customer demand and best practices, Axway might discontinue support for some capabilities. As part of this review, no capabilities have been removed.

## Fixed issues

This version of API Portal includes:

* Fixes from all 7.5.5, 7.6.2, and 7.7 service packs released prior to this version. For details of all the service pack fixes included, see the corresponding *SP Readme* attached to each service pack on [Axway Support](https://support.axway.com).
* Fixes from all 7.7 updates released prior to this version. For details of all the update fixes included, see the corresponding [release note](/docs/apim_relnotes/) for each 7.7 update.

### Fixed security vulnerabilities

| Internal ID | Case ID | CVE Identifier | Description                                                                                                                                                                                                                                                                                                                                                                                          |
| ----------- | ------- | -------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| IAP-3188    |         |                | **Issue**: Audit log and its exporting functionality was missing. **Resolution**: API Portal now has rich audit log, which can be exported to a file or email to the requester.                                                                                                                                                                                                                      |
| IAP-3171    |         |                | **Issue**: Documentation was not raising the awareness about the risk of using self-signed certificate in production. **Resolution**: Documentation about HTTPS connection with self-signed certificate was updated. For more info see [Configure API Portal to run with HTTP or HTTPS](/docs/apim_installation/apiportal_install/install_software/#configure-api-portal-to-run-with-http-or-https). |
| IAP-1933    |         |                | **Issue**: API Portal does not officially support 2FA. **Resolution**: Official support of 2FA was added on both admin and site sections.                                                                                                                                                                                                                                                            |
| IAP-3186    |         |                | **Issue**: A privacy communication channel for requesting export or removal of user data was missing. **Resolution**: A privacy communication channel for requesting export or removal of user data is supported in API Portal. For more information see, [Manage privacy and personal data](/docs/apim_administration/apiportal_admin/manage_privacy_personal_data/).                               |

### Other fixed issues

| Internal ID | Case ID | Description                                                                                                                                                                                                                                |
| ----------- | ------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| IAP-3388    | 1173080 | **Issue**: Control buttons for removing array items in Swagger UI were appearing on a new line. **Resolution**: The controls now appear next to the array item.                                                                            |
| IAP-3517    | 1188465 | **Issue**: In API Portal all header names sent along with Try-It requests are Pascal-Case, but when there is no dash, like in KeyId, the transformation results to Keyid. **Resolution**: Headers are Pascal-Case only if there is a dash. |
| IAP-3518    |         | **Issue**: Manual password is not set when org admins create users because of invalid password argument passed. **Resolution**: Correct password argument is passed.                                                                       |

## Known issues

The following are known issues for this update.

### When Multi Manager feature is configured, API Portal users are no longer able to login

After a recent bug fix in API Manager (RDAPI-20021), the `Authenticate to Master` policy is no longer working in releases earlier than [API Portal July 2020](/docs/apim_relnotes/20200730_apip_relnotes/). To fix this, perform the following steps:

1. Open all slave managers configurations in Policy Studio, and click to **Edit** the `AuthenticateToMaster` policy.
2. Click the **Login to Master** (Connect to URL) filter, and enter `Accept: */*` for the **Request Protocol Header**.
3. Click the **Enter** key twice to create two blank lines after `Accept: */*`.

Related Issue:IAP-3435

### Page layout and alignment for Arabic language

Changing the API Portal language to Arabic (or any other right to left language) results in issues with page layout and alignment on the API Portal Home and Pricing pages, and some buttons are not visible. As a workaround, you can turn on the development mode in JAI. Follow these steps:

1. Log in to Joomla! Admin Interface (JAI).
2. In the JAI top navigation bar, click **Extensions > Templates**.
3. Click your template style (for example, `purity_III * Default`) to open it.
4. Click the **General** tab.
5. Change **Development Mode** to `ON`.
6. Click **Save** and click **Close** to close the template style.

Related Issue: IAP-308

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
