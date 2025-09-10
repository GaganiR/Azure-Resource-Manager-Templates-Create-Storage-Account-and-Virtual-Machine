# Azure Storage Account and VM Deployment via ARM Template
This project demonstrates how to deploy an Azure Storage Account using an ARM (Azure Resource Manager) template and Azure CLI. It includes a simple JSON template and provisions resources in a specified resource group.
## Prerequisites
* Azure CLI installed and authenticated (az login)
* Active Azure subscription
* Azure Resource Manager Template installed in VSCode.
## Create a Resource Group
This creates a resource group named vscode in the South East Asia region.
```
az group create --name vscode --location southeastasia
```
## ARM Template
* Create a json file in vscode. I named it as azuredeploy.json
* In the file type arm-storage. When you hit enter, it will automatically load the required template in to the file.
* Now change the value of the storage account name to make it unique.
<img width="548" height="523" alt="image" src="https://github.com/user-attachments/assets/f78df653-4f75-4f3c-a796-4b73c6ae6ce8" />

## Deploy the Storage Account Using ARM Template

