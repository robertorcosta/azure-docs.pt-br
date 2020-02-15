---
title: Sintaxe e expressões de modelo
description: Descreve a sintaxe JSON declarativa para modelos de Azure Resource Manager.
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: 7bca3125f80225d2180734f483194a63e39d9cf5
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207393"
---
# <a name="syntax-and-expressions-in-azure-resource-manager-templates"></a>Sintaxe e expressões em modelos de Azure Resource Manager

A sintaxe básica do modelo é JSON. No entanto, você pode usar expressões para estender os valores JSON disponíveis no modelo.  As expressões começam e terminam com colchetes: `[` e `]`, respectivamente. O valor da expressão é avaliado quando o modelo é implantado. Uma expressão pode retornar uma cadeia de caracteres, um inteiro, um booliano, uma matriz ou um objeto.

Uma expressão de modelo não pode exceder 24.576 caracteres.

## <a name="use-functions"></a>Usar funções

Azure Resource Manager fornece [funções](template-functions.md) que você pode usar em um modelo. O exemplo a seguir mostra uma expressão que usa uma função no valor padrão de um parâmetro:

```json
"parameters": {
  "location": {
    "type": "string",
    "defaultValue": "[resourceGroup().location]"
  }
},
```

Dentro da expressão, a sintaxe `resourceGroup()` chama uma das funções que o Gerenciador de recursos fornece para uso em um modelo. Nesse caso, é a função [resourcegroup](template-functions-resource.md#resourcegroup) . Assim como no JavaScript, as chamadas de função são formatadas como `functionName(arg1,arg2,arg3)`. A sintaxe `.location` recupera uma propriedade do objeto retornado por essa função.

As funções do modelo e seus parâmetros não diferenciam maiúsculas de minúsculas. Por exemplo, o Resource Manager resolve **variables('var1')** e **VARIABLES('VAR1')** da mesma forma. Quando avaliada, a função preservará as maiúsculas e minúsculas, a menos que a função modifique-as expressamente (como toUpper ou toLower). Determinados tipos de recursos podem ter requisitos de caso separados de como as funções são avaliadas.

Para passar um valor de cadeia de caracteres como um parâmetro para uma função, use aspas simples.

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]"
```

A maioria das funções funciona da mesma, seja implantada em um grupo de recursos, assinatura, grupo de gerenciamento ou locatário. As funções a seguir têm restrições baseadas no escopo:

* [resourcegroup](template-functions-resource.md#resourcegroup) -só pode ser usado em implantações em um grupo de recursos.
* [ResourceId](template-functions-resource.md#resourceid) -pode ser usado em qualquer escopo, mas os parâmetros válidos são alterados dependendo do escopo.
* [assinatura](template-functions-resource.md#subscription) -só pode ser usada em implantações em um grupo de recursos ou assinatura.

## <a name="escape-characters"></a>Caracteres de escape

Para que uma cadeia de caracteres literal comece com um colchete esquerdo `[` e termine com um colchete direito `]`, mas não o tenha interpretado como uma expressão, adicione um colchete extra para iniciar a cadeia de caracteres com `[[`. Por exemplo, a variável:

```json
"demoVar1": "[[test value]"
```

Resolve para `[test value]`.

No entanto, se a cadeia de caracteres literal não terminar com um colchete, não Percorra o primeiro colchete. Por exemplo, a variável:

```json
"demoVar2": "[test] value"
```

Resolve para `[test] value`.

Para escapar aspas duplas em uma expressão, como adicionar um objeto JSON no modelo, use a barra invertida.

```json
"tags": {
    "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
},
```

## <a name="null-values"></a>Valores nulos

Para definir uma propriedade como nula, você pode usar **NULL** ou **[JSON (' NULL ')]** . A [função JSON](template-functions-array.md#json) retorna um objeto vazio quando você fornece `null` como o parâmetro. Em ambos os casos, os modelos do Resource Manager o tratam como se a propriedade não estiver presente.

```json
"stringValue": null,
"objectValue": "[json('null')]"
```

## <a name="next-steps"></a>Próximas etapas

* Para obter a lista completa das funções de modelo, veja [Funções de modelo do Gerenciador de Recursos do Azure](template-functions.md).
* Para obter mais informações sobre arquivos de modelo, consulte [entender a estrutura e a sintaxe de modelos de Azure Resource Manager](template-syntax.md).
