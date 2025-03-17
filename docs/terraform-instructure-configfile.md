## Main component in config file of Terraform
##Example about config
```js
terraform {
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0"
    }
  }
  backend "azurerm" {
    resource_group_name  = "terraform-state-rg"
    storage_account_name = "tfstatebackend"
    container_name       = "tfstate"
    key                 = "terraform.tfstate"
  }
}

provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "East US"
}

output "resource_group_name" {
  value = azurerm_resource_group.example.name
}
```
1. Terraform block
- This block vbalidate require of Terraform like version, provider,...
```js
terraform {
  required_version = ">= 1.0.0"
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }
}
```
2. Provider block
 - Determine provider will use
 ```
 provider "aws" {
  region = "us-east-1"
}
```
3. Resource block
- What action will happen with resource like VM, DB. Creatre, Update or Delete
```js
resource "azurerm_virtual_network" "example" {
  name                = "example-network"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  address_space       = ["10.0.0.0/16"]
}
```
4. Varriable block
```
variable "region" {
  description = "The AWS region"
  type        = string
  default     = "us-east-1"
}
```
5. Output block
- Exported information after done
```
output "instance_ip" {
  value = aws_instance.example.public_ip
}
```
6. Backend block
- Dertermine location status of terraform saved
```js
terraform {
  backend "azurerm" {
    resource_group_name  = "terraform-state-rg"
    storage_account_name = "tfstatebackend"
    container_name       = "tfstate"
    key                 = "terraform.tfstate"
  }
}
```

## Structure of variable.tf
```
variable "location" {
  description = "Azure region"
  type        = string
  default     = "East US"
}

variable "vnet_name" {
  description = "Virtual Network Name"
  type        = string
}
```
## Standard structure of directory
```bash
terraform-project/
│── main.tf            # Calls the module
│── variables.tf       # Variables for the root module
│── outputs.tf         # Outputs from the root module
│── modules/
│   ├── network/       # Network module
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── outputs.tf
│   ├── compute/       # Compute module (VMs)
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   ├── outputs.tf
```
