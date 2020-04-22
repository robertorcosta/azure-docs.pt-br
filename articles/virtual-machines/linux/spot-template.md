---
title: Use um modelo para implantar VMs do Azure Spot
description: Aprenda a usar um modelo para implantar VMs Spot para economizar custos.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 2d546e9154352ec90aa1b1a457eb5320979239d2
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758357"
---
# <a name="deploy-spot-vms-using-a-resource-manager-template"></a>Implantar VMs spot usando um modelo de gerenciador de recursos

O uso [de VMs Spot](spot-vms.md) permite que você aproveite nossa capacidade não utilizada com uma economia significativa de custos. A qualquer momento em que o Azure precisar da capacidade de volta, a infra-estrutura do Azure despejará VMs spot. Portanto, as VMs spot são ótimas para cargas de trabalho que podem lidar com interrupções como trabalhos de processamento em lote, ambientes de dev/teste, grandes cargas de trabalho de computação e muito mais.

Os preços das VMs spot são variáveis, com base na região e no SKU. Para obter mais informações, consulte os preços da VM para [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) e [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/).

Você tem opção de definir um preço máximo que você está disposto a pagar, por hora, para a VM. O preço máximo de um VM Spot pode ser definido em dólares americanos (USD), usando até 5 casas decimais. Por exemplo, `0.98765`o valor seria um preço máximo de US$ 0,98765 por hora. Se você definir o `-1`preço máximo para ser, a VM não será despejada com base no preço. O preço da VM será o preço atual para spot ou o preço de uma VM padrão, que seja menor, desde que haja capacidade e cota disponíveis. Para obter mais informações sobre como definir o preço máximo, consulte [Spot VMs - Pricing](spot-vms.md#pricing).


## <a name="use-a-template"></a>Como usar um modelo

Para implantações de`"apiVersion": "2019-03-01"` modelo spot, use ou posteriormente. Adicione `priority`as `evictionPolicy` `billingProfile` propriedades e propriedades do modelo:

```json
"priority": "Spot",
"evictionPolicy": "Deallocate",
"billingProfile": {
    "maxPrice": -1
}
```

Aqui está um modelo de exemplo com as propriedades adicionadas para uma VM Spot. Substitua os nomes dos `<password>` recursos por seus próprios e por uma senha para a conta do administrador local na VM.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2019-03-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
    },
    "variables": {
        "vnetId": "/subscriptions/ec9fcd04-e188-48b9-abfc-abcd515f1836/resourceGroups/spotVM/providers/Microsoft.Network/virtualNetworks/spotVM",
        "subnetName": "default",
        "networkInterfaceName": "spotVMNIC",
        "publicIpAddressName": "spotVM-ip",
        "publicIpAddressType": "Dynamic",
        "publicIpAddressSku": "Basic",
        "virtualMachineName": "spotVM",
        "osDiskType": "Premium_LRS",
        "virtualMachineSize": "Standard_D2s_v3",
        "adminUsername": "azureuser",
        "adminPassword": "<password>",
        "diagnosticsStorageAccountName": "diagstoragespot2019",
        "diagnosticsStorageAccountId": "Microsoft.Storage/storageAccounts/diagstoragespot2019",
        "diagnosticsStorageAccountType": "Standard_LRS",
        "diagnosticsStorageAccountKind": "Storage",
        "subnetRef": "[concat(variables('vnetId'), '/subnets/', variables('subnetName'))]"
    },
    "resources": [
        {
            "name": "spotVM",
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIpAddresses/', variables('publicIpAddressName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            },
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIpAddress": {
                                "id": "[resourceId(resourceGroup().name, 'Microsoft.Network/publicIpAddresses', variables('publicIpAddressName'))]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "name": "[variables('publicIpAddressName')]",
            "type": "Microsoft.Network/publicIpAddresses",
            "apiVersion": "2019-02-01",
            "location": "eastus",
            "properties": {
                "publicIpAllocationMethod": "[variables('publicIpAddressType')]"
            },
            "sku": {
                "name": "[variables('publicIpAddressSku')]"
            }
        },
        {
            "name": "[variables('virtualMachineName')]",
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2019-03-01",
            "location": "eastus",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', variables('networkInterfaceName'))]",
                "[concat('Microsoft.Storage/storageAccounts/', variables('diagnosticsStorageAccountName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('virtualMachineSize')]"
                },
                "storageProfile": {
                    "osDisk": {
                        "createOption": "fromImage",
                        "managedDisk": {
                            "storageAccountType": "[variables('osDiskType')]"
                        }
                    },
                    "imageReference": {
                        "publisher": "Canonical",
                        "offer": "UbuntuServer",
                        "sku": "18.04-LTS",
                        "version": "latest"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('networkInterfaceName'))]"
                        }
                    ]
                },
                "osProfile": {
                    "computerName": "[variables('virtualMachineName')]",
                    "adminUsername": "[variables('adminUsername')]",
                    "adminPassword": "[variables('adminPassword')]"
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[concat('https://', variables('diagnosticsStorageAccountName'), '.blob.core.windows.net/')]"
                    }
                },
                "priority": "Spot",
                "evictionPolicy": "Deallocate",
                "billingProfile": {
                    "maxPrice": -1
                }
            }
        },
        {
            "name": "[variables('diagnosticsStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-04-01",
            "location": "eastus",
            "properties": {},
            "kind": "[variables('diagnosticsStorageAccountKind')]",
            "sku": {
                "name": "[variables('diagnosticsStorageAccountType')]"
            }
        }
    ],
    "outputs": {
        "adminUsername": {
            "type": "string",
            "value": "[variables('adminUsername')]"
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Você também pode criar uma VM spot usando [o Azure PowerShell](../windows/spot-powershell.md) ou o [Azure CLI](spot-cli.md).

Se você encontrar um erro, consulte [Códigos de erro](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).