### Why we need Terraform?
Normally, When we need build infrastructure on Cloud, We will access that portal to control everything. But it is getting more and more big and difficult to manage it anh Terraform guy come next to you and told "Let me" 🐶

### 
![Terrform workflow](https://images.viblo.asia/cb965d4e-4095-4159-93f6-980ef3a783cc.png)

### Main commands we often face

```bash
terraform init
terrform plan
terraform apply
```
> Terraform will use tf to execute the code inline

## Structure of Terraform 
1. Configuration file
- This is .tf file or tf.json, include IaC
- It describle *provide, resource, module,..*
**Example**
```bash
provider "azurerm" {
    feature{}
}
resource "azurerm_resourcegroup" "example" {
    name = "lab_resource"
    location = "East US"
}

```
2. Providers
- This is public cloud you use for infrastructure like Azure, AWS or GCP,...
```js
providers "aws"
providers "azurerm" #Azure Resource Manager
providers "google"
providers "kubernestes"
```
3. Resources
- Represent for infrastructure component such as VM, DB, VPC,...
```js
resource "azurerm_virtual_network" "example" {
  name                = "example-network"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  address_space       = ["10.0.0.0/16"]
}
```
4. Module
- To reuse and restrict make a mistake, *module* born to organize and manage better
```js
module "network" {
  source = "./modules/network"
  vnet_name = "my-vnet"
  location = "East US"
}
```
5. State
- Using to monitor the changing and avoid conflic when we apply new change. It's storge status of infrastrutre on `terraform.tsstate`
- Can local storage or storage on cloud
6. Terraform CLI
