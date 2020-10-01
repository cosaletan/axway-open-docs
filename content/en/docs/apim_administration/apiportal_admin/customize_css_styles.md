---
title: Customize CSS styles
linkTitle: Customize CSS styles
weight: 30
date: 2019-07-30
description: Customize CSS styles directly or create a custom CSS stylesheet.
---

You can directly edit the files in `local/less/themes/<your copy of axway theme>` to customize your CSS styles. This folder contains all files related to your API Portal theme. Alternatively, you can use a custom CSS file, rather than manually editing the Less files. For details, see [Add a custom stylesheet](#add-a-custom-stylesheet).

## Edit CSS files directly

There are several items you can change within your CSS, and the file you must edit depends on the element and the change in question. For some elements, you may need to edit more than one file for the changes to take effect.

{{< alert title="Caution" color="warning">}}
It is strongly recommended that you make a copy of the theme you want to change before making any edits. For more details on copying a theme, see [Create a new theme](/docs/apim_administration/apiportal_admin/customize_getting_started/#create-a-new-theme).
{{< /alert >}}

In this example, the font in the main menu is changed to uppercase.

### Check the current CSS

When planning changes to CSS files, you can quickly check which file controls the changes to the element you want to change.

1. In API Portal, right-click an element in the main menu, such as **APIs**, and select **Inspect element**.
    ![Inspect element in browser](/Images/APIPortal/cssselectelement.png)
2. Using the developer tools, find the style element you want to change. The CSS file reference for the element shows you the name, class, or ID of that element as well as where it is located.
    ![API Portal customizing the CSS sample modification to an element screen](/Images/APIPortal/csssamplemod.png)

### Manually edit the Less files

{{< alert title="Warning" color="warning">}}It is not recommended to manually edit the `less/themes/axway/variables-custom.less` file. This file contains the attribute values customized in the ThemeMagic editor. For more details, see [Customize with ThemeMagic](/docs/apim_administration/apiportal_admin/customize_getting_started/#customize-with-thememagic).{{< /alert >}}

1. Log in to the Joomla! Admin Interface (JAI), and click **Extensions > Templates**.
2. In the Template sidebar, click **Templates**, and select **Purity III_Details and Files**.
    ![API Portal customizatoin list of available templates through Purity tool](/Images/APIPortal/customation_puritIII_detailsandfiles.png)
3. On the **Editor** tab, open the following folder:

    ```
    local/css/themes/<your theme>
    ```

4. Select the `template.less` file containing the element, and locate the correct line. In this example, the value for `text-transform` is changed from `inherit` to `uppercase`.
    ![An example screenshot on editing a style css.](/Images/APIPortal/cssjoomlasamplecodechange.png)
5. Select **Save > Close File**, and close the editor.
6. In Templates sidebar, select **Styles**.
7. Select **Purity III - Default**, and click **</> LESS to CSS** to compile a new css file.
    ![Joomla Purity III styles template manager to compile changes to API Portal css](/Images/APIPortal/csspuriistylesconfig.png)
8. Refresh the API Portal home page in the browser to see your changes.

## Add a custom stylesheet

If you prefer to use a CSS file to edit the look and feel of your page, add a new custom stylesheet or upload an existing one to the Purity III - Default style.

The custom CSS file is the last file to be loaded in your site. The custom CSS file is not compiled from the Less variables, so it is not overridden or lost if you decide compile another CSS file using the Less variables.

### Create or upload a custom stylesheet

1. Log in to Joomla! Administrator Interface (JAI), and click **Extensions > Templates**.
2. In the Templates sidebar, click **Templates**, and select the style **Purity III_Details and Files**.
3. On the **Editor** tab, select **New file**.
4. Select the `css` folder, set **File Type** to `css`, enter a name for you file (such as `custom.css`), and click **Create**. To upload a CSS file, click **Choose File**, select the CSS file, and click **Upload**.

You can now edit your custom stylesheet in JAI like any CSS file.

### Copy a custom stylesheet

You can also copy a CSS file already included in the Purity III - Default style as your custom stylesheet.

1. In JAI, click **Extensions > Templates**.
2. In the Templates sidebar, click **Templates**, and select the style **Purity III_Details and Files**.
3. On the **Editor** tab, open the `css` folder, select the CSS file to copy, and click **New file**.
4. Select the `css` folder, enter a name for your copy (such as `copied-custom.css`) in **Copied File Name**, and click **Copy File**.

You can now edit the details in your copied custom stylesheet.

### Add Less functionality to a custom stylesheet

If you need the Less functionality in the custom stylesheet as well, create a less file for your custom CSS file. This less file compiles the Less variables to your custom CSS file.

1. In JAI, click **Extensions > Templates**.
2. In the Template sidebar, click **Templates**, and select the style **Purity III_Details and Files**.
3. On the **Editor** tab, select **New file**.
4. Select the `less` folder, set **File Type** to `less`, set **File Name** to `<your css file name>.less`, and click **Create**.

{{< alert title="Note" >}}Styles defined in `custom.less` are independent of the template's stylesheet and do not render with updated variable values in ThemeMagic's preview.{{< /alert >}}
