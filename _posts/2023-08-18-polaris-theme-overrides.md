---
title: "How to Build Instance-Specific Theme Overrides in ServiceNow's Next Experience"
author: Jack Thomas
date: '2023-08-18'
slug: polaris-theme-overrides
categories:
  - category:
    - servicenow
---

Many of my clients want to use themes to visually differentiate their DEV, TEST, and PROD ServiceNow instances. Unfortunately, that's become slightly more challenging with the release of the Next Experience and the accompanying "Polaris" theme. In this article, I'll show you how to build such thematic overrides.

It boils down to creating records in three tables:

1. UX Style [sys_ux_style]
2. UX Theme Style [m2m_theme_style]
3. Clone Data Preserver [clone_data_preserver]

## Create the Style

This specific example turns the banner hot pink, so adjust accordingly.

1. Navigate to "sys_ux_style.list".
2. Click the "New" button.
3. Populate the fields (for example):
    -   Name: ``DEV Override``
    -   Theme: ``{"base": {"--now-color_chrome--brand": "255,105,180"}}``
4. Save.
5. Note the sys_id for later!

## Connect the Style to the Theme

1. Open the UX Style [sys_ux_style] record that you just created.
2. In the "UX Theme Styles" related list, click the "New" button.
3. Populate the fields:
    - Style: ``DEV Override`` (i.e., the style you just created)
    - Theme: ``Polaris``
    - Order: ``100`` (i.e., something greater than zero)
4. Save.
5. Note the sys_id for later!

## Create Clone Preservers

In order to ensure that our records won't get cloned over, we'll need to create two clone preservers in PROD (or whatever your source instance is for clones).

1. Navigate to "System Clone > Clone Definition > Preserve Data".
2. Click the "New" button.
3. Populate the fields:
    - Name: ``DEV Style``
    - Table: ``UX Style [sys_ux_style]``
    - Conditions: ``Sys ID is x`` (i.e., substituting ``x`` for whatever you noted above)
4. Save.
5. Create a second clone preserver for the theme/style connection:
    - Name: ``DEV Theme Style``
    - Table: ``UX Theme Style [m2m_theme_style]``
    - Conditions: ``Sys ID is x`` (i.e., substituting ``x`` for whatever you noted above)
6. Save.
