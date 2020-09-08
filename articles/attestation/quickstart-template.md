---
title: Criar um certificado do Atestado do Azure usando um modelo do Azure Resource Manager
description: Saiba como criar um certificado do Atestado do Azure usando um modelo do Azure Resource Manager.
services: azure-resource-manager
author: msmbaldwin
ms.service: azure-resource-manager
ms.topic: quickstart
ms.custom: subject-armqs
ms.author: mbaldwin
ms.date: 08/31/2020
ms.openlocfilehash: a73294e41f27a15d70ab95b3aa7b100cab5320b4
ms.sourcegitcommit: d68c72e120bdd610bb6304dad503d3ea89a1f0f7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89236809"
---
# <a name="quickstart-create-an-azure-attestation-provider-with-an-arm-template"></a>Início Rápido: Criar um provedor do Atestado do Azure com um modelo do ARM

O [Atestado do Microsoft Azure](overview.md) é uma solução usada para atestar TEEs (Ambientes de Execução Confiáveis). Este guia de início rápido tem como foco o processo de implantação de um modelo do ARM (Azure Resource Manager) para a criação de uma política do Atestado do Microsoft Azure.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se seu ambiente atender aos pré-requisitos e você estiver familiarizado com o uso de modelos ARM, selecione o botão **Implantar no Azure**. O modelo será aberto no portal do Azure.

[![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)


## <a name="review-the-template"></a>Examinar o modelo

O modelo usado neste início rápido é proveniente dos [Modelos de Início Rápido do Azure](https://azure.microsoft.com/resources/templates/101-attestation-provider-create).

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "name": {
      "type": "string"
    },
    "location": {
      "type": "string"
    },
    "tags": {
      "type": "Object"
    },
    "policySigningCertificates": {
      "type": "string"
    }
  },
  "variables": {
    "PolicySigningCertificates": {
      "PolicySigningCertificates": {
        "keys": [
          {
            "kty": "RSA",
            "use": "sig",
            "x5c": [ "[parameters('policySigningCertificates')]" ]
          }
        ]
      }
    }
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "[parameters('name')]",
      "location": "[parameters('location')]",
      "type": "Microsoft.Attestation/attestationProviders",
      "tags": "[parameters('tags')]",
      "Properties": "[if(empty(parameters('policySigningCertificates')), json('{}'), variables('PolicySigningCertificates'))]"
    }
  ]
}
```

Os recursos do Azure definidos no modelo:
- Microsoft.Attestation/attestationProviders

## <a name="deploy-the-template"></a>Implantar o modelo

1. Selecione a imagem a seguir para entrar no Azure e abrir o modelo.

    [![Implantar no Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-attestation-provider-create%2Fazuredeploy.json)

2. Selecione ou insira os seguintes valores.

    A menos que o valor seja especificado, use o valor padrão para criar o provedor de atestado.

    * **nome**: escolha um nome para o provedor do Atestado do Azure.
    * **location**: Selecione um local. Por exemplo, **Centro dos EUA**.
    * **marcas**: escolha uma localização. Por exemplo, **Centro dos EUA**.

3. Selecione **Comprar**. Depois que o recurso de atestado for implantado com êxito, você receberá uma notificação:

O portal do Azure é usado para implantar o modelo. Além do portal do Azure, você também pode usar o Azure PowerShell, a CLI do Azure e a API REST. Para saber mais sobre outros métodos de implantação, confira [Implantar modelos](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="review-deployed-resources"></a>Examinar os recursos implantados

Use o portal do Azure para verificar o recurso de atestado.

## <a name="clean-up-resources"></a>Limpar os recursos

Outro Atestado do Azure se baseia neste guia de início rápido. Se você planeja continuar a trabalhar com os tutoriais e inícios rápidos subsequentes, deixe esses recursos onde estão.

Quando você não precisar mais do grupo de recursos, exclua-o, o que excluirá o recurso de atestado. Para excluir o grupo de recursos usando a CLI do Azure ou o Azure PowerShell:

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>Próximas etapas

Neste guia de início rápido, você criou um recurso de atestado usando um modelo do ARM e validou a implantação. Para saber mais sobre o Atestado do Azure, confira [Visão geral do Atestado do Azure](overview.md).
