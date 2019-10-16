---
title: Elemento de interface do usuário DropDown do Azure| Microsoft Docs
description: Descreve o elemento de interface do usuário Microsoft.Common.DropDown para o Portal do Azure. Use para selecionar as opções disponíveis ao implantar um aplicativo gerenciado.
services: managed-applications
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: managed-applications
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: 5784b5df9b522f0489ca1f6087627b45cec5349d
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2019
ms.locfileid: "72331733"
---
# <a name="microsoftcommondropdown-ui-element"></a>Elemento de interface do usuário Microsoft.Common.DropDown
Um controle de seleção com uma lista suspensa.

## <a name="ui-sample"></a>Exemplo de interface do usuário
![Microsoft.Common.DropDown](./media/managed-application-elements/microsoft.common.dropdown.png)

## <a name="schema"></a>Esquema
```json
{
  "name": "element1",
  "type": "Microsoft.Common.DropDown",
  "label": "Example drop down",
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

## <a name="remarks"></a>Comentários

- O rótulo de `constraints.allowedValues` é o texto exibido para um item e seu valor é o valor de saída do elemento quando selecionado.
- Se especificado, o valor padrão deve ser um rótulo presente em `constraints.allowedValues`. Se não for especificado, o primeiro item em `constraints.allowedValues` será selecionado. O valor padrão é **null**.
- `constraints.allowedValues`deve ter pelo menos um item.
- Para emular um valor que não seja necessário, adicione um item com um rótulo e valor de `""`(string vazia) a`constraints.allowedValues`.

## <a name="sample-output"></a>Saída de exemplo
```json
"two"
```

## <a name="next-steps"></a>Próximos passos
* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
