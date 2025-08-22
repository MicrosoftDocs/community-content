---
title: Secure workflows in Azure Logic Apps with Azure API Management
description: Learn how to secure Standard integration workflows in Azure Logic Apps using API Management and SAS keys or Easy Auth in Microsoft Entra.
ms.service: azure #Required. Don't change this value.
services: azure-logic-apps
author: Andrew-D-Wilson #Required. GitHub alias for article author.
contributor-type: community #Required. Don't change this value.
ms.author: mlcc-owners #Required. Don't change this value.
ms.reviewers: estfan, wsilveira #Required for Microsoft owners and reviewers. Don't change these values.
ms.topic: how-to #Required. Don't change this value.
ms.date: 04/08/2025 #Required in mm/dd/yyyy format.
#CustomerIntent: I want to learn how I can secure access to my workflow in Azure Logic Apps by creating an API in Azure API Management and using either SAS keys or Microsoft Entra with Easy Auth.
---

# Secure Standard workflows in Azure Logic Apps with Azure API Management

---

**Principal author**: [Andrew Wilson](https://github.com/Andrew-D-Wilson)

---

Everything that we build requires security as a fundamental requirement. Through every stage in the software development lifecycle, starting with requirements and design to how our solutions evolve over time, we should keep a "security first" mindset so we can focus and deliberate on security's importance.

To help keep security at the forefront, I often remember the "Three As":

- Access

  Implement mechanisms that control access to applications and services, for example, IP address restrictions.

- Authentication

  How do you identify who can access an application or service? For example, you might require an identity that you can validate, or a key that is shared with a specific audience and is required when requesting access.

- Authorization

  Just because you define access to your service or application and might have a valid identity (or key) doesn't mean you have permission to use the service, application, or resource. To specify the access that users, services, and applications should have, use the Principle of Least Privilege (PoLP).

With these security concerns, this article describes the following methods to secure your integration workflows:

- For scenarios where you can't use a managed identity, you can secure Standard logic app workflows by using Azure API Management with Shared Access Signature (SAS) keys.

- For scenarios where you can use a managed identity, you can secure Standard logic app workflows by using Azure API Management with its built-in authentication and authorization, sometimes called "Easy Auth". This option uses Microsoft Entra with a managed identity, which is recommended for providing the highest security level possible.

The overall design goal behind these methods abstracts the API Management backend API service for the Standard logic app from the API operations for the workflows. For example, the API Management backend API service points at the logic app, and the individual API operations point at respective workflows. This design also specifies granular access to secrets such as SAS keys or client secrets stored in an Azure key vault and secured using role-based access control (RBAC).

Technical examples use Infrastructure as Code (IaC), specifically Bicep, as demonstrations.

## Method 1: Security using SAS keys

When you use an HTTP request-based trigger, such as the **Request** trigger named **When an HTTP request is received**, to start a logic app workflow, the trigger can receive inbound requests trigger from elsewhere to run the workflow. When you save or deploy the workflow for the first time, a callable endpoint and a URL are generated for the trigger. You can then use this endpoint URL to accept an inbound request from elsewhere, which triggers the workflow to start running.

The endpoint URL uses the following format:

**`https://<logic-app-name>.azurewebsites.net:443/api/<workflow-name>/triggers/<trigger-name>/invoke?api-version=2022-05-01&sp=%2Ftriggers%2F<trigger-name>%2Frun&sv=1.0&sig=<shared-access-signature>`**

By default, this endpoint URL includes the following elements:

| Element | Description |
|---------|-------------|
| **api-version** | The service API version for the logic app. |
| SAS query parameters | - **sp**: Specifies the permissions to use for the allowed HTTP methods. <br><br>- **sv**: Specifies the SAS version to use for generating the signature. <br><br>- **sig**: Specifies the signature (key) to use for authenticating access to the trigger. <br><br>**Important**: The signature key is never exposed or published, and is kept secret, encrypted, and stored with the logic app. Protect this key as you would any other account key. |

Looking back to the "Three As", the SAS key in the trigger's endpoint URL plays two parts:

- Authentication where a valid SAS key is provided.

- Authorization where the SAS key is given permissions for the workflow trigger.

Anyone who has the SAS key can call your logic app workflow. So, as with account keys, protect your SAS keys using secure storage and limited access, based on the principle of least privilege. Even with these precautions, the risk of a leak still exists.

To mitigate leaks, here are some best practices:

- Always use HTTPS. If the SAS key travels over HTTP, an attacker can perform a man-in-the-middle attack and read the key.

- Make sure to have a revocation plan for revoking a compromised SAS key.

- Make sure to have a rotation plan that regularly replaces SAS keys with new generated keys.

For access, the third "A", you can use an option such as restricting inbound IP addresses so that only specific applications or services can call the logic app workflow.

### Example solution architecture

The following diagram shows a high-level architecture where a logic app workflow receives calls only through Azure API Management. For security, the design uses SAS authorization and authentication while following the principle of least privilege using RBAC and Azure Key Vault.

In the diagram, the application security boundary specifies that services and applications outside the boundary get stricter scrutiny. For example, the logic app might have access to all secrets in the key vault. However, the API Management instance can access only specific secrets, such as the SAS keys for each workflow, through RBAC.

:::image type="content" source="media/secure-integration-workflows-azure-logic-apps-api-management/sas-auth-api-management.png" alt-text="Architecture diagram shows security using Shared Access Signature (SAS) with API Management for Standard workflows in Azure Logic Apps." lightbox="media/secure-integration-workflows-azure-logic-apps-api-management/sas-auth-api-management.png":::

The following steps describe the call to the logic app workflow through API Management:

1. Caller sends a request to API Management to invoke logic app API operation.

1. API Management receives the inbound request.

1. API Management routes the request to the API operation.

1. Implements **"set-backend-service"** policy (API policy scope) to link and point to the Standard logic app through API Management backend.

1. Implements **"set-query-parameter"** policy (API operation policy scope) to link and point to an individual workflow in Standard logic app. Also, adds the following parameters to the workflow signature:

   | Parameter name | Description |
   |----------------|-------------|
   | **api-version** | The service API version, as plain text in the policy. |
   | **sp** | The permissions, which are generally "read" or "write", as plain text in the policy. |
   | **sv** | The version number of the query parameters, as plain text in the policy. |
   | **sig** | The SAS key, which links (Step 5) to a named value that references this key in the key vault |

1. Gets the SAS key from the application's key vault.

1. Sends the request to Standard logic app workflow.

The API Management setup allows multiple workflows to act as operations for the logic app API. To accomplish this design, an API Management backend service (Step 4) is set up to generically point at the Standard logic app. A mix of operation policies and named values point at specific logic app workflows (Step 5). The named values are key vault references to secrets that contain the workflow's SAS keys.

To restrict access, IP address restrictions are set up to prevent the workflow trigger endpoint from accepting calls from any application or service other than the API Management instance.

The diagrams in the following sections break up the architecture implementation into the following parts:

- Application deployment
- API deployment

### Application deployment

The application deployment phase has the following stages:

1. Deploy the core application components.

   1. Create and deploy a key vault in Azure to store application secrets.

   1. Create and deploy a Standard logic app in Azure.

1. Create and deploy the workflows in the Standard logic app.

1. Create secrets in the key vault to store the workflow SAS keys for later use.

The following diagram shows the deployment for the application components, which consist of a Standard logic app resource, its workflows, and a key vault.

:::image type="content" source="media/secure-integration-workflows-azure-logic-apps-api-management/application-deployment.png" alt-text="Step-by-step diagram shows deployment for a Standard logic app resource and workflows in Azure Logic Apps and a key vault in Azure." lightbox="media/secure-integration-workflows-azure-logic-apps-api-management/application-deployment.png":::

#### Stage 1: Deploy core application components

This section shows the Bicep file that deploys the following Azure resources as application components:

- Key vault (Standard tier)

- Standard logic app, which is associated with the following resources:

  - Standard logic app resource

  - App Service Plan (Workflow Service Plan - WS1 tier)

  - Azure Storage account (Standard v2, locally redundant storage)

```bicep
/**********************************
Bicep template: Deploy application components.
        Author: Andrew Wilson
***********************************/

targetScope = 'resourceGroup'

// ** Parameters **
// ****************

@description('A prefix that identifies the application resources.')
param applicationPrefixName string

@description('The application name to use for tags.')
param applicationName string

@description('The location where to deploy the resources, defaults to the resource group location.')
param location string = resourceGroup().location

@description('The environment where to deploy the resources.')
@allowed([
  'dev'
  'test'
  'prod'
])
param env string = 'dev'

// ** Variables **
// ***************

var applicationKeyVaultName = '${applicationPrefixName}${env}kv'
var lgApplicationAppServicePlanName = '${applicationPrefixName}${env}asp'
var lgStorageAccountName = '${applicationPrefixName}${env}st'
var applicationLogicAppName = '${applicationPrefixName}${env}logic'

var isProduction = env == 'prod'

// ** Resources **
// ***************

@description('Deploy the application-specific key vault.')
resource applicationKeyVaultDeploy 'Microsoft.KeyVault/vaults@2023-07-01' = {
  name: applicationKeyVaultName
  location: location
  tags: {
    Application: applicationName
    Environment: env
    Version: deployment().properties.template.contentVersion
  }
  properties: {
    sku: {
      family: 'A'
      name: 'standard'
    }
    tenantId: tenant().tenantId
    enableRbacAuthorization: true
    enableSoftDelete: isProduction
  }
}

@description('Deploy the App Service Plan for the Standard logic app.')
resource lgAppServicePlanDeploy 'Microsoft.Web/serverfarms@2024-04-01' = {
  name: lgApplicationAppServicePlanName
  location: location
  tags: {
    Application: applicationName
    Environment: env
    Version: deployment().properties.template.contentVersion
  }
  kind: 'elastic'
  sku: {
    name: 'WS1'
    tier: 'WorkflowStandard'
    size: 'WS1'
    family: 'WS'
    capacity: 1
  }
}

@description('Deploy the Azure Storage account for the Standard logic app.')
resource lgStorageAccountDeploy 'Microsoft.Storage/storageAccounts@2023-05-01' = {
  name: lgStorageAccountName
  location: location
  tags: {
    Application: applicationName
    Environment: env
    Version: deployment().properties.template.contentVersion
  }
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'StorageV2'
  properties: {
    supportsHttpsTrafficOnly: true
    minimumTlsVersion: 'TLS1_2'
    defaultToOAuthAuthentication: true
  }
}

@description('Deploy the Standard logic app.')
resource applicationLogicAppStandardDeploy 'Microsoft.Web/sites@2024-04-01' = {
  name: applicationLogicAppName
  location: location
  tags: {
    Application: applicationName
    Environment: env
    Version: deployment().properties.template.contentVersion
  }
  identity: {
    type: 'SystemAssigned'
  }
  kind: 'functionapp,workflowapp'
  properties: {
    serverFarmId: lgAppServicePlanDeploy.id
    publicNetworkAccess: 'Enabled'
    httpsOnly: true
    siteConfig:{
      ipSecurityRestrictions: [
        {
          tag: 'ServiceTag'
          ipAddress: 'AzureCloud.westeurope'
          action: 'Allow'
          priority: 100
          name: 'Allow Service Tag'
        }
        {
          ipAddress: 'Any'
          action: 'Deny'
          priority: 214783647
          name: 'Deny all'
        }
      ]
    }
  }
  resource config 'config@2022-09-01' = {
    name: 'appsettings'
    properties: {
      FUNCTIONS_EXTENSION_VERSION: '~4'
      FUNCTIONS_WORKER_RUNTIME: 'dotnet'
      WEBSITE_NODE_DEFAULT_VERSION: '~18'
      AzureWebJobsStorage: 'DefaultEndpointsProtocol=https;AccountName=${lgStorageAccountDeploy.name};AccountKey=${listKeys(lgStorageAccountDeploy.id, '2019-06-01').keys[0].value};EndpointSuffix=core.windows.net'
      WEBSITE_CONTENTAZUREFILECONNECTIONSTRING: 'DefaultEndpointsProtocol=https;AccountName=${lgStorageAccountDeploy.name};AccountKey=${listKeys(lgStorageAccountDeploy.id, '2019-06-01').keys[0].value};EndpointSuffix=core.windows.net'
      WEBSITE_CONTENTSHARE: lgStorageAccountDeploy.name
      AzureFunctionsJobHost__extensionBundle__id: 'Microsoft.Azure.Functions.ExtensionBundle.Workflows'
      AzureFunctionsJobHost__extensionBundle__version: '${'[1.*,'}${' 1.0.0)'}'
      APP_KIND: 'workflowApp'
      MICROSOFT_PROVIDER_AUTHENTICATION_SECRET: empty(applicationEasyAuthClientSecret)
        ? ''
        : '@Microsoft.KeyVault(VaultName=${applicationKeyVaultDeploy.name};SecretName=${vaultLogicAppRegSecret.name})'
    }
  }
}

// ** Outputs **
// *************

output keyVaultName string = applicationKeyVaultName
output applicationLogicAppName string = applicationLogicAppName
```

The Bicep file for the Standard logic app further describes how you can put controls in place to restrict access from unexpected sources by using IP address restrictions, specifically using the **`ipSecurityRestrictions`** object in the Bicep file. This example includes the service tag **`AzureCloud.westeurope`** in the allowed IP list because this IP address range is where the Bicep file deploys the API Management instance but denies access to all other IP address ranges.

#### Stage 2: Create and deploy logic app workflows

This section deploys workflows to the previously deployed Standard logic app through mechanisms such as Visual Studio Code or automated pipeline steps or actions in Azure DevOps or GitHub. The following example shows a basic Request-Response workflow definition. The workflow definition is set up as **`Stateful`**, which lets you get run history and more data for troubleshooting.

```json
{
   "definition": {
      "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
      "actions": {
         "Response": {
            "type": "Response",
            "kind": "Http",
            "inputs": {
               "statusCode": 200
            },
            "runAfter": {}
         }
      },
      "contentVersion": "1.0.0.0",
      "outputs": {},
      "triggers": {
         "When_a_HTTP_request_is_received": {
            "type": "Request",
            "kind": "Http"
         }
      }
   },
   "kind": "Stateful"
}
```
The following example GitHub action deploys the workflows to the previously deployed Standard logic app:

```yml
- name: Deploy to Azure Logic Apps
  uses: Azure/functions-action@v1
  id: la
  with:
    app-name: ${{ steps.ladeploy.outputs.applicationLogicAppName }}
    package: build-art/${{ github.run_id }}.zip
    publish-profile: ${{steps.laapp.outputs.profile}}
```

#### Stage 3: Create secrets for workflow SAS keys

The following section shows how to get the SAS keys for the previously deployed workflows, as derived from the Bicep parameters, and creates secrets for those keys in the key vault. To simplify, document, and validate parameter inputs, the example Bicep creates and employs user-defined types.

To get individual workflow SAS keys, this example uses the Bicep function named **`listCallbackUrl()`**. This function accepts the workflow trigger's **`resourceId`** and **`api-version`** values as inputs. The function then returns an object that contains the trigger property values, including the SAS key, as outputs.

```bicep
/*****************************************
Bicep template: Deploy application secrets
        Author: Andrew Wilson
*****************************************/

targetScope = 'resourceGroup'

// ** User-defined types **
// ************************

@description('The object type that identifies a workflow and trigger.')
@metadata({
  workflowName: 'The name for the workflow in your Standard logic app.'
  workflowTrigger: 'The name for the Request trigger in the workflow.'
})
@sealed()
type workflow = {
  workflowName: string
  workflowTrigger: string
}

@description('An array of Standard logic app workflows.')
@minLength(1)
type workflowArray = workflow[]

// ** Parameters **
// ****************

@description('The name for the logic app with workflow SAS keys to store in the key vault.')
param applicationLogicAppName string

@description('The name for the key vault where to store secrets.')
param keyVaultName string

@description('The array of workflows from where to get the SAS keys.')
param workflows workflowArray

// ** Variables **
// ***************

// ** Resources **
// ***************

@description('Get the existing logic app.')
resource logicApp 'Microsoft.Web/sites@2022-09-01' existing = {
  name: applicationLogicAppName
}

@description('Get the existing key vault.')
resource keyVault 'Microsoft.KeyVault/vaults@2023-07-01' existing = {
  name: keyVaultName
}

@description('Store the logic app workflow SAS key as a secret. The deployment principal that deploys this Bicep requires RBAC permissions for this task.')
resource vaultLogicAppKey 'Microsoft.KeyVault/vaults/secrets@2023-07-01' = [for workflow in workflows: {
  name: '${logicApp.name}-${workflow.workflowName}-sig'
  parent: keyVault
  tags: {
    ResourceType: 'LogicAppStandard'
    ResourceName: logicApp.name
  }
  properties: {
    contentType: 'string'
    value: listCallbackUrl(resourceId('Microsoft.Web/sites/hostruntime/webhooks/api/workflows/triggers', logicApp.name, 'runtime', 'workflow', 'management', workflow.workflowName, workflow.workflowTrigger), '2022-09-01').queries.sig
  }
}]

// ** Outputs **
// *************
```

### API deployment

The API deployment phase has the following stages:

1. Create and deploy the API Management instance.

1. Create and deploy the respective API Management components.

The following diagram shows the deployed API Management instance, which includes the API and backend API service for the Standard logic app:

:::image type="content" source="media/secure-integration-workflows-azure-logic-apps-api-management/api-deployment.png" alt-text="Step-by-step diagram shows deployment for API Management instance and logic app API with API operation." lightbox="media/secure-integration-workflows-azure-logic-apps-api-management/api-deployment.png":::

The API backend service and API operations that respectively point at the Standard logic app and workflows require the following components and steps to create those components:

| Step | Component | Description |
|------|-----------|-------------|
| 1. Create API Management instance that uses a system-assigned managed identity. | API Management instance | Abstracts interactions for the Standard logic app and workflows. |
| 2. Create API and API operations in API Management instance. <br><br>a. Create API for Standard logic app. <br><br>b. Create an API operation for each workflow. | - API for Standard logic app <br><br>- API operation for each logic app workflow | Represents an API with a set of operations where each references a backend API service that implements the API. |
| 3. Create a role assignment for API Management instance to get reader access for key vault with SAS secrets. | Azure role assignment | The authorization mechanism for the API Management instance to use a system-assigned managed identity for accessing the key vault with the SAS secrets. |
| 4. Create a named value for each workflow SAS key in the key vault. | Named values in API Management instance for workflow SAS keys | A global collection of name-value pairs in the API Management instance. With API Management policies, you can store the named values as constant string values, secrets, or more importantly, key vault references to secrets. <br><br>The examples in the stage for storing these values use the key vault references that point to the SAS secrets in the key vault. |
| 5. Create workflow-agnostic backend API service for Standard logic app. | Backend API service in API Management instance for Standard logic app | An HTTP service that implements a front-end API. Usually, the backend API service is automatically created when you import a Consumption logic app into API Management. <br><br>Although Standard logic apps currently don't support this behavior, they have the same foundation as Azure Functions. This capability means you can set up the same behavior using a custom backend that is treated as a Functions backend. That way, you abstract the backend service information, promote reusability, and improve governance. |
| 6. Create the API Management policies. <br><br>a. API policy scope: Implement the **"set-backend-service"** policy using the backend API service link to Standard logic app. <br><br>b. API operation policy scope: Implement the **"set-query-parameter"** policy to add the following workflow parameters: **api-version**, **sp**, **sv**, and **sig**, which links to the named value stored in the key vault. | API Management policies | Statements that sequentially run with a given request or response for an API. These statements extend the capability to configure the API and its abilities. For example, you can add more parameters, set a backend, and use configured named values. <br><br>In this step, the key goal is to abstract the logic app implementation by using an API Management instance. That way, callers can use consolidated operations in a single API that securely calls the backend API service, which links and points to the Standard logic app. |

#### Stage 1: Create and deploy the API Management instance

The following Bicep creates and deploys an API Management instance that uses the Consumption plan:

```bicep
/**********************************
Bicep template: Deploy API Management instance
        Author: Andrew Wilson
***********************************/

targetScope = 'resourceGroup'

// ** Parameters **
// ****************

@description('A prefix that identifies the API resources.')
param apiPrefixName string

@description('The location where to deploy the resources, defaults to the resource group location.')
param location string = resourceGroup().location

@description('The environment where to deploy the resources.')
@allowed([
  'dev'
  'test'
  'prod'
])
param env string = 'dev'

@description('The publisher email for the API Management instance.')
param apimPublisherEmail string

@description('The publisher name for the API Management instance.')
param apimPublisherName string

// ** Variables **
// ***************

var apimInstanceName = '${apiPrefixName}${env}apim'

// ** Resources **
// ***************

@description('Deploy the API Management instance.')
resource apimInstanceDeploy 'Microsoft.ApiManagement/service@2022-08-01' = {
  name: apimInstanceName
  location: location
  tags: {
    Environment: env
    Version: deployment().properties.template.contentVersion
  }
  sku: {
    capacity: 0
    name: 'Consumption'
  }
  properties: {
    publisherEmail: apimPublisherEmail
    publisherName: apimPublisherName
  }
  identity: {
    type: 'SystemAssigned'
  }
}

// ** Outputs **
// *************

output apimInstanceName string = apimInstanceName
```

#### Stage 2: Deploy the respective API Management components

This section shows how Bicep uses module deployments and policies, which are loaded as text into variables. The first part shows the main orchestration template with the following steps:

1. Get the previously created API Management instance.

   This instance is used as a parent reference during deployment for the logic app API and polices.

1. In your API Management instance, create an API for your Standard logic app using the API Management instance as the parent reference.

1. Create a backend API service that links and points to your Standard logic app.

1. Create an API operation for each workflow by using the **`apimOperation`** deployment module and workflow information as parameter values.

1. Get the key vault instance for your logic app. For each workflow specified in the parameters, get the reference to the corresponding SAS secret in the key vault.

1. For each reference to an SAS secret, grant **Reader** access (RBAC) to the key vault for your API Management instance.
 
   This access level grants permissions to your API Management instance only for the required secrets, not all the secrets.

1. For each referenced SAS secret, create a named value to use as the key vault reference for each logic app API operation.

1. Create the backend API service for your logic app.

1. Deploy the **"set-backend-service"** API policy in API Management that tells your logic app API to use the backend API service that points to your logic app.

1. Deploy the **"set-query-parameter"** API operation policy for each operation using the **`apimOperationPolicy`** deployment module.

   Each policy points an operation to a specific logic app workflow and applies the corresponding SAS key for authentication and authorization.

The following example Bicep orchestration template uses the same **`listCallbackUrl()`** function for a logic app workflow to get query parameters, specifically **`api-version`**, **`sp`**, **`sv`**, and **`sig`** in this scenario. To simplify, document, and validate parameter inputs, the Bicep in the following example orchestration template creates and employs user-defined types.

```bicep
/******************************************
Bicep template: Orchestration template - API Management API for Standard logic app
        Author: Andrew Wilson
*******************************************/

targetScope = 'resourceGroup'

// ** User-defined types **
// ************************

@description('The properties to set up an API Management API operation for a Standard logic app workflow.')
@metadata({
  name: 'The name for the API operation.'
  displayName: 'The user-friendly name for the API operation.'
  method: 'The HTTP method to use for the API operation.'
  path: 'The API Management API operation path that is replaced with the backend implementation through policy. Includes relative paths and the matching logic app.'
  lgWorkflowName: 'The name for the Standard logic app workflow to use for the operation backend.'
  lgWorkflowTrigger: 'The name for the request-based trigger that starts the workflow.'
})
@sealed()
type apimAPIOperation = {
  name: string
  displayName: string
  method: 'GET' | 'PUT' | 'POST' | 'PATCH' | 'DELETE'
  path: string
  lgWorkflowName: string
  lgWorkflowTrigger: string
}

@description('One or more API Management API operations to configure.')
@minLength(1)
type apimAPIOperationArray = apimAPIOperation[]

// ** Parameters **
// ****************

@description('The name for the logic app to add as a backend.')
param logicAppName string

@description('The name for the API Management instance.')
param apimInstanceName string

@description('The name for the key vault instance.')
param keyVaultName string

@description('The name for the API to create in API Management.')
param apiName string

@description('The path to the API in API Management.')
param apimAPIPath string

@description('The display name for the API in API Management.')
param apimAPIDisplayName string

@description('An array of API operations.')
param apimAPIOperations apimAPIOperationArray

// ** Variables **
// ***************

// Logic app base URL
var lgBaseUrl = 'https://${logicApp.properties.defaultHostName}/api'

// Key vault Read access
var keyVaultSecretsUserRoleDefinitionId = '4633458b-17de-408a-b874-0445c86b69e6'

// API all operations policy
var apimAPIPolicyRaw = loadTextContent('./APIM-Policies/APIMAllOperationsPolicy.xml')
var apimAPIPolicy = replace(apimAPIPolicyRaw, '__apiName__', apiName)

// API operation policy template
var apimOperationPolicyRaw = loadTextContent('./APIM-Policies/APIMOperationPolicy.xml')

// ** Resources **
// ***************

@description('Get existing API Management instance to later add APIs and policies to this resource.')
resource apimInstance 'Microsoft.ApiManagement/service@2022-08-01' existing = {
  name: apimInstanceName
}

@description('Create logic app API in API Management.')
resource logicAppAPI 'Microsoft.ApiManagement/service/apis@2022-08-01' = {
  name: apiName
  parent: apimInstance
  properties: {
    displayName: apimAPIDisplayName
    subscriptionRequired: true
    path: apimAPIPath
    protocols: [
      'https'
    ]
  }
}

@description('Get existing logic app for linking as a backend.')
resource logicApp 'Microsoft.Web/sites@2022-09-01' existing = {
  name: logicAppName
}

@description('Deploy logic app API operations.')
module logicAppAPIOperation 'Modules/apimOperation.azuredeploy.bicep' = [for operation in apimAPIOperations: {
  name: '${operation.name}-deploy'
  params: {
    parentName: '${apimInstance.name}/${logicAppAPI.name}'
    lgCallBackObject: listCallbackUrl(resourceId('Microsoft.Web/sites/hostruntime/webhooks/api/workflows/triggers', logicAppName, 'runtime', 'workflow', 'management', operation.lgWorkflowName, operation.lgWorkflowTrigger), '2022-09-01')
    operationDisplayName: operation.displayName
    operationMethod: operation.method
    operationPath: operation.path
    operationName: operation.name
  }
}]

@description('Get existing key vault instance.')
resource keyVault 'Microsoft.KeyVault/vaults@2023-07-01' existing = {
  name: keyVaultName
}

@description('Get SAS secret for existing logic app.')
resource vaultLogicAppKey 'Microsoft.KeyVault/vaults/secrets@2023-07-01' existing = [for operation in apimAPIOperations: {
  name: '${logicAppName}-${operation.lgWorkflowName}-sig'
  parent: keyVault
}]

@description('Grant Reader access for API Management to access key vault for logic app API secrets.')
resource grantAPIMPermissionsToSecret 'Microsoft.Authorization/roleAssignments@2022-04-01' = [for (operation, index) in apimAPIOperations: {
  name: guid(keyVaultSecretsUserRoleDefinitionId, keyVault.id, operation.lgWorkflowName)
  scope: vaultLogicAppKey[index]
  properties: {
    roleDefinitionId: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', keyVaultSecretsUserRoleDefinitionId)
    principalId: apimInstance.identity.principalId
    principalType: 'ServicePrincipal'
  }
}]

@description('Create named values for the logic app API secrets.')
resource logicAppBackendNamedValues 'Microsoft.ApiManagement/service/namedValues@2022-08-01' = [for (operation, index) in apimAPIOperations: {
  name: '${apiName}-${operation.name}-sig'
  parent: apimInstance
  properties: {
    displayName: '${apiName}-${operation.name}-sig'
    tags: [
      'sig'
      'logicApp'
      '${apiName}'
      '${operation.name}'
    ]
    secret: true
    keyVault: {
      identityClientId: null
      secretIdentifier: '${keyVault.properties.vaultUri}secrets/${vaultLogicAppKey[index].name}'
    }
  }
  dependsOn: [
    grantAPIMPermissionsToSecret
  ]
}]

@description('Create backend for the logic app API.')
resource logicAppBackend 'Microsoft.ApiManagement/service/backends@2022-08-01' = {
  name: apiName
  parent: apimInstance
  properties: {
    protocol: 'http'
    url: lgBaseUrl
    resourceId: uri(environment().resourceManager, logicApp.id)
    tls: {
      validateCertificateChain: true
      validateCertificateName: true
    }
  }
}

@description('Create policy for logic App API and all its operations for linking to the logic app backend.')
resource logicAppAPIAllOperationsPolicy 'Microsoft.ApiManagement/service/apis/policies@2022-08-01' = {
  name: 'policy'
  parent: logicAppAPI
  properties: {
    value: apimAPIPolicy
    format: 'xml'
  }
  dependsOn: [
    logicAppBackend
  ]
}

@description('Add query strings through the policy.')
module operationPolicy './Modules/apimOperationPolicy.azuredeploy.bicep' = [for (operation, index) in apimAPIOperations: {
  name: 'operationPolicy-${operation.name}'
  params: {
    parentStructureForName: '${apimInstance.name}/${logicAppAPI.name}/${operation.name}'
    rawPolicy: apimOperationPolicyRaw
    lgCallBackObject: listCallbackUrl(resourceId('Microsoft.Web/sites/hostruntime/webhooks/api/workflows/triggers', logicAppName, 'runtime', 'workflow', 'management', operation.lgWorkflowName, operation.lgWorkflowTrigger), '2022-09-01')
    sig: '{{${apiName}-${operation.name}-sig}}'
  }
  dependsOn: [
    logicAppAPIOperation
  ]
}]

// ** Outputs **
// *************
```

The following example API policy template configures the logic app API to use the backend API service. The statement **`set-header name=<subscription-key> exists-action="delete"`** removes subscription key headers from the request that's forwarded to the backend. That way, these headers don't unintentionally pass through to the backend services.

```xml
<!-- API ALL OPERATIONS SCOPE -->
<policies>
    <inbound>
        <base />
        <set-backend-service id="logicapp-backend-policy" backend-id="__apiName__" />
        <set-header name="Ocp-Apim-Subscription-Key" exists-action="delete" />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

The following example API operation policy template appends the query parameters required to call the endpoint URL for the logic app workflow and updates the URI for the workflow endpoint URL.

These values are tokens that are replaced in the API operation policy Bicep module. Setting these parameters as policy means that those that call the API Management API don't need to know about the backend configuration, which promotes the complete separation of concerns.

```xml
<!-- API operation policy (API operation scope) -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="__uri__" />
        <set-query-parameter name="api-version" exists-action="append">
            <value>__api-version__</value>
        </set-query-parameter>
        <set-query-parameter name="sp" exists-action="append">
            <value>__sp__</value>
        </set-query-parameter>
        <set-query-parameter name="sv" exists-action="append">
            <value>__sv__</value>
        </set-query-parameter>
        <set-query-parameter name="sig" exists-action="append">
            <value>__sig__</value>
        </set-query-parameter>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

The next example Bicep orchestration template shows the Bicep API Operation Module. The template accepts the object obtained from the **`listCallbackUrl()`** function used on the workflow trigger. You can get the key operation details by combining the object results and passed-in parameters. Some operation details include the following examples:

- Display name.
- Method, such as GET, POST, PUT, and so on.
- URL template.
- Template parameters if used.

```bicep 
/**********************************
Bicep template: Deploy an API operation - API operation module
        Author: Andrew Wilson
***********************************/

targetScope = 'resourceGroup'

// ** Parameters **
// ****************

@description('The path to the name for the API Management service API.')
param parentName string

@description('The name for the API operation.')
param operationName string

@description('The display name for the API operation.')
param operationDisplayName string

@description('The HTTP method for the API operation, for example, GET or POST.')
param operationMethod string

@description('The API operation path to replace with the backend implementation through policy.')
param operationPath string

@description('The callback object with the endpoint URL and other details for the logic app workflow trigger.')
param lgCallBackObject object

// ** Variables **
// ***************

var hasRelativePath = lgCallBackObject.?relativePath != null ? true : false
var pathParametersList = hasRelativePath ? lgCallBackObject.relativePathParameters : []
var pathParameters = [for pathParameter in pathParametersList: {
    name: pathParameter
    type: 'string'
}]

// ** Resources **
// ***************

@description('Deploy logic app API operation.')
resource logicAppAPIGetOperation 'Microsoft.ApiManagement/service/apis/operations@2022-08-01' = {
  name: '${parentName}/${operationName}'
  properties: {
    displayName: operationDisplayName
    method: operationMethod
    urlTemplate: operationPath
    templateParameters: hasRelativePath ? pathParameters : null
  }
}

// ** Outputs **
// *************
```

As the last puzzle piece, the next Bicep template shows the API operation policy module where the URI for the logic app workflow is set and replaced in the operation policy, following the required query parameters including the SAS key. The SAS replacement that happens here doesn't apply to the key, but to the named value reference that holds the key vault reference to the secret.

```bicep
/********************************************
Bicep template: API operation policy module for logic app API in API Management
        Author: Andrew Wilson
********************************************/

targetScope = 'resourceGroup'

// ** Parameters **
// ****************

@description('The parent naming structure for the policy.')
param parentStructureForName string

@description('The raw policy document template.')
param rawPolicy string

@description('The name for the named value that references the key vault secret.')
param sig string = ''

@description('The callback object that has the endpoint URL and other details about the logic app workflow.')
param lgCallBackObject object

// ** Variables **
// ***************

var operationUrlBase = split(split(lgCallBackObject.value, '?')[0], '/api')[1]
var hasRelativePath = lgCallBackObject.?relativePath != null ? true : false
var RelativePathHasBeginingSlash = hasRelativePath ? first(lgCallBackObject.relativePath) == '/' : false
var operationUrl = hasRelativePath && RelativePathHasBeginingSlash ? '${operationUrlBase}${lgCallBackObject.relativePath}' : hasRelativePath && !RelativePathHasBeginingSlash ? '${operationUrlBase}/${lgCallBackObject.relativePath}' : operationUrlBase

var policyURI = replace(rawPolicy, '__uri__', operationUrl)
var policyApiVersion = replace(policyURI, '__api-version__', lgCallBackObject.queries['api-version'])
var policySP = replace(policyApiVersion, '__sp__', lgCallBackObject.queries.sp)
var policySV = replace(policySP, '__sv__', lgCallBackObject.queries.sv)
var policySIG = replace(policySV, '__sig__', sig)

// ** Resources **
// ***************

@description('Add query strings through the policy.')
resource operationPolicy 'Microsoft.ApiManagement/service/apis/operations/policies@2022-08-01' = {
  name: '${parentStructureForName}/policy'
  properties: {
    value: policySIG
    format: 'xml'
  }
}

// ** Outputs **
// *************
```

### Summary for security using SAS

The SAS method for securing integration workflows relies on the following critical factors:

- Set up secure storage and access for the logic app workflow SAS keys.

  This aspect requires strict implementation of the principle of least privilege by using RBAC and application security boundaries.

- Fully adhere to best practices for handling and using SAS keys:

  - Always use HTTPS for handling requests.

  - Have a plan to revoke compromised or unused keys.

  - Have a plan to regularly rotate keys.

- Limit access to the logic app workflow endpoints and allow audiences through mechanisms such as IP address restrictions.

## Method 2: Security using Easy Auth

"Easy Auth" is a built-in authentication and authorization capability provided by Azure Functions and Azure App Service. Easy Auth uses a federated identity where an external identity provider manages the user identities and authentication flow for you. Fortunately, Azure Logic Apps (Standard) supports this capability because the underlying foundation for Standard logic apps is the same as Azure Functions.

Easy Auth is a platform feature that runs on the same Azure virtual machine as your logic app and workflows. When Easy Auth is set up, any incoming HTTP request, except for webhook triggers, passes through Easy Auth before your application handles the request.

The following diagram shows how Easy Auth runs separately from your application. You can set up this feature using an ARM template settings file or a configuration file.

:::image type="content" source="media/secure-integration-workflows-azure-logic-apps-api-management/easy-auth-arch.png" alt-text="Architecture diagram shows how Easy Auth runs separately from applications that support this built-in authentication and authorization method.":::

Easy Auth provides stronger security than an SAS key by using an identity, rather than with a key that grants access to anyone who has that key.

Looking back at the three As, an identity covers the following aspects:

- Authentication

  Are you who you say you are? An identity provider validates your identity.

- Authorization

  Do you have permission to access what you want to access?

These identity aspects make Easy Auth a stronger option for securing your components, especially when you add an Azure managed identity to the mix. A managed identity provides significant benefits, for example:

- You don't need to manage credentials, nor does anyone have access to them.

- You can use a managed identity to authenticate for any resource that supports Microsoft Entra authentication, such as Azure Logic Apps.

- You can use managed identities at no extra cost.

When you use a managed identity, you can perform the following tasks:

- Set up Easy Auth on the logic app using Microsoft Entra as the identity provider.

- Use the system-assigned managed identity as the identity for your API Management instance.

- Restrict the allowed Easy Auth identities to the identity fer your API Management instance.

As a result, your API Management instance is the only valid Microsoft Entra identity that can call the request-based trigger endpoint on a logic app workflow.

Like the previous SAS method, you can achieve the third "A" (access) using a security option such as restricting inbound IP addresses so that only specific applications or services can call the logic app workflow.

### Example solution architecture

The following high-level architecture diagram shows how Easy Auth protects request-based triggers in workflows by allowing only the managed identity for the API Management instance to send requests to workflows.

:::image type="content" source="media/secure-integration-workflows-azure-logic-apps-api-management/easy-auth-api-management.png" alt-text="Architecture diagram shows security using Easy Auth with API Management for Standard workflows in Azure Logic Apps." lightbox="media/secure-integration-workflows-azure-logic-apps-api-management/easy-auth-api-management.png":::

> [!NOTE]
>
> When Easy Auth is set up, you no longer need to use SAS query parameters, such as sp, sv, and sig, 
> in the request that is sent. However, you still need to specify the api-version value.

The authentication and authorization process works in the following way:

1. The user or system has defined access and sends a request using the path to the logic app API in your API Management instance.

1. Apply the "API all operations" policy to complete the following tasks:

   1. Set the backend API service to link and point to the Standard logic app.

   1. Get the authorization token (managed identity) to use in the request header.

1. Route the request using the path to the API operation.

1. Apply the "API operation" policy to complete the following tasks for the API operation:

   1. Set the API operation to link and point to the corresponding logic app workflow.

   1. Set the query parameter value for **`api-version`**.

1. Send request to workflow trigger endpoint.

### Set up logic app and API Management instance for Easy Auth

As a starting point, this section follows the Application and API deployment stages previously described in Method 1 as a starting point. The steps shown describe how to set up Easy Auth for your logic app and restrict access to only API Management. 

#### Step 1: Create and configure an app registration for the logic app

The following steps create an app registration that your Standard logic app uses to sign in with Microsoft Entra:

1. In the [Azure portal](https://portal.azure.com), enter **app registrations**.

1. From the results, under **Services**, select **App registrations**.

1. On the **App registrations** toolbar, select **New registration**, which opens the **Register an application** page.

   1. Provide a name for your app registration.

   1. Under **Supported account types**, select **Accounts in this organizational directory only (<*your-tenant*> - Single tenant)**.

   1. Now, set up a redirect URI for your logic app:

      1. Under **Redirect URI**, select **Web** as the platform.

      1. For the URI, enter a URL with the following syntax:

         **`https://<logic-app-url>/.auth/login/aad/callback`**, for example:

         **`https://contoso.azurewebsites.net/.auth/login/aad/callback`**

1. When you're done, select **Register**.

   Azure creates and opens the **Overview** page for the app registration.

1. Copy and save the **Application (client) ID** value for later use.

1. Create the client secret by following these steps:

   1. On the app registration menu, under **Manage**, select **Certificates & secrets** > **Client secrets** > **New client secret**.

   1. On the **Client secrets** tab, select **New client secret**.

   1. On the **Add a client secret** pane, enter a description and expiration for the secret, and select **Add**.

      The **Client secrets** tab now shows your new client secret.

   1. Make sure to copy the client secret from the **Value** column. The value becomes obscured after you leave this page.

1. Securely store the client secret for later use in deployment.

   For example, you can use GitHub Actions secrets and variables or Azure DevOps Library variable groups.

Your logic app uses this secret to prove its identity when requesting a token. This value is saved in your app configuration as a "slot-sticky" app setting named **MICROSOFT_PROVIDER_AUTHENTICATION_SECRET**.

> [!WARNING]
>
> If you don't set the client secret, the sign-in operation from the service uses the OAuth 1.0 
> implicit grant flow, which Microsoft doesn't recommend due to increased security risks.

#### Step 2: Set up managed identity for your API Management instance

The following Bicep snippet specifies the managed identity to use with an API Management instance. The example uses the system assigned managed identity, but based on your scenario's needs, you can specify a user-assigned identity instead.

```bicep
@description('Deploy the API Management instance.')
resource apimInstanceDeploy 'Microsoft.ApiManagement/service@2022-08-01' = {

  <...>

  identity: {
    type: 'SystemAssigned'
  }

  <...>

}
```

#### Step 3: Securely store and reference the app registration secret

The following Bicep snippet stores the app registration secret in a key vault, followed by referencing that secret in the logic app settings so that the logic app can prove its identity.

```bicep

<...>

@secure()
@description('The client secret for the Easy Auth app registration.')
param applicationEasyAuthClientSecret string

<...>

@description('The role definition ID for the role named Key Vault Secrets User.')
var keyVaultSecretsUserRoleDefId = '4633458b-17de-408a-b874-0445c86b69e6'

<...>

@description('Deploy the Easy Auth app registration secret to key vault.')
resource vaultLogicAppRegSecret 'Microsoft.KeyVault/vaults/secrets@2023-07-01' = {
  name: '${applicationLogicAppName}-EasyAuth-Secret'
  parent: applicationKeyVaultDeploy
  properties: {
    contentType: 'string'
    value: applicationEasyAuthClientSecret
  }
}

<...>

@description('Deploy the Standard logic app.')
resource applicationLogicAppStandardDeploy 'Microsoft.Web/sites@2024-04-01' = {
  name: applicationLogicAppName
  location: location
  identity: {
    type: 'SystemAssigned'
  }

  <...>

    resource config 'config@2024-09-01' = {
    name: 'appsettings'
    properties: {

      <...>

      MICROSOFT_PROVIDER_AUTHENTICATION_SECRET: '@Microsoft.KeyVault(VaultName=${applicationKeyVaultDeploy.name};SecretName=${vaultLogicAppRegSecret.name})'

      <...>
    }
  }
}

<...>

@description('Create the role assignment for the logic app to read the secret from key vault.')
resource applicationLogicAppRBACWithKV 'Microsoft.Authorization/roleAssignments@2022-04-01' = {
  name: guid(applicationKeyVaultDeploy.id, applicationLogicAppStandardDeploy.id, keyVaultSecretsUserRoleDefId)
  scope: vaultLogicAppRegSecret
  properties: {
    principalId: applicationLogicAppStandardDeploy.identity.principalId
    roleDefinitionId: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', keyVaultSecretsUserRoleDefId)
    principalType: 'ServicePrincipal'
  }
}
```

#### Step 4: Set up Easy Auth

The following Bicep example sets up Easy Auth for the logic app. Azure App Service manages Easy Auth, so an incoming request makes a hop before going to the Azure Logic Apps runtime. When you enable Easy Auth for a Standard logic app, the policies in your authentication settings validate all incoming requests.

```bicep
@description('Set up the Easy Auth config settings for the Standard logic app.')
resource applicationAuthSettings 'Microsoft.Web/sites/config@2024-04-01' = {
  name: 'authsettingsV2'
  parent: applicationLogicAppStandardDeploy // Standard logic app where to set up Easy Auth.
  properties: {
    globalValidation: {
      requireAuthentication: true
      unauthenticatedClientAction: 'AllowAnonymous' // Don't change. See note under the example.
    }
    httpSettings: {
      requireHttps: true
      routes: {
        apiPrefix: '/.auth'
      }
      forwardProxy: {
        convention: 'NoProxy'
      }
    }
    identityProviders: {
      azureActiveDirectory: {
        enabled: true
        registration: {
          openIdIssuer: uri('https://sts.windows.net/', tenant().tenantId)
          clientId: logicAppEasyAuthClientId
          clientSecretSettingName: 'MICROSOFT_PROVIDER_AUTHENTICATION_SECRET'
        }
        validation: {
          allowedAudiences: environment().authentication.audiences // Azure Management Plane [management.core.windows.net and management.azure.com]
          defaultAuthorizationPolicy: {
            allowedPrincipals: {
              identities: [
                apimInstance.identity.principalId // API Management system-assigned principal ID
              ]
            }
          }
        }
      }
    }
    platform: {
      enabled: true
      runtimeVersion: '~1'
    }
  }
}
```

> [!IMPORTANT]
>
> When the **`"unauthenticatedClientAction"`** attribute is set to **`"AllowAnonymous"`**, 
> all successful and failed calls are routed to the Azure Logic Apps runtime. The runtime 
> knows whether the request failed with Easy Auth or succeeded and processes the request 
> accordingly. If a request fails Easy Auth with the error **`"unauthenticatedClientAction": "Return401"`**, 
> the request doesn't get routed to the Azure Logic Apps runtime and fails with the **401** 
> error from Azure App Service. With this error, you also get a broken Azure portal experience.

#### Step 5: Set up "API all operations" policy to get bearer token

Rather than send an SAS key in the request to the endpoint for your logic app workflow, send an authorization bearer token by using the managed identity for your API Management instance. To get and add the bearer token to the request, use API Management policies.

The following example shows the "API all operations" policy, which uses **`authentication-managed-identity`** to output the bearer token to a context variable. You can then use this variable in a subsequent policy that adds the token to request headers.

```xml
<!-- API ALL OPERATIONS SCOPE -->
<policies>
    <inbound>
        <base />
        ...        
        <!-- Uses system-assigned managed identity for the API Management instance -->
        <authentication-managed-identity resource="https://management.azure.com/" output-token-variable-name="msi-access-token" ignore-error="false" />
        <set-header name="Authorization" exists-action="override">
            <value>@("Bearer " + (string)context.Variables["msi-access-token"])</value>
        </set-header>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

#### Step 6: Set up API operation policy

You no longer need to specify SAS query parameters in the request because you now have Easy Auth set up for calls sent to the request-based trigger in the logic app workflow. However, you still need to specify the **api-version** value. 

The following example API operation policy provides this value:

```xml
<!-- API OPERATION SCOPE -->
<policies>
    <inbound>
        <base />
        <rewrite-uri template="__uri__" />
        <set-query-parameter name="api-version" exists-action="append">
            <value>__api-version__</value>
        </set-query-parameter>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### Summary for security using Easy Auth

Easy Auth provides much stronger security than SAS keys. Easy Auth works by using a managed identity, rather than a key that gives access to anyone who has that key. This identity removes the need to manage credentials and prevents unwanted access.

For this scenario, the managed identity helps you make sure that your API Management instance is the only valid Microsoft Entra identity that has permissions to call the trigger endpoint for your logic app workflow.

## Conclusion

Both SAS and Easy Auth are options for securing your integration workflows behind an API Management instance. When Easy Auth and a managed identity are available as a supported security option, choose this option due to the stronger security provided. If you can use only SAS keys, make sure to consider the bigger picture and follow these best practices:

- Securely store and protect access to SAS keys.

- Follow SAS key best practices:

  - Always use HTTPS for communication.

  - Have a revocation plan for SAS keys.

  - Have a rotation plan for SAS keys.
