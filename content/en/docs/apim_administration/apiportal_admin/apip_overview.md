---
title: API Portal overview
linkTitle: Overview
weight: 10
date: 2019-07-30T00:00:00.000Z
description: Learn the key capabilities and features of API Portal.
---
Axway API Portal is a self-service developer portal layered on both API Manager and API Gateway.

With API Portal, you can enable both internal or external client application (app) developers to browse, consume, build, and test APIs for use in their applications on their own. You can use several channels, such as FAQs, articles, forums or blogs, to provide more information for the developers and to encourage developer engagement. The look and feel of the web-based API Portal is fully customizable to match your brand and image.

![Diagram illustrating the API Management concepts in API Portal](/Images/APIPortal/API_Portal_cncpt_api_mgmt.png)

## Key capabilities in API Portal

API Portal is built on top of [Joomla!](http://www.joomla.org/), an open source CMS platform for developing and deploying websites.

API Portal provides the following capabilities specifically for the organization administrator:

* **User and application administration** — You can execute basic operations, such as approvals and rejections, on both API Portal users and applications from your organization.

API Portal provides the following capabilities for both internal and external app developers:

* **Self-registration and profile management** — App developers can self-register and manage their profiles.
* **Browsing and testing APIs in API Catalog** — API Catalog contains the APIs that have been registered in API Manager and are available for use. App developers can browse these APIs and their associated documentation, and invoke APIs using the built-in test capability. They can also download API definitions (Swagger or WSDL) and client SDKs (iOS, Android, Titanium, or Node.js).
* **Creating and managing applications** — Applications allow your app developers to generate credentials (API Key, OAuth, or external credentials) to consume APIs that are protected by authentication.
* **Monitoring API usage** — App developers can register their application's use of APIs through graphical real-time charts.
* **Pricing** - You can provide specific pricing information relating to APIs, products, plans, and services.
* **Help Center** — Provides a central point for links that you can use to offer additional information, for example, FAQs, documentation, discussion forums, or further contact information.
* **[Blog](http://stackideas.com/easyblog)** and **[Discussion forums](http://stackideas.com/easydiscuss)** - These are Joomla! plugins from a third-party vendor that you can use to share information and interact with the developer community.
* **Documentation** - You can use content management capabilities of Joomla! to provide additional content relating to your APIs, terms and conditions for their use, or best practices to your developer communities. The content can include PDF documents, images, and videos.

## Additional features – API Catalog view

API Portal supports two rendering tools to customize the visualization of the APIs and their methods.

### SwaggerUI 3.x

This is the UI tool used to visualize REST APIs. It supports OpenAPI specifications and it is built over the official Swagger.io. In addition to the commonly known fields of a method, the AMPLIFY SwaggerUI also provides the following:

* **Examples (or, snippets)** - This section displays a line or block of code that you can copy and paste, and run straight away using the relevant tool (curl, Titanium, node.js, web.js).
* **OAuth Authorization code flow** - Allows you to request a token to authorize access to your requests using an OAuth authorization code, as opposed to the `Client credentials` option where you need a secret key to request the token.
* **External OAuth resource servers** - Supports authorization code flow only.

{{% alert title="Note" %}}
Clients in external OAuth servers must be created as public, and their redirect URL must be set to `{apiportal-url}/cb`.
{{% /alert %}}

### SwaggerUI 1.x

This interface shows the commonly known fields of a method, and it supports only API Gateway as an OAuth resource server. This is the UI tool for SOAP APIs because the API Gateway does not convert WSDL to an OpenAPI Specification (OAS) version higher than 1.1.

## API Portal users

This section describes the type of users and their roles in API Portal.

* **Organization administrators** – Full control over their organization in API Manager and the users and applications belonging to it. They have also an additional menu item, **Users**, in the API Portal menu.
* **Application developers** – API Portal users who write and test applications, and consume the exposed APIs.
* **Joomla! administrators** - Full control over API Portal configuration (look and feel, and localization).

This is the default behavior for each type of user. You can change this behavior using the [user groups mapping](/docs/apim_administration/apiportal_admin/role_mapping) functionality of API Portal.
