---
title: Az Terraform - Deploy Resource Group and Azure Blob Storage #Required; page title displayed in search results. Don't enclose in quotation marks. 
description: How to deploy resource group and Azure Blob Storage using Azure Terraform #Required; article description that's displayed in search results. Don't enclose in quotation marks. Do end with a period.
author: gxgrammatikos #Required; your GitHub user alias, with correct capitalization.
ms.author: cahublou #Required; a Microsoft employee's alias; don't change. 
ms.service: azure #Required; service per approved list.
ms.topic: Azure #Required; leave this attribute/value as-is.
ms.date: 01/22/2024 #Required; mm/dd/yyyy format.
content_well_notification: 
  - Human-created-Community #Required; don't change.
---

# Azure Terraform: Deploy Resource Group and Azure Blob Storage

---
**Principal author**: [George Chrysovaladis Grammatikos](/users/georgechrysovalantisgrammatikos-8518/)

---

## Let's demo it

Occasionally, Terraform versions in Cloud Shell might be outdated. To update, type the commands below.

### Update Terraform if needed

Occasionally, Terraform versions in Cloud Shell might be outdated. To update, type the commands below.

```terraform


terraform
# Identify the Terraform version that is currently being utilized in Cloud Shell.
terraform version

# Get the link to Terraform's download page, https://www.terraform.io/downloads.html
# Download the latest version

curl -O https://releases.hashicorp.com/terraform/1.4.6/terraform_1.4.6_linux_amd64.zip

# Unzip the file

unzip terraform_1.4.6_linux_amd64.zip

# If the directory does not already exist, make one called bin.

mkdir bin

# Move the terraform file to the bin directory.

mv terraform bin/

# Restart Azure Cloud Shell to update the Terraform version
# Verify that the version has changed

terraform version

```

> [!NOTE]
> You can read more about the above commands from the official MS documentation, which I used for this demo. [Configure Terraform in Azure Cloud Shell with Bash](/azure/developer/terraform/get-started-cloud-shell-bash?tabs=bash)

## Deploy Azure Blob Storage

By following the below code steps, you can deploy an Azure Resource Group and Azure Blob Storage.

* Open the [nano editor](https://www.nano-editor.org/docs.php) to add the Terraform configuration code.

```terraform

george [ ~ ]$ nano blobstorageterraform.tf

 ```

* Update the necessary values to the properties which are into braces.

> [!IMPORTANT]
> To save the changes to the file, press Ctrl + ^ + X, then choose Y or type (Yes) and press Enter.

```terraform

terraform{
        required_providers {
            azurerm = {
              source = "hashicorp/azurerm"
                }
           }
  }
provider "azurerm" {
        features {}
   }
resource "azurerm_resource_group" "{resource group}" {
        name="{resource group}"
        location = "westeurope"
  }
resource "azurerm_storage_account" "{storage account}" {
                name = "{storage account}"
                resource_group_name = azurerm_resource_group.{resource group}.name
                location = azurerm_resource_group.{resource group}.location
                account_tier = "Standard"
                account_replication_type = "LRS"
  }
resource "azurerm_storage_container" "images" {
                name = "images"
                storage_account_name = azurerm_storage_account.{storage account}.name
                container_access_type = "private"
        }

```

* The next command initiates Terraform

```terraform
terraform init
```

* The terraform plan command creates an execution plan and lets you examine Terraforms actions to deploy your infrastructure.

```terraform
terraform plan -out {name}.tfplan
```

* Terraform apply command, executes the above terraform plan and deploys the resources.

```terraform
terraform apply "{name}.tfplan"
```

## Demo in practice

The video link below includes all the above steps with the resources deployed.

* [demo in practice] - [Azure Terraform - Azure Blob Storage (En)](https://www.youtube.com/watch?v=bPNRc8uOq6E)

---
