---
title: Políticas de autor para propriedades de array em recursos
description: Aprenda a trabalhar com parâmetros de matriz e expressões de linguagem de matriz, avalie o alias [*] e apêndice elementos com as regras de definição da Diretiva Azure.
ms.date: 11/26/2019
ms.topic: how-to
ms.openlocfilehash: 991d159f6444133d902382bc9ca43bc2acd201e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280658"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Políticas de autor para propriedades de array em recursos do Azure

As propriedades do Azure Resource Manager são comumente definidas como strings e booleanos. Quando existe uma relação de um a muitos, propriedades complexas são definidas como matrizes. Na Diretiva Azure, os arrays são usados de várias maneiras diferentes:

- O tipo de parâmetro de [definição,](../concepts/definition-structure.md#parameters)para fornecer múltiplas opções
- Parte de uma [regra política](../concepts/definition-structure.md#policy-rule) usando as condições **em** ou **nãoIn**
- Parte de uma regra de política que avalia o [ \[ \* \] alias](../concepts/definition-structure.md#understanding-the--alias) para avaliar:
  - Cenários como **None,** **Any**ou **All**
  - Cenários complexos com **contagem**
- No [efeito apêndice](../concepts/effects.md#append) para substituir ou adicionar a uma matriz existente

Este artigo abrange cada uso da Política Do Azure e fornece várias definições de exemplo.

## <a name="parameter-arrays"></a>Matrizes de parâmetros

### <a name="define-a-parameter-array"></a>Definir uma matriz de parâmetros

Definir um parâmetro como matriz permite a flexibilidade da diretiva quando mais de um valor é necessário.
Esta definição de diretiva permite qualquer local único para o parâmetro **permitidoLocais** e padrões para _eastus2_:

```json
"parameters": {
    "allowedLocations": {
        "type": "string",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2"
    }
}
```

Como **o tipo** era _string,_ apenas um valor pode ser definido ao atribuir a diretiva. Se essa política for atribuída, os recursos no escopo só são permitidos em uma única região Azure. A maioria das definições de políticas precisa permitir uma lista de opções aprovadas, como permitir _eastus2_, _eastus_e _westus2_.

Para criar a definição de diretiva para permitir várias opções, use o tipo _de_ **matriz**. A mesma política pode ser reescrita da seguinte forma:

```json
"parameters": {
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of allowed locations for resources.",
            "displayName": "Allowed locations",
            "strongType": "location"
        },
        "defaultValue": "eastus2",
        "allowedValues": [
            "eastus2",
            "eastus",
            "westus2"
        ]

    }
}
```

> [!NOTE]
> Uma vez que uma definição de diretiva é salva, a propriedade **do tipo** em um parâmetro não pode ser alterada.

Essa nova definição de parâmetro leva mais de um valor durante a atribuição da diretiva. Com a propriedade de matriz **permitidaValores definidos,** os valores disponíveis durante a atribuição são ainda mais limitados à lista predefinida de opções. O uso de **valores permitidos** é opcional.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Passe valores para uma matriz de parâmetros durante a atribuição

Ao atribuir a diretiva através do portal Azure, um parâmetro da _matriz_ de **tipo** é exibido como uma única caixa de texto. A dica diz "Use; para separar valores. (por exemplo, Londres; Nova York)". Para passar os valores de localização permitidos de _Eastus2_, _Eastus_e _Westus2_ para o parâmetro, use a seguinte string:

`eastus2;eastus;westus2`

O formato para o valor do parâmetro é diferente ao usar o Azure CLI, o Azure PowerShell ou a API REST. Os valores são passados através de uma seqüência JSON que também inclui o nome do parâmetro.

```json
{
    "allowedLocations": {
        "value": [
            "eastus2",
            "eastus",
            "westus2"
        ]
    }
}
```

Para usar esta string com cada SDK, use os seguintes comandos:

- Azure CLI: [Designação de política de](/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) comando az criar com **parâmetros**
- Azure PowerShell: Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) com parâmetro **PolicyParameter**
- API REST: Na _operação PUT_ [criar](/rest/api/resources/policyassignments/create) como parte do Corpo de Solicitação como o valor da propriedade **properties.parameters**

## <a name="policy-rules-and-arrays"></a>Regras e matrizes de políticas

### <a name="array-conditions"></a>Condições da matriz

As [condições](../concepts/definition-structure.md#conditions) de regra da política com as que `in` um `notIn`**tipo** de parâmetro de _matriz_
pode ser usado são limitadas a e . Tome a seguinte `equals` definição de política com condição como exemplo:

```json
{
  "policyRule": {
    "if": {
      "not": {
        "field": "location",
        "equals": "[parameters('allowedLocations')]"
      }
    },
    "then": {
      "effect": "audit"
    }
  },
  "parameters": {
    "allowedLocations": {
      "type": "Array",
      "metadata": {
        "description": "The list of allowed locations for resources.",
        "displayName": "Allowed locations",
        "strongType": "location"
      }
    }
  }
}
```

Tentar criar essa definição de política através do portal Azure leva a um erro como esta mensagem de erro:

- "A diretiva '{GUID}' não pôde ser parametrizada devido a erros de validação. Verifique se os parâmetros da diretiva estão devidamente definidos. A exceção interna 'Resultado de avaliação da expressão da linguagem '[parâmetros('allowedLocations']' é o tipo 'Array', tipo esperado é 'String'.'."

O **tipo** esperado `equals` de condição é _string_. Uma **vez que allowedLocais** é definido como **matriz de tipo,** _array_o mecanismo de diretiva avalia a expressão do idioma e lança o erro. Com `in` a `notIn` condição e a condição, o mecanismo de política espera que a _matriz de_ **tipo** na expressão da linguagem. Para resolver esta mensagem de erro, altere `equals` para um ou `in` `notIn`.

### <a name="evaluating-the--alias"></a>Avaliando o alias [*]

Os alias ** \[ \* ** que foram anexados ao seu nome indicam que o **tipo** é uma _matriz_. Em vez de avaliar o valor ** \[ \* ** de toda a matriz, torna possível avaliar cada elemento da matriz individualmente, com lógica E entre eles. Existem três cenários padrão em que a avaliação por item é útil em: _Nenhum_, _Nenhum_ou _Todos os_ elementos correspondem. Para cenários complexos, [contagem de uso](../concepts/definition-structure.md#count).

O mecanismo de política aciona o **efeito** **somente** quando a regra **se** avaliar como verdadeira.
Este fato é importante para entender ** \[ \* ** no contexto da forma como avalia cada elemento individual da matriz.

A regra de política de exemplo para a tabela de cenários abaixo:

```json
"policyRule": {
    "if": {
        "allOf": [
            {
                "field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules",
                "exists": "true"
            },
            <-- Condition (see table below) -->
        ]
    },
    "then": {
        "effect": "[parameters('effectType')]"
    }
}
```

O **array ipRules** é o seguinte para a tabela de cenários abaixo:

```json
"ipRules": [
    {
        "value": "127.0.0.1",
        "action": "Allow"
    },
    {
        "value": "192.168.1.1",
        "action": "Allow"
    }
]
```

Para cada exemplo de `<field>` `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`condição abaixo, substitua por .

Os resultados a seguir são o resultado da combinação da condição e da regra da política de exemplo e matriz de valores existentes acima:

|Condição |Resultado | Cenário |Explicação |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |nada |Nenhum jogo |Um elemento de matriz avalia como falso (127.0.0.1 != 127.0.0.1) e outro como verdadeiro (127.0.0.1 != 192.168.1.1), de modo que a condição **notEquals** é _falsa_ e o efeito não é acionado. |
|`{<field>,"notEquals":"10.0.4.1"}` |Efeito político |Nenhum jogo |Ambos os elementos de matriz avaliam como verdadeiros (10.0.4.1 != 127.0.0.1 e 10.0.4.1 != 192.168.1.1), de modo que a condição **notEquals** é _verdadeira_ e o efeito é acionado. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Efeito político |Um ou mais fósforos |Um elemento de matriz avalia como falso (127.0.0.1 != 127.0.0.1) e um como verdadeiro (127.0.0.1 != 192.168.1.1), de modo que a condição **notEquals** é _falsa_. O operador lógico avalia como verdadeiro **(não** _falso),_ de modo que o efeito é acionado. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |nada |Um ou mais fósforos |Ambos os elementos de matriz avaliam como verdadeiros (10.0.4.1 != 127.0.0.1 e 10.0.4.1 != 192.168.1.1), de modo que a condição **notEquals** é _verdadeira_. O operador lógico avalia como falso **(não** _é verdade),_ de modo que o efeito não é acionado. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Efeito político |Nem todos combinam |Um elemento de matriz avalia como verdadeiro (127.0.0.1 == 127.0.0.1) e um como falso (127.0.0.1 == 192.168.1.1), de modo que a condição **Igual** é _falsa_. O operador lógico avalia como verdadeiro **(não** _falso),_ de modo que o efeito é acionado. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Efeito político |Nem todos combinam |Ambos os elementos de matriz avaliam como **falsos** (10.0.4.1 == 127.0.0.1 e 10.0.4.1 == 192.168.1.1), de modo que a condição Igual é _falsa_. O operador lógico avalia como verdadeiro **(não** _falso),_ de modo que o efeito é acionado. |
|`{<field>,"Equals":"127.0.0.1"}` |nada |Todos os fósforos |Um elemento de matriz avalia como verdadeiro (127.0.0.1 == 127.0.0.1) e um como falso (127.0.0.1 == 192.168.1.1), de modo que a condição **Equals** é _falsa_ e o efeito não é acionado. |
|`{<field>,"Equals":"10.0.4.1"}` |nada |Todos os fósforos |Ambos os elementos de matriz avaliam como falsos (10.0.4.1 == 127.0.0.1 e 10.0.4.1 == 192.168.1.1), de modo que a condição **Equals** é _falsa_ e o efeito não é acionado. |

## <a name="the-append-effect-and-arrays"></a>O efeito apêndice e as matrizes

O [efeito apêndice](../concepts/effects.md#append) se comporta de forma diferente ** \[ \* ** dependendo se o **detalhes.campo** é um alias ou não.

- Quando não ** \[ \* ** é um alias, o apêndice substitui toda a matriz pela propriedade **de valor**
- Quando ** \[ \* ** um alias, anexar adiciona a propriedade **de valor** à matriz existente ou cria a nova matriz

Para obter mais informações, consulte os exemplos de [apêndice](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Próximas etapas

- Revisar exemplos em [amostras de política do Azure](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Entenda como [criar políticas programáticas.](programmatically-create.md)
- Aprenda a [remediar recursos não compatíveis.](remediate-resources.md)
- Reveja o que é um grupo de gestão com [organize seus recursos com grupos de gerenciamento do Azure.](../../management-groups/overview.md)
