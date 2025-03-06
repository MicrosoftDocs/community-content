---
title: Deploy Azure Integration Services with Azure Verified Modules
description: Streamline deployment for integration applications with Azure Verified Modules for Bicep.
contributor-type: community #Required. Don't change this value.
author: Viktorh89
ms.author: mlcc-owners #Required. Don't change this value.
ms.reviewer: wsilveira, estfan #Required for Microsoft internal owners and reviewers. Don't change these values.
ms.service: azure #Required. Don't change this value.
ms.topic: conceptual #Required. Don't change this value.
ms.date: 03/06/2025 #Required in mm/dd/yyyy format.
---

# Streamline deployment for Azure Integration Services with Azure Verified Modules for Bicep

---

**Principal author**: [Viktor Hodberg](https://github.com/Viktorh89)

---

Azure Verified Modules is a single Microsoft standard that gives you a unified experience to streamline deployment for Bicep modules and publish them in the public [Azure Bicep Registry in GitHub](https://github.com/Azure/bicep-registry-modules). These modules speed up your experience when working with deployments - no more guessing, copying and pasting, or unclear dependencies between resources. You can use these modules as your source of truth, which helps you leapfrog to deployments that align with the [Azure Well-Architected Framework](/azure/well-architected/) without having to know each aspect about every part in the framework.

For more information, see the following documentation:

- [Azure Verified Modules](https://azure.github.io/Azure-Verified-Modules/overview/introduction/)
- [Azure Integration Services](https://azure.microsoft.com/solutions/integration-services)
- [Bicep](/azure/azure-resource-manager/bicep/overview?tabs=bicep)

When you use Azure Verified Modules, this experience makes the border almost disappear between infrastructure-as-code "shared" resources such as Azure Service Bus, Azure API Management, and Azure Key Vault and for your Azure Integration Services resources.

Azure Verified Modules includes the following module types:

- Resource modules
- Pattern modules
- Utility modules

Not all modules exist at this time, but the project is open source, so anyone can contribute!

## Prerequisites

- [Visual Studio Code](https://code.visualstudio.com/download)
- The latest [Bicep extension for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep)

## Get started with the basics

To deploy your Azure Integration Services resources, your trusty **main.bicep** file is all you really need. In this file, you define your resources by using modules that reside in the Azure Verified Modules repository. In Visual Studio Code, IntelliSense also really helps here. You can initialize a module and get the required properties plus the documentation, for example:

```
module laStd 'br/public:avm/res/web/site:0.13.1' = {
```

When you work with deployments, one good practice is that you minimize the number of parameters, or at the very least, use a [Bicep parameters (.**bicepparam**) file](/azure/azure-resource-manager/bicep/parameter-files?tabs=Bicep), variables, or YAML (.yml) pipeline parameters.

The following example shows how to deploy a Standard logic app resource in the simplest form using the .**bicepparam** file:

1. Define the required parameters, for example:

   ```
   @description('Required')
   @allowed(['dev', 'qa', 'prod'])
   param environment string = 'dev'
   ```

   At deployment, the **`param environment string`** value identifies the deployment environment, is represented by the "<**environment**>" placeholder, and is automatically added as a suffix to following attributes:

   - <**resource-group-name**>
   - <**App-Service-Plan-name**>
   - <**Application-Insights-name**>
   - <**deployment-name**>
   - <**storage-account-name**>

   In most cases, a hyphen (**-**) is added as the separation character except where special characters aren't allowed, such as storage account names. For example, in this article, "<**resource-group-name**>" becomes "**myResourceGroup-dev**".

1. Define the hosting option for your Standard logic app resource. To keep thing simple, this example reuses an existing Workflow Standard WS1 App Service Plan by using the keyword "**existing**":

   ```
   resource aspWS1 'Microsoft.Web/serverfarms@2022-09-01' existing = {
      scope: resourceGroup('<resource-group-name>')
      name: '<App-Service-Plan-name>'
   ```

1. Define your existing Application Insights resource:

   ```
   resource appInsights 'Microsoft.Insights/components@2020-02-02' existing = {
       scope: resourceGroup('<resource-group-name>')
       name = '<Application-Insights-name>'
   ```

1. Define the storage account used by your Standard logic app resource:

   ```
   module laStdStorage 'br/public:avm/res/storage/storage-account:0.16.0' = {
      name: '<deployment-name>'
      params: {
         name: '<storage-account-name>'
         networkAcls: {
            bypass: 'AzureServices'
             virtualNetworkRules: []
             ipRules: []
             defaultAction: 'Allow'
         }
      }
   }
   ```

1. Finally, define your Standard logic app resource using information from the previous modules:

   ```
   module laStd 'br/public:avm/res/web/site:0.13.1' = {
      name: '<deployment-name>'
      params: {
         name: '<logic-app-name>'
         kind: 'functionapp,workflowapp'
         appInsightResourceId: appInsights.id
         serverFarmResourceId: aspWS1.id
         storageAccountResourceId: laStdStorage.outputs.resourceId
         httpsOnly: true
         siteConfig: {
            alwaysOn: false
            ftpsState: 'FtpsOnly'
            minTlsVersion: '1.2'
         }
         managedIdentities: {
            systemAssigned: true
         }
         appSettingsKeyValuePairs: {
            AzureFunctionsJobHost__logging__logLevel__default: 'Trace'
            FUNCTIONS_EXTENSION_VERSION: '~4'
            FUNCTIONS_WORKER_RUNTIME: 'dotnet'
            AzureFunctionsJobHost__extensionBundle__id: 'Microsoft.Azure.Functions.ExtensionBundle.Workflows'
            AzureFunctionsJobHost__extensionBundle__version: '[1.*, 2.0.0)'
         }
      }
   }
   ```

## Implement role-based access control and identity

Want to fully implement [role-based access control](/azure/role-based-access-control/overview) in a clear and easy way for your Azure Integration Services resources? Azure Verified Modules can also help with this scenario, for example, when you use the following common options:

- Deploy your infrastructure-as-code with role-based access control included. Most Azure Verified Modules include this option.
- Deploy your role-based access control as a separate step in your **main.bicep** file. Modules support this capability at the management, subscription, and resource group levels.

The following sections show examples for setting up these capabilities.

### Example - Role assignment at resource level

The following example shows a module that deploys a role assignment at the resource level using minimal parameters:

```
module component 'br/public:avm/res/insights/component:0.5.0' = {
   name: 'componentDeployment'
   params: {
      // Required parameters
      name: '<Application-Insights-name>'
      workspaceResourceId: '<Log-Analytics-workspace-ID>'
      // Optional parameters
      diagnosticSettings: []
      disableIpMasking: false
      disableLocalAuth: true
      forceCustomerStorageForProfiler: true
      linkedStorageAccountResourceId: '<linked-storage-account-resource-ID>'
      location: '<Azure-region>'
      lock: {}
      roleAssignments: [
         {
            principalId: '<principal-ID>'
            principalType: 'ServicePrincipal'
            roleDefinitionIdOrName: '<role-definition-ID-or-name>'
         }
      ]
   }
}
```

### Example - Role assignment at resource group level

The following example shows a module that deploys a role assignment at the resource group level using minimal parameters:

```
module roleAssignment 'br/public:avm/ptn/authorization/role-assignment:<version>' = {
   name: 'roleAssignmentDeployment'
   params: {
      // Required parameters
      principalId: '<principal-ID>'
      roleDefintiionIdOrName: '/providers/Microsoft.Authorization/roleDefinitions/<predefined-rbac-role-GUID>'
      // Optional parameters
      location: '<Azure-region>'
      principalType: 'ServicePrincipal'
      resourceGroupName: '<resource-group-name>'
      subscriptionId: '<Azure-subscription-ID'>
   }
}
```

## Add alerts

You can continue creating your **.bicepparam** file by adding alerts, an Azure workbook, or an Azure Service Bus topic. Maybe you have requirements to lock down your resources more or become more aligned with the Well-Architected Framework. You can perform these tasks quickly and easily using Azure Verified Modules.

The following example comes from the Azure Verified Modules repository and shows a Standard logic app that aligns with the Well-Architected Framework:

```
module site 'br/public:avm/res/web/site:<version>' = {
   name: 'siteDeployment'
   params: {
      // Required parameters
      kind: 'app'
      name: '<Standard-logic-app-name>'
      serverFarmResourceId: '<server-farm-resource-ID>'
      // Optional parameters
      basicPublishingCredentialPolicies: [
         {
            allow: false
            name: 'ftp'
         }
         {
            allow: false
            name: 'scm'
         }
      ]
      diagnosticSettings: [
         {
            eventHubAuthorizationRuleResourceId: '<event-hub-authorization-rule-resource-ID>'
            eventHubName: '<event-hub-name>'
            storageAccountResourceId: '<storage-account-resource-ID>'
            workspaceResourceId: '<Log-Analytics-workspace-ID>'
         }
      ]
      httpsOnly: true
      publicNetworkAccess: 'Disabled'
      scmSiteAlsoStopped: true
      siteConfig: {
         alwaysOn: true
         ftpsState: 'FtpsOnly'
         healthCheckPath: '/healthz'
         metadata: [
            {
               name: 'CURRENT_STACK'
               value: 'dotnetcore'
            }
         ]
         minTlsVersion: '1.2'
      }
      vnetContentShareEnabled: true
      vnetImagePullEnabled: true
      vnetRouteAllEnabled: true
   }
}
```

For more information and resources, see the full GitHub repository at [https://github.com/Viktorh89/SampleIntegration](https://github.com/Viktorh89/SampleIntegration).

## Related content

- [Choose integration services in Azure for enterprise integration scenarios](/azure/logic-apps/azure-integration-services-choose-capabilities)
- [Azure Integration Services](https://azure.microsoft.com/solutions/integration-services#Products-2)
