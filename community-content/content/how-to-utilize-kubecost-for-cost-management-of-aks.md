---
title: How to: Manage cost and optimize resources in AKS with Kubecost
description: In this article, we delve into the installation, configuration, and utilization of a Community version of the Kubecost tool for cost management and resource optimization in Azure Kubernetes Service (AKS) clusters.
author: guidemetothemoon
ms.author: shchow
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

Cost management and resource optimization are crucial aspects of operating Kubernetes, including managed Kubernetes services like Azure Kubernetes Service (AKS). In this article, we explore Kubecost community version which is an open-core tool that can help managing costs and utilizing resources more efficiently within AKS clusters. 

We will look into:

- How Kubecost community version can be installed and configured in an AKS cluster;
- How Kubecost dashboard can be publicly exposed over HTTPS and secured with Microsoft Entra ID authentication with help of OAuth2 Proxy;
- How Azure-related out-of-cluster costs can be integrated into the same Kubecost dashboard with help of Azure Cloud Integration;

Finally we will briefly touch upon core capabilities of Kubecost community version like cluster cost trends, resource utilization insights, and cluster improvement recommendations.

## Prerequisites

- Helm CLI (version 3.1+)

- Azure CLI

- PowerShell

- Existing AKS cluster 

- (Optional) **Publicly accessible Kubecost dashboard:** Ingress Controller must be installed on AKS. As an example, you can follow this tutorial to deploy Application Gateway Ingress Controller add-on for a new or existing AKS cluster: [Tutorial: Enable application gateway ingress controller add-on for an existing AKS cluster with an existing application gateway](https://learn.microsoft.com/en-us/azure/application-gateway/tutorial-ingress-controller-add-on-existing?toc=https%3A%2F%2Flearn.microsoft.com%2Fen-us%2Fazure%2Faks%2Ftoc.json&bc=https%3A%2F%2Flearn.microsoft.com%2Fen-us%2Fazure%2Fbread%2Ftoc.json)

- (Optional) **Publicly accessible Kubecost dashboard:** custom domain that you can use to publicly expose a Kubecost instance, as well as provision a TLS certificate to.

- (Optional) **Publicly accessible Kubecost dashboard:** certificate authority that can provision TLS certificates for the custom domain. Certificate management tool like ```cert-manager``` will query the CA to request TLS certificates. A popular and good alternative is [Let's Encrypt](https://letsencrypt.org/docs).

- (Optional) **Publicly accessible Kubecost dashboard:** Certificate management tool for AKS cluster to provision TLS certificates for secure connection to Kubecost dashboard. A popular and good alternative is [cert-manager](https://cert-manager.io/docs/installation).

## 1 - Create namespace for Kubecost resources

Now you can create a namespace in AKS cluster where Kubecost resources will be deployed. Default and recommended name for the namespace is ```kubecost```, and it will be the one we will use in this walkthrough. 
**Please ensure that active Kubernetes context is set to the correct AKS cluster prior to executing below command.**

``` sh
kubectl create namespace kubecost
```

## 2 - Configure Azure Cloud Integration for Kubecost

In this step you will integrate total daily costs overview for the Azure subscription where AKS cluster is deployed into Kubecost dashboard. This will allow you see out-of-cluster costs for other Azure services side-by-side with in-cluster costs in the same Kubecost dashboard.

In order to configure Azure Cloud Integration you will need to set up daily export of Azure cost reports to a dedicated Azure storage. Kubecost will access Azure storage via API to retrieve the cost data for display in the dashboard.

> [!NOTE]
> - Not all Microsoft Azure offers are currently supported in Cost Management. Please ensure that your Azure subscription type is supported by Cost Management prior to setting up Azure cost daily export. You may find supported Azure offers here: [Supported Microsoft Azure offers](https://learn.microsoft.com/en-us/azure/cost-management-billing/costs/understand-cost-mgt-data#supported-microsoft-azure-offers).

``` powershell

# Declare required configuration values
$subscriptionId = "[AZURE_SUBSCRIPTION_ID]"
$resourceGroup = "kubecost-rg"
$location = "northeurope"
$storageAccount = "kubecostst"
$storageContainer = "costreports"
$storageContainerPath = "costreportsdir"

# Register service provider to create cost reports
az provider register --namespace 'Microsoft.CostManagementExports'

# Create new resource group where Kubecost resources will be located
az group create --name $resourceGroup --location $location

# Create new storage account where Azure cost reports will be located
az storage account create --resource-group $resourceGroup --name $storageAccount --location $location --kind StorageV2 --sku Standard_LRS

# Create container in the newly created storage to store cost reports
az storage container create --name $storageContainer --account-name $storageAccount

# Create daily Azure cost exports to the newly created storage
az costmanagement export create --name kubecostexport --type AmortizedCost `
--scope "subscriptions/$subscriptionId" `
--storage-account-id "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/Microsoft.Storage/storageAccounts/$storageAccount" `
--storage-container $storageContainer --timeframe MonthToDate --recurrence Daily `
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
        "azureSubscriptionID": "$subscriptionId",
        "azureStorageAccount": "$storageAccount",
        "azureStorageAccessKey": "$storageAccessKey",
        "azureStorageContainer": "$storageContainer",
        "azureContainerPath": "$storageContainerPath",
        "azureCloud": "public"
    }
  ]
}
"@

# Save JSON object as a file with the required name
$cloudIntegrationObject | Set-Content "$tempFilePath/cloud-integration.json" -Encoding UTF8
```

## 3 - Create Kubernetes Secret for Azure Cloud Integration configuration

Now that Azure Cloud Integration is configured we can deploy ```cloud-integration.json```, which we generated in the previous step, as a Kubernetes Secret in the respective AKS cluster, in the same namespace where Kubecost will be deployed. Kubecost will use this object in order to identify which storage resource it needs to query in order to retrieve daily Azure cost report data.

``` sh
kubectl create secret generic kubecostazstorage --from-file "$tempFilePath/cloud-integration.json" --namespace kubecost
```

## 4 - Deploy Kubecost

You are ready to deploy Kubecost Helm chart with Azure Cloud Integration to an AKS cluster! Prior to running below commands you need to update ```version``` with the Kubecost Helm chart version of your choice.

> [!NOTE]
> - Usage of a latest/wildcard version is not recommended. At the point of writing this article the latest stable version of Kubecost Helm chart is ```1.105.0```. You can find stable release version with help of following command: ```helm search repo kubecost/cost-analyzer```.

``` sh
# Add Kubecost Helm repository and get latest repository updates
helm repo add kubecost https://kubecost.github.io/cost-analyzer
helm repo update

# Deploy Kubecost Helm chart with enabled Azure Cloud Integration
helm upgrade kubecost kubecost/cost-analyzer --install --namespace kubecost --set kubecostProductConfigs.cloudIntegrationSecret=kubecostazstorage --version [KUBECOST_HELM_CHART_VERSION]
```
Once Helm deployment executes you can access Kubecost dashboard at ```http://localhost:9090``` with help of port forwarding:
```kubectl port-forward -n kubecost deployment/kubecost-cost-analyzer 9090```

In a real-life scenario you may want multiple resources to be able to access Kubecost dashboard at specific URL address. At the same time you would still want this access to be secure and controlled with some form of authentication in order to prevent full public exposure of this type of data.

SSO and RBAC functionality is unfortunately only supported in Kubecost enterprise version, but it's still possible to achieve by implementing TLS and Microsoft Entra ID authentication to Kubecost dashboard with help of another open source tool - OAuth2 Proxy. Below steps will demonstrate how you can do that. 

> [!NOTE]
> - Please ensure that you've set up optional pre-requisites prior to executing below steps.

## 5 - (Optional) Create OAuth2 Proxy application in Microsoft Entra ID

The last two steps require optional pre-requisites, that were mentioned in the beginning of this article, to be installed and configured. Installation and configuration of Ingress Controller and cert-manager are outside of the scope of this article, but you can check out the documentation that was provided both in the pre-requisites and next steps section for additional information.

In this step we need to register an application in Microsoft Entra ID tenant that will represent OAuth2 Proxy installation for Kubecost.

``` powershell
# Create application registration in Microsoft Entra ID with reply URL representing OAuth2 Proxy authentication URL for the respective Kubecost instance
$proxyClientId = az ad app create --display-name "kubecost-oauth2-proxy" --sign-in-audience AzureADMyOrg --web-redirect-uris "[KUBECOST_PUBLIC_URL]/oauth2/callback" | ConvertFrom-Json | Select-Object -First 1 -ExpandProperty appId

# Create client secret for the application: you can either create it manually in Azure portal or use below command to create a client secret based on the value that you specify
$proxyClientSecret = $(az ad app credential reset --id $proxyClientId --append --display-name "kubecost-client-secret" --query password --output tsv)
```

## 6 - (Optional) Deploy OAuth2 Proxy

In this step you will deploy OAuth2 Proxy application that will enable Microsoft Entra ID authentication for the Kubecost dashboard that you have deployed in step 5. Please note that the same namespace is used for OAuth2 Proxy and Kubecost deployments.

``` powershell
$proxyValuesObject = @"
ingress:
  enabled: true
  className: azure/application-gateway
  path: /oauth2
  pathType: Prefix
  hosts:
    - [KUBECOST_HOSTNAME]
  annotations:
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    cert-manager.io/cluster-issuer: [CertClusterIssuerName]
  tls:
    - secretName: [KUBECOST_HOSTNAME]-oauth2-proxy-ingress-tls-secret
      hosts:
        - [KUBECOST_HOSTNAME]
proxyVarsAsSecrets: true
config:
  cookieName: kubecostproxycookie
  clientID: $proxyClientId
  clientSecret: $proxyClientSecret
  cookieSecret: $cookieSecret
resources:
   limits:
     cpu: 100m
     memory: 128Mi
   requests:
     cpu: 100m
     memory: 128Mi
extraArgs:
  provider: oidc
  azure-tenant: [MICROSOFT_ENTRA_ID_TENANT_ID] # Microsoft Entra ID OAuth2 Proxy application Tenant ID
  pass-access-token: true
  email-domain: "*"
  upstream: file:///dev/null
  http-address: 0.0.0.0:4180
  oidc-issuer-url: https://login.microsoftonline.com/[MICROSOFT_ENTRA_ID_TENANT_ID]/v2.0
podLabels:
  application: [KUBECOST_HOSTNAME]-oauth2-proxy
customLabels:
  application: [KUBECOST_HOSTNAME]-oauth2-proxy
replicaCount: 1
"@

# Save JSON object as a file with the required name
$proxyValuesObject | Set-Content "$tempFilePath/values.yaml" -Encoding UTF8

helm repo add oauth2-proxy https://oauth2-proxy.github.io/manifests
helm repo update
helm upgrade --install kubecost-oauth2-proxy --namespace kubecost -f "$tempFilePath/values.yaml" oauth2-proxy/oauth2-proxy

```

## 7 - (Optional) Deploy Kubecost Ingress

In the last step you will deploy Kubecost Ingress that will allow external access to the Kubecost dashboard.

``` powershell

$kubecostIngressObject = @"
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: kubecost-cost-analyzer
  namespace: kubecost
  annotations:   
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
    nginx.ingress.kubernetes.io/auth-url: "https://[KUBECOST_HOSTNAME]/oauth2/auth"
    nginx.ingress.kubernetes.io/auth-signin: "https://[KUBECOST_HOSTNAME]/oauth2/start?rd=https://[KUBECOST_HOSTNAME]/oauth2/callback"
    meta.helm.sh/release-name: kubecost
    meta.helm.sh/release-namespace: kubecost
  labels:
    app: cost-analyzer
    app.kubernetes.io/instance: kubecost
    app.kubernetes.io/managed-by: Helm
    app.kubernetes.io/name: cost-analyzer
spec:
  ingressClassName: azure/application-gateway
  tls:
  - hosts:
    - [KUBECOST_HOSTNAME]
    secretName: kubecost-secret-name
  rules:
  - host: [KUBECOST_HOSTNAME]
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: kubecost-cost-analyzer
            port:
              number: 9090
"@

# Save JSON object as a file with the required name
$kubecostIngressObject | Set-Content "$tempFilePath/ingress.yaml" -Encoding UTF8

# Deploy Kubecost Ingress
kubectl apply -f "$tempFilePath/ingress.yaml" --namespace kubecost
```

Now, you can access your Kubecost dashboard at a public URL that you've configured and once you attempt to access it you should be first redirected to Microsoft login page prior to accessing the dashboard itself, thanks to OAuth2 Proxy!

## Functional highlights

Let's finish up this walkthrough with a few highlights of Kubecost dashboard that can provide you with some useful information about cost and efficiency of your AKS clusters.

## Next steps

To learn more about Kubecost, as well as how OAuth 2.0 authentication with OAuth2 Proxy works in AKS in detail, please check out following resources:

- [Kubecost documentation](https://docs.kubecost.com/install-and-configure/install/getting-started)
- [OAuth2 Proxy documentation](https://oauth2-proxy.github.io/oauth2-proxy/docs)
- [Blog post: Setting Up OAuth 2.0 Authentication for Applications in AKS With NGINX and OAuth2 Proxy](https://kristhecodingunicorn.com/post/k8s_nginx_oauth)
