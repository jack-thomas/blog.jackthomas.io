---
title: 'How to Build a UI Action to Add RITMs to an Existing REQ'
author: Jack Thomas
date: '2023-10-28'
slug: ritm-to-req
categories:
  - category:
    - servicenow
---

One of my clients has a process where the end user submits an empty REQ with a description of what they want, and the fulfiller adds the proper RITMs to get it to them. Think of it as a fairly white glove experience. The requirement, therefore, is to add a UI Action to the Request form on the Workspace that allows the fulfiller to add RITMs to that REQ by selecting them from a catalog, populating the required fields, and submitting.

## Checklist

Here's what you'll need to build to accomplish that requirement:

- UI Action
- Catalog
- Record Producer(s)
	- Variable Set
		- Variable
		- Catalog Client Script

## (1) Clone the existing "Create Request" UI Action from Incident

When you open an Incident in the Service Operations Workspace, there's a UI Action to "Create Request" (to be used, for example, the end user submitted an Incident when they really should have submitted a request - shame on them). That UI Action is available here: ``https://instance.service-now.com/sys_ui_action.do?sys_id=084959b087081300e3010cf888cb0b0e``. (Change the ``instance`` part to open it in one of your instances.)

If you simply clone that UI Action and make some small adjustments, as follows. (Make sure you insert and stay instead of overwriting. Also pay attention to the scope you're putting the new UI Action in.)

| Field | Previous Value | New Value |
| :--- | :--- | :--- |
| Name | Create Request | Add Item |
| Table | Incident \[Incident\] | Request \[sc_request\] |
| Condition | ``...`` | ``current.request_state == "in_process" && gs.hasRole("itil")`` |
| Workspace Client Script | ``...`` | (See Below) |

The Workspace Client Script becomes:

```javascript
function onClick() {
	var result = g_form.submit('sysverb_ws_save');
	if (!result) {
		//failed form submission
		return;
	}
	
	result.then(function() {
		var params ={};
		params.sysparm_parent_table = "sc_request"; // changed from incident to sc_request
		params.sysparm_parent_sys_id = g_form.getUniqueValue();
		g_service_catalog.openCatalogItem('sc_cat_item', '-1', params);
	});
}
```

## (2) Configuring which Catalogs are available

Next, you'll need to configure which Catalogs are available in the Workspace. To do this, follow [these directions](https://docs.servicenow.com/en-US/bundle/vancouver-platform-user-interface/page/administer/workspace/task/assign-service-catalog.html) from the docs:

1. Navigate to **All > Service Portal > Portals**.
2. Select the **Service Workspace Portal**.
3. In the related lists, click the **Catalogs** tab, and use the **Edit** button to add or remove catalogs.

My recommendation is to build a specific Catalog for this purpose since it may have different items than the items that you're showing to your end users on the portal.

## (3) Build Record Producers

### (3.1) Create Record Producer

Now that you have a Catalog added to the Workspace, let's add some items to it. These items are going to be Record Producers rather than Catalog Items because we need them to insert RITM records without creating a new REQ record. To create that new Record Producer:

1. Navigate to **Service Catalog > Catalog Definitions > Record Producers**.
2. Click **New**.
3. Populate the fields as necessary for your use case, but be sure to set "Table name" to "Requested Item \[sc_req_item\]" and set "Catalogs" to include the Catalog that you added to the Workspace in the previous step.
4. Save.

When you save the Record Producer, you might get an error stating that "Record Producer on Request Management tables is not supported." You can ignore this error.

### (3.2) Create Variable Set

In addition, you'll need to add a Variable Set (Single Row, named something like "RITM Parent Parameters") to process the ``sysparm_parent_table`` and ``sysparm_parent_sys_id`` parameters that we provided in the Workspace Client Script in the "Add Item" UI Action. We're building this as a Variable Set so that's reusable across Record Producers.

1. From the related lists of the Record Producer, click the **Variable Sets** tab, and click **New**.
2. Select **Single-Row Variable Set**.
3. Set "Title" to "RITM Parent Parameters" or something like that, and save.

#### (3.2.1) Variable Set Variable

Next, you'll need to add a Variable:

1. From the related lists of the Variable Set, click the **Variables** tab, and click **New**.
2. Set the fields as follows, and Submit.

| Field     | Value                  |
| :-------- | :--------------------- |
| Type      | Reference              |
| Order     | -100                   |
| Active    | true                   |
| Read only | true                   |
| Hidden    | true                   |
| Question  | Request                |
| Name      | request                |
| Reference | Request \[sc_request\] |

Optionally, instead of marking the variable as hidden here, you could build a Catalog UI Policy to hide the variable when it's populated. This is my preference, because it allows you to see the variable when it's incorrectly not populated.

#### (3.2.2) Variable Set Catalog Client Script

Finally, you'll need to acc a Catalog Client Script.

1. From the related lists of the Variable Set, click the **Catalog Client Scripts** tab, and click **New**.
2. Change a few fields:

| Field      | Value             |
| :--------- | :---------------- |
| Name       | Parent Parameters |
| UI Type    | All               |
| Type       | onLoad            |
| Script     | (See Below)       |

The Script for the Catalog Client Script should be something like this (with credit to [this ServiceNow Community post](https://www.servicenow.com/community/developer-forum/passing-parent-url-parameters-from-configurable-workspace-to/m-p/2488049) for information on how to parse URL parameters in a Workspace setting):

```javascript
function onLoad() {
	// Parse parameters
	sysparm_parent_table = getParmVal("sysparm_parent_table");
	sysparm_parent_sys_id = getParmVal("sysparm_parent_sys_id");

	// Update field accordingly
	if (sysparm_parent_table == "sc_request" && sysparm_parent_sys_id)
		g_form.setValue("request", sysparm_parent_sys_id);
	else
		g_form.addErrorMessage("Not created from a parent request.");
}

function getParmVal(name) {
	var url = decodeURIComponent(top.location.href); //Get the URL and decode it
	var workspaceParams = url.split('extra-params/')[1]; //Split off the url on Extra params
	var allParams = workspaceParams.split('/'); //The params are split on slashes '/'
	
	//Search for the parameter requested
	for (var i=0; i< allParams.length; i++) {
		if(allParams[i] == name) {
			return allParams[i+1];
		}
	}
}
```

## (4) Next Steps

At this point, you should have a process that creates RITMs for an exsting REQ. However, it does not:

- Set the "Item" field properly
- Translate "Requested for" from REQ to RITM
- Trigger any Flows to add Catalog Tasks or Approval processes

Adding this functionality is left as an exercise for the reader. (Or maybe I'll work on this more later.)
