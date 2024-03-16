---
title: Azure REST API - How to create a Bearer token #Required; page title displayed in search results. Don't enclose in quotation marks. 
description: How to create a Bearer token and use it to authenticate on Azure REST API #Required; article description that's displayed in search results. Don't enclose in quotation marks. Do end with a period.
author: gxgrammatikos #Required; your GitHub user alias, with correct capitalization.
ms.author: cahublou #Required; a Microsoft employee's alias; don't change. 
ms.service: azure #Required; service per approved list.
ms.topic: Azure #Required; leave this attribute/value as-is.
ms.date: 03/16/2024 #Required; mm/dd/yyyy format.
contributor-type: community


# Azure REST API - How to create a Bearer token

---
**Principal author**: [George Chrysovaladis Grammatikos](https://learn.microsoft.com/users/georgechrysovalantisgrammatikos-8518/)

---


In this post, we'll read how to create a Bearer token and use it to authenticate Azure REST API.

## Prerequisites

- An Active Azure Subscription
- [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest) or [Cloud Shell](https://shell.azure.com/)
- [Postman](https://www.getpostman.com/downloads/)

## Create the Bearer token

For the demo purposes, we are going to use Azure CLI to create the Bearer Token.

## Step 1. Az-Login command

Open elevated **CMD**, type ***az login*** and press **Enter**.

```azurecli

Microsoft Windows [Version 10.0.18362.476]
(c) 2019 Microsoft Corporation. All rights reserved.
 
C:\WINDOWS\system32>az login
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code ######### to authenticate.

```
## Step 2. Authenticate to Azure

Open in a web browser the page [https://microsoft.com/devicelogin](https://microsoft.com/devicelogin), enter the code **########** to authenticate the device on azure.

:::image type="content" source="../../../Media/5277.code.png" alt-text="":::

## Step 3. Set the Azure Subscription

After we authenticate the device to connect to Azure we can proceed with the rest commands with the first being used to set the default subscription.

```azurecli
az account set  --subscription "########-####-####-####-############"
```
## Step 4. Create Azure Service Principal

Type the following commands to create the Azure Service Principal,** **

```azurecli
C:\WINDOWS\system32>az ad sp create-for-rbac -n "Azure_Service_Principal_Name"
Retrying role assignment creation: 1/36
Retrying role assignment creation: 2/36
{
  "appId": "########-#####-####-####-############",
  "displayName": "test_aztable_spn",
  "name": "http://test_aztable_spn",
  "password": "########-#####-####-####-############",
  "tenant": "########-#####-####-####-############",
}
```

> [!IMPORTANT]
> Copy the values for the appId, password, and tenant into a text file, because it will be used to next steps..



# Create Azure REST API collection

Following the steps below we'll be able to create a new collection in Postman called Azure REST API.

## Step 1. Manage Environments

Open Postman, and click the button Manage Environments:::image type="content" source="../../../Media/3465.settings.png" alt-text=" ":::

## Step 2. Add New Manage Environment

Select Add, to Add a new Manage Environment

:::image type="content" source="../../../Media/5226.addenvironment.png" alt-text=" ":::

## Step 3. Add the variables, Initial and current values

At the next step, we have to add the variables (**tenantId**, **clientId**, **clientSecret**, **resource**, **subscriptionId**) with the initial and current values.

:::image type="content" source="../../../Media/3323.environments.png" alt-text=" ":::

# Get the MS Entra ID (formerly known as Azure AD) token

To get the Azure Active Directory token we have to do:

- Select the "Azure REST API" manage environment
- Select the POST method
- Type the request [https://login.microsoftonline.com/{{tenantId}}/oauth2/token](https://login.microsoftonline.com/{{tenantId}}/oauth2/token) and click the button Send.

:::image type="content" source="../../../Media/3348.getaad.png" alt-text=" ":::

As we can see below the Bearer Token has been created and we can use it to execute requests using Azure REST API.

:::image type="content" source="../../../Media/2744.result.png" alt-text=" ":::

# Using the Azure REST API

At the final step, we are able to execute a request using Azure REST API to get the Resource Groups.

To get the Azure Active Directory token we have to do:

- Select the GET method
- Type the request [https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups?api-version=2017-05-10](https://management.azure.com/subscriptions/{{subscriptionId}}/resourcegroups?api-version=2017-05-10)
- Select Authorization Type "Bearer Token", and paste the token that we have been created on the previous step

:::image type="content" source="../../../Media/8546.result2.png" alt-text=" ":::

# Conclusion

To sum up, we have read how quickly and easily we can create a Bearer token to use Azure REST API. We need to have in the back of our mind that Azure subscription is a mandatory requirement to do a complete demo.

# See Also

- [Getting Started with Azure API Management REST API](https://azure.microsoft.com/en-us/resources/videos/getting-started-with-azure-api-management-rest-api/)
- [Postman API Client](https://www.getpostman.com/product/api-client)
- [Azure REST API Reference](/rest/api/azure/)

