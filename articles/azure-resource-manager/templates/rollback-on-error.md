---
title: Reverter o erro para a implantação bem-sucedida
description: Especifique que uma implantação com falha deve reverter para uma implantação bem-sucedida.
ms.topic: conceptual
ms.date: 10/04/2019
ms.openlocfilehash: 206c794996f58a4c5b6982c551ae50128ed4f5eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460136"
---
# <a name="rollback-on-error-to-successful-deployment"></a>Reversão do erro para implantação bem-sucedida

Quando uma implantação falha, é possível reimplantar automaticamente uma implantação anterior bem-sucedida com base em seu histórico de implantações. Essa funcionalidade é útil se você tiver um bom estado conhecido para a implantação da sua infra-estrutura e quiser reverter para este estado. Há uma série de ressalvas e restrições:

- A reimplantação é executada exatamente como foi executada anteriormente com os mesmos parâmetros. Você não pode mudar os parâmetros.
- A implantação anterior é executada usando o [modo completo](./deployment-modes.md#complete-mode). Todos os recursos não incluídos na implantação anterior são excluídos e quaisquer configurações de recursos são definidas para o estado anterior. Certifique-se de entender completamente os [modos de implantação](./deployment-modes.md).
- A reimplantação afeta apenas os recursos, quaisquer alterações de dados não são afetadas.
- Você só pode usar esse recurso com implantações de grupos de recursos, não implantações de nível de grupo de assinatura ou gerenciamento. Para obter mais informações sobre a implantação do nível de assinatura, consulte [Criar grupos de recursos e recursos no nível de assinatura](./deploy-to-subscription.md).
- Você só pode usar essa opção com implantações de nível raiz. Implantações de um modelo aninhado não estão disponíveis para reimplantação.

Para usar essa opção, as implantações devem ter nomes exclusivos para que possam ser identificadas no histórico. Se você não tiver nomes exclusivos, a implantação atual com falha pode substituir a implantação bem-sucedida anteriormente no histórico.

## <a name="powershell"></a>PowerShell

Para reimplementar a última implantação bem-sucedida, adicione o parâmetro `-RollbackToLastDeployment` como um sinalizador.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollbackToLastDeployment
```

Para reimplantar uma implantação específica, use o `-RollBackDeploymentName` parâmetro e forneça o nome da implantação. A implantação especificada deve ter êxito.

```azurepowershell-interactive
New-AzResourceGroupDeployment -Name ExampleDeployment02 `
  -ResourceGroupName $resourceGroupName `
  -TemplateFile c:\MyTemplates\azuredeploy.json `
  -RollBackDeploymentName ExampleDeployment01
```

## <a name="azure-cli"></a>CLI do Azure

Para reimplementar a última implantação bem-sucedida, adicione o parâmetro `--rollback-on-error` como um sinalizador.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Para reimplantar uma implantação específica, use o `--rollback-on-error` parâmetro e forneça o nome da implantação. A implantação especificada deve ter êxito.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

## <a name="rest-api"></a>API REST

Para reimplantar a última implantação bem-sucedida se a implantação atual falhar, use:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "LastSuccessful",
    }
  }
}
```

Para reimplantar uma implantação específica se a implantação atual falhar, use:

```json
{
  "properties": {
    "templateLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
      "contentVersion": "1.0.0.0"
    },
    "mode": "Incremental",
    "parametersLink": {
      "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
      "contentVersion": "1.0.0.0"
    },
    "onErrorDeployment": {
      "type": "SpecificDeployment",
      "deploymentName": "<deploymentname>"
    }
  }
}
```

A implantação especificada deve ter êxito.

## <a name="next-steps"></a>Próximas etapas

- Para distribuir com segurança seu serviço para mais de uma região, consulte [Azure Deployment Manager](deployment-manager-overview.md).
- Para especificar como lidar com os recursos existentes no grupo de recursos, mas que não estão definidos no modelo, confira [Modos de implantação do Azure Resource Manager](deployment-modes.md).
- Para entender como definir parâmetros em seu modelo, confira [Noções básicas de estrutura e sintaxe dos modelos do Azure Resource Manager](template-syntax.md).
- Para saber mais sobre como implantar um modelo que exija um token SAS, veja [Implantar o modelo particular com o token SAS](secure-template-with-sas-token.md).
