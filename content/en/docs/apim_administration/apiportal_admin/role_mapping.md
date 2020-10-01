---
title: Configure user groups mapping
linkTitle: Configure user groups mapping
weight: 80
date: 2019-07-30
description: Map API Manager user roles to Joomla! user groups.
---
As an API Portal administrator you can map user roles from API Manager to Joomla! Administrator Interface (JAI) user groups (Roles), and provide these user groups with limited or full access to API Portal features. This allows for API Manager users to create contents, like for example, articles, categories, and menu items in JAI.

* A single API Manager role can be mapped to multiple Joomla! user groups.
* The `Developer` role is mapped to `Registered`, and the `Organization Admin` role is mapped to both `Registered` and `Manager` by default. This means that when an API Manager user is created as a Developer, for example, this user is automatically mapped to `Registered` in JAI.
* Only `Developer` and `Organization Admin` roles can be mapped to JAI.

## Before you start

Joomla! provides a powerful level of access control and user groups management. A Joomla! administrator user can, for example, create user groups and customize access controls. Joomla! also supports mapping between user groups, user actions (for example, create, view, edit, delete), and its components (for example, API Portal).

The mapping feature allows to give only view permission to a user group over the content produced by a component (for example, `com_content, com_menu, or com_apiportal`), and permissions for all user actions on the same components to another user group. In combination, API Portal user groups mapping and Joomla! give almost unlimited options for API Manager users to manage contents, menu items, and the look and feel of the API Portal.

{{< alert title="Caution" color="warning" >}}The combined use of API Portal user groups mapping, the built-in Joomla! Access Control List (ACL), and the user groups feature require advanced knowledge and experience with Joomla!. Incorrect configuration may result in API Portal malfunctioning. {{< /alert >}}

For more details about Joomla! ACL and User Group management, see [Joomla! official documentation](https://docs.joomla.org/Special:MyLanguage/J3.x:Access_Control_List_Tutorial).

## User groups mapping table

This section describes the user groups mapping table, available from **Components > API Portal > User Groups Mapping**.

The table consists of five columns: **Roles**, **Org. Admin**, **Developer**, **Organizations**, **Email Pattern**.

**Roles**
: Displays all the Joomla! user groups.

**Org. Admin**
: Represents the API Manager **Organization Admin** role. Check this box to map API Manager Organization Admin role to the corresponding Joomla! user group.

**Developer**
: Represents the API Manager **User** role. Check this box to map the API Manager User role to the corresponding Joomla! user group.

**Organizations**
: Allows you to map all users from one or more API Manager organizations to a Joomla! user group. You can add, edit, and delete organizations. This field supports `*` and `?` wildcards. For example, `myorg*` will map all users that are part of `myorg`, `myorgs`, `myorganization`, and so on.

**Email Pattern**
: Allows you to map all users with a given text in their email domain to a Joomla! user group. You can add, edit, and delete email domains. This field supports `" "` (blank space) and `?` wildcard. For example, `@mail.com` will map all users whose email domain is `mail`.

![User groups mapping table](/Images/APIPortal/role_mapping_expanded.png)

The **Search** searches for a given term and automatically expands the rows with the matches.

The mapping options are applied individually and cannot be combined. This means that you can apply one mapping setting (Organizations, Email Pattern, and so on) *OR* another, and you cannot, for example, configure a mapping of an organization *AND* a certain email pattern to a user group.  

The mapping takes effect on a user's next login to API Portal.

## Example use cases

This section describe some examples of how to use the user groups mapping feature.

### Map all users of an organization to editors

To make all API Manager users of an organization called **API Development** editors in JAI:

1. On the User Groups Mapping table, expand the **Editor** role to see the Organizations box.
2. Add the name of the organization, either in full or using wildcards. For example: `API Dev*.`
3. Click **Save**.

The next time users from **API Development** organization log in to API Portal, they can use all features available for the **Editor** group in JAI.

### Map all users of an email domain to authors

To make all API Manager users whose email domain is `acmecompany` authors in JAI:

1. On the User Groups Mapping table, expand the **Author** role to see the Email Pattern box.
2. Add the email domain, either in full or using wildcards. For example: `acme*.`
3. Click **Save**.

After the first time that users with an **acmecompany** email domain log in to API Portal, they will be able to see all features available for the **Author** group in JAI.

### Map all Organization Admins to Administrators in JAI

To make all API Manager Organization Admins as administrators in JAI:

1. On the User Groups Mapping table, click the **Org. Admin** checkbox in the **Administrator** role.
2. Click **Save**.

After the first time that users with API Manager Organization Admin role log in to API Portal, they will be able to see all features available for the **Administrator** group in JAI.
