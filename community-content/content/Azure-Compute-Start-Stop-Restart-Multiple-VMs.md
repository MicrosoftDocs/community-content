---
title: Azure Compute -Start, Stop , And Restart Multiple VMs #Required; page title displayed in search results. Don't enclose in quotation marks. 
description: Azure Compute -Start, Stop , And Restart Multiple VMs #Required; article description that's displayed in search results. Don't enclose in quotation marks. Do end with a period.
author: gxgrammatikos #Required; your GitHub user alias, with correct capitalization.
ms.author: mlcc-owners #Required; a Microsoft distribution list; don't change. 
ms.service: azure #Required; request from Microsoft admin. 
ms.topic: conceptual #Required; leave this attribute/value as-is.
ms.date: 07/13/2024 #Required; mm/dd/yyyy format.
contributor-type: community #Required; don't change.

---

# Azure Compute -Start, Stop , And Restart Multiple VMs

---

**Principal author**: [George Chrysovalantis Grammatikos](/users/georgechrysovalantisgrammatikos-8518/)

---

## Introduction

In today's dynamic cloud computing landscape, effectively managing virtual machines (VMs) is essential for maintaining optimal performance and cost-effectiveness.Microsoft Azure, a leading cloud service provider, offers robust tools and resources to facilitate this management. One such powerful tool is PowerShell, a task automation and configuration management framework from Microsoft.

This article delves into the practical aspects of using Azure PowerShell to manage Azure VMs. Specifically, we will explore the steps to start, stop, and restart Azure VMs using PowerShell commands. No matter what your role is, whether you are a system administrator, a DevOps engineer, or a member of the IT department, mastering these commands will greatly streamline your workflow, reduce manual intervention, and ensure your virtual machines run as they should.

## Start, Stop VM's via Azure Portal

In this video, we'll walk you through the easy procedures of starting and stopping virtual machines, allowing you to manage your cloud resources more efficiently via the Azure Portal.

[![Video that shows how to start and stop virtual machines using the Azure portal](https://img.youtube.com/vi/4UlfI9MzRF0/0.jpg)](https://www.youtube.com/watch?v=4UlfI9MzRF0)

## Start, Stop VM's via Azure Cloud Shell

In the next video, you will discover how to start and stop Azure Virtual Machines (VMs) using the Azure Cloud Shell.

[![Video that shows how to start and stop virtual machines using the Azure cloud shell](https://img.youtube.com/vi/4EK_RSF6zY0/0.jpg)](https://www.youtube.com/watch?v=4EK_RSF6zY0)

Please check below the Azure PowerShell scripts to Start, Stop, and Restart massive Virtual Machines.

```powershell
#Start Azure VMs 
$ResourceGroupName = "MyVMRG"
Get-AzVM -ResourceGroupName $ResourceGroupName | Select Name | ForEach-Object { Start-AzVM -ResourceGroupName $ResourceGroupName -Name $_.Name }
 
#Stop Azure VMs 
Get-AzVM -ResourceGroupName $ResourceGroupName | Select Name | ForEach-Object { Stop-AzVM -ResourceGroupName $ResourceGroupName -Name $_.Name }
 
#Restart Azure VMs 
Get-AzVM -ResourceGroupName $ResourceGroupName | Select Name | ForEach-Object { Restart-AzVM -ResourceGroupName $ResourceGroupName -Name $_.Name }

```

## Useful links

- [Install Azure PowerShell](/powershell/azure/install-az-ps)
- [Get-AzVM (Module: Az.Compute)](/powershell/module/az.compute/get-azvm)
- [Start-AzVM (Module: Az.Compute)](/powershell/module/Az.Compute/Start-AzVM)
- [Stop-AzVM (Module: Az.Compute)](/powershell/module/az.compute/stop-azvm)
- [Restart-AzVM (Module: Az.Compute)](/powershell/module/Az.Compute/Restart-AzVM)