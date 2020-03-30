---
title: Elemento de IU do Seletor de Identidade
description: Descreve o elemento Microsoft.ManagedIdentity.IdentitySelector UI para o portal Azure. Use para atribuir identidades gerenciadas a um recurso.
author: tfitzmac
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: tomfitz
ms.openlocfilehash: cb66a2684e0b83f4f0cc01a07cc724f6beab4d68
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087538"
---
# <a name="microsoftmanagedidentityidentityselector-ui-element"></a>Microsoft.ManagedIdentity.IdentitySelector uI element

Um controle para atribuir [identidades gerenciadas](../../active-directory/managed-identities-azure-resources/overview.md) para um recurso em uma implantação.

## <a name="ui-sample"></a>Exemplo de interface do usuário

O controle consiste nos seguintes elementos:

![Microsoft.ManagedIdentity.IdentitySelectou primeira etapa](./media/managed-application-elements/microsoft.managedidentity.identityselector1.png)

Quando o usuário seleciona **Adicionar,** o formulário a seguir é aberto. O usuário pode selecionar uma ou mais identidades atribuídas pelo usuário para o recurso.

![Microsoft.ManagedIdentity.IdentitySelectou segunda etapa](./media/managed-application-elements/microsoft.managedidentity.identityselector2.png)

As identidades selecionadas são exibidas na tabela. O usuário pode adicionar ou excluir itens desta tabela.

![Microsoft.ManagedIdentity.IdentitySelectou terceira etapa](./media/managed-application-elements/microsoft.managedidentity.identityselector3.png)

## <a name="schema"></a>Esquema

```json
{
  "name": "identity",
  "type": "Microsoft.ManagedIdentity.IdentitySelector",
  "label": "Managed Identity Configuration",
  "toolTip": {
    "systemAssignedIdentity": "Enable system assigned identity to grant the resource access to other existing resources.",
    "userAssignedIdentity": "Add user assigned identities to grant the resource access to other existing resources."
  },
  "defaultValue": {
    "systemAssignedIdentity": "Off"
  },
  "options": {
    "hideSystemAssignedIdentity": false,
    "hideUserAssignedIdentity": false
  },
  "visible": true
}
```

## <a name="sample-output"></a>Saída de exemplo

```json
{
  "identity": {
    "value": {
      "type": "UserAssigned",
      "userAssignedIdentities": {
        "/subscriptions/xxxx/resourceGroups/TestResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/TestUserIdentity1": {}
      }
    }
  }
}
```

## <a name="remarks"></a>Comentários

- Use **o defaultValue.systemAssignedIdentity** para definir um valor inicial para o controle de opções de identidade atribuídas ao sistema. O valor padrão está **desligado**. Os seguintes valores são permitidos:
  - **On** – Uma identidade atribuída ao sistema é atribuída ao recurso.
  - **Off** – Uma identidade atribuída ao sistema não é atribuída ao recurso.
  - **OnOnly** – Uma identidade atribuída ao sistema é atribuída ao recurso. Os usuários não podem editar esse valor durante a implantação.
  - **OffOnly** – Uma identidade atribuída ao sistema não é atribuída ao recurso. Os usuários não podem editar esse valor durante a implantação.

- Se **options.hideSystemAssignedIdentity** for definido **como true,** a ui para configurar a identidade atribuída ao sistema não será exibida. O valor padrão para esta opção é **falso**.
- Se **opções.hideUserAssignedIdentity** for definido **como true,** a ui para configurar a identidade atribuída pelo usuário não será exibida. O recurso não é atribuído a um usuário atribuído identidade. O valor padrão para esta opção é **falso**.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma introdução à criação de definições de interface do usuário, consulte [Introdução ao CreateUiDefinition](create-uidefinition-overview.md).
- Para obter uma descrição das propriedades comuns em elementos de interface do usuário, consulte [Elementos de CreateUiDefinition](create-uidefinition-elements.md).