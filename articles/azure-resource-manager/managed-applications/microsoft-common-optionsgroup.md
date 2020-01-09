---
title: Elemento de interface do usuário de opções
description: Descreve o elemento Microsoft.Common.OptionsGroup da interface do usuário para o Portal do Azure. Permite que os usuários selecionem as opções disponíveis ao implantar um aplicativo gerenciado.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: c1da0b0082bfe046adf9596a10397e9063c888be
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652340"
---
# <a name="microsoftcommonoptionsgroup-ui-element"></a>Elemento de interface do usuário Microsoft.Common.OptionsGroup

Um controle de seleção com uma linha de opções disponíveis.

## <a name="ui-sample"></a>Exemplo de interface do usuário

![Microsoft.Common.OptionsGroup](./media/managed-application-elements/microsoft.common.optionsgroup.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.OptionsGroup",
  "label": "Some options group",
  "defaultValue": "Value two",
  "toolTip": "",
  "constraints": {
    "allowedValues": [
      {
        "label": "Value one",
        "value": "one"
      },
      {
        "label": "Value two",
        "value": "two"
      }
    ],
    "required": true
  },
  "visible": true
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
"two"
```

## <a name="remarks"></a>Comentários

- O rótulo de `constraints.allowedValues` é o texto exibido para um item e seu valor é o valor de saída do elemento quando selecionado.
- Se especificado, o valor padrão deve ser um rótulo presente em `constraints.allowedValues`. Se não for especificado, o primeiro item em `constraints.allowedValues` será selecionado por padrão. O valor padrão é **null**.
- `constraints.allowedValues`deve ter pelo menos um item.

## <a name="next-steps"></a>Próximos passos

* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
