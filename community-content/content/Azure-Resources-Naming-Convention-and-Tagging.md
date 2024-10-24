---
title: Azure Resources Naming Convention and Tagging #Required; page title displayed in search results. Don't enclose in quotation marks. 
description: Azure Resources Naming Convention and Tagging #Required; article description that's displayed in search results. Don't enclose in quotation marks. Do end with a period.
author: gxgrammatikos #Required; your GitHub user alias, with correct capitalization.
ms.author: mlcc-owners #Required; a Microsoft distribution list; don't change. 
ms.service: azure #Required; service per approved list.
ms.topic: conceptual #Required; leave this attribute/value as-is.
ms.date: 05/25/2024 #Required; mm/dd/yyyy format.
contributor-type: community
---

# Azure Resources Naming Convention and Tagging

In this blog post, we discuss a key aspect of Cloud Adoption Framework methodology, namely the Azure resource name convention and why this is an essential aspect of keeping resources organized, easy to identify, and protects you from making things difficult when managing them.

**Principal author**: [George Chrysovalantis Grammatikos](/users/georgechrysovalantisgrammatikos-8518/) 

---

## Why use a naming convention practice?

A proper naming convention is essential for a number of reasons:

- Easy to find a resource when a user (Admin) filters Azure Portal resources
- You can easily adhere to a unique names policy for resources
- It is useful for calculating the cost of resources.

The naming rules and restrictions for Azure resources, such as length limits and which characters you can use, vary based on resource type. For more details, click [Naming rules and restrictions for Azure resources](/azure/azure-resource-manager/management/resource-name-rules)

## Determine a naming convention

Let us see an example of naming an Azure resource. The general naming convention proposal would be something like the one below:

:::image type="content" source="media/Azure-Resources-Naming-Conventions-and-tagging/NamingConventionProposal.jpg" alt-text="Naming convention proposal":::

> [!NOTE]
> Βetween the components that form the name you can for example use **hyphen (-)** or **underscore (_)**, i.e. vm-webapp-dev-westeurope-01

The matrix below depicts a few more examples with several types of Azure resources:

| **Resource type**           | **Workload / App** | **Enviornment**  | **Azure Region** | **Instance**  |
|---------------------------- |------------------- |------------------|------------------|---------------|
| rg – {resource group}       | webapp             | dev              | westeurope       |  01           |
| asp – {app service plan}    | webapp             | dev              | westeurope       |  01           |
| ai – {application insights} | webapp             | dev              | westeurope       |  01           |

The image below depicts how your Azure resources name will show after applying the above rules:

:::image type="content" source="media/Azure-Resources-Naming-Conventions-and-tagging/ResourcesImage.jpg" alt-text="Resources":::
To get more abbreviation examples about Azure resources, see below:

* [AI + machine learning](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)
* [Analytics and IoT](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)
* [Compute and web](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)
* [Containers](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)
* [Databases](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)
* [Developer tools](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)
* [DevOps](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)
* [Integration](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)
* [Management and governance](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)
* [Migration](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)
* [Networking](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)
* [Security](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)
* [Storage](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)
* [Virtual desktop infrastructure](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-abbreviations)

## Tagging strategy

Another significant aspect that has to do with Azure resource management is the tagging strategy.
The benefit that the tagging strategy can have is for filtering and reporting on resources when it comes to resource usage and billing.
The image below depicts how your tags will show on your resource group:

:::image type="content" source="media/Azure-Resources-Naming-Conventions-and-tagging/HowTagsShown.jpg" alt-text="How tags are shown":::

To read more about how you can define your tagging strategy, see [Define your tagging strategy](/azure/cloud-adoption-framework/ready/azure-best-practices/resource-tagging)

> [!NOTE]
>Whenever someone creates resources, resource groups or subscriptions, you can force them to add a new tag by adding a policy definition, as shown in the image below:
>
> :::image type="content" source="media/Azure-Resources-Naming-Conventions-and-tagging/AvailableDefinitions.jpg" alt-text="Available definitions":::

## Summary

To summarize, understanding and using a clear and consistent Azure resource naming standard is essential for successful cloud adoption and administration within the Azure ecosystem. Organizations may expedite resource organization, make it simpler to identify resources, and reduce the risk of complexity in resource management by using established naming standards. Finally, a well-designed naming convention is a key component in encouraging efficiency, clarity, and scalability in cloud systems, resulting in smoother operations and increased overall productivity.
