---
title: Criar arquivo de parâmetros
description: Criar arquivo parâmetro para passar valores durante a implantação de um modelo do Azure Resource Manager
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: a1a1f703594f8eaa572ea38ecef88b4cd6ba5a4b
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682885"
---
# <a name="create-resource-manager-parameter-file"></a>Criar arquivo de parâmetros do Gerenciador de recursos

Em vez de passar parâmetros como valores embutidos no script, talvez seja mais fácil usar um arquivo JSON que contenha os valores de parâmetro. Este artigo mostra como criar o arquivo de parâmetros.

## <a name="parameter-file"></a>Arquivo de parâmetro.

O arquivo parâmetro tem o seguinte formato:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "value": "<second-value>"
    }
  }
}
```

Observe que os valores dos parâmetros são armazenados como texto simples no arquivo de parâmetros. Essa abordagem funciona para valores que não são sensíveis, como especificar o SKU para um recurso. Não funciona para valores sensíveis, como senhas. Se você precisar passar um valor sensível como parâmetro, armazene o valor em um cofre de chaves e faça referência ao cofre de chaves no arquivo do parâmetro. O valor sensível é recuperado com segurança durante a implantação.

O seguinte arquivo de parâmetro inclui um valor de texto simples e um valor armazenado em um cofre de chaves.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "<first-parameter-name>": {
      "value": "<first-value>"
    },
    "<second-parameter-name>": {
      "reference": {
        "keyVault": {
          "id": "<resource-id-key-vault>"
        },
        "secretName": "<secret-name>"
      }
    }
  }
}
```

Para obter mais informações sobre o uso de valores de um cofre de chaves, consulte [Use Azure Key Vault para passar o valor do parâmetro seguro durante a implantação](key-vault-parameter.md).

## <a name="define-parameter-values"></a>Definir valores de parâmetros

Para descobrir como definir os valores dos parâmetros, abra o modelo que você está implantando. Veja a seção de parâmetros do modelo. O exemplo a seguir mostra os parâmetros de um modelo.

```json
"parameters": {
  "storagePrefix": {
    "type": "string",
    "maxLength": 11
  },
  "storageAccountType": {
    "type": "string",
    "defaultValue": "Standard_LRS",
    "allowedValues": [
    "Standard_LRS",
    "Standard_GRS",
    "Standard_ZRS",
    "Premium_LRS"
    ]
  }
}
```

O primeiro detalhe a ser notado é o nome de cada parâmetro. Os valores no arquivo de parâmetros devem corresponder aos nomes.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
    },
    "storageAccountType": {
    }
  }
}
```

Observe o tipo do parâmetro. Os valores no seu arquivo de parâmetros devem ter os mesmos tipos. Para este modelo, você pode fornecer ambos os parâmetros como strings.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": ""
    },
    "storageAccountType": {
      "value": ""
    }
  }
}
```

Em seguida, procure um valor padrão. Se um parâmetro tiver um valor padrão, você pode fornecer um valor, mas não precisa.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "" // This value must be provided.
    },
    "storageAccountType": {
      "value": "" // This value is optional. Template will use default value if not provided.
    }
  }
}
```

Por fim, veja os valores permitidos e quaisquer restrições como o comprimento máximo. Eles dizem a faixa de valores que você pode fornecer para o parâmetro.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storagePrefix": {
      "value": "storage"
    },
    "storageAccountType": {
      "value": "Standard_ZRS"
    }
  }
}
```

## <a name="parameter-type-formats"></a>Formatos de tipo parâmetro

O exemplo a seguir mostra os formatos de diferentes tipos de parâmetros.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "exampleString": {
      "value": "test string"
    },
    "exampleInt": {
      "value": 4
    },
    "exampleBool": {
      "value": true
    },
    "exampleArray": {
      "value": [
        "value 1",
        "value 2"
      ]
    },
    "exampleObject": {
      "value": {
        "property1": "value1",
        "property2": "value2"
      }
    }
   }
}
```

## <a name="file-name"></a>Nome do arquivo

A convenção geral para nomear o arquivo de parâmetros é adicionar **.parâmetros** ao nome do modelo. Por exemplo, se o seu modelo for chamado **azuredeploy.json,** seu arquivo de parâmetros será chamado **azuredeploy.parameters.json**. Esta convenção de nomeação ajuda você a ver a conexão entre o modelo e os parâmetros.

Para implantar em diferentes ambientes, crie mais de um arquivo de parâmetros. Ao nomear o arquivo de parâmetro, adicione uma maneira de identificar seu uso. Por exemplo, use **azuredeploy.parameters-dev.json** e **azuredeploy.parameters-prod.json**


## <a name="parameter-precedence"></a>Precedência de parâmetro

Você pode usar parâmetros embutidos e um arquivo de parâmetro local na mesma operação de implantação. Por exemplo, você pode especificar alguns valores no arquivo de parâmetro local e adicionar outros valores embutidos durante a implantação. Se você fornecer valores para um parâmetro no arquivo de parâmetros local e embutido, o valor embutido terá precedência.

No entanto, quando você usa um arquivo de parâmetro externo, não é possível transmitir outros valores em linha ou em um arquivo local. Todos os parâmetros inline são ignorados. Forneça todos os valores de parâmetro no arquivo externo.

## <a name="parameter-name-conflicts"></a>Conflitos de nome de parâmetro

Se o modelo incluir um parâmetro com o mesmo nome que um dos parâmetros no comando do PowerShell, o PowerShell apresentará o parâmetro do modelo com o postfix **FromTemplate**. Por exemplo, um parâmetro chamado **ResourceGroupName** em seu modelo entra em conflito com o parâmetro **ResourceGroupName** no cmdlet [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Você é solicitado a fornecer um valor para **ResourceGroupNameFromTemplate**. Você pode evitar essa confusão usando nomes de parâmetros que não são usados para comandos de implantação.

## <a name="next-steps"></a>Próximas etapas

- Para entender como definir parâmetros em seu modelo, consulte [Parâmetros nos modelos do Azure Resource Manager](template-parameters.md).
- Para obter mais informações sobre o uso de valores de um cofre de chaves, consulte [Use Azure Key Vault para passar o valor do parâmetro seguro durante a implantação](key-vault-parameter.md).
- Para obter mais informações sobre parâmetros, consulte [Parâmetros nos modelos do Azure Resource Manager](template-parameters.md).
