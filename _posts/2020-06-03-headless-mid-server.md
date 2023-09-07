---
title: 'How to Install a ServiceNow MID Server on Headless Ubuntu Server 20.04'
author: Jack Thomas
date: '2020-06-03'
slug: headless-mid-server
categories:
  - category:
    - linux
  - category:
    - servicenow
---

Here's what we need to do:

1. Install prerequisites,
2. Download the package (update that path according to the ``MID buildstamp`` on your ``stats.do`` page),
3. Unzip the package,
4. Configure the MID server, and
5. Start the MID server.

Here's how we do it:

```shell
$ sudo -s
# apt install openjdk-8-jre-headless unzip
# mkdir -p /servicenow/prdmid01
# cd /servicenow/prdmid01
# wget https://install.service-now.com/glide/distribution/builds/package/mid/2019/12/17/mid.newyork-06-26-2019__patch4-hotfix1-12-16-2019_12-17-2019_0856.linux.x86-64.zip
# unzip mid.newyork-06-26-2019__patch4-hotfix1-12-16-2019_12-17-2019_0856.linux.x86-64.zip
# nano /servicenow/prdmid01/agent/config.xml
# ./start.sh
```

But it won't restart automatically on a reboot, right? So...

```shell
$ sudo -s
# cd /servicenow/prdmid01/agent/bin/
# ./mid.sh install
# nano /etc/systemd/system/mid.service
    # Comment out the entire PIDFile line
# systemctl daemon-reload
# systemctl start mid.service
# systemctl status mid.service
```
