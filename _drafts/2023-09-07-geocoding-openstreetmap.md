---
title: 'How to Geocode Locations in ServiceNow with OpenStreetMap Nominatim'
author: Jack Thomas
date: '2023-09-07'
slug: geocoding-openstreetmap
categories:
  - category:
    - servicenow
---

## Current Error

You're currently getting a "connection refused" error. It's likely that the whole block of IP addresses that ServiceNow uses is blocked by OpenStreetMap Nominatim. Consider setting up a MID Server and routing the requests through it.

## Introduction

The default geocoding engine in ServiceNow is Google Maps, but I don't have a subscription for that. One alternative is the [OpenStreetMap Nominatim](https://wiki.openstreetmap.org/wiki/Nominatim). This article will provide brief implementation details.

(As a reminder, latitude is x and longitude is y. I always forget which is which.)

There are essentially two components to this build:

1. The outbound REST process (REST Message + HTTP Method)
2. A Business Rule to call it and write the data to the location

## REST Message

Navigate to "System Web Service > Outbound > REST Message". Click New. Update fields as follows:

- Name: ``OpenStreetMap Nominatim``
- Endpoint: ``https://nominatim.openstreetmap.org/``

Save.

## HTTP Method

Open the "Default Get" HTTP Method from the related list. Update fields as follows:

- Name: ``Geocode``
- HTTP Method: ``GET``
- Endpoint: ``https://nominatim.openstreetmap.org/search.php``

Create the following HTTP Query Parameters in the "HTTP Request" tab:

| Name       | Value         | Order |
| :--------- | :------------ | ----: |
| format     | ``jsonv2``    |   100 |
| street     | ``${street}`` |   200 |
| city       | ``${city}``   |   300 |
| state      | ``${state}``  |   400 |
| country    | ``USA``       |   500 |
| postalcode | ``${zip}``    |   600 |

Save.

In the Related Links, click "Auto-generate variables".

In the "Variable Subtitutions" related list, provide test values. You can use whatever address you want, but I'm going to use a random hockey rink.

- city: ``Minneapolis``
- state: ``MN``
- street: ``600 Kenwood Pkwy``
- zip: ``55403``

In the Related Links, click "Test". Validate that the test worked successfully.

## Business Rule

Navigate to "System Definition > Business Rules". Click New. Update fields as follows:

- Name: ``Geocoding with OpenStreetMap Nominatim``
- Table: ``Location [cmn_location]``
- Active: ``true``
- Advanced: ``true``
- When to run
	- When: ``async``
	- Insert: ``true``
	- Update: ``true``
- Advanced:
	- Script: ``(see below)``

Script:

```javascript
(function executeRule(current, previous /*null when async*/) {
	var r = new sn_ws.RESTMessageV2("global.OpenStreetMap Nominatim", "Geocode");
	r.setStringParameterNoEscape("street", current.street);
	r.setStringParameterNoEscape("city", current.city);
	r.setStringParameterNoEscape("state", current.state);
	r.setStringParameterNoEscape("zip", current.zip);
	var response = r.execute();
	var httpStatus = response.getStatusCode();
	var responseBody = response.getBody();
	if (httpStatus == 200) {
		var responseJSON = JSON.parse(responseBody);
		if (responseJSON.length > 0) {
			current.setValue("latitude", responseJSON[0]["lat"]);
			current.setValue("longitude", responseJSON[0]["lon"]);
			current.update();
		}
	}
})(current, previous);
```

Save.
