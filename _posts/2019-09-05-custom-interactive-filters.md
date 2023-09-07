---
title: 'How to Build Custom Interactive Filters in ServiceNow (Without PA)'
author: Jack Thomas
date: '2019-09-05'
slug: custom-interactive-filters
categories:
  - category:
    - servicenow
---

To build a [custom interactive filter](https://docs.servicenow.com/bundle/madrid-performance-analytics-and-reporting/page/use/dashboards/concept/c_CustomPublishers.html), you need to use a Dynamic Content Block and the [DashboardMessageHandler](https://docs.servicenow.com/bundle/madrid-performance-analytics-and-reporting/page/app-store/dev_portal/API_reference/DashboardMessageHandler/concept/c_DashboardMessageHandler.html) class.

Let's say, for example, that you wanted to build an interactive filter based on Task Type. Your script might look a little something like this:

```html
<?xml version="1.0" encoding="utf-8" ?>
<j:jelly trim="false" xmlns:j="jelly:core" xmlns:g="glide" xmlns:j2="null" xmlns:g2="null">
    <script>
        var dbmh = new DashboardMessageHandler("task_type");
        function publishFilter(input) {
            if (input == "") {
                dbmh.removeFilter();
            } else {
                dbmh.publishFilter("sn_hr_core_case", "sys_class_name=" + input);
            }
        }
        function removeFilter() {
            dbmh.removeFilter();
        }
    </script>

    <div id="${jvar_name}_if_display" class="form-horizontal" style="margin:0 10px 15px;">
        <select class="form-control widget-content" onChange="publishFilter(this.value);">
            <option value="">-- All --</option>
            <option value="sn_hr_core_case">HR Case</option>
            <option value="sn_hr_core_case_extension">HR Case Extension</option>
        </select>
    </div>
</j:jelly>
```

I didn't bother using the ``getAllExtensions()`` function because there are only two task types that I care about here, but if I were going to, I would use [this documentation](https://developer.servicenow.com/app.do#!/api_doc?v=madrid&id=r_TU-getAllExtensions).

```javascript
var table = new TableUtils("sn_hr_core_case");
var extensions = table.getAllExtensions();
```
