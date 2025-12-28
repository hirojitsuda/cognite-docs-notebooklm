# PORTAL Documentation
Generated from: docs.cognite.com (portal) - Legacy/Archived content

<!-- SOURCE_START: docs.cognite.com/portal (legacy) -->
## File: docs.cognite.com/portal (legacy content)

---
title: What's new?
pagination_next: null
pagination_prev: null
---

# What's new in Solutions Portal

This article documents the ongoing improvements we're making to Cognite Solutions Portal.

## Version 1.4.0 - 14 Sep, 2022

- Add sub-suite thumbnails.
- Bring suite description to the suite overview page.
- Fix Log out button.

<img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/suite_overview_with_thumbnails.png" alt="Suite overview with thumbnails" width="80%"/>

## Version 1.3.0 - 17 May, 2022

In addition to bug fixes, this release includes many new features and improvements for the **CDF Explorer** and the **Suites** menu.

#### **CDF Explorer**

- We have updated the asset tabs design.
- On the Details tab, we have added a metadata section.
- We have added a sidebar to the Events tab.
- We have improved the charts application URL templating.

- The new **full-screen view** allows you to preview **time series** and **documents** with **annotations**.

  You can open the full-screen view from any Solutions Portal page by adding a URL parameter: `fullscreen` plus a document ID (`docid`) or a time series ID (`tsid`). For example, `https://cogniteapp.com/fusion/?fullscreen&docid=1133581432976453`.

    <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/document_preview.png" alt="Preview document" width="80%"/>

#### Suites menu.

- We've added a menu item on the Suite menu to move a suite.
- To align with the suite tile menu, we have added a three dots menu to the sub-suite tile.

## Version 1.2.0 - 07 March, 2022

- We have redesigned the **home screen**.

  <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/home.png" alt="Solutions Portal Home" width="80%"/>

- Users can select **Browse solutions** to see all available Cognite solutions. Administrators can also install solutions or request a demo from Cognite:

  <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/browse_applications.png" alt=" " width="80%"/>

- **Beta**: A **CDF Explorer** designed specifically for subject matter experts. You can navigate the asset hierarchy or search by asset name to see detailed information about 3D models, related documents, events, time series and more.

  <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/cdf_explorer.png" alt=" " width="80%"/>

- **Beta**: A new **global search** feature to search for CDF resources. Select a search result to open it in the CDF Explorer.

  <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/global_search_bar.png" alt=" " width="40%"/>

## Version 1.1.0 - 12 November, 2021

- Add possibility to [create sub-suites](guides/set_up_suites_and_boards.md#add-sub-suites).
- Add menu option to move a board to another suite. See [Move a board to another suite](guides/set_up_suites_and_boards.md#move-a-board-to-another-suite).
- Load boards with embedded content sequentially with an interval of 3 sec. See [Working with boards](guides/set_up_suites_and_boards.md#working-with-boards).
- Rename “Maintenance Planner” to “Maintain”.

## Version 1.0.0 - 09 August, 2021

- Support for OIDC authentication.
- New grid layout for boards. See [Rearrange boards](guides/set_up_suites_and_boards.md#rearrange-boards).
- Rearrange suites. See [Rearrange suites](./guides/set_up_suites_and_boards.md#rearrange-suites).
- Improved performance of the process of validating suites and boards.
- Various UI fixes and improvements.

## Version 0.7.1 - 31 May, 2021

- Switch from using `RAW database` to `db-service`.
- Add applications as 1st class citizens. System administrators can now toggle Cognite applications to be shown on the home page.
- Update Top Bar appearance.
- Change API calls to be azure auth friendly.
- Various UI fixes and improvements.

## Version 0.6.2 - 19 March, 2021

- Uploading your company/program logo (Solutions Portal administrators only). Select the settings icon in the top right menu. In the pop-up window, upload your logo. The maximum image size is 100 KB.

    <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/customer_logo_clean_tinypng.png" alt=" " width="40%"/>

- Delete uploaded images (Solutions Portal administrators only). Administrators can now delete previously uploaded images on a board. Select the ‘x’ symbol on the name of the uploaded image or select the upload button to upload a new image and replace the existing one.

- New dataset solution for storing images files in CDF.

- Ability to see events in MixPanel (User behavioural tool) tracking users who enter user interface but don’t have any access to suites or boards.

- Various user interface fixes and improvements.

## Version 0.5.0 - 05 March, 2021

- **Share a suite**. Users can now collaborate by sharing links to suites to make other users aware of new information. If the person you share the link with does not have access to the suite, contact your Solutions Portal administrator for help.

  <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/share_suite_2.png" alt=" " width="60%"/>

- **Error notifications**. To help troubleshoot issues, we show more user-friendly error messages with related error codes if there are problems with the back-end services.

- **Mixpanel events tracking**. To help stakeholders see how users are adopting the solution, we have set up Mixpanel data views to track usage behavior for the Solutions Portal. The data views can be made available to stakeholders.

- **Bugs fixed** include incorrect release date and other small user interface improvements.

## Version 0.4.0 - 10 Feb, 2021

This first release of Solutions Portal encompasses several main features described on this documentation site enabling companies to access all their use cases in one place:

- **Useful place to create & discover all organisation’s use cases** e.g., applications, dashboards (Grafana, PowerBI, Plotly dash, Spotfire etc.) and other links.

- **Solutions Portal administration support and governance** to give users access to only what they need access to using CDF groups aligned with Azure Active Directory (AAD) groups.


---
pagination_next: null
pagination_prev: null
---

# About Solutions Portal

The Cognite Solutions Portal collects applications, use cases, and other helpful links to solutions your organization has built on top of Cognite Data Fusion. You no longer have to keep track of many different URLs to access the information you need in your daily work.

To access the Solutions Portal, navigate to [https://cogniteapp.com](https://cogniteapp.com), enter your company name, and sign in with your company ID and credentials.

To see all available Cognite solutions, select **Browse solutions**.

<img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/home.png" alt="Solutions Portal home page" width="100%"/>

:::info NOTE
If you are a Solutions Portal administrator, follow the steps in the [Set up suites and boards](./guides/set_up_suites_and_boards.md) article to **create** and **maintain** suites.
:::


---
pagination_next: null
pagination_prev: null
---

# Set up access and admin users

Follow the steps below to grant users **access** to the Solutions Portal and designate Solutions Portal **admin user(s)**.

:::info NOTE
You need CDF administrator rights to set up access to the Solutions Portal.
:::

## Grant user access and designate Solutions Portal admins

The Solutions Portal uses the Cognite Data Fusion (CDF) access management system to grant access to suites and boards.

### Before you start

Make sure you have [registered the Cognite API and the CDF application in Azure AD](../../cdf/access/guides/configure_cdf_azure_oidc.md) and [set up Azure AD and CDF groups to control access to CDF data](../../cdf/access/guides/create_groups_oidc.md).

### Grant standard user access

All Solutions Portal users must have these capabilities via any of their group memberships:

- `groups:list(current-user)`
- `files:read`

Make sure that users belong to an existing group and have the required capabilities. See [Set up Azure AD and CDF groups to control access](../../cdf/access/guides/create_groups_oidc.md) to create a CDF group and link it to a group in Azure AD.

### Designate Solutions Portal admins

1. If the group doesn't already exist, create a CDF group named `dc-system-admin`.

1. Grant these capabilities to the group:

   - `groups:list(all)`
   - `files:read`, `files:write`
   - `dataset:read`, `dataset:write`

1. Add the users you want to be **Solutions Portal admin users** to the `dc-system-admin` group.

## Create a data set for image files (optional)

Data sets for image files are automatically created when you sign in to the portal as an admin for the first time. 

You can also manually create and change the data set to restrict the `files:write` and `files:read` capabilities to the **dc-system-admin** group or set other configuration options for the data set. Make sure you set the **externalId** for the data set to `dc_img_preview_storage`.

## Upload your organization's logo

1. On the user menu, select **Upload customer logo**.

   <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/user_menu_upload_logo.png" alt=" " width="33%"/>

2. Choose the logo file you want to upload, and then select **Upload customer logo**. The maximum image size is 1 MB.

---
pagination_next: null
pagination_prev: null
---

# Set up suites and boards

Follow the steps below to **create** and **maintain** suites according to the needs and workflows of your organization.

:::info NOTE
To create and maintain suites, you need to be a [Solutions Portal administrator](set_up_access_and_portal_admins.md#designate-solution-portal-admins).
:::

## Working with suites

Suites are shared spaces that contain links to applications, dashboards from analytics and visualization tools, and other helpful links.

### Create a suite

1. On the home page, under **Company Suites** section select **New suite**
2. Give your suite a name, select a color, and add a description. The description helps your colleagues understand what the suite contains.

  <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/create_suite_2.png" alt=" " width="33%"/>

3. Select **Add boards** to [populate your suite](#add-a-board-to-a-suite) with content, or select **Create** to save the suite and add boards later.

### Rearrange suites

On the left-hand sidebar, select and drag a suite to reorder it.

### Add sub-suites

You can create sub-suites to organize your workspace with nested sub-suites. On a suite, select **Add subsuite** in the top right corner.

### Delete a suite

1. On the suite you want to delete, select **More options** (**...**) > **Delete suite**.
2. Confirm that you want to delete the suite.

## Working with boards

Boards are the content of a suite. A board can be a dashboard, an application, or a link with relevant information for your organization. You can choose to see live data from reports, such as [Grafana](#add-a-link-to-a-grafana-report) and [Power BI](#add-a-link-to-a-power-bi-report) dashboards. Users can hover over a board to see more details or select the board to open it in a separate tab.

On a suite page, boards with embedded dashboards (Grafana, Power BI, etc.) load sequentially (one-by-one) to ensure that the dashboards load correctly.

### Add a board to a suite

1. In the Solutions Portal, select the suite you want to add a board to, and then select **Add board**.

1. In the **Add board to suite** dialog box, enter a **title** for your board.

  <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/add_board_suite.png" alt=" " width="40%"/>

2. Choose the **type** of board you want to add.

3. Create a **link** to your board. This URL links your dashboard, application, or other relevant items to your suite.

   <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/link.png" alt=" " width="60%"/>

   If you add a dashboard, you can **add embedded tags** to show live data directly in the suite, for example, from [Grafana](#add-a-link-to-a-grafana-report) and [Power BI](#add-a-link-to-a-power-bi-report).

4. Select **Manage access to board** to decide which groups should have access to the board. Type to search or scroll through the list of groups

   :::info Tip
   Leave the field blank if you want only admins to access the board, for example, to test new suites and boards before making them available to users.
   :::

5. Select **Add board**, and continue to add more boards.
6. Select **Save**.

### Add a link to a Grafana report

1. In **Grafana**, select the dashboard you want to add to a board and select **Share**.

   Grafana displays the **Share panel**:

   <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/embed.png" alt=" " width="66%"/>

2. On the **Link** tab, copy the URL and paste it into the **Add link to board** on the **Board form** in Solutions Portal.

3. On the **Embed** tag in Grafana, turn off **Current time range** if you want live data (recommended).

   <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/copy_html.png" alt=" " width="66%"/>

4. Copy the code and paste it into **Add embedded tag** on the **Board form** in Solutions Portal:

### Add a link to a Power BI report

1. In Power BI, select the dashboard you want to add to a board and select **File** > **Embed report** > **Website or portal**.

    Power BI displays the **Securely embed this report in a website or portal** dialog box:

    <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/secure_embed.png" alt=" " width="66%"/>

2.  Copy the **URL** link and paste it into **Add link to board** on the **Board form** in Solutions Portal.
3.  Copy the **HTML** link and paste it into **Add embedded tag for board**.

   <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/add_link_to_board.png" alt=" " width="66%"/>

4. If you're adding an application or a dashboard that doesn't offer an embedded link, you can upload an image by selecting **Upload image**. The maximum size for the image is 1 MB.

    :::info TIP
    Use [tinypng](https://tinypng.com) to compress your .png or .jpeg file.
    :::

### Edit a board

1. To edit a board, select **More options** (**...**) > **Edit board**.

   <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/more_options_board.png" alt=" " width="66%"/>

1. Select **Update board** and then **Save**.

### Rearrange boards

1. On the suite, select **Edit layout** in the top right corner.
2. Drag a board to the desired position.
3. Use the drag handle at the bottom right of a board to resize a board.
4. Select **Save layout** to save changes.

### Move a board to another suite

1. Select **More options** (**...**) > **Move to...**.
2. Select the target suite.
3. Save the changes.

### Remove a board

1. Select **More options** (**...**) > **Remove board**.
2. Select **Remove board**.

## See which suites and boards a group can access

As a Solutions Portal administrator, you can see which suites and boards different groups have access to:

1. Select the **Users** icon and then the group you want to see access information for.

   <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/access_icon.png" alt=" " width="40%"/>

1. Select **Clear view** to go back to admin mode.

:::info NOTE
You can't edit when you are in group view.
:::

## Change configuration for fetching user groups

The Solutions Portal uses a list of groups to detect which boards a user has access to. By default, it fetches only CDF groups that are linked to external groups from the IdP system (Azure AD, for example).

To fetch all CDF groups (linked and not linked) that a user belongs to:

1. On the user menu, select **Change configuration**.
1. Turn on **Use all user groups**.

  <img class="screenshot" src="https://apps-cdn.cogniteapp.com/@cognite/docs-portal-images/1.0.0/images/cdf/portal/change_configuration.png" alt=" " width="40%"/>

View the list of groups in the [Select group access](#see-which-suites-and-boards-a-group-can-access) menu to see the changes.



<!-- SOURCE_END: docs.cognite.com/portal (legacy) -->
