---
title: Início rápido da implantação automatizada de VM com a Configuração de Aplicativos do Azure
description: Este início rápido demonstra como usar o módulo do Azure PowerShell e os modelos do Azure Resource Manager para implantar um repositório da Configuração de Aplicativos do Azure. Em seguida, use os valores no repositório para implantar uma VM.
author: lisaguthrie
ms.author: lcozzens
ms.date: 04/14/2020
ms.topic: quickstart
ms.service: azure-app-configuration
ms.custom:
- mvc
- subject-armqs
ms.openlocfilehash: 02afa2cb36323e0c3c38c2451b1924b636f7faed
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309103"
---
# <a name="quickstart-automated-vm-deployment-with-app-configuration-and-resource-manager-template"></a>Início Rápido: Implantação automatizada de VM com a Configuração de Aplicativos e o modelo do Resource Manager

O módulo do Azure PowerShell é usado para criar e gerenciar recursos do Azure usando scripts ou cmdlets do PowerShell. Este início rápido mostra como usar o Azure PowerShell e os modelos do Azure Resource Manager para implantar um repositório da Configuração de Aplicativos do Azure. Em seguida, você aprenderá a usar os pares chave-valor no repositório para implantar uma VM.

Use o modelo de pré-requisito para criar um repositório da Configuração de Aplicativos e, em seguida, adicione pares chave-valor no repositório usando o portal do Azure ou a CLI do Azure. O modelo primário referencia as configurações de pares chave-valor existentes de um repositório de configurações existente. Os valores recuperados são usados para definir as propriedades dos recursos criados pelo modelo, como uma VM, neste exemplo.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="before-you-begin"></a>Antes de começar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* Se você não tiver uma assinatura do Azure, crie uma [conta gratuita.](https://azure.microsoft.com/free/)

* Este início rápido requer o módulo Azure PowerShell. Execute `Get-Module -ListAvailable Az` para localizar a versão que está instalada em seu computador local. Se você precisar instalá-lo ou atualizá-lo, confira [Instalar o módulo do Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

## <a name="sign-in-to-azure"></a>Entrar no Azure

Entre em sua assinatura do Azure com o comando `Connect-AzAccount` e insira suas credenciais do Azure no navegador pop-up:

```azurepowershell-interactive
# Connect to your Azure account
Connect-AzAccount
```

Caso tenha mais de uma assinatura, selecione a assinatura que deseja usar neste início rápido executando os cmdlets a seguir. Não se esqueça de substituir `<your subscription name>` pelo nome da sua assinatura:

```azurepowershell-interactive
# List all available subscriptions.
Get-AzSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzSubscription -SubscriptionName "<your subscription name>" | Select-AzSubscription
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos do Azure com [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados.

```azurepowershell-interactive
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzResourceGroup `
    -Name $resourceGroup `
    -Location $location
```

## <a name="deploy-an-azure-app-configuration-store"></a>Implantar um repositório da Configuração de Aplicativos do Azure

Antes de aplicar pares chave-valor à VM, você precisará ter um repositório da Configuração de Aplicativos do Azure existente. Esta seção fornece detalhes sobre como implantar um repositório da Configuração de Aplicativos do Azure usando um modelo do Azure Resource Manager. Caso já tenha um repositório de configuração de aplicativo, vá para a próxima seção deste artigo. 

1. Copie e cole o código JSON a seguir em um novo arquivo chamado *prereq.azuredeploy.json*.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "type": "string",
        "metadata": {
          "description": "Specifies the name of the app configuration store."
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Specifies the Azure location where the app configuration store should be created."
        }
      },
      "skuName": {
        "type": "string",
        "defaultValue": "standard",
        "metadata": {
          "description": "Specifies the SKU of the app configuration store."
        }
      }
    },
    "resources": [
      {
        "type": "Microsoft.AppConfiguration/configurationStores",
        "name": "[parameters('configStoreName')]",
        "apiVersion": "2019-10-01",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('skuName')]"
        }
      }
    ]
   }
   ```

1. Copie e cole o código JSON a seguir em um novo arquivo chamado *prereq.azuredeploy.parameters.json*. Substitua **GET-UNIQUE** por um nome exclusivo para o repositório de configurações.

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "configStoreName": {
        "value": "GET-UNIQUE"
      }
    }
   }
   ```

1. Na janela do PowerShell, execute o comando a seguir para implantar o repositório da Configuração de Aplicativos do Azure. Não se esqueça de substituir o nome do grupo de recursos, o caminho do arquivo de modelo e o caminho do arquivo de parâmetro do modelo.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>" 
       -TemplateFile "<path to prereq.azuredeploy.json>" `
       -TemplateParameterFile "<path to prereq.azuredeploy.parameters.json>"
   ```

## <a name="add-vm-configuration-key-values"></a>Adicionar pares chave-valor de configuração da VM

Você pode criar um repositório da Configuração de Aplicativos usando um modelo do Azure Resource Manager, mas precisará adicionar pares chave-valor usando o portal do Azure ou a CLI do Azure. Neste início rápido, você adicionará pares chave-valor usando o portal do Azure.

1. Depois que a implantação for concluída, navegue até o repositório da Configuração de Aplicativos recém-criado no [portal do Azure](https://portal.azure.com).

1. Selecione **Configurações** > **Chaves de Acesso**. Anote a cadeia de conexão de chave somente leitura primária. Você usará essa cadeia de conexão posteriormente para configurar o aplicativo a comunicar-se com o repositório de Configurações de Aplicativo que você criou.

1. Selecione **Gerenciador de Configurações** > **Criar** para adicionar os seguintes pares chave-valor:

   |Chave|Valor|
   |-|-|
   |windowsOsVersion|2019-Datacenter|
   |diskSizeGB|1023|
  
   Insira *modelo* em **Rótulo**, mas mantenha **Tipo de Conteúdo** vazio.

## <a name="deploy-vm-using-stored-key-values"></a>Implantar a VM usando pares chave-valor armazenados

Agora que adicionou pares chave-valor ao repositório, você está pronto para implantar uma VM usando um modelo do Azure Resource Manager. O modelo referencia as chaves **windowsOsVersion** e **diskSizeGB** criadas.

> [!WARNING]
> Os modelos do ARM não podem referenciar chaves em um repositório de Configurações de Aplicativos que tenha o Link Privado habilitado.

1. Copie e cole o código JSON a seguir em um novo arquivo chamado *azuredeploy.json* ou baixe o arquivo nos [Modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "adminUsername": {
            "type": "string",
            "metadata": {
                "description": "Admin user name."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "description": "Password for the Virtual Machine."
            }
        },
        "appConfigStoreName": {
            "type": "string",
            "metadata": {
                "description": "App configuration store name."
            }
        },
        "appConfigStoreResourceGroup": {
            "type": "string",
            "metadata": {
                "description": "Name of the resource group for the app config store."
            }
        },
        "domainNameLabel": {
            "type": "string",
            "metadata": {
                "description": "The DNS label for the public IP address. It must be lowercase. It should match the following regular expression, or it will raise an error: ^[a-z][a-z0-9-]{1,61}[a-z0-9]$."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]",
            "metadata": {
                "description": "Location for all resources."
            }
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_D2_v3",
            "metadata": {
                "description": "Size of the VM"
            }
        },
        "vmSkuKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM windows sku"
            }
        },
        "diskSizeKey": {
            "type": "string",
            "metadata": {
                "description": "Name of the key in the app config store for the VM disk size"
            }
        },
        "storageAccountName": {
            "type": "string",
            "metadata": {
                "description": "The name of the storage account."
            }
        }
    },
    "variables": {
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressName": "myPublicIP",
        "vmName": "SimpleWinVM",
        "virtualNetworkName": "MyVNET",
        "subnetRef": "[resourceId('Microsoft.Network/virtualNetworks/subnets', variables('virtualNetworkName'), variables('subnetName'))]",
        "appConfigRef": "[resourceId(parameters('appConfigStoreResourceGroup'), 'Microsoft.AppConfiguration/configurationStores', parameters('appConfigStoreName'))]",
        "windowsOSVersionParameters": {
            "key": "[parameters('vmSkuKey')]",
            "label": "template"
        },
        "diskSizeGBParameters": {
            "key": "[parameters('diskSizeKey')]",
            "label": "template"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2018-11-01",
            "name": "[parameters('storageAccountName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Network/publicIPAddresses",
            "apiVersion": "2018-11-01",
            "name": "[variables('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "Dynamic",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('domainNameLabel')]"
                }
            }
        },
        {
            "type": "Microsoft.Network/virtualNetworks",
            "apiVersion": "2018-11-01",
            "name": "[variables('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Network/networkInterfaces",
            "apiVersion": "2018-11-01",
            "name": "[variables('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[resourceId('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines",
            "apiVersion": "2018-10-01",
            "name": "[variables('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computerName": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "MicrosoftWindowsServer",
                        "offer": "WindowsServer",
                        "sku": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('windowsOSVersionParameters')).value]",
                        "version": "latest"
                    },
                    "osDisk": {
                        "createOption": "FromImage"
                    },
                    "dataDisks": [
                        {
                            "diskSizeGB": "[listKeyValue(variables('appConfigRef'), '2019-10-01', variables('diskSizeGBParameters')).value]",
                            "lun": 0,
                            "createOption": "Empty"
                        }
                    ]
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                },
                "diagnosticsProfile": {
                    "bootDiagnostics": {
                        "enabled": true,
                        "storageUri": "[reference(resourceId('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))).primaryEndpoints.blob]"
                    }
                }
            }
        }
    ],
    "outputs": {
        "hostname": {
            "type": "string",
            "value": "[reference(variables('publicIPAddressName')).dnsSettings.fqdn]"
        }
    }
   }
   ```

1. Copie e cole o código JSON a seguir em um novo arquivo chamado *azuredeploy.parameters.json* ou baixe o arquivo nos [Modelos de Início Rápido do Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/101-app-configuration/azuredeploy.parameters.json).

   ```json
   {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "adminPassword": {
        "value": "GEN-PASSWORD"
      },
      "appConfigStoreName":{
        "value": "GEN-APPCONFIGSTORE-NAME"
      },
      "appConfigStoreResourceGroup": {
         "value": "GEN-APPCONFIGSTORE-RESOURCEGROUP-NAME"
      },
      "vmSkuKey":{
        "value": "GEN-APPCONFIGSTORE-WINDOWSOSVERSION"
      },
      "diskSizeKey" :{
         "value": "GEN-APPCONFIGSTORE-DISKSIZEGB"
      },
      "adminUsername":{
        "value": "GEN-UNIQUE"
      },
      "storageAccountName":{
        "value": "GEN-UNIQUE"
      },
      "domainNameLabel":{
        "value": "GEN-UNIQUE"
      }
    }
   }
   ```

   Substitua os valores de parâmetro do modelo pelos seguintes valores:

   |Parâmetro|Valor|
   |-|-|
   |adminPassword|Uma senha do administrador da VM.|
   |appConfigStoreName|O nome do repositório da Configuração de Aplicativos do Azure.|
   |appConfigStoreResourceGroup|O grupo de recursos que contém o repositório da Configuração de Aplicativos.|
   |vmSkuKey|*windowsOSVersion*|
   |diskSizeKey|*diskSizeGB*|
   |adminUsername|Um nome de usuário do administrador da VM.|
   |storageAccountName|Um nome exclusivo para uma conta de armazenamento associada à VM.|
   |domainNameLabel|Um nome de domínio exclusivo.|

1. Na janela do PowerShell, execute o comando a seguir para implantar a VM. Não se esqueça de substituir o nome do grupo de recursos, o caminho do arquivo de modelo e o caminho do arquivo de parâmetro do modelo.

   ```azurepowershell
   New-AzResourceGroupDeployment `
       -ResourceGroupName "<your resource group>"
       -TemplateFile "<path to azuredeploy.json>" `
       -TemplateParameterFile "<path to azuredeploy.parameters.json>"
   ```

Parabéns! Você implantou uma VM usando as configurações armazenadas na Configuração de Aplicativos do Azure.

## <a name="clean-up-resources"></a>Limpar os recursos

Exclua o grupo de recursos, o repositório da Configuração de Aplicativos, a VM e todos os recursos relacionados quando eles não forem mais necessários. Se estiver planejando usar o repositório da Configuração de Aplicativos ou a VM no futuro, ignore a exclusão deles. Se você não pretende continuar usando esse trabalho, exclua todos os recursos criados por este início rápido executando o seguinte cmdlet:

```azurepowershell-interactive
Remove-AzResourceGroup `
  -Name $resourceGroup
```

## <a name="next-steps"></a>Próximas etapas

Neste início rápido, você implantou uma VM usando um modelo do Azure Resource Manager e pares chave-valor da Configuração de Aplicativos do Azure.

Para saber mais sobre como criar outros aplicativos com a Configuração de Aplicativos do Azure, prossiga para o seguinte artigo:

> [!div class="nextstepaction"]
> [Início Rápido: Criar um aplicativo ASP.NET Core com a Configuração de Aplicativos do Azure](quickstart-aspnet-core-app.md)
