---
title: Elemento de interface do usuário suspensa
description: Descreve o elemento de interface do usuário Microsoft.Common.DropDown para o Portal do Azure. Use para selecionar as opções disponíveis ao implantar um aplicativo gerenciado.
author: tfitzmac
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: tomfitz
ms.openlocfilehash: f5eac1d331bd439ad4066d1dea1b9aa950fcce60
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87004414"
---
# <a name="microsoftcommondropdown-ui-element"></a>Elemento de interface do usuário Microsoft.Common.DropDown

Um controle de seleção com uma lista suspensa. Você pode permitir a seleção de apenas um único item ou vários itens. Opcionalmente, você também pode incluir uma descrição com os itens.

## <a name="ui-sample"></a>Exemplo de interface do usuário

O elemento DropDown tem opções diferentes que determinam sua aparência no Portal.

Quando apenas um único item é permitido para seleção, o controle aparece como:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-1.png" alt-text="Seleção única de Microsoft. Common. DropDown":::

Quando as descrições são incluídas, o controle aparece como:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-2.png" alt-text="Seleção única Microsoft. Common. DropDown com descrições":::

Quando seleção múltipla está habilitada, o controle adiciona uma opção **selecionar tudo** e caixas de seleção para selecionar mais de um item:

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-3.png" alt-text="Seleção múltipla da lista suspensa Microsoft. Common.":::

As descrições podem ser incluídas com a seleção múltipla habilitada.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-4.png" alt-text="Seleção múltipla do Microsoft. Common. DropDown com descrições":::

Quando a filtragem está habilitada, o controle inclui uma caixa de texto para adicionar o valor de filtragem.

:::image type="content" source="./media/managed-application-elements/microsoft-common-dropdown-5.png" alt-text="Seleção múltipla do Microsoft. Common. DropDown com descrições":::

## <a name="schema"></a>Esquema

```json
{
    "name": "element1",
    "type": "Microsoft.Common.DropDown",
    "label": "Example drop down",
    "defaultValue": "Value two",
    "toolTip": "",
    "multiselect": true,  
    "selectAll": true,  
    "filter": true,  
    "filterPlaceholder": "Filter items ...",  
    "multiLine": true,  
    "defaultDescription": "A value for selection",  
    "constraints": {
        "allowedValues": [
            {
                "label": "Value one",
                "description": "The value to select for option 1.",
                "value": "one"
            },
            {
                "label": "Value two",
                "description": "The value to select for option 2.",
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

- Use `multiselect` para especificar se os usuários podem selecionar mais de um item.
- Por padrão, o `selectAll` é `true` quando a seleção múltipla está habilitada.
- A `filter` propriedade permite que os usuários pesquisem dentro de uma longa lista de opções.
- O rótulo de `constraints.allowedValues` é o texto exibido para um item e seu valor é o valor de saída do elemento quando selecionado.
- Se especificado, o valor padrão deve ser um rótulo presente em `constraints.allowedValues`. Se não for especificado, o primeiro item em `constraints.allowedValues` será selecionado. O valor padrão é **NULL**.
- `constraints.allowedValues`deve ter pelo menos um item.
- Para emular um valor que não seja necessário, adicione um item com um rótulo e valor de `""`(string vazia) a`constraints.allowedValues`.
- A `defaultDescription` propriedade é usada para itens que não têm uma descrição.

## <a name="next-steps"></a>Próximas etapas

* Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
* Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).
