---
title: Elemento de IU da caixa de texto
description: Descreve o elemento de interface do usuário Microsoft.Common.TextBox para o Portal do Azure. Use para adicionar texto não formatado.
author: tfitzmac
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: tomfitz
ms.openlocfilehash: e9f084badda9ea1905e43c6f00b29aaf957a6dbd
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2020
ms.locfileid: "75652275"
---
# <a name="microsoftcommontextbox-ui-element"></a>Elemento de interface do usuário Microsoft.Common.TextBox

Um controle que pode ser usado para editar texto não formatado.

## <a name="ui-sample"></a>Exemplo de interface do usuário

![Microsoft.Common.TextBox](./media/managed-application-elements/microsoft.common.textbox.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "element1",
  "type": "Microsoft.Common.TextBox",
  "label": "Example text box 1",
  "defaultValue": "my text value",
  "toolTip": "Use only allowed characters",
  "constraints": {
    "required": true,
    "regex": "^[a-z0-9A-Z]{1,30}$",
    "validationMessage": "Only alphanumeric characters are allowed, and the value must be 1-30 characters long."
  },
  "visible": true
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
"my text value"
```

## <a name="remarks"></a>Comentários

- Se `constraints.required` estiver definido como **true**, a caixa de texto deve ter um valor para validar com êxito. O valor padrão é **false**.
- `constraints.regex` é um padrão de expressão regular JavaScript. Se especificado, o valor da caixa de texto deve corresponder ao padrão para validar com êxito. O valor padrão é **null**.
- `constraints.validationMessage` é uma cadeia de caracteres a ser exibida quando o valor da caixa de texto falha na validação. Se não for especificado, as mensagens de validação internas da caixa de texto serão usadas. O valor padrão é **null**.
- É possível especificar um valor para `constraints.regex` quando `constraints.required` é definido como **false**. Nesse cenário, um valor não é necessário para a caixa de texto para validar com êxito. Se um valor for especificado, ele deve corresponder ao padrão da expressão regular.

## <a name="next-steps"></a>Próximos passos

* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
