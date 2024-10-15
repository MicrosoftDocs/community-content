---
title: Azure Firewall vs Azure NSGs #Required; page title displayed in search results. Don't enclose in quotation marks. 
description: Comparison of Azure Firewall and Network Security Groups for cloud protection. #Required; article description that's displayed in search results. Don't enclose in quotation marks. Do end with a period.
author: gxgrammatikos #Required; your GitHub user alias, with correct capitalization.
ms.author: mlcc-owners #Required; a Microsoft distribution list; don't change. 
ms.service: azure #Required; service per approved list.
ms.topic: conceptual #Required; leave this attribute/value as-is.
ms.date: 10/07/2024 #Required; mm/dd/yyyy format.
contributor-type: community
---

# Introduction

One of the most significant reasons a company moves its workloads to Azure is security. In this post, we'll talk about the two most prominent Azure security services, Azure Firewall and NSG. With these two services, we can manage the inbound and outbound traffic of a VNet.

**Principal author**: [George Chrysovalantis Grammatikos](/users/georgechrysovalantisgrammatikos-8518/) 

---

## Overview

Let us have a brief overview of both Azure services.

## Azure Firewall

Azure Firewall is an OSI layer 4 & 7 network security service to protect a VNet with workloads in it. It’s fully managed by Microsoft and we just need to create and configure the rules (NAT rules, Network rules, and Application rules collection), in order to secure the resources.

## Azure Firewall versions

Customers can choose from three different versions of Azure Firewall.

- Azure Firewall Basic is recommended for Small-to-Medium-Business customers (SMBs) with throughput needs under 250Mbps.
- For customers looking for a Layer 3–Layer 7 firewall that can handle peak traffic periods of up to 30 Gbps, Azure Firewall Standard is recommended. Enterprise features like threat intelligence, DNS proxy, custom DNS, and web categories are supported.
- The Azure Firewall Premium is recommended for highly sensitive applications (like payment processing). It supports malware inspection and TLS for advanced threat protection.

## Azure Firewall Features

In the links listed below, you can find the features for each SKU (Stock Keeping Unit)

- [Azure Firewall Basic SKU features](/azure/firewall/basic-features)
- [Azure Firewall Standard SKU features](/azure/firewall/features)
- [Azure Firewall Premium SKU features](/azure/firewall/premium-features)

## Deploy an Azure Firewall (Essential)

In this section, we'll walk through the steps we need to take to deploy a basic version of Azure Firewall.

## Prerequisites

- A valid Azure subscription
- Azure Firewall and Azure VNet MUST coexist in the same Resource group
- An Azure VNet
  - A subnet named AzureFirewallSubnet
  - Enabled forced tunneling
  - A subnet named AzureFirewallManagementSubnet for Force tunneling

### Azure Firewall Deployment steps

On the "**Create a firewall**" page from the [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/), choose to create a new Azure firewall having as guidance the image below:

:::image type="content" source="media/azure-firewall-vs-azure-nsg/AzFirewallCreate_img.png" alt-text="AzureFirewallCreate":::

The table below provides key directions on the first page of the configuration screen when creating an Azure firewall.

| **Setting**           | **Value** |
|-----------------------|------------------- |
| Resource group        | Create a new or select an existing Resource group |
| Name | Type the Azure Firewall name |
| Region | Select a region to deploy the resource |
| Availability zone | Select an Availability zone to achieve 99,99% availability for the service |
| Firewall SKU | Choose an Azure Firewall SKU based on the deployment features and cost needs |
| Firewall management | Choose to Use a Firewall Policy to manage this firewall |
| Firewall policy | If it does not exist, choose to create an Azure Firewall policy. |
| Virtual network | Create a new or choose an existing VNet |
| Public IP address | Create a new or choose an existing Publi IP for the Azure Firewall |
| Management public IP address | Create a new or choose an existing Publi IP for the Azure Firewall Management|

> [!NOTE]
> **Azure Firewall forced tunneling:** All internet-bound traffic from Azure Firewall is tunnelled through a designated next-hop, typically an on-premises gateway or third-party security appliance. Before traffic reaches the internet, this setup filters or logs it to meet regulatory, security, or inspection requirements.

In the next tab, we can add tags to better organize resources and select "**Next: Review + Create**" to go to the next tab.

:::image type="content" source="media/azure-firewall-vs-azure-nsg/AzFirewallTags_img.png" alt-text="AzureFirewallTags":::

At the final tab, we make a final review of the configuration and tap "**Create**" to begin the deployment of the Azure Firewall.

:::image type="content" source="media/azure-firewall-vs-azure-nsg/AzFirewallValidation_img.png" alt-text="AzureFirewallValidation":::

## Azure Network Security Groups (NSG's)

Azure NSG is an OSI Layer 3 & 4 network security service for filtering traffic from and to Azure VNet. A network security group contains a number of security rules (allow or deny). The evaluation of these security rules is done using a 5-pair hash. The 5-pair hashing depends on the source IP, source port, destination IP, destination port, and protocol type. NSGs can be associated with a VNet or with the network interface of a VM.

## Deploy a Network Security Group

A few lines up above, we have read about the deployment of an Azure Firewall here in this section, we find out how to deploy an NSG.

### Azure NSG Deployment steps

The image below shows which mandatory fields should fill up to begin the NSG deployment.

:::image type="content" source="media/azure-firewall-vs-azure-nsg/AzNSGCreate_img.png" alt-text="AzureNSGCreate":::

The final tab deals with the Configuration Overview and initiating the creation of the NSG by clicking the Create button.:::image type="content" source="media/azure-firewall-vs-azure-nsg/AzNSGValidation_img.png" alt-text="AzureNSGValidation":::

### Default Azure Network Security Group (NSG) Rules

The first time an NSG is deployed, it includes a set of preset security rules for inbound and outbound connections. In the image below, we can see these rules.

:::image type="content" source="media/azure-firewall-vs-azure-nsg/AzNSGRules_img.png" alt-text="AzureNSGRules":::

## Summarize

In this post, we have read about what is and how to deploy an Azure Firewall and an Azure NSG. The reality is that each service offers security at different network layers. NSGs protect inbound and outbound network traffic, and Azure Firewall can filter network traffic using more intelligence. We can have NSG on a VM, and at the same time, we can have an Azure Firewall to protect the resources running on a VNet. Eventually, we also need to point out that NSG is another layer of security for a VM because, in addition to the security provided by the firewall, there is additional security on the network ports of the VMs.
