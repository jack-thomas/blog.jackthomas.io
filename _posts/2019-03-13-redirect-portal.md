---
title: 'How to Redirect an Old Portal to a New Portal in ServiceNow'
author: Jack Thomas
date: '2019-03-13'
slug: redirect-portal
categories:
  - category:
    - servicenow
---

When rolling out a new portal, it's possible that the portal suffix might change. But what about end users who've bookmarked the old portal? What if they happen to read a communication with the old portal link in it?

Rather than confusing those users by presenting a 404 page when the old portal suffix doesn't work, let's just redirect them to the new portal!

For this example, I'm going to be using the following sample URLs:

- Old Portal: ``https://instance.service-now.com/oldportal``
- New Portal: ``https://instance.service-now.com/newportal``

Here, therefore are a few examples of the expected behavior:

- Example 1:
  - Navigates to: ``https://instance.service-now.com/oldportal?anything``
  - Redirected to: ``https://instance.service-now.com/newportal?anything``
- Example 2:
  - Navigates to: ``https://instance.service-now.com/oldportal/anything``
  - Redirected to: ``https://instance.service-now.com/newportal/anything``

There are a few moving parts to this redirect:

1. The redirect portal itself,
2. A portal page to serve as the redirect portal's homepage,
3. A footer for the redirect portal that actually performs the redirection, and
4. A theme for the redirect portal to make sure the footer is loaded.

Note that the term "redirect portal" refers to the old portal.

## Redirect Portal

The first step to building this portal is (unsurprisingly) to start creating the portal. The important part to watch here is that you're using the old URL suffix, since that's what you want to redirect to the new URL suffix.

Based on the sample presented above, I'm going to fill in the following values:

- Title: ``Redirect Portal``
- URL Suffix: ``/oldportal``

It is also important to make sure that you're already using ``/newportal`` for the new portal before creating this one with the old URL. You can't (or at the very least shouldn't!) have two portals with the same URL suffix.

## Portal Page for Redirect Portal

The next part of the process is to create a portal page for the new portal. This might technically be optional, but having a portal doesn't really make sense without a sinle portal page. This is mostly there just in case the footer fails to load without a page present. I'm not sure if this would actually happen, but I didn't want to leave it up to chance.

Use whatever title and ID you think make sense here. For the example presented above, I'm going to use "Redirect Page" and "redirect", respectively.

## Portal Footer for Redirect Portal

This is where the magic happens. I choce to put the code in the footer since it is guaranteed (via the next two steps) to be loaded any time any page is loaded using ``/oldportal``. You can give the footer any name you want (I'll use "Redirect Footer"), but make sure to include the following code in the Client controller script:

```javascript
function() {
  var currentUrl = window.location.href;
  var redirectUrl = currentUrl.replace("/oldportal", "/newportal");
  // alert(currentUrl + "\n" + redirectUrl);
  location.href = redirectUrl;
}
```

For testing purposes, it's nice to have a popup to let you know where you're being redirected from and to. However, I ended up commenting this out before promoting to production.

## Portal Theme for Redirect Portal

To make sure that the footer is loaded on the redirect portal, let's tie it to a theme. The most important part is obviously to select the footer that you just created. As mentioned, this will ensure that the code you placed there is run every time anyone tries to use the old URL.

## Update Redirect Portal

In this step, you'll need to make some updates to the portal that you created in the first step. Set the homepage and 404 page to the portal page you just created, and set the theme to the one that you just created. Again, this is critical to ensure that the old URL is redirected properly.

## Conclusion

To redirect one entire portal to another, we performed the following steps:

1. Create the redirect portal
2. Create a page for the redirect portal
3. Create a footer for the redirect portal to actually perform the redirect
4. Create a theme to make sure the footer is loaded for every page
5. Update the redirect portal to use the other items we created

As an example:

- I navigated to: ``https://instance.service-now.com/oldportal?id=kb_view2``
- I was redirected to: ``https://instance.service-now.com/newportal?id=kb_view2``

Success!
