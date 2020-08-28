---
title: Criar políticas para propriedades de matriz em recursos
description: Aprenda a trabalhar com parâmetros de matriz e expressões de linguagem de matriz, avaliar o alias [*] e acrescentar elementos com regras de definição do Azure Policy.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: 5b9392a943e264ae5eca989ee87eb9ff09b36972
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/28/2020
ms.locfileid: "89048475"
---
# <a name="author-policies-for-array-properties-on-azure-resources"></a>Criar políticas para propriedades de matriz em recursos do Azure

As propriedades do Azure Resource Manager são normalmente definidas como cadeias de caracteres e boolianos. Quando existe uma relação um-para-muitos, as propriedades complexas são definidas como matrizes. No Azure Policy, as matrizes são usadas de várias maneiras diferentes:

- O tipo de um [parâmetro de definição](../concepts/definition-structure.md#parameters), para fornecer várias opções
- Parte de uma [regra de política](../concepts/definition-structure.md#policy-rule) usando as condições **in** ou **notIn**
- Parte de uma regra de política que avalia o [alias \[\*\]](../concepts/definition-structure.md#understanding-the--alias) para avaliar:
  - Cenários como **None**, **Any** ou **All**
  - Cenários complexos com **count**
- No [efeito append](../concepts/effects.md#append) para substituir ou adicionar a uma matriz existente

Este artigo aborda cada uso pelo Azure Policy e fornece várias definições de exemplo.

## <a name="parameter-arrays"></a>Matrizes de parâmetros

### <a name="define-a-parameter-array"></a>Definir uma matriz de parâmetros

A definição de um parâmetro como uma matriz permite a flexibilidade da política quando mais de um valor é necessário.
Essa definição de política permite qualquer local único para o parâmetro **allowedLocations** e usa _eastus2_ como padrão:

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

Como **type** era _string_, apenas um valor pode ser definido ao atribuir a política. Se essa política for atribuída, os recursos no escopo só serão permitidos em uma região do Azure. A maioria das definições de política precisa permitir uma lista de opções aprovadas, como permitir _eastus2_, _eastus_ e _westus2_.

Para criar a definição de política para permitir várias opções, use o **type** _array_. A mesma política pode ser reescrita da seguinte maneira:

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
> Quando uma definição de política é salva, a propriedade **type** em um parâmetro não pode ser alterada.

Essa nova definição de parâmetro usa mais de um valor durante a atribuição de política. Com a propriedade de matriz **allowedValues** definida, os valores disponíveis durante a atribuição são limitados ainda mais à lista predefinida de opções. O uso de **allowedValues** é opcional.

### <a name="pass-values-to-a-parameter-array-during-assignment"></a>Passar valores para uma matriz de parâmetros durante a atribuição

Ao atribuir a política por meio do portal do Azure, um parâmetro de **type** _array_ é exibido como uma caixa de texto. A dica diz "Use ; para separar valores. (por exemplo, Londres; Nova York)". Para passar os valores de local permitidos de _eastus2_, _eastus_ e _westus2_ para o parâmetro, use a seguinte cadeia de caracteres:

`eastus2;eastus;westus2`

O formato do valor do parâmetro é diferente ao usar CLI do Azure, o Azure PowerShell ou a API REST. Os valores são passados por meio de uma cadeia de caracteres JSON que também inclui o nome do parâmetro.

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

Para usar essa cadeia de caracteres com cada SDK, use os seguintes comandos:

- CLI do Azure: Comando [az policy assignment create](/cli/azure/policy/assignment#az-policy-assignment-create) com o parâmetro **params**
- Azure PowerShell: Cmdlet [New-AzPolicyAssignment](/powershell/module/az.resources/New-Azpolicyassignment) com o parâmetro **PolicyParameter**
- API REST: Na operação [create](/rest/api/resources/policyassignments/create) _PUT_ como parte do corpo da solicitação, como o valor da propriedade **Properties.Parameters**

## <a name="policy-rules-and-arrays"></a>Matrizes e regras de política

### <a name="array-conditions"></a>Condições de matriz

A regra de política [condições](../concepts/definition-structure.md#conditions) com a qual um **type**
_array_ do parâmetro pode ser usado limita-se a `in` e `notIn`. Use a seguinte definição de política com a condição `equals` como um exemplo:

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

A tentativa de criar essa definição de política por meio do portal do Azure leva a um erro como esta mensagem de erro:

- "A política '{GUID}' não pôde ser parametrizada devido a erros de validação. Verifique se os parâmetros da política estão definidos corretamente. A exceção interna 'O resultado da avaliação da expressão de linguagem [parameters('allowedLocations')]' é do tipo 'Array', o tipo esperado é 'String'.".

O **type** esperado da condição `equals` é _string_. Como **allowedLocations** é definido como **type** _array_, o mecanismo de política avalia a expressão de linguagem e gera o erro. Com a condição `in` e `notIn`, o mecanismo de política espera o **type** _array_ na expressão de linguagem. Para resolver essa mensagem de erro, altere `equals` para `in` ou `notIn`.

### <a name="evaluating-the--alias"></a>Avaliar o alias [*]

Os aliases que têm **\[\*\]** anexados ao nome indicam que **type** é _array_. Em vez de avaliar o valor de toda a matriz, **\[\*\]** torna possível avaliar cada elemento da matriz individualmente, com AND lógico entre eles. Há três cenários padrão em que essa avaliação por item é útil: _None_, _Any_ ou _All_ (respectivamente, nenhum, alguns ou todos) os elementos correspondem. Para cenários complexos, use [count](../concepts/definition-structure.md#count).

O mecanismo de política dispara o **effect** em **then** somente quando a regra **if** é avaliada como true.
É importante entender esse fato no contexto da maneira em que **\[\*\]** avalia cada elemento individual da matriz.

A regra de política de exemplo para a tabela de cenário abaixo:

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

Para o cenário de tabela mostrado abaixo, a matriz **ipRules** é da seguinte maneira:

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

Para cada exemplo de condição abaixo, substitua `<field>` por `"field": "Microsoft.Storage/storageAccounts/networkAcls.ipRules[*].value"`.

Os seguintes resultados são o resultado da combinação entre a condição, da regra de política de exemplo e da matriz de valores existentes acima:

|Condição |Resultado | Cenário |Explicação |
|-|-|-|-|
|`{<field>,"notEquals":"127.0.0.1"}` |Nada |Nenhuma correspondência |Um elemento de matriz é avaliado como false (127.0.0.1 != 127.0.0.1) e outro como true (127.0.0.1 != 192.168.1.1), portanto, a condição **notEquals** é _false_ e o efeito não é disparado. |
|`{<field>,"notEquals":"10.0.4.1"}` |Efeito de política |Nenhuma correspondência |Ambos os elementos da matriz são avaliados como true (10.0.4.1 != 127.0.0.1 e 10.0.4.1 != 192.168.1.1), então a condição **notEquals** é _true_ e o efeito é disparado. |
|`"not":{<field>,"notEquals":"127.0.0.1" }` |Efeito de política |Uma ou mais correspondências |Um elemento de matriz é avaliado como false (127.0.0.1 != 127.0.0.1) e outro como true (127.0.0.1 != 192.168.1.1), então a condição **notEquals** é _false_. O operador lógico é avaliado como true (**not** _false_), então o efeito é disparado. |
|`"not":{<field>,"notEquals":"10.0.4.1"}` |Nada |Uma ou mais correspondências |Ambos os elementos da matriz são avaliados como true (10.0.4.1 != 127.0.0.1 e 10.0.4.1 != 192.168.1.1), então a condição **notEquals** é _true_. O operador lógico é avaliado como falso (**not** _true_), então o efeito não é disparado. |
|`"not":{<field>,"Equals":"127.0.0.1"}` |Efeito de política |Nem todos correspondem |Um elemento de matriz é avaliado como true (127.0.0.1 == 127.0.0.1) e outro como false (127.0.0.1 == 192.168.1.1), então a condição **Equals** é _false_. O operador lógico é avaliado como true (**not** _false_), então o efeito é disparado. |
|`"not":{<field>,"Equals":"10.0.4.1"}` |Efeito de política |Nem todos correspondem |Ambos os elementos da matriz são avaliados como false (10.0.4.1 == 127.0.0.1 e 10.0.4.1 == 192.168.1.1), então a condição **Equals** é _false_. O operador lógico é avaliado como true (**not** _false_), então o efeito é disparado. |
|`{<field>,"Equals":"127.0.0.1"}` |Nada |Todos correspondem |Um elemento de matriz é avaliado como true (127.0.0.1 == 127.0.0.1) e outro como false (127.0.0.1 == 192.168.1.1), portanto, a condição **Equals** é _false_ e o efeito não é disparado. |
|`{<field>,"Equals":"10.0.4.1"}` |Nada |Todos correspondem |Ambos os elementos da matriz são avaliados como true (10.0.4.1 == 127.0.0.1 e 10.0.4.1 == 192.168.1.1), então a condição **Equals** é _false_ e o efeito não é disparado. |

## <a name="the-append-effect-and-arrays"></a>O efeito append e as matrizes

O [efeito append](../concepts/effects.md#append) se comporta de maneira diferente dependendo de o **details.field** ser um alias de **\[\*\]** ou não.

- Quando ele não é um alias **\[\*\]** , append substitui toda a matriz pela propriedade **value**
- Quando ele é um alias **\[\*\]** , append adiciona a propriedade **value** à matriz existente ou cria outra matriz

Para obter mais informações, consulte os [exemplos de append](../concepts/effects.md#append-examples).

## <a name="next-steps"></a>Próximas etapas

- Examine os exemplos em [amostras do Azure Policy](../samples/index.md).
- Revise a [estrutura de definição do Azure Policy](../concepts/definition-structure.md).
- Revisar [Compreendendo os efeitos da política](../concepts/effects.md).
- Entenda como [criar políticas de maneira programática](programmatically-create.md).
- Saiba como [corrigir recursos fora de conformidade](remediate-resources.md).
- Veja o que é um grupo de gerenciamento com [Organizar seus recursos com grupos de gerenciamento do Azure](../../management-groups/overview.md).
