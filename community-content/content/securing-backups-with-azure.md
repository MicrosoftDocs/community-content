---
title: Securing your Microsoft Azure Backups
description: How to use features such as Resource Guard, and immutability to secure your Microsoft Azure backups. 
author: jehchow 
ms.author: shchow 
ms.service: azure
ms.topic: how-to 
ms.date: 09/21/2023
ms.custom: template-how-to-pattern 
content_well_notification: 
  - Human-created-Community
---

# Securing your Microsoft Azure Backups

---

**Principal author**: [Luke Murray](https://learn.microsoft.com/users/lukemurraynz/?WT.mc_id=AZ-MVP-5004796)

---

So, you have enabled your Azure Virtual Machine or Azure File Share backups using Azure Backup services and see your first Backup job run… great!

**Protecting your workloads and their backups doesn't stop there.**

Backups are vital to the continuity of your workloads, whether recovering from a malware attack, reverting recent changes as part of a Change Control, or storing your data for legal requirements – having backups of your data can be essential to the continued running of your services, so you want to protect them as much as possible.

Over the last few years, as security concerns have grown across a plethora of platforms and services, Microsoft Backup as a product hasn't stood still, with numerous features such as:

* The transition of the Backup alerts from Classic to Azure Monitor allows you to take advantage of action groups, email/SMS notifications, Azure Mobile app notifications and automation on failed backups or if someone deletes a backup job.
* Support for Azure Virtual Machines, running Premium SSD v2, with v2 Premium SSD offering the next level of storage performance early on.
* Azure Backup support for Confidential Virtual Machines using Customer Managed Keys
* SAP HANA system replication backup support on Azure Virtual Machines
* Greater visibility with Azure Backup Reports lets you see your backup compliance visually.

This is just a list of SOME of the recent changes over the past few months at the time this article was written; as always, you can review the Azure updates page for the latest updates, but hopefully, this gives you a taste that the environment that is securing and storing your backups doesn't stand still.

So let us go on a journey to protect our backups from malicious attacks and, frankly – ourselves!

Let's us take a look at:

* Immutable vault
* Soft delete
* Resource Guard
* Enhanced policy
* Intelligent Tiering

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

<!-- 5. Next steps ------------------------------------------------------------------------

Required: Provide at least one next step and no more than three. Include some context so the 
customer can determine why they would click the link.
Add a context sentence for the following links.

-->

## Next steps

TODO: Add your next step link(s)

<!--
Remove all the comments in this template before you sign-off or merge to the main branch.

-->
