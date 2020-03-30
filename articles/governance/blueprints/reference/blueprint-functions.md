---
title: Funções do Azure Blueprints
description: Descreve as funções disponíveis para uso com artefatos de projeto nas definições e atribuições do Azure Blueprints.
ms.date: 12/09/2019
ms.topic: reference
ms.openlocfilehash: 0aab2fe0511ccc11842d0e132a83d6e3f7fac27f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280671"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funções para uso com plantas azure

O Azure Blueprints fornece funções tornando a definição de um projeto mais dinâmica. Essas funções são para uso com definições de plantas e artefatos de projeto. Um artefato de modelo de gerenciador de recursos suporta o uso completo das funções do Gerenciador de Recursos, além de obter um valor dinâmico através de um parâmetro de projeto.

As seguintes funções são suportadas:

- [Artefatos](#artifacts)
- [concat](#concat)
- [Parâmetros](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [Assinatura](#subscription)

## <a name="artifacts"></a>artefatos

`artifacts(artifactName)`

Retorna um objeto de propriedades povoadas com as saídas de artefatos de projeto.

> [!NOTE]
> A `artifacts()` função não pode ser usada de dentro de um modelo de gerenciador de recursos. A função só pode ser usada na definição de projeto JSON ou no artefato JSON ao gerenciar o projeto com a Azure PowerShell ou Rest API como parte do [Blueprints-as-code](https://github.com/Azure/azure-blueprints/blob/master/README.md).

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| artefatoNome |Sim |string |O nome de um artefato de projeto. |

### <a name="return-value"></a>Valor retornado

Um objeto de propriedades de saída. As **propriedades das saídas** dependem do tipo de artefato de projeto que está sendo referenciado. Todos os tipos seguem o formato:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Artefato de atribuição de políticas

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Artefato de modelo do Gerenciador de recursos

As propriedades de saídas do objeto retornado são **definidas** no modelo Gerenciador de recursos e devolvidas pela implantação.

#### <a name="role-assignment-artifact"></a>Artefato de atribuição de função

```json
{
    "outputs": {
        "roleAssignmentId": "{resourceId-of-role-assignment}",
        "roleDefinitionId": "{resourceId-of-role-definition}",
        "principalId": "{principalId-role-is-being-assigned-to}",
    }
}
```

### <a name="example"></a>Exemplo

Um artefato de modelo do Gerenciador de recursos com o ID _myTemplateArtifact_ contendo a seguinte propriedade de saída de amostra:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    ...
    "outputs": {
        "myArray": {
            "type": "array",
            "value": ["first", "second"]
        },
        "myString": {
            "type": "string",
            "value": "my string value"
        },
        "myObject": {
            "type": "object",
            "value": {
                "myProperty": "my value",
                "anotherProperty": true
            }
        }
    }
}
```

Alguns exemplos de recuperação de dados da amostra _myTemplateArtifact_ são:

| Expression | Type | Valor |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["primeiro", "segundo"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | String | "Primeiro" |
|`[artifacts("myTemplateArtifact").outputs.myString]` | String | "meu valor de corda" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Objeto | { "myproperty": "meu valor", "anotherProperty": true } |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | String | "Meu valor" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | True |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Combina vários valores de cadeia de caracteres e retorna o resultado concatenado.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| string1 |Sim |string |O primeiro valor de concatenação. |
| argumentos adicionais |Não |string |Valores adicionais na ordem seqüencial para concatenação |

### <a name="return-value"></a>Valor retornado

Uma série de valores concatenados.

### <a name="remarks"></a>Comentários

A função Azure Blueprint difere da função de modelo do Azure Resource Manager, na qual ela só funciona com strings.

### <a name="example"></a>Exemplo

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parâmetros

`parameters(parameterName)`

Retorna um valor de parâmetro de projeto. O nome do parâmetro especificado deve ser definido na definição do projeto ou em artefatos de projeto.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| parameterName |Sim |string |O nome do parâmetro a retornar. |

### <a name="return-value"></a>Valor retornado

O valor do parâmetro de projeto ou artefato de projeto especificado.

### <a name="remarks"></a>Comentários

A função Azure Blueprint difere da função de modelo do Azure Resource Manager, na qual só funciona com parâmetros de projeto.

### <a name="example"></a>Exemplo

Defina _os principaiids de parâmetros_ na definição do projeto:

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "parameters": {
            "principalIds": {
                "type": "array",
                "metadata": {
                    "displayName": "Principal IDs",
                    "description": "This is a blueprint parameter that any artifact can reference. We'll display these descriptions for you in the info bubble. Supply principal IDs for the users,groups, or service principals for the RBAC assignment.",
                    "strongType": "PrincipalId"
                }
            }
        },
        ...
    }
}
```

Em _seguida, use os principaiides_ como argumento para `parameters()` um artefato de projeto:

```json
{
    "type": "Microsoft.Blueprint/blueprints/artifacts",
    "kind": "roleAssignment",
    ...
    "properties": {
        "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
        "principalIds": "[parameters('principalIds')]",
        ...
    }
}
```

## <a name="resourcegroup"></a>resourceGroup

`resourceGroup()`

Retorna um objeto que representa o grupo de recursos atual.

### <a name="return-value"></a>Valor retornado

O objeto retornado está no seguinte formato:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="remarks"></a>Comentários

A função Azure Blueprint difere da função de modelo do Azure Resource Manager. A `resourceGroup()` função não pode ser usada em um artefato de nível de assinatura ou na definição do projeto. Ele só pode ser usado em artefatos de projeto que fazem parte de um artefato de grupo de recursos.

Um uso comum da `resourceGroup()` função é criar recursos no mesmo local que o artefato do grupo de recursos.

### <a name="example"></a>Exemplo

Para usar a localização do grupo de recursos, definido na definição do projeto ou durante a atribuição, como o local para outro artefato, declare um objeto de espaço reservado de grupo de recursos na definição do projeto. Neste exemplo, _NetworkingPlaceholder_ é o nome do espaço reservado do grupo de recursos.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Em seguida, use a `resourceGroup()` função no contexto de um artefato de projeto que está mirando um objeto de espaço reservado de grupo de recursos. Neste exemplo, o artefato de modelo é implantado no grupo de recursos _NetworkingPlaceHolder_ e fornece recursos de _parâmetroLocalização_ dinamicamente preenchida sinuosamente com o local do grupo de recursos _NetworkingPlaceHolder_ para o modelo. A localização do grupo de recursos _NetworkingPlaceHolder_ poderia ter sido definida estáticamente na definição do projeto ou definida dinamicamente durante a atribuição. Em ambos os casos, o artefato de modelo é fornecido essa informação como parâmetro e usa-as para implantar os recursos para o local correto.

```json
{
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "kind": "template",
  "properties": {
      "template": {
        ...
      },
      "resourceGroup": "NetworkingPlaceholder",
      ...
      "parameters": {
        "resourceLocation": {
          "value": "[resourceGroup().location]"
        }
      }
  }
}
```

## <a name="resourcegroups"></a>resourceGroups

`resourceGroups(placeholderName)`

Retorna um objeto que representa o artefato de grupo de recursos especificado. Ao `resourceGroup()`contrário , que requer o contexto do artefato, esta função é usada para obter as propriedades de um espaço reservado de grupo de recursos específico quando não no contexto desse grupo de recursos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| lugar reservadoNome |Sim |string |O nome do espaço reservado do artefato do grupo de recursos para retornar. |

### <a name="return-value"></a>Valor retornado

O objeto retornado está no seguinte formato:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Exemplo

Para usar a localização do grupo de recursos, definido na definição do projeto ou durante a atribuição, como o local para outro artefato, declare um objeto de espaço reservado de grupo de recursos na definição do projeto. Neste exemplo, _NetworkingPlaceholder_ é o nome do espaço reservado do grupo de recursos.

```json
{
    "type": "Microsoft.Blueprint/blueprints",
    "properties": {
        ...
        "resourceGroups": {
            "NetworkingPlaceholder": {
                "location": "eastus"
            }
        }
    }
}
```

Em seguida, use a `resourceGroups()` função a partir do contexto de qualquer artefato de projeto para obter uma referência ao objeto espaço reservado do grupo de recursos. Neste exemplo, o artefato de modelo é implantado fora do grupo de recursos _NetworkingPlaceHolder_ e fornece artefato de _parâmetroSLocalização_ dinamicamente preenchida com o local do grupo de recursos _NetworkingPlaceHolder_ para o modelo. A localização do grupo de recursos _NetworkingPlaceHolder_ poderia ter sido definida estáticamente na definição do projeto ou definida dinamicamente durante a atribuição. Em ambos os casos, o artefato de modelo é fornecido essa informação como parâmetro e usa-as para implantar os recursos para o local correto.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "artifactLocation": {
          "value": "[resourceGroups('NetworkingPlaceholder').location]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="subscription"></a>subscription

`subscription()`

Retorna detalhes sobre a assinatura da atribuição atual do projeto.

### <a name="return-value"></a>Valor retornado

O objeto retornado está no seguinte formato:

```json
{
    "id": "/subscriptions/{subscriptionId}",
    "subscriptionId": "{subscriptionId}",
    "tenantId": "{tenantId}",
    "displayName": "{name-of-subscription}"
}
```

### <a name="example"></a>Exemplo

Use o nome de exibição da assinatura e a `concat()` função para criar uma convenção de nomeação passada como recurso de _parâmetroNome_ para o artefato do modelo.

```json
{
  "kind": "template",
  "properties": {
      "template": {
          ...
      },
      ...
      "parameters": {
        "resourceName": {
          "value": "[concat(subscription().displayName, '-vm')]"
        }
      }
  },
  "type": "Microsoft.Blueprint/blueprints/artifacts",
  "name": "myTemplate"
}
```

## <a name="next-steps"></a>Próximas etapas

- Conheça o [ciclo de vida](../concepts/lifecycle.md)do projeto .
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).