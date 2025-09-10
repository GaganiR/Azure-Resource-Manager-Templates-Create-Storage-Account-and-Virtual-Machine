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
# Deploying a VM through ARM
## Create new JSON file
* Created a new file as create-vm.json
* In the file type arm and select the template.
* The following lines will be filled in.
* 
<img width="626" height="337" alt="image" src="https://github.com/user-attachments/assets/d2fb0ad3-e1f0-4496-afaf-9b68826932a2" />

In the "resourses" section, type arm-vm and select the ubuntu option.

<img width="513" height="258" alt="image" src="https://github.com/user-attachments/assets/b013cfd3-2c00-4414-8fc2-0a7bde0df189" />

The full template will be filled with sections for NSG, public IPAddress, virtual Network like so:

<img width="602" height="797" alt="image" src="https://github.com/user-attachments/assets/a025f7c4-5414-4048-9aa2-53e01683128b" />

Changed the name of the vm as arm-vm:

<img width="458" height="83" alt="image" src="https://github.com/user-attachments/assets/f497ec75-5485-4149-8c8a-cb445dc1cf40" />

Change the admin username and password like:

<img width="493" height="102" alt="image" src="https://github.com/user-attachments/assets/46fe635a-57b6-4385-8bce-2b29a954052b" />
