---
title: Customize API Portal look and feel
linkTitle: Customize look and feel
weight: 20
date: 2019-07-30T00:00:00.000Z
description: Customize the look and feel of API Portal with your own logos,
  colors, and so on.
---
For internally-facing API deployments, you can deploy API Portal "as is" using the out-of-the-box Axway branding. This type of deployment requires no customization.

For external-facing API deployments, you may want to customize API Portal to provide a branded developer portal experience. This type of deployment contains a collection of style settings that can be configured in your account, including logos, colors, fonts or you can perform advanced modification of the layout and structure.

## Supported API Portal customization

Customization can be performed at three levels:

* **Customization through configuration**: Use the Joomla! Admin Interface (JAI) (`https://<API Portal host>/administrator`) to change CSS stylesheets, templates, and layouts. These types of customizations are can be upgraded and retained when you move to new version. The customization does not modify the API Portal source code and is supported by Axway.
* **Customization through the addition of Joomla! plug-ins**: The Joomla! CMS offers thousands of extensions that are available from their website. Axway is only responsible for the support to extensions that are delivered out of the box (EasyBlog and EasyDiscuss).
* **Customization through code**: API Portal is developed using the PHP scripting language and the source code is provided. This is how Joomla! applications are deployed. You can modify the PHP source code to customize API Portal, such as to change the functionality of pages and to extend by adding new pages. This type of customization is only recommended for customers with Joomla! or PHP experience that need to deploy a highly tailored developer portal.   {{< alert title="Caution" color="warning" >}}Customization through code are lost when you upgrade. The source code is subject to frequent changes without notice; therefore, you must reintegrate customizations into the new API Portal code to avoid restoring a deprecated code along with the customizations.{{< /alert >}}

If you submit a case to Axway Support and it is suspected that unsupported third-party extensions may be the root cause of the issue, you must reproduce the issue on a non-customized API Portal.

## Prerequisites

To get started with customization, you need the following:

* API Portal installed and configured. For more details, see the [API Portal Installation and Upgrade Guide](/docs/apim_installation/apiportal_install/).
* An API Portal user account. When you log in, the default API Portal web page is displayed, so you can check how the changes look to your end users.
* Basic understanding of Joomla! ThemeMagic. This feature enables you to change CSS stylesheets, templates, and layouts. For more advanced modifications, you can modify the PHP source code to customize API Portal, such as to change the functionality of pages and to extend by adding new pages.

## Customize with ThemeMagic

API Portal uses the Joomla! framework to extend the ThemeMagic feature. ThemeMagic is part of the Purity III template that is based on the T3 template framework. For more details on how to use and customize Purity III, see the [official Purity III documentation](https://www.joomlart.com/documentation/joomla-templates/purity-iii).

With ThemeMagic, you have an administrative interface for creating or modifying themes, such as colors and fonts. Use the live preview to follow how your theme configuration looks before saving the changes. Themes are built using theming variables based on the [Less](http://lesscss.org/) language.

### Open ThemeMagic

1. Log in to the Joomla! Administrator Interface (JAI), and click **Extensions > Templates**.
2. In Templates sidebar, select **Styles**, then select the style **Purity III - Default**.

    ![Joomla user interface with Purity III selecting the styles](/Images/APIPortal/JoomlaThemeMagicStyles.png)

3. Select **ThemeMagic**. ThemeMagic opens your portal home page with theme variables are displayed on the left.

    ![Joomla User Interface with Purity III theme magic](/Images/APIPortal/joomlathememagic.png)

4. In the ThemeMagic window, sign in to API Portal. You are now ready to start customizing your portal. ![Screenshot on ThemeMagic](/Images/APIPortal/JoomlaThemeMagiconAPIPortal.png)

### Create a new theme

API Portal includes one theme named **Axway**. Create any additional themes from a copy of the Axway theme to ensure that they work properly.

1. Open the ThemeMagic tool, and ensure that the theme selected is the default **Axway** theme.
2. Click the drop-down next to the **Preview** button, and select **Save As**.

    ![API Portal customize color screen](/Images/APIPortal/portal_customize.png)

3. Enter a name for your theme, click **Accept**, and wait until the new theme is ready. A new folder is created for your new theme in `local/less/themes/`.
4. Ensure that the theme selected is your new theme, and change the theming variables on the left as needed to customize your theme.
5. To check how your changes look on the page, click **Preview**.
6. When you are happy with your theme, click the drop-down and select **Save**.

#### Theming variables

Theming variables are grouped into different levels:

* **Key Colors**: These variables control the base colors for all styles. The default key colors are sea blue and gray.
* **Basic Colors**: These variables control the colors of the major UI elements, such as buttons and menus. The default values for the basic colors are based on the key colors, but you can overridden these to control the styles of individual UI elements.
* **Global Fonts** and **Headings**: These variables control the typefaces and sizes of the main text elements

In addition, there are some other variables for fine-grain customization of the UI elements, if needed. Most of these variables are based on Basic Color variables.

{{< alert title="Note" color="primary" >}}Variable names begin with the "@" character in Less language. In addition to variable values, you can also use Less expressions and functions to set the value of a theme variable.{{< /alert >}}

### Use the new theme

1. In JAI, click **Extensions > Templates**.
2. In the Templates sidebar, select **Styles**, then select the style **Purity III - Default**.
3. Select the **Theme** page, and select your new theme from the **Theme** drop-down menu: ![API Portal sample screen on how to save a new theme in templates](/Images/APIPortal/portal_templates.png)
4. Click **Save**, then click **</> Less to CSS**. This is the preferred option as it will only compile the theme you want to use.

### Configuration files

API Portal includes files added to the Purity III template's folders and files that replace those belonging to the Purity III template. All paths are relative to the Purity III template's folder.

* Before updating the Purity III template or T3 framework, back up the Purity III files API Portal replaces. After the update, restore or merge the backed up files.
* Before updating the API Portal plugin, if you have customized the Axway theme or any of the mentioned configuration files, back up the files to avoid losing your customizations. After the update, you may have to merge the backed up files.

#### Added files

The following list details the added files:

* `less/themes/axway/template.less` – Styles for the theme.
* `less/themes/axway/variables.less` – Theme values customized in a file editor.
* `less/themes/axway/variables-custom.less` – Theme values customized in the ThemeMagic. Do not edit this file manually.

#### Replaced Purity III files

The following list summarizes the replaced Purity III files:

* `thememagic.xml` – Configuration for the ThemeMagic GUI.
* `language/en-GB/en-GB.tpl_purity_iii.ini` – Language strings displayed in the ThemeMagic GUI. In order to be utilized by ThemeMagic, this file must be copied to Joomla!'s main language folder, `language/en-GB/en-GB.tpl_purity_iii.ini` when the API Portal plugin is installed.
* `less/variables.less` – Global variables for the Purity III template. Default values for theming variables must be defined in this file.

## Customize your home page layout

**apiportal-homepage** layout is assigned to the home menu item by default. This layout is available from the `apiportal-homepage` template.

The home page consists of the following main sections:

* Menu
* Banner
* Tiles section
* Footer

To customize the layout of your portal:

1. In JAI, click **Extensions > Templates**.
2. In the Templates sidebar, select **Styles**, then select the style **apiportal-homepage**.
3. Customize the layout, and click **Save**. ![Home page layout](/Images/APIPortal/layout.png)

For more details see [T3 Framework Layout](http://www.t3-framework.org/documentation/bs3-layout-system#about-layout) documentation.

### Customize your banner

Change the banner of your portal using the **Home Page Banner** module.

To customize the banner:

1. In JAI, click **Extensions > Modules > Home Page Banner**.
2. Customize the following:

   * **Title** - Free text field for the title of the banner. Defaults to **Enter API Portal**.
   * **Title colour** - Colour picker to choose the color of the title.
   * **Sub-title** - Free text field for the subtitle of the banner. Defaults to **Explore and test our APIs**.
   * **Subtitle colour** - Colour picker to choose the color of the subtitle.
   * **Explore Button** - Show / Hide Explore Button.
   * **Button text** - Free text field for the button text.
   * **Button text colour** - Colour picker to choose the color of the button text.
   * **Button has background** - Yes / No.
   * **Link button to a menu item** - Drop-down list with all menu items. Choose a page to link to when click the button. Defaults to **Sign in** page.
   * **Button border colour** - Colour picker to choose the color of the button border.
   * **Border radius** - Numbers only field for border radius of the button border. Defaults to **500**.
   * **Background image** - Change the background image of the banner. You can choose an image from the media manager or upload a new image.
   * **Text alignment** - Choose one of the three options (left, center, right) to position the text and the button on the banner. The module position defaults to **api-home-banner**.
3. Click **Save**.

### Customize the tiles

Modify the tiles in the home page using the **Home Tiles** module.

![Tiles home page](/Images/APIPortal/tiles-homepage.png)

By default, there are four instances of this module:

* **Home Tiles 1** (Explore&Test)
* **Home Tiles 2** (Create)
* **Home Tiles 3** (Manage & analyze)
* **Home Tiles 4** (Connect with a community of developers)

To customize the tiles:

1. In JAI, click **Extensions > Modules > Home Tiles 1**.
2. Customize the following:

   * **Title** - Free text field for the title. Defaults to **Explore & Test**.
   * **Title Colour** - Colour picker to choose the colour of the title.
   * **Description** - Free text field for the description of the tile (The short text under the title).
   * **Description colour** - Colour picker to choose the colour of the description text.
   * **Background image** - Change the icon of the tile. You can choose an image from the media manager or upload a new image.
   * **Background colour** - Colour picker to choose the background colour of the whole tile. Defaults to **white**.
   * **Tile has link**:

     * **Menu item** - Drop-down list with all menu items. Choose a menu item to link the Tile to (Defaults to the chosen option).
     * **Custom** - Free text field. Enter any valid URL to link the tile to.
     * **No** - No link at all.

The home page layout is designed to have up to six tiles, so positions **api-home-tiles-5** and **api-home-tiles-6** are available for use.

To add a new tile:

1. In JAI, click **Extensions > Modules > New > Home Tiles**.
2. After finishing customizing, click **Save** or **Save & close.**

## Customize your logo

Change the API Portal site logo using the Joomla! Media Manager.

### Upload your image file

1. In Joomla! Administrator Interface (JAI), click **Content > Media**.
2. Select the place where you want to upload the image. For example: **com_apiportal** folder.
3. Upload the image file you want to use, and select **Save**.

### Link the logo to your template styles

To link your logo to template:

1. In JAI, click **Extensions > Templates > Styles**. A list of template styles is displayed.
2. Click on template style.
3. Go to the **Theme** tab, and click **Select** from **Logo Image**.
4. Navigate to the folder where the image was uploaded.
5. Select your logo from the list of the available image files, and click **Insert**.

    Files in **SVG** format are not listed. In that case, you must enter the path to that image in **Image URL**. The path must start with the `images/` segment. For example, `images/path-to-logo/logo-filename.svg`.

6. Click **Save**.

{{< alert title="Note" color="primary" >}}You must repeat this procedure to apply your logo to all your templates.{{< /alert >}}

## Customize standard footer

You can customize API Portal standard footer `Copyright © YYYY Axway` to display the name of your company, and you can also add more information to it.

1. In the JAI, click **Extensions > Modules**.
2. Click **New** to create a new module.
3. From the list of module types click **Custom**.
4. On the right sidebar, select `Footer[footer]` from the **Position list**, and select `Hide` on the **Show title** option.
5. Use the text editor to enter the text.
6. Click **Save**.

## Customize 404 error page

Customize a `404` error page for your API Portal.

### Create a template

Follow these steps create a new template for your new `404` page.

1. Log in to the Joomla! Administrator Interface (JAI), and click **Extensions > Templates**.
2. Select **Templates** in the sidebar, and click the template **Purity_III Details and Files**.
3. Click **Copy Template**.
4. Enter a name for the template and click **Copy Template**.
5. Click **Close**.

### Customize the page

Customize the message for the page.

1. From the list of templates, click your new template.
2. On the **Editor** tab, click **error.php** to edit this file.
3. Customize the line describing the `404` page.
4. Save and close the template.

### Assign the template

Assign your new template as the default `404` page in API Portal.

1. Select **Styles** in the sidebar.
2. On the list of styles, locate the style created for your new template and click **Default**.
3. Click the style to open it, and select the **Assignment** tab.
4. Select **Home** in the **Main Menu**.
5. Save and close the style.
6. Refresh the API Portal home page in the browser. Your customization is available, and is displayed when a user triggers the error page.
