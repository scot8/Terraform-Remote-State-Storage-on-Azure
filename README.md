# Terraform Remote State Storage on Azure

A lightweight infrastructure-as-code project demonstrating how to configure remote state management using **Azure Blob Storage** with **Terraform**. This ensures secure, centralized, and collaborative handling of infrastructure state files—crucial for teams and CI/CD pipelines.

---

## 🌐 Project Summary

This project provisions the following resources on Azure using Terraform:

- A **dedicated resource group** to isolate backend infrastructure
- An **Azure Storage Account** and **Blob Container** for storing Terraform state remotely
- A **virtual network (VNet)** and a **subnet**, managed via the Terraform state stored in Azure Blob Storage

Remote state configuration allows safe collaboration across multiple team members and environments by ensuring only one source of truth for infrastructure state.

---

## 🧰 Technologies Used

- **Terraform v1.5+**
- **Azure CLI**
- **Azure Resource Manager (ARM)**
- **Backend: Azure Blob Storage**
- **IaC Concepts: Remote state, VNet, Subnet**

---

## 📁 Project Structure

```bash
terraform-remote-state-azure/
├── terraform/
│   ├── main.tf           # Main infrastructure definitions
│   └── terraform.tf      # Backend configuration for remote state
├── .gitignore
└── README.md
```

---

## 🔧 Setup Instructions

### 1. Clone the repo and initialize the project

```bash
git clone https://github.com/yourusername/terraform-remote-state-azure.git
cd terraform-remote-state-azure/terraform
```

### 2. Create an Azure Resource Group and Storage Account

```bash
az group create --name scot0568-tf-backend --location westus3

az storage account create \
  --name 04117220tfstorage \
  --resource-group scot0568-tf-backend \
  --location westus3 \
  --sku Standard_LRS

az storage container create \
  --name tfstate \
  --account-name 04117220tfstorage
```

---

## 🔐 Configure Backend

```hcl
# terraform/terraform.tf

terraform {
  required_version = "~> 1.5"

  backend "azurerm" {
    resource_group_name  = "scot0568-tf-backend"
    storage_account_name = "04117220tfstorage"
    container_name       = "tfstate"
    key                  = "dev.terraform.tfstate"
  }

  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "3.96.0"
    }
  }
}
```

---

## 🏗️ Define Resources

```hcl
# terraform/main.tf

provider "azurerm" {
  features {}
}

variable "resource_prefix" {
  description = "A prefix for all resources"
  type        = string
  default     = "scot0568"
}

resource "azurerm_resource_group" "rg" {
  name     = "${var.resource_prefix}-infra-rg"
  location = "westus3"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "${var.resource_prefix}-vnet"
  resource_group_name = azurerm_resource_group.rg.name
  location            = azurerm_resource_group.rg.location
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "subnet" {
  name                 = "${var.resource_prefix}-subnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
}
```

---

## ⚙️ Deploy Infrastructure

### 1. Export access key

```bash
export ARM_ACCESS_KEY=$(az storage account keys list \
  --account-name 04117220tfstorage \
  --resource-group scot0568-tf-backend \
  --query '[0].value' -o tsv)
```

### 2. Initialize and Apply

```bash
cd terraform
terraform init
terraform plan -out=tfplan
terraform apply tfplan
```

---

## ✅ Outcome

- Resources are provisioned and managed by Terraform
- The `.tfstate` file is securely stored in Azure Blob Storage
- Collaboration-safe and CI/CD-ready infrastructure setup

---

## 🧹 Cleanup

To tear down the infrastructure:

```bash
terraform destroy
```

---
