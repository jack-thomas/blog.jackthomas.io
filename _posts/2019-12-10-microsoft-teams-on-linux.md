---
title: 'How to Install Microsoft Teams on Linux'
author: Jack Thomas
date: '2019-12-10'
slug: microsoft-teams-on-linux
categories:
  - category:
    - linux
---

**UPDATE (2022)**: There are probably better options for this.

Microsoft provides a ``.deb``-based installer [on their website](https://www.microsoft.com/en-us/microsoft-365/microsoft-teams/download-app), but I often prefer to install things directly via the terminal. (Plus, it makes it scriptable!) Here's how to install Microsoft Teams on Linux via the terminal.

First, we're going to add the repo. To do so, add the following line to the end of ``/etc/apt/sources.list`` (``sudo`` usually required):

```shell
deb https://packages.microsoft.com/repos/ms-teams stable main
```

Next, we can update our package list and install:

```shell
sudo apt-get update
sudo apt-get install teams
```

That's it!
