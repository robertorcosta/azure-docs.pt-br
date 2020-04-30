---
title: Funções de plantas do Azure
description: Descreve as funções disponíveis para uso com artefatos de Blueprint em definições e atribuições de plantas do Azure.
ms.date: 12/09/2019
ms.topic: reference
ms.openlocfilehash: 0aab2fe0511ccc11842d0e132a83d6e3f7fac27f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79280671"
---
# <a name="functions-for-use-with-azure-blueprints"></a>Funções para uso com plantas do Azure

Os planos gráficos do Azure fornecem funções que tornam a definição Blueprint mais dinâmica. Essas funções são para uso com definições de Blueprint e artefatos de Blueprint. Um artefato de modelo do Resource Manager dá suporte ao uso completo de funções do Resource Manager, além de obter um valor dinâmico por meio de um parâmetro Blueprint.

Há suporte para as seguintes funções:

- [artefato](#artifacts)
- [Concat](#concat)
- [parâmetro](#parameters)
- [resourceGroup](#resourcegroup)
- [resourceGroups](#resourcegroups)
- [scriçõe](#subscription)

## <a name="artifacts"></a>artefatos

`artifacts(artifactName)`

Retorna um objeto das propriedades preenchidas com as saídas dos artefatos do Blueprint.

> [!NOTE]
> A `artifacts()` função não pode ser usada de dentro de um modelo do Resource Manager. A função só pode ser usada na definição do Blueprint JSON ou no artefato JSON ao gerenciar o plano gráfico com Azure PowerShell ou a API REST como parte de [plantas como código](https://github.com/Azure/azure-blueprints/blob/master/README.md).

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| artefatoname |Sim |cadeia de caracteres |O nome de um artefato de plano gráfico. |

### <a name="return-value"></a>Valor retornado

Um objeto de propriedades de saída. As propriedades de **saída** são dependentes do tipo de artefato de plano gráfico que está sendo referenciado. Todos os tipos seguem o formato:

```json
{
  "outputs": {collectionOfOutputProperties}
}
```

#### <a name="policy-assignment-artifact"></a>Artefato de atribuição de política

```json
{
    "outputs": {
        "policyAssignmentId": "{resourceId-of-policy-assignment}",
        "policyAssignmentName": "{name-of-policy-assignment}",
        "policyDefinitionId": "{resourceId-of-policy-definition}",
    }
}
```

#### <a name="resource-manager-template-artifact"></a>Artefato do modelo do Resource Manager

As propriedades de **saídas** do objeto retornado são definidas no modelo do Resource Manager e retornadas pela implantação.

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

Um artefato do modelo do Resource Manager com a ID _myTemplateArtifact_ que contém a seguinte propriedade de saída de exemplo:

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

Alguns exemplos de recuperação de dados do exemplo _myTemplateArtifact_ são:

| Expression | Type | Valor |
|:---|:---|:---|
|`[artifacts("myTemplateArtifact").outputs.myArray]` | Array | \["primeiro", "segundo"\] |
|`[artifacts("myTemplateArtifact").outputs.myArray[0]]` | Cadeia de caracteres | primeiro |
|`[artifacts("myTemplateArtifact").outputs.myString]` | Cadeia de caracteres | "meu valor de cadeia de caracteres" |
|`[artifacts("myTemplateArtifact").outputs.myObject]` | Objeto | {"MyProperty": "meu valor", "anotherproperty": true} |
|`[artifacts("myTemplateArtifact").outputs.myObject.myProperty]` | Cadeia de caracteres | "meu valor" |
|`[artifacts("myTemplateArtifact").outputs.myObject.anotherProperty]` | Bool | verdadeiro |

## <a name="concat"></a>concat

`concat(string1, string2, string3, ...)`

Combina vários valores de cadeia de caracteres e retorna o resultado concatenado.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| string1 |Sim |cadeia de caracteres |O primeiro valor de concatenação. |
| argumentos adicionais |Não |cadeia de caracteres |Valores adicionais em ordem sequencial para concatenação |

### <a name="return-value"></a>Valor retornado

Uma cadeia de caracteres de valores concatenados.

### <a name="remarks"></a>Comentários

A função Azure Blueprint difere da função de modelo Azure Resource Manager, pois só funciona com cadeias de caracteres.

### <a name="example"></a>Exemplo

`concat(parameters('organizationName'), '-vm')`

## <a name="parameters"></a>parâmetros

`parameters(parameterName)`

Retorna um valor de parâmetro Blueprint. O nome do parâmetro especificado deve ser definido na definição do Blueprint ou em artefatos do Blueprint.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| parameterName |Sim |cadeia de caracteres |O nome do parâmetro a retornar. |

### <a name="return-value"></a>Valor retornado

O valor do parâmetro de artefato Blueprint ou Blueprint especificado.

### <a name="remarks"></a>Comentários

A função Azure Blueprint difere da função de modelo de Azure Resource Manager, pois só funciona com parâmetros de plano gráfico.

### <a name="example"></a>Exemplo

Defina o parâmetro _principalIds_ na definição do Blueprint:

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

Em seguida, use _principalIds_ como o `parameters()` argumento para em um artefato de Blueprint:

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

A função Azure Blueprint difere da função de modelo Azure Resource Manager. A `resourceGroup()` função não pode ser usada em um artefato de nível de assinatura ou na definição de Blueprint. Ele só pode ser usado em artefatos de Blueprint que fazem parte de um artefato de grupo de recursos.

Um uso comum da `resourceGroup()` função é criar recursos no mesmo local que o artefato do grupo de recursos.

### <a name="example"></a>Exemplo

Para usar o local do grupo de recursos, defina na definição do plano gráfico ou durante a atribuição, como o local de outro artefato, declare um objeto de espaço reservado do grupo de recursos na definição do Blueprint. Neste exemplo, _NetworkingPlaceholder_ é o nome do espaço reservado do grupo de recursos.

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

Em seguida, `resourceGroup()` use a função no contexto de um artefato Blueprint destinado a um objeto de espaço reservado do grupo de recursos. Neste exemplo, o artefato do modelo é implantado no grupo de recursos _NetworkingPlaceholder_ e fornece o parâmetro _localização_ populado dinamicamente com o local do grupo de recursos _NetworkingPlaceholder_ para o modelo. O local do grupo de recursos _NetworkingPlaceholder_ poderia ter sido estaticamente definido na definição do Blueprint ou definido dinamicamente durante a atribuição. Em ambos os casos, o artefato do modelo recebe essas informações como um parâmetro e as usa para implantar os recursos no local correto.

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

Retorna um objeto que representa o artefato do grupo de recursos especificado. Ao `resourceGroup()`contrário de, que requer o contexto do artefato, essa função é usada para obter as propriedades de um espaço reservado do grupo de recursos específico quando não está no contexto desse grupo de recursos.

### <a name="parameters"></a>Parâmetros

| Parâmetro | Obrigatório | Type | Descrição |
|:--- |:--- |:--- |:--- |
| espaço reservado |Sim |cadeia de caracteres |O nome do espaço reservado do artefato do grupo de recursos a ser retornado. |

### <a name="return-value"></a>Valor retornado

O objeto retornado está no seguinte formato:

```json
{
  "name": "{resourceGroupName}",
  "location": "{resourceGroupLocation}",
}
```

### <a name="example"></a>Exemplo

Para usar o local do grupo de recursos, defina na definição do plano gráfico ou durante a atribuição, como o local de outro artefato, declare um objeto de espaço reservado do grupo de recursos na definição do Blueprint. Neste exemplo, _NetworkingPlaceholder_ é o nome do espaço reservado do grupo de recursos.

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

Em seguida, `resourceGroups()` use a função do contexto de qualquer artefato do Blueprint para obter uma referência ao objeto de espaço reservado do grupo de recursos. Neste exemplo, o artefato do modelo é implantado fora do grupo de recursos _NetworkingPlaceholder_ e fornece o parâmetro _artifactLocation_ populado dinamicamente com o local do grupo de recursos _NetworkingPlaceholder_ para o modelo. O local do grupo de recursos _NetworkingPlaceholder_ poderia ter sido estaticamente definido na definição do Blueprint ou definido dinamicamente durante a atribuição. Em ambos os casos, o artefato do modelo recebe essas informações como um parâmetro e as usa para implantar os recursos no local correto.

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

Retorna detalhes sobre a assinatura para a atribuição de Blueprint atual.

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

Use o nome de exibição da assinatura e `concat()` a função para criar uma Convenção de nomenclatura passada como parâmetro _resourceName_ para o artefato do modelo.

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

- Saiba mais sobre o [ciclo de vida do blueprint](../concepts/lifecycle.md).
- Saiba como usar [parâmetros estáticos e dinâmicos](../concepts/parameters.md).
- Saiba como personalizar a [ordem de sequenciamento de blueprint](../concepts/sequencing-order.md).
- Saiba como usar o [bloqueio de recurso de blueprint](../concepts/resource-locking.md).
- Saiba como [atualizar atribuições existentes](../how-to/update-existing-assignments.md).
- Resolver problemas durante a atribuição de blueprint com [solução de problemas gerais](../troubleshoot/general.md).