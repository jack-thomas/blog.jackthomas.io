---
title: "How to Increase Playback Speed on YouTube (beyond what's provided in the player!)"
author: Jack Thomas
date: '2021-07-03'
slug: youtube-speed
categories:
  - category:
    - other
---

The YouTube playback window has a few options for increasing the playback speed, but they top out at 2x.

If you want to go beyond that, you can open up the console and use the following command:

```javascript
document.getElementsByTagName("video")[0].playbackRate = 3
```

To open the console, you can simply hit "F12".
