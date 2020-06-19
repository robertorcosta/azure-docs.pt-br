---
title: Criar uma instância gerenciada (modelo do Resource Manager e PowerShell)
titleSuffix: Azure SQL Managed Instance
description: Use este script de exemplo do Azure PowerShell para criar uma instância gerenciada.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: seo-dt-2019
ms.devlang: PowerShell
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 03/12/2019
ms.openlocfilehash: f4c4c63e6461dd3aa3cabe86b871434814fb400e
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84708528"
---
# <a name="use-powershell-with-an-azure-resource-manager-template-to-create-a-managed-instance"></a>Use o PowerShell com um modelo do Azure Resource Manager para criar uma instância gerenciada

[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqlmi.md)]

Você pode criar uma instância gerenciada usando a biblioteca do Azure PowerShell e os modelos do Azure Resource Manager.

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Quando você opta por instalar e usar o PowerShell localmente, este tutorial exige o Azure PowerShell 1.4.0 ou posterior. Se você precisa atualizar, consulte [Instalar o módulo do Azure PowerShell](/powershell/azure/install-az-ps). Se você estiver executando o PowerShell localmente, execute `Connect-AzAccount` para criar uma conexão com o Azure.

Os comandos do Azure PowerShell podem começar a implantação usando um modelo predefinido do Azure Resource Manager. As propriedades a seguir podem ser especificadas no modelo:

- Nome da instância gerenciada
- Nome de usuário do administrador do SQL e senha.
- Tamanho da instância (número de núcleos e o tamanho máximo de armazenamento).
- Rede virtual e sub-rede onde a instância será colocada.
- Ordenação de nível de servidor da instância (versão prévia).

O nome da instância, o nome de usuário do administrador do SQL, a rede virtual/sub-rede e a ordenação não podem ser alterados posteriormente. As outras propriedades da instância podem ser alteradas.

## <a name="prerequisites"></a>Pré-requisitos

Este exemplo pressupõe que você tenha [criado um ambiente de rede válido](../virtual-network-subnet-create-arm-template.md) ou [modificado a VNet existente](../vnet-existing-add-subnet.md) para sua instância gerenciada. Você pode preparar o ambiente de rede usando um [Modelo do Azure Resource Manager](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) separado, se necessário. 


A amostra usa os cmdlets [New-AzResourceGroupDeployment](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroupdeployment) e [Get-AzVirtualNetwork](https://docs.microsoft.com/powershell/module/az.network/get-azvirtualnetwork), portanto, verifique se você instalou os seguintes módulos do PowerShell:

```powershell
Install-Module Az.Network
Install-Module Az.Resources
```

## <a name="azure-resource-manager-template"></a>Modelo do Azure Resource Manager


Salve o script a seguir em um arquivo .json e anote o local do arquivo: 

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.1",
    "parameters": {
        "instance": {
            "type": "string"
        },
        "user": {
            "type": "string"
        },
        "pwd": {
            "type": "securestring"
        },
        "subnetId": {
            "type": "string"
        }
    },
    "resources": [
        {
            "name": "[parameters('instance')]",
            "location": "West Central US",
            "tags": {
                "Description":"GP Instance with custom instance collation - Serbian_Cyrillic_100_CS_AS"
            },
            "sku": {
                "name": "GP_Gen5",
                "tier": "GeneralPurpose"
            },
            "properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS"
            },
            "type": "Microsoft.Sql/managedInstances",
            "identity": {
                "type": "SystemAssigned"
            },
            "apiVersion": "2015-05-01-preview"
        }
    ]
}
```

Atualize o seguinte script do PowerShell com o caminho de arquivo correto para o arquivo .json que você salvou anteriormente e altere os nomes dos objetos no script:

```powershell
$subscriptionId = "ed827499-xxxx-xxxx-xxxx-xxxxxxxxxx"
Select-AzSubscription -SubscriptionId $subscriptionId

# Managed instance properties
$resourceGroup = "rg_mi"
$location = "West Central US"
$name = "managed-instance-name"
$user = "miSqlAdmin"
$secpasswd = ConvertTo-SecureString "<Put some strong password here>" -AsPlainText -Force

# Network configuration
$vNetName = "my_vnet"
$vNetResourceGroup = "rg_mi_vnet"
$subnetName = "ManagedInstances"
$vNet = Get-AzVirtualNetwork -Name $vNetName -ResourceGroupName $vNetResourceGroup
$subnet = Get-AzVirtualNetworkSubnetConfig -Name $SubnetName -VirtualNetwork $vNet
$subnetId = $subnet.Id

# Deploy instance using Azure Resource Manager template:
New-AzResourceGroupDeployment  -Name MyDeployment -ResourceGroupName $resourceGroup  `
                                    -TemplateFile 'C:\...\create-managed-instance.json' `
                                    -instance $name -user $user -pwd $secpasswd -subnetId $subnetId
```

Após o script ser concluído, a instância gerenciada poderá ser acessada de todos os serviços do Azure e do endereço IP configurado.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o Azure PowerShell, confira a [Documentação do Azure PowerShell](/powershell/azure/overview).

Exemplos adicionais de scripts do PowerShell para a Instância Gerenciada de SQL do Azure podem ser encontrados nos [Scripts de PowerShell da Instância Gerenciada de SQL do Azure](../../database/powershell-script-content-guide.md).
