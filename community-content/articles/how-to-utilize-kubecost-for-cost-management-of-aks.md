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

Cost management and resource optimization are crucial aspects of operating Kubernetes, including managed Kubernetes services like Azure Kubernetes Service (AKS). In this article, we explore Kubecost Community Version which is an open-core tool that can help managing costs and efficiently utilizing resources within AKS clusters. 

We will look into:

- How Kubecost community version can be installed in an AKS cluster;
- How access to Kubecost dashboard can be secured with help of OAuth2 Proxy;
- How Azure-related out-of-cluster costs can be integrated into the same kubecost dashboard;

Finally we will briefly touch upon core capabilities of Kubecost community version like cluster cost trends, resource utilization insights, and improvement recommendations.

## Prerequisites

- Helm CLI
- Azure CLI
- PowerShell
- Existing AKS cluster with Ingress Controller to make kubecost dashboard accessible

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

## 1 - [Doing the first thing]
TODO: Add introduction sentence(s)
TODO: Add ordered list of procedure steps

## 2 - [Doing the second thing]
TODO: Add introduction sentence(s)
TODO: Add ordered list of procedure steps

## 3 - [Doing the next thing]
TODO: Add introduction sentence(s)
TODO: Add ordered list of procedure steps

## [N - Doing the last thing]
TODO: Add introduction sentence(s)
TODO: Add ordered list of procedure steps

## Next steps

To learn more about Kubecost functionality, as well as how OAuth 2.0 authentication with OAuth2 Proxy works in AKS in detail, please check out following resources:

- [Kubecost documentation](https://docs.kubecost.com/install-and-configure/install/getting-started)

- [Blog post: Setting Up OAuth 2.0 Authentication for Applications in AKS With NGINX and OAuth2 Proxy](https://kristhecodingunicorn.com/post/k8s_nginx_oauth)
