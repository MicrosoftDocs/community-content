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

# [H1 heading]
How to: Manage cost and optimize resources in AKS with Kubecost

---

**Principal author**: [Kristina Devochko](https://learn.microsoft.com/en-us/users/kristinadevochko-7890)

---

Cost management and resource optimization are crucial aspects of operating Kubernetes, including managed Kubernetes services like Azure Kubernetes Service (AKS). In this article, we explore Kubecost community version which is an open-core tool that can help managing costs and efficiently utilizing resources within AKS clusters. 

We will look into:

- How Kubecost community version can be installed in an AKS cluster;
- How access to Kubecost dashboard can be secured with help of OAuth2 Proxy;
- How Azure-related out-of-cluster costs can be integrated into the same kubecost dashboard;

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

<!-- 4. Task H2s ------------------------------------------------------------------------------

Required: Each major step in completing a task should be represented as an H2 in the article.
These steps should be numbered.
The procedure should be introduced with a brief sentence or two.
Multiple procedures should be organized in H2 level sections.
Procedure steps use ordered lists.

-->
- get kubecost community version token: https://www.kubecost.com/install
- azure integration prep
- kubecost azure integration
- install kubecost helm chart
- install oauth2 proxy to publicly expose kubecost with auth
- showcase some functionality

## 1 - Acquire Kubecost community version unique token

First you will need to retrieve a unique token for Kubecost community version by providing an e-mail address here: [https://www.kubecost.com/install](https://www.kubecost.com/install).

Make a note of the token since it will be used later in the installation process.

## 2 - Configure Azure Cloud Integration

In this step you will integrate total daily costs for the Azure subscription where AKS cluster is deployed to Kubecost. This will allow you see out-of-cluster costs for other Azure services side-by-side with in-cluster costs in the same Kubecost dashboard.

In order to configure Azure Cloud Integration you will need to set up daily export of Azure cost reports to Azure storage. Kubecost will access Azure storage through API to retrieve the cost data for display.

``` powershell
$subscriptionId = "[AZURE_SUBSCRIPTION_ID]"
$resourceGroup = "kubecost-rg"
$location = "northeurope"
$storageAccount = "kubecost-st"
$storageContainer = "costreports"
$storageContainerPath = "costreportsdir"

az provider register --namespace 'Microsoft.CostManagementExports'
az group create --name $resourceGroup --location $location
az storage account create --resource-group $resourceGroup --name $storageAccount --location $location --kind StorageV2 --sku Standard_LRS
az storage container create --name $storageContainer --account-name $storageAccount

az costmanagement export create --name kubecostexport --type AmortizedCost \
--scope "subscriptions/$subscriptionId" \
--storage-account-id /subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.Storage/storageAccounts/$storageAccount \
--storage-container $storageContainer --timeframe MonthToDate --recurrence Daily \
--schedule-status Active --storage-directory $storageContainerPath
```

## 3 - Create namespace for Kubecost resources

Now you need to create a namespace in AKS cluster where Kubecost resources will live. Default and recommended name for the namespace is ```kubecost```, and it will be the one we will use in this tutorial.

``` sh
kubectl create namespace kubecost
```

## 4 - Create Kubernetes Secret for Azure Cloud Integration configuration

Now that Azure Cloud Integration is configured we can save the configuration as a Kubernetes Secret in the respective AKS cluster, in the same namespace where Kubecost will be deployed.

``` sh
kubectl create secret generic kubecostazstorage --from-file "./cloud-integration.json" -n kubecost
```

## 5 - Deploy Kubecost Helm chart

In this step you will deploy Kubecost Helm chart with Azure Cloud Integration to an AKS cluster. Prior to running the command you need to update the ```kubecostToken``` value with the unique token that was acquired in step 1 of this tutorial, and ```version``` with the Kubecost Helm chart version of your choice. 

Please note that usage of a latest/wildcard version is not recommended. At the point of writing this article the latest stable version of Kubecost Helm chart is ```1.104.4```. You can find stable release version with help of following command: ```helm search repo kubecost/cost-analyzer```.

``` sh
# Add Kubecost Helm repo and get latest repo updates
helm repo add kubecost https://kubecost.github.io/cost-analyzer/
helm repo update

# Deploy Kubecost Helm chart with Azure Cloud Integration
helm upgrade kubecost kubecost/cost-analyzer --install --namespace kubecost --set kubecostToken="[KUBECOST_UNIQUE_TOKEN]" --set kubecostProductConfigs.cloudIntegrationSecret=kubecostazstorage --version [KUBECOST_HELM_CHART_VERSION]
```

## 6 - (Optional) Create OAuth2 Proxy application in Azure AD
TODO: Add introduction sentence(s)
TODO: Add ordered list of procedure steps

## 7 - (Optional) Deploy OAuth2 Proxy application and Kubecost Ingress

## Next steps

To learn more about Kubecost functionality, as well as how OAuth 2.0 authentication with OAuth2 Proxy works in AKS in detail, please check out following resources:

- [Kubecost documentation](https://docs.kubecost.com/install-and-configure/install/getting-started)

- [Blog post: Setting Up OAuth 2.0 Authentication for Applications in AKS With NGINX and OAuth2 Proxy](https://kristhecodingunicorn.com/post/k8s_nginx_oauth)
