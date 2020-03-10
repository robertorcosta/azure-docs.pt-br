---
title: Atribuir funções de recurso do Azure no Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Saiba como atribuir funções de recurso do Azure no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 10/23/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34051a31c6ccf69356f330d7c5ecb009f760857a
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375446"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Atribuir funções de recurso do Azure no Privileged Identity Management

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) pode gerenciar as funções de recurso internas do Azure, bem como as funções personalizadas, incluindo (mas não se limitando a):

- Proprietário
- Administrador de Acesso do Usuário
- Contribuinte
- Administrador de Segurança
- Gerenciador de Segurança

> [!NOTE]
> Os usuários ou membros de um grupo atribuído ao proprietário ou às funções de assinatura do administrador de acesso do usuário e aos administradores globais do Azure AD que habilitam o gerenciamento de assinaturas no Azure AD têm permissões de administrador de recursos por padrão. Esses administradores podem atribuir funções, definir configurações de função e revisar o acesso usando Privileged Identity Management para recursos do Azure. Um usuário não pode gerenciar Privileged Identity Management para recursos sem permissões de administrador de recursos. Exiba a lista de [funções internas para recursos do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Atribuir uma função

Siga estas etapas para tornar um usuário qualificado para uma função de recurso do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função de [administrador de função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) .

    Para obter informações sobre como conceder a outro administrador acesso para gerenciar Privileged Identity Management, consulte [conceder acesso a outros administradores para gerenciar Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Abra o **Azure AD Privileged Identity Management**.

1. Selecione **Recursos do Azure**.

1. Use o **Filtro de recurso** para filtrar a lista de recursos gerenciados.

    ![Lista de recursos do Azure para gerenciar](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Selecione o recurso que você deseja gerenciar, como uma assinatura ou grupo de gerenciamento.

1. Em gerenciar, selecione **funções** para ver a lista de funções para recursos do Azure.

    ![Funções dos recursos do Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Selecione **Adicionar membro** para abrir o painel nova atribuição.

1. Selecione **selecionar uma função** para abrir o painel Selecionar uma função.

    ![Novo painel de atribuição](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Selecione uma função que você deseja atribuir e clique em **Selecionar**.

    O painel Selecionar um membro ou grupo é aberto.

1. Selecione um membro ou grupo que você deseja atribuir à função e, em seguida, clique em **selecionar**.

    ![Selecione um painel de membro ou grupo](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    O painel de configurações de Associação é aberto.

1. Na lista **Tipo de atribuição**, selecione **Qualificado** ou **Ativo**.

    ![Painel de configurações das Associações](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    Privileged Identity Management para recursos do Azure fornece dois tipos distintos de atribuição:

    - Atribuições **qualificadas** exigem que o membro da função execute uma ação para usar a função. As ações podem incluir a execução de uma verificação de MDA (Autenticação Multifator), fornecimento de uma justificativa comercial ou solicitação de aprovação dos aprovadores designados.

    - Atribuições **ativas** não exigem que o membro execute qualquer ação para usar a função. Membros atribuídos como ativos sempre possuem privilégios atribuídos pela função.

1. Se a atribuição deve ser permanente (permanentemente qualificada ou permanentemente atribuída), marque a caixa de seleção **permanentemente** .

    Dependendo das configurações de função, a caixa de seleção poderá não aparecer ou não ser modificável.

1. Para especificar uma duração de atribuição específica, desmarque a caixa de seleção e modifique as caixas de data e hora de início e/ou término.

    ![Configurações de associação - data e hora](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Quando terminar, selecione **concluído**.

    ![Nova atribuição - Adicionar](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Para criar a nova atribuição de função, selecione **Adicionar**. Uma notificação do status é exibida.

    ![Nova atribuição - Notificação](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Atualizar ou remover uma atribuição de função existente

Siga estas etapas para atualizar ou remover uma atribuição de função existente.

1. Abra o **Azure AD Privileged Identity Management**.

1. Selecione **Recursos do Azure**.

1. Selecione o recurso que você deseja gerenciar, como uma assinatura ou grupo de gerenciamento.

1. Em gerenciar, selecione **funções** para ver a lista de funções para recursos do Azure.

    ![Funções de recursos do Azure - Selecionar função](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Selecione a função que você deseja atualizar ou remover.

1. Localize a atribuição de função nas guias **Funções qualificadas** ou **Funções ativas**.

    ![Atualizar ou remover atribuição de função](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Selecione **Atualizar** ou **Remover** para atualizar ou remover a atribuição de função.

    Para obter informações sobre como estender uma atribuição de função, consulte [estender ou renovar funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- [Estender ou renovar funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-renew-extend.md)
- [Definir configurações de função de recurso do Azure no Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções do Azure AD no Privileged Identity Management](pim-how-to-add-role-to-user.md)
