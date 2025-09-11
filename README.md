# Azure Storage Account and VM Deployment via ARM Template
This project demonstrates how to deploy an Azure Storage Account and a fully configured Ubuntu virtual machine using an ARM (Azure Resource Manager) template and Azure CLI. It includes a simple JSON template and provisions resources in a specified resource group.
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

<img width="626" height="337" alt="image" src="https://github.com/user-attachments/assets/d2fb0ad3-e1f0-4496-afaf-9b68826932a2" />

## VM Configuration Details
__OS:__ Ubuntu 22.04 LTS (Canonical publisher)

__Size:__ Standard_A2_v2

__Admin Username:__ azureuser

__Admin Password:__ azureuser@123 (change this in production!)

__SSH Access:__ Port 22 allowed via NSG

In the "resourses" section, type arm-vm and select the ubuntu option.

<img width="513" height="258" alt="image" src="https://github.com/user-attachments/assets/b013cfd3-2c00-4414-8fc2-0a7bde0df189" />

The full template will be filled with sections for NSG, public IPAddress, virtual Network like so:

<img width="602" height="797" alt="image" src="https://github.com/user-attachments/assets/a025f7c4-5414-4048-9aa2-53e01683128b" />

Changed the name of the vm as arm-vm:

<img width="458" height="83" alt="image" src="https://github.com/user-attachments/assets/f497ec75-5485-4149-8c8a-cb445dc1cf40" />

Change the admin username and password like:

<img width="493" height="102" alt="image" src="https://github.com/user-attachments/assets/46fe635a-57b6-4385-8bce-2b29a954052b" />

```
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "functions": [],
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2024-03-01",
            "name": "networkSecurityGroupName",
            "location": "[resourceGroup().location]",
            "properties": {
                "securityRules": [
                    {
                        "name": "ssh_rule",
                        "properties": {
                            "description": "Locks inbound down to ssh default port 22.",
                            "protocol": "Tcp",
                            "sourcePortRange": "*",
                            "destinationPortRange": "22",
                            "sourceAddressPrefix": "*",
                            "destinationAddressPrefix": "*",
                            "access": "Allow",
                            "priority": 123,
                            "direction": "Inbound"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2024-03-01",
            "name": "ubuntuVM1-PublicIP",
            "location": "[resourceGroup().location]",
            "properties": {
                "publicIPAllocationMethod": "Static"
            },
            "sku": {
                "name": "Standard"
            },
            "zones": ["1"]
        },
        {
            "comments": "Simple Network Security Group for subnet [variables('vNetSubnetName')]",
            "type": "Microsoft.Network/networkSecurityGroups",
            "apiVersion": "2024-03-01",
            "name": "UBUNTUvm1-NSG",
            "location": "[resourceGroup().location]",
            "properties": {
                "securityRules": [
                    {
                        "name": "default-allow-22",
                        "properties": {
                            "priority": 1000,
                            "access": "Allow",
                            "direction": "Inbound",
                            "destinationPortRange": "22",
                            "protocol": "Tcp",
                            "sourceAddressPrefix": "*",
                            "sourcePortRange": "*",
                            "destinationAddressPrefix": "*"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2024-03-01",
            "name": "ubuntuVM1-vnet",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkSecurityGroups', 'UBUNTUvm1-NSG')]"
            ],
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "10.0.0.0/16"
                    ]
                },
                "subnets": [
                    {
                        "name": "ubuntuVM1-vnet-subnet",
                        "properties": {
                            "addressPrefix": "10.0.0.0/24",
                            "networkSecurityGroup": {
                                "id": "[resourceId('Microsoft.Network/networkSecurityGroups', 'UBUNTUvm1-NSG')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2024-03-01",
            "name": "ubuntuVM1-networkInterface",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses', 'ubuntuVM1-PublicIP')]",
                "[resourceId('Microsoft.Network/virtualNetworks', 'ubuntuVM1-vnet')]",
                "[resourceId('Microsoft.Network/networkSecurityGroups', 'UBUNTUvm1-NSG')]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses', 'ubuntuVM1-PublicIP')]"
                            },
                            "subnet": {
                                "id": "[resourceId('Microsoft.Network/virtualNetworks/subnets', 'ubuntuVM1-vnet', 'ubuntuVM1-vnet-subnet')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2024-07-01",
            "name": "arm-vm",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/networkInterfaces', 'ubuntuVM1-networkInterface')]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "Standard_A2_v2"
                },
                "osProfile": {
                    "computerName": "arm-vm",
                    "adminUsername": "azureuser",
                    "adminPassword": "azureuser@123"
                    
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "0001-com-ubuntu-server-jammy",
                        "sku": "22_04-lts",
                        "version": "latest"
                    },
                    "osDisk": {
                        "createOption": "fromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', 'ubuntuVM1-networkInterface')]"
                        }
                    ]
                }
            }
        }
    ],
    "outputs": {}
}
```

Deploy the vm

<img width="792" height="83" alt="image" src="https://github.com/user-attachments/assets/e6fa7380-bfee-42da-8302-007b251cc428" />

## Post-Deployment Verification
To verify the VM and its resources:
```
az resource list --resource-group vscode --output table
```
<img width="1455" height="177" alt="image" src="https://github.com/user-attachments/assets/fd45a5c3-092f-4d30-8281-0c6a27a6213d" />

Check from the portal as well.

<img width="996" height="372" alt="image" src="https://github.com/user-attachments/assets/287354c2-7937-45f3-9184-3359d58c8aa8" />

