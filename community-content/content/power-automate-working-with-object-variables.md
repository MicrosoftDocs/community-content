---
title: Power Automate - Working with object variables #Required; page title displayed in search results. Don't enclose in quotation marks. 
description: Introduction to Power Automate object variables #Required; article description that's displayed in search results. Don't enclose in quotation marks. Do end with a period.
author: gxgrammatikos #Required; your GitHub user alias, with correct capitalization.
ms.author: mlcc-owners #Required; a Microsoft distribution list; don't change. 
ms.service: azure #Required; request from Microsoft admin. 
ms.topic: conceptual #Required; leave this attribute/value as-is.
ms.date: 10/02/2024 #Required; mm/dd/yyyy format.
contributor-type: community #Required; don't change.

---

# Introduction to Power Automate object variables

**Credits :** This article was originally published on the Microsoft Wiki.

## Introduction

Power Automate provides Variables of different datatypes like Boolean, Integer, Float, String, Object and Array to provide a storage mechanism with the running flow. In this article, we will see how we can use the variable of type object to work with a business use case in Power Automate

---

**Principal author**: [George Chrysovalantis Grammatikos](/users/georgechrysovalantisgrammatikos-8518/)

---

## Business Use Case

When we use the Send an HTTP Request to SharePoint action to create a new site, it creates the site and shares back a result body which does not natively contain the success status. We get the result back as:

:::image type="content" source="media/power-automate-working-with-object-variables/business-use-case.png" alt-text="business_use_case_img":::

As per the official documentation of the Site Creation API, the Site Status can potentially four (4) values depending on the Success or Failure of the action:

- 0 - Not Found. The site doesn't exist.
- 1 - Provisioning. The site is currently being provisioned.
- 2 - Ready. The site has been created.
- 3 - Error. An error occurred while provisioning the site.

In such a situation, Having this table maintained as a Variable Object will help us cross-check the returned Site Status against the variable to pick the status and intimate an end user.

## Implementation

To test this scenario, we will create a manually triggered flow which will accept three (3) text inputs that are required for the site creation. We will accept the Site Title, Site Name and Description from the user while triggering the flow.

:::image type="content" source="media/power-automate-working-with-object-variables/manually-trigger-a-flow.png" alt-text="manually_trigger_a_flow_img":::

We will then declare a Site Status Variable which will contain the Site Status ID and the corresponding Outcome Message as an Object variable.

:::image type="content" source="media/power-automate-working-with-object-variables/init-variable-compose-the-site-status-object.png" alt-text="init_variable_compose_the_site_status_object_img":::

Let's add the action Send an HTTP request to SharePoint to provision the site. We will issue a POST request to the URI - _api/SPSiteManager/create and keep the Site Address as the SharePoint Admin Center URL. In the Body, we will paste the request payload with the below values:

| Title | The Title that should come up in the Site Title bar |
|:------|:----------------------------------------------------|
| URL   | The URL where the site should be provisioned, we are getting the Site Address from the user that should come after /sites |
| Lcid  | Locale identifier . By default 1033 is English(United States) |
| Description | Description for the site obtained from the user |
| Web template | By default we are creating a communication site which has the web template ID : SITEPAGEPUBLISHING#0 |

:::image type="content" source="media/power-automate-working-with-object-variables/send-an-http-request-to-sharepoint.png" alt-text="send_an_http_request_to_sharepoint_img":::

The action will start the site provisioning process and return back the output in the format :

:::image type="content" source="media/power-automate-working-with-object-variables/init-variable-get-site-creation-status.png" alt-text="initialize_variable_get_site_creation_status_img":::

To get the SiteStatus Value from the returned JSON object, we will initialize a string variable- varOutputStatus and extract the value using the expression :

```json
outputs('Send_an_HTTP_request_to_SharePoint')?['body']['d']['Create']['SiteStatus']

```

:::image type="content" source="media/power-automate-working-with-object-variables/json-output.png" alt-text="json_output_img":::

This will fetch the SiteStatus. For instance, if provisioning is complete, the status returned would be two (2). Then, we'll have to fetch the corresponding Status text from the Variable object we have initialized. To fetch this, we'll use the below expression where we'll index into the varStatusCollection with the SiteStatus that we received from the HTTP Action and get back the Site status Text, which we will use in the mail body to intimate the user.

```json
variables('varStatusCollection')[variables('varOutputStatus')]

```

:::image type="content" source="media/power-automate-working-with-object-variables/send-email-v2.png" alt-text="send_email_v2_img":::

Thus the overall flow will look like :

:::image type="content" source="media/power-automate-working-with-object-variables/flow-actions.png" alt-text="flow_actions_img":::

## Test the flow

Let us test the flow by triggering it with manual inputs

:::image type="content" source="media/power-automate-working-with-object-variables/run-flow-values.png" alt-text="run_flow_values_img":::

The Site Creation has been completed now by the HTTP Action and has returned the Site Status as  two (2)

:::image type="content" source="media/power-automate-working-with-object-variables/send-an-http-request-to-sharepoint-2.png" alt-text="send_an_http_request_to_sharepoint_img_2":::

We have also received the mail with the Site Status Text that was retrieved from the Variable Object

:::image type="content" source="media/power-automate-working-with-object-variables/new-site-creation.png" alt-text="new_site_creation_img":::

## Summary

Thus, we saw how to use the Array Object in Power Automate using a real-time use case and how to fetch the Site Status code and compare it with the Status Text stored in the Array object to determine the site creation Success/Failure.
