---
title: TagsByResource UI elemento
description: Descreve o elemento Microsoft.TagsByResource UI para o portal Azure. Use para aplicar tags a um recurso durante a implantação.
author: tfitzmac
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: tomfitz
ms.openlocfilehash: 23a7c54a84ec083b8fa470f26582913fcc3d2ee6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75652197"
---
# <a name="microsoftcommontagsbyresource-ui-element"></a>Microsoft.Common.TagsByResource UI element

Um controle para associar [tags](../management/tag-resources.md) com os recursos em uma implantação.

## <a name="ui-sample"></a>Exemplo de interface do usuário

![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.tagsbyresource.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TagsByResource",
  "resources": [
    "Microsoft.Storage/storageAccounts",
    "Microsoft.Compute/virtualMachines"
  ]
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
{
  "Microsoft.Storage/storageAccounts": {
    "Dept": "Finance",
    "Environment": "Production"
  },
  "Microsoft.Compute/virtualMachines": {
    "Dept": "Finance"
  }
}
```

## <a name="remarks"></a>Comentários

- Pelo menos um item na `resources` matriz deve ser especificado.
- Cada elemento `resources` em deve ser um tipo de recurso totalmente qualificado. Esses elementos aparecem na gota **de recursos** e são marcados pelo usuário.
- A saída do controle é formatada para fácil atribuição de valores de tag em um modelo do Azure Resource Manager. Para receber a saída do controle em um modelo, inclua um parâmetro no modelo, conforme mostrado no exemplo a seguir:

  ```json
  "parameters": {
    "tagsByResource": { "type": "object", "defaultValue": {} }
  }
  ```

  Para cada recurso que pode ser marcado, atribua a propriedade de tags ao valor do parâmetro para esse tipo de recurso:

  ```json
  {
    "name": "saName1",
    "type": "Microsoft.Storage/storageAccounts",
    "tags": "[ if(contains(parameters('tagsByResource'), 'Microsoft.Storage/storageAccounts'), parameters('tagsByResource')['Microsoft.Storage/storageAccounts'], json('{}')) ]",
    ...
  ```

- Use a função [if](../templates/template-functions-logical.md#if) ao acessar o parâmetro tagsByResource. Ele permite que você atribua um objeto vazio quando nenhuma marca é atribuída ao determinado tipo de recurso.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
- Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
