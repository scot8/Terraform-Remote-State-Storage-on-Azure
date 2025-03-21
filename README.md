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
