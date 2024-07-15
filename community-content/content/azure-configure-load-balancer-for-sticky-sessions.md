---
title: Configure Azure Load Balancer for sticky sessions #Required; page title displayed in search results. Don't enclose in quotation marks. 
description: Configure Azure Load Balancer for sticky sessions #Required; article description that's displayed in search results. Don't enclose in quotation marks. Do end with a period.
author: gxgrammatikos #Required; your GitHub user alias, with correct capitalization.
ms.author: mlcc-owners #Required; a Microsoft distribution list; don't change. 
ms.service: azure #Required; service per approved list.
ms.topic: conceptual #Required; leave this attribute/value as-is.
ms.date: 03/20/2024 #Required; mm/dd/yyyy format.
contributor-type: community
---

# Configure Azure Load Balancer for sticky sessions

The purpose of this post is to describe how we can configure a sticky session on an Azure Load Balancer and explain what a sticky session is in simple terms.

## Sticky sessions

When a client starts a session in one of the web servers, session stays in that specific server.

:::image type="content" source="media/azure-configure-load-balancer-for-sticky-sessions/8510.sticky_2d00_sessions.jpg" alt-text="stickysessions":::

## Non-Sticky Sessions

 In a Non-sticky Session example. session are dispatched randomly , depending on the load balancing policy.
:::image type="content" source="media/azure-configure-load-balancer-for-sticky-sessions/8004.non_2d00_sticky_2d00_sessions.jpg" alt-text="nonstickysessions":::

## Configure Azure Load Balancer for sticky sessions

In the following image we can see sticky session configuration from Azure Portal.

:::image type="content" source="media/azure-configure-load-balancer-for-sticky-sessions/0250.load_2d00_balancer.png" alt-text="loadbalancersetting":::

## Related links

- [Load Balancer](/azure/load-balancer/)
- [Configure Azure Load Balancer For Sticky Sessions](https://cloudopszone.com/configure-azure-load-balancer-for-sticky-sessions/)
- [Configure the distribution mode for Azure Load Balancer](/azure/load-balancer/load-balancer-distribution-mode)
