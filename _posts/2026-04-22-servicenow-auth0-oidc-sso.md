---
title: 'How to Configure Auth0 SSO via OIDC in ServiceNow'
author: Jack Thomas
date: '2026-04-22'
slug: servicenow-auth0-oidc-sso
categories:
  - category:
    - servicenow
---

## (1) Install Plugin

Install the "Integration - Multiple Provider Single Sign-On Installer" \[com.snc.integration.sso.multi.installer\] plugin.

## (2) Disable ACR (Optional)

For my PDI, I tend to disable ACR. This is *not* recommended, but it's fine for non-PROD -- and especially for a PDI.

This is accomplished by setting the ``glide.sso.acr.enabled`` property to ``false``.

## (3) Enable Multi-Provider SSO

Navigate to "Multi-Provider SSO > Administration > Properties". Check the box labeled "Enable multi-provider SSO" and save. (This sets the ``glide.authenticate.multisso.enabled`` property to ``true``.)

Optionally, you can also check the "Enable debug logging for the multiple provider SSO integration" box, which will set the ``glide.authenticate.multisso.debug`` property to ``true``. This is useful for debugging in non-PROD environments.

## (4) Create Application in Auth0

1. In the left-side navigation pane, navigate to "Applications > Applications". (Do not navigate to SSO Integrations, as there is not a pre-built integration with ServiceNow as of today.)
2. Click the button to create a new application.
3. Provide a Name for the application, and select "Regular Web Application" for the application type.
4. Open the "Settings" tab (i.e., instead of the "Quickstart" tab).
5. Configure the following settings:

| Section                | Setting Name          | Setting Value                                      | Comments  |
| :--------------------- | :-------------------- | :------------------------------------------------- | :-------- |
| Application Properties | Application Logo      | ``https://<instance>.service-now.com/favicon.ico`` | Optional. |
| Application URIs       | Application Login URI | ``https://<instance>.service-now.com/navpage.do``  |           |
| Application URIs       | Allowed Callback URLs | ``https://<instance>.service-now.com/navpage.do``  |           |

## (5) Create Identity Provider in ServiceNow

Create a new Identity Provider in ServiceNow. Select "OpenID Connect" as the type. Copy in the Client ID, Client Secret, and Well Known Configuration URL from Auth0. (The well-known configuration URL can be found at the bottom of the application record in Auth0 under "Advanced Settings > Endpoints > OAuth > OpenID Configuration".)

## (6) Test

There are a variety of ways to test this. The easiest is to copy the sys_id of the Identity Provider and build the following URL:

``https://<instance>.service-now.com/login_with_sso.do?glide_sso_id=<identity_provider_sys_id>``
