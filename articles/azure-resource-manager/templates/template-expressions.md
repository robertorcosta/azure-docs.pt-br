---
title: Sintaxe de modelo sintaxe e expressões
description: Descreve a sintaxe declarativa JSON para modelos do Azure Resource Manager.
ms.topic: conceptual
ms.date: 03/17/2020
ms.openlocfilehash: 172838fa24709eb60fbcb6a68277f44bbd42f01e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79460102"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Sintaxe e expressões nos modelos do Azure Resource Manager

A sintaxe básica do modelo é JSON. No entanto, você pode usar expressões para estender os valores JSON disponíveis dentro do modelo.  As expressões começam e terminam com colchetes: `[` e `]`, respectivamente. O valor da expressão é avaliado quando o modelo é implantado. Uma expressão pode retornar uma cadeia de caracteres, um inteiro, um booliano, uma matriz ou um objeto.

Uma expressão de modelo não pode exceder 24.576 caracteres.

## <a name="use-functions"></a>Usar funções

O Azure Resource Manager fornece [funções](template-functions.md) que você pode usar em um modelo. O exemplo a seguir mostra uma expressão que usa uma função no valor padrão de um parâmetro:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

Dentro da expressão, a `resourceGroup()` sintaxe chama uma das funções que o Resource Manager fornece para uso dentro de um modelo. Neste caso, é a função [resourceGroup.](template-functions-resource.md#resourcegroup) Assim como no JavaScript, as chamadas de função são formatadas como `functionName(arg1,arg2,arg3)`. A sintaxe `.location` recupera uma propriedade do objeto devolvido por essa função.

As funções do modelo e seus parâmetros não diferenciam maiúsculas de minúsculas. Por exemplo, o Resource Manager resolve **variables('var1')** e **VARIABLES('VAR1')** da mesma forma. Quando avaliada, a função preservará as maiúsculas e minúsculas, a menos que a função modifique-as expressamente (como toUpper ou toLower). Certos tipos de recursos podem ter requisitos de caso separados de como as funções são avaliadas.

Para passar um valor de string como parâmetro para uma função, use aspas simples.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

A maioria das funções funciona da mesma forma, seja implantada em um grupo de recursos, assinatura, grupo de gerenciamento ou inquilino. As seguintes funções têm restrições com base no escopo:

* [resourceGroup](template-functions-resource.md#resourcegroup) - só pode ser usado em implantações para um grupo de recursos.
* [resourceId](template-functions-resource.md#resourceid) - pode ser usado em qualquer escopo, mas os parâmetros válidos mudam dependendo do escopo.
* [assinatura](template-functions-resource.md#subscription) - só pode ser usado em implantações para um grupo de recursos ou assinatura.

## <a name="escape-characters"></a>Caracteres de escape

Para ter uma seqüência `[` literal começar com `]`um suporte esquerdo e terminar com um suporte direito, `[[`mas não tê-lo interpretado como uma expressão, adicione um suporte extra para iniciar a seqüência com . Por exemplo, a variável:

```json
"demoVar1": "[[test value]"
```

Resolve para `[test value]`.

No entanto, se a seqüência literal não terminar com um suporte, não fuja do primeiro suporte. Por exemplo, a variável:

```json
"demoVar2": "[test] value"
```

Resolve para `[test] value`.

Para escapar de aspas duplas em uma expressão, como adicionar um objeto JSON no modelo, use a barra invertida.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

Ao passar os valores dos parâmetros, o uso de caracteres de fuga depende de onde o valor do parâmetro é especificado. Se você definir um valor padrão no modelo, você precisará do suporte esquerdo extra.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1":{
            "type": "string",
            "defaultValue": "[[test value]"
        }
    },
    "resources": [],
    "outputs": {
        "exampleOutput": {
            "type": "string",
            "value": "[parameters('demoParam1')]"
        }
    }
}
```

Se você usar o valor `[test value]`padrão, o modelo retorna .

No entanto, se você passar em um valor de parâmetro através da linha de comando, os caracteres são interpretados literalmente. Implantação do modelo anterior com:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[[test value]"
```

Retorna `[[test value]`. Em vez disso, use:

```azurepowershell
New-AzResourceGroupDeployment -ResourceGroupName demoGroup -TemplateFile azuredeploy.json -demoParam1 "[test value]"
```

A mesma formatação se aplica ao passar valores de um arquivo de parâmetro. Os personagens são interpretados literalmente. Quando usado com o modelo anterior, `[test value]`o seguinte parâmetro de arquivo retorna:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "demoParam1": {
            "value": "[test value]"
        }
   }
}
```

## <a name="null-values"></a>Valores nulos

Para definir uma propriedade como nula, você pode usar **nulo** ou **[json('nulo')]**. A [função json](template-functions-array.md#json) retorna um `null` objeto vazio quando você fornece como parâmetro. Em ambos os casos, os modelos do Gerenciador de Recursos tratam-no como se a propriedade não estivesse presente.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Próximas etapas

* Para obter a lista completa das funções de modelo, veja [Funções de modelo do Gerenciador de Recursos do Azure](template-functions.md).
* Para obter mais informações sobre arquivos de modelo, consulte [Entenda a estrutura e a sintaxe dos modelos do Azure Resource Manager](template-syntax.md).
