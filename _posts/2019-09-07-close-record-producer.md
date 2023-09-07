---
title: 'How to Close a Record Producer After Submission in ServiceNow'
author: Jack Thomas
date: '2019-09-07'
slug: close-record-producer
categories:
  - category:
    - servicenow
---

Usually when an end user submits the Record Producer, they expect to be redirected to the record that they just created. However, I've run into scenarios where the desired behavior is simply to close the window upon submission. The easiest way to accomplish this is to create a UI Page that simply closes itself and redirect the Record Producer there when the "Submit" button is pressed. To accomplish this, you need to setup two items:

1. UI Page
2. Record Producer

## UI Page

First, let's create a UI Page that simply closes itself. This UI Page only needs to do one thing: close. To accomplish this, place the following code in the Client Script:

```javascript
window.close();
```

That's all there is to it.

## Record Producer

Next, let's create a Record Producer. Include something like this in the Record Producer Script.

This example is specific to Customer Service Contacts, where we need to make sure that the Record Producer will actually work (!) before we allow the redirect to happen. Specifically, we need to make sure that a Contact doesn't already exist for the email address.

```javascript
// Check that the record will insert correctly BEFORE redirecting to closure page
var gr = new GlideRecord("customer_contact");
gr.addQuery("email", producer.email);
gr.query();
if (gr.next()) {
	// Redirect to the contact that they attempted to duplicate.
	gs.addErrorMessage("Please add a contact relationship rather than creating a duplicate contact with this email.");
	producer.redirect = 'customer_contact.do?sys_id=' + gr.sys_id;
} else {
	// Redirect to a UI page that simply closes itself (only if it's a valid insert)
	producer.redirect = "sn_customerservice_ClosePage.do";
}
```

## Conclusion

It took me a long time to figure out how to make this work, so I hope that this was helpful! Essentially, the solution boils down to two things:

- (1) Create a custom UI Page that simply closes itself:

```javascript
window.close();
```

- (2) Redirect the Record Producer to that UI Page upon submission:

```javascript
producer.redirect = "sn_customerservice_ClosePage.do";
```
