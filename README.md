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
Check whether RG was properly created.
```
az group list --output table
```
<img width="422" height="93" alt="image" src="https://github.com/user-attachments/assets/3f187405-5e60-4c1a-b2da-1f4adb44e9c7" />

## ARM Template
* Create a json file in vscode. I named it as azuredeploy.json
* In the file type arm-storage. When you hit enter, it will automatically load the required template in to the file.
* Now change the value of the storage account name to make it unique. I named it as gaganistorageaccount1.

<img width="548" height="523" alt="image" src="https://github.com/user-attachments/assets/f78df653-4f75-4f3c-a796-4b73c6ae6ce8" />

## Deploy the Storage Account Using ARM Template
Navigate to the folder containing your ARM template (01-storage-account.json), then run:
```
az deployment group create \
  --resource-group vscode \
  --template-file gaganistorageaccount1
```
Check whether SA is created inside the above RG.
```
 az resource list --resource-group vscode --output table
```
<img width="807" height="122" alt="image" src="https://github.com/user-attachments/assets/b2cb15bf-b676-4003-9ff1-4c8b257bb3a6" />

