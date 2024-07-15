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

**Principal author**: [George Chrysovalantis Grammatikos](/users/georgechrysovalantisgrammatikos-8518/) <!-- Add your name and a link to your Learn profile or community leader page>

---

## Introduction

There are many times when we need to do operations on Azure VMs. 
In this post, we will read how easily we can Start, Stop, or Restart massively Azure virtual machines via Azure Portal or Azure PowerShell.

## Start, Stop VM's via Azure Portal

[![Video that shows how to start and stop virtual machines using the Azure portal](https://img.youtube.com/vi/4UlfI9MzRF0/0.jpg)](https://www.youtube.com/watch?v=4UlfI9MzRF0)

## Start, Stop VM's via Azure Cloud Shell

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

- [Install Azure PowerShell](/powershell/azure/install-az-ps?view=azps-4.4.0)
- [Get-AzVM (Module: Az.Compute)](/powershell/module/az.compute/get-azvm?view=azps-4.4.0)
- [Start-AzVM (Module: Az.Compute)](/powershell/module/Az.Compute/Start-AzVM?view=azps-4.4.0)
- [Stop-AzVM (Module: Az.Compute)](/powershell/module/az.compute/stop-azvm?view=azps-4.4.0)
- [Restart-AzVM (Module: Az.Compute)](/powershell/module/Az.Compute/Restart-AzVM?view=azps-4.4.0)