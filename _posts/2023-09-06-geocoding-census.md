---
title: 'How to Geocode Locations in ServiceNow with the US Census Bureau API'
author: Jack Thomas
date: '2023-09-06'
slug: geocoding-census
categories:
  - category:
    - servicenow
---

The default geocoding engine in ServiceNow is Google Maps, but I don't have a subscription for that. One alternative is the [Census Geocoder](https://geocoding.geo.census.gov/geocoder/) from the US Census Bureau. This article will provide brief implementation details.

(As a reminder, latitude is x and longitude is y. I always forget which is which.)

There are essentially two components to this build:

1. The outbound REST process (REST Message + HTTP Method)
2. A Business Rule to call it and write the data to the location

## REST Message

Navigate to "System Web Service > Outbound > REST Message". Click New. Update fields as follows:

- Name: ``US Census Bureau``
- Endpoint: ``https://geocoding.geo.census.gov/``

Save.

## HTTP Method

Open the "Default Get" HTTP Method from the related list. Update fields as follows:

- Name: ``Geocode``
- HTTP Method: ``GET``
- Endpoint: ``https://geocoding.geo.census.gov/geocoder/locations/address``

Create the following HTTP Query Parameters in the "HTTP Request" tab:

| Name      | Value            | Order |
| :-------- | :--------------- | ----: |
| benchmark | ``${benchmark}`` |   100 |
| format    | ``json``         |   200 |
| street    | ``${street}``    |   300 |
| city      | ``${city}``      |   400 |
| state     | ``${state}``     |   500 |
| zip       | ``${zip}``       |   600 |

Save.

In the Related Links, click "Auto-generate variables".

In the "Variable Subtitutions" related list, provide test values. You can use whatever address you want, but I'm going to use a random hockey rink.

- benchmark: ``Public_AR_Current``
- city: ``Minneapolis``
- state: ``MN``
- street: ``600 Kenwood Pkwy``
- zip: ``55403``

In the Related Links, click "Test". Validate that the test worked successfully.

## Business Rule

Navigate to "System Definition > Business Rules". Click New. Update fields as follows:

- Name: ``Geocoding with US Census Bureau``
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
	var r = new sn_ws.RESTMessageV2("global.US Census Bureau", "Geocode");
	r.setStringParameterNoEscape("benchmark", "Public_AR_Current");
	r.setStringParameterNoEscape("street", current.street);
	r.setStringParameterNoEscape("city", current.city);
	r.setStringParameterNoEscape("state", current.state);
	r.setStringParameterNoEscape("zip", current.zip);
	var response = r.execute();
	var httpStatus = response.getStatusCode();
	var responseBody = response.getBody();
	if (httpStatus == 200) {
		var responseJSON = JSON.parse(responseBody);
		if ("addressMatches" in responseJSON["result"]) {
			if (responseJSON["result"]["addressMatches"].length > 0) {
				current.setValue("latitude", responseJSON["result"]["addressMatches"][0]["coordinates"]["y"]);
				current.setValue("longitude", responseJSON["result"]["addressMatches"][0]["coordinates"]["x"]);
				current.update();
			}
		}
	}
})(current, previous);
```

Save.
