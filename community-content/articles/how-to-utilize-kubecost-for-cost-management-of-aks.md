---
title: How to: Manage cost and optimize resources in AKS with Kubecost
description: In this article, we delve into the installation, configuration, and utilization of a Community version of the Kubecost tool for cost management and resource optimization in Azure Kubernetes Service (AKS) clusters.
author: guidemetothemoon
ms.author: Kristina Devochko
ms.service: azure
ms.topic: how-to
ms.date: 07/10/2023
ms.custom: template-how-to-pattern
content_well_notification: 
  - Human-created-Community
---

# Manage cost and optimize resources in AKS with Kubecost

---

**Principal author**: [Kristina Devochko](https://learn.microsoft.com/en-us/users/kristinadevochko-7890)

---

Cost management and resource optimization are crucial aspects of operating Kubernetes, including managed Kubernetes services like Azure Kubernetes Service (AKS). In this article, we explore Kubecost community version which is an open-core tool that can help managing costs and efficiently utilizing resources within AKS clusters. 

We will look into:

- How Kubecost community version can be installed and configured in an AKS cluster;
- How Kubecost dashboard can be publicly exposed over HTTPS and secured with Azure AD authentication with help of OAuth2 Proxy;
- How Azure-related out-of-cluster costs can be integrated into the same kubecost dashboard with help of Azure Cloud Integration;

Finally we will briefly touch upon core capabilities of Kubecost community version like cluster cost trends, resource utilization insights, and improvement recommendations.

## Prerequisites

- Helm CLI (version 3.1+)

- Azure CLI

- PowerShell

- Existing AKS cluster 

- (Optional) **Publicly accessible kubecost dashboard:** Ingress Controller must be installed on AKS. As an example, you can follow this tutorial to deploy Application Gateway Ingress Controller add-on for new or existing AKS cluster: [Tutorial: Enable application gateway ingress controller add-on for an existing AKS cluster with an existing application gateway](https://learn.microsoft.com/en-us/azure/application-gateway/tutorial-ingress-controller-add-on-existing?toc=https%3A%2F%2Flearn.microsoft.com%2Fen-us%2Fazure%2Faks%2Ftoc.json&bc=https%3A%2F%2Flearn.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

- (Optional) **Publicly accessible kubecost dashboard:** custom domain that you can use to publicly expose kubecost instance, as well as provision a TLS certificate to.

- (Optional) **Publicly accessible kubecost dashboard:** certificate authority that can provision TLS certificates for the custom domain. Certificate management tool like cert-manager will query the CA to request TLS certificates. A popular and good alternative is Let's Encrypt: [Let's Encrypt](https://letsencrypt.org/docs).

- (Optional) **Publicly accessible kubecost dashboard:** Certificate management tool for AKS cluster to provision TLS certificates for secure connection to kubecost dashboard. A popular and good alternative is cert-manager: [cert-manager installation](https://cert-manager.io/docs/installation).

## 1 - Acquire Kubecost community version unique token

First you will need to retrieve a unique token for Kubecost community version by providing an e-mail address here: [https://www.kubecost.com/install](https://www.kubecost.com/install).

Make a note of the token since it will be used later in the installation process.

## 2 - Create namespace for Kubecost resources

Now you can create a namespace in AKS cluster where Kubecost resources will live. Default and recommended name for the namespace is ```kubecost```, and it will be the one we will use in this tutorial.

``` sh
kubectl create namespace kubecost
```

## 3 - Configure Azure Cloud Integration

In this step you will integrate total daily costs for the Azure subscription where AKS cluster is deployed to Kubecost. This will allow you see out-of-cluster costs for other Azure services side-by-side with in-cluster costs in the same Kubecost dashboard.

In order to configure Azure Cloud Integration you will need to set up daily export of Azure cost reports to Azure storage. Kubecost will access Azure storage through API to retrieve the cost data for display.

``` powershell

# Declare required configuration values
$subscriptionId = "[AZURE_SUBSCRIPTION_ID]"
$resourceGroup = "kubecost-rg"
$location = "northeurope"
$storageAccount = "kubecost-st"
$storageContainer = "costreports"
$storageContainerPath = "costreportsdir"

# Register service provider to create cost reports
az provider register --namespace 'Microsoft.CostManagementExports'

# Create new resource group where Kubecost resources will be stored
az group create --name $resourceGroup --location $location

# Create new storage account where Azure cost reports will be stored
az storage account create --resource-group $resourceGroup --name $storageAccount --location $location --kind StorageV2 --sku Standard_LRS

# Create container in the newly created storage to store cost reports
az storage container create --name $storageContainer --account-name $storageAccount

# Create daily Azure cost exports to the newly created storage
az costmanagement export create --name kubecostexport --type AmortizedCost \
--scope "subscriptions/$subscriptionId" \
--storage-account-id /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.Storage/storageAccounts/$storageAccount \
--storage-container $storageContainer --timeframe MonthToDate --recurrence Daily \
--schedule-status Active --storage-directory $storageContainerPath

# Get access key for the newly created storage
$storageAccessKey = az storage account keys list --resource-group $resourceGroup --account-name $storageAccount | ConvertFrom-Json | Select-Object -First 1 -ExpandProperty value
```

Now that the daily export of Azure cost reports is set up you will need to save some of it's configuration information as a JSON file named ```cloud-integration.json```. This file will be used to create a Kubernetes Secret in the next step.

``` powershell
# Get temporary file path to save the JSON file to
$tempFilePath = [System.IO.Path]::GetTempPath()

# Create JSON object that will store Azure Cloud Integration configuration
$cloudIntegrationObject = @"
{
  "azure": [
    {
        "azureSubscriptionID": $subscriptionId,
        "azureStorageAccount": $storageAccount,
        "azureStorageAccessKey": "AZ_cloud_integration_azureStorageAccessKey",
        "azureStorageContainer": $storageContainer,
        "azureContainerPath": $storageContainerPath,
        "azureCloud": "public"
    }
  ]
}
"@

# Save JSON object as a file with the required name
$cloudIntegrationObject | Set-Content "$tempFilePath/cloud-integration.json" -Encoding UTF8
```

## 4 - Create Kubernetes Secret for Azure Cloud Integration configuration

Now that Azure Cloud Integration is configured we can deploy ```cloud-integration.json```, which we generated in the previous step, as a Kubernetes Secret in the respective AKS cluster, in the same namespace where Kubecost will be deployed. Kubecost will use this object in order to identify which storage resource it needs to query in order to retrieve daily Azure cost report data.

``` sh
kubectl create secret generic kubecostazstorage --from-file "./cloud-integration.json" -n kubecost
```

## 5 - Deploy Kubecost

You are ready to deploy Kubecost Helm chart with Azure Cloud Integration to an AKS cluster! Prior to running below commands you need to update the ```kubecostToken``` value with the unique token that was acquired in step 1 of this tutorial, and ```version``` with the Kubecost Helm chart version of your choice. 

**Please note that usage of a latest/wildcard version is not recommended.** At the point of writing this article the latest stable version of Kubecost Helm chart is ```1.104.4```. You can find stable release version with help of following command: ```helm search repo kubecost/cost-analyzer```.

``` sh
# Add Kubecost Helm repository and get latest repository updates
helm repo add kubecost https://kubecost.github.io/cost-analyzer/
helm repo update

# Deploy Kubecost Helm chart with enabled Azure Cloud Integration
helm upgrade kubecost kubecost/cost-analyzer --install --namespace kubecost --set kubecostToken="[KUBECOST_UNIQUE_TOKEN]" --set kubecostProductConfigs.cloudIntegrationSecret=kubecostazstorage --version [KUBECOST_HELM_CHART_VERSION]
```
Once Helm deployment executes you can access Kubecost dashboard with help of port forwarding:
```kubectl port-forward -n kubecost deployment/kubecost-cost-analyzer 9090```

As you can see, default deployment of Kubecost doesn't make the dashboard easily accessible. In a real-life scenario you may want multiple resources to be able to access it via a defined URL for instance. At the same time you would still want this access to be secure and controlled with some form of authentication in order to prevent full public exposure of this type of data.

SSO and RBAC functionality is only supported in Kubecost enterprise version, but it's still possible to implement TLS and Azure AD authentication to Kubecost dashboard with help of OAuth2 Proxy. Below steps will demonstrate how you can do that.

## 6 - (Optional) Create OAuth2 Proxy application in Azure AD
The last two steps require optional pre-requisites, that were mentioned in the beginning of this article, to be installed and configured. Installation and configuration of Ingress Controller and cert-manager are outside of the scope of this article, but you can check out the documentation that was provided both in the pre-requisites and next steps section for additional information.

In this step we need to register an application in Azure AD tenant that will represent OAuth2 Proxy installation for Kubecost.

``` powershell
# Create application registration in Azure AD with reply URL representing OAuth2 Proxy authentication URL for the respective Kubecost instance

# Create client secret for the application: you can either create it manually in Azure portal or use below command to create a client secret based on the value that you specify



```

## 7 - (Optional) Deploy OAuth2 Proxy

In this step you will deploy OAuth2 Proxy application that will enable Azure AD authentication for the Kubecost dashboard that you have deployed in step 5. Please note that the same namespace is used for OAuth2 Proxy and Kubecost deployments.

``` powershell

```

## 8 - (Optional) Deploy Kubecost Ingress

In the last step you will deploy Kubecost Ingress that will allow external access to the Kubecost dashboard.

``` powershell

```

## Functional highlights


## Next steps

To learn more about Kubecost functionality, as well as how OAuth 2.0 authentication with OAuth2 Proxy works in AKS in detail, please check out following resources:

- [Kubecost documentation](https://docs.kubecost.com/install-and-configure/install/getting-started)

- [Blog post: Setting Up OAuth 2.0 Authentication for Applications in AKS With NGINX and OAuth2 Proxy](https://kristhecodingunicorn.com/post/k8s_nginx_oauth)
