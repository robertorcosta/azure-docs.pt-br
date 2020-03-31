---
title: Atribuir funções de recursos do Azure no Gerenciamento de Identidade Privilegiada - Diretório Ativo do Azure | Microsoft Docs
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266553"
---
# <a name="assign-azure-resource-roles-in-privileged-identity-management"></a>Atribuir funções de recurso do Azure no Gerenciamento de Identidade Privilegiada

O Azure Active Directory (Azure AD) Privileged Identity Management (PIM) pode gerenciar as funções de recurso incorporadas do Azure, bem como funções personalizadas, incluindo (mas não se limitando a):

- Proprietário
- Administrador de Acesso do Usuário
- Colaborador
- Administrador de Segurança
- Gerenciador de Segurança

> [!NOTE]
> Os usuários ou membros de um grupo atribuído às funções de assinatura Proprietário ou Administrador de Acesso ao Usuário e administradores globais do Azure AD que permitem o gerenciamento de assinaturas no Azure AD têm permissões de administrador de recursos por padrão. Esses administradores podem atribuir funções, configurar configurações de funções e revisar o acesso usando o Gerenciamento de Identidade Privilegiada para recursos do Azure. Um usuário não pode gerenciar o Gerenciamento de Identidade Privilegiada para Recursos sem permissões de administrador de recursos. Exiba a lista de [funções internas para recursos do Azure](../../role-based-access-control/built-in-roles.md).

## <a name="assign-a-role"></a>Atribuir uma função

Siga estas etapas para tornar um usuário qualificado para uma função de recurso do Azure.

1. Faça login no [portal Azure](https://portal.azure.com/) com um usuário que é membro da função [de administrador de funções privilegiadas.](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

    Para obter informações sobre como conceder a outro administrador acesso para gerenciar o Gerenciamento de Identidade Privilegiada, consulte [Conceder acesso a outros administradores para gerenciar o Gerenciamento de Identidade Privilegiada](pim-how-to-give-access-to-pim.md).

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **Recursos do Azure**.

1. Use o **Filtro de recurso** para filtrar a lista de recursos gerenciados.

    ![Lista de recursos do Azure para gerenciar](./media/pim-resource-roles-assign-roles/resources-list.png)

1. Selecione o recurso que deseja gerenciar, como uma assinatura ou um grupo de gerenciamento.

1. Em Gerenciar, selecione **Funções** para ver a lista de funções para os recursos do Azure.

    ![Funções dos recursos do Azure](./media/pim-resource-roles-assign-roles/resources-roles.png)

1. Selecione **Adicionar membro** para abrir o painel de atribuição Novo.

1. Selecione **Selecionar uma função** para abrir o Painel Selecionar uma função.

    ![Novo painel de atribuição](./media/pim-resource-roles-assign-roles/resources-select-role.png)

1. Selecione uma função que você deseja atribuir e clique em **Selecionar**.

    O painel Selecionar um membro ou grupo é aberto.

1. Selecione um membro ou grupo que deseja atribuir à função e clique em **Selecionar**.

    ![Selecione um painel de membro ou grupo](./media/pim-resource-roles-assign-roles/resources-select-member-or-group.png)

    O painel de configurações de Associação é aberto.

1. Na lista **Tipo de atribuição**, selecione **Qualificado** ou **Ativo**.

    ![Painel de configurações das Associações](./media/pim-resource-roles-assign-roles/resources-membership-settings-type.png)

    O gerenciamento de identidade privilegiado para recursos do Azure fornece dois tipos de atribuição distintos:

    - **As** atribuições elegíveis exigem que o membro da função execute uma ação para usar a função. As ações podem incluir a execução de uma verificação de MDA (Autenticação Multifator), fornecimento de uma justificativa comercial ou solicitação de aprovação dos aprovadores designados.

    - **As** atribuições ativas não exigem que o membro execute qualquer ação para usar a função. Membros atribuídos como ativos sempre possuem privilégios atribuídos pela função.

1. Se a atribuição for permanente (permanentemente elegível ou permanentemente atribuído), selecione a caixa de seleção **Permanente.**

    Dependendo das configurações de função, a caixa de seleção poderá não aparecer ou não ser modificável.

1. Para especificar uma duração de atribuição específica, desmarque a caixa de seleção e modifique as caixas de data e hora de início e/ou término.

    ![Configurações de associação - data e hora](./media/pim-resource-roles-assign-roles/resources-membership-settings-date.png)

1. Quando terminar, selecione **Concluído**.

    ![Nova atribuição - Adicionar](./media/pim-resource-roles-assign-roles/resources-new-assignment-add.png)

1. Para criar a nova atribuição de função, selecione **Adicionar**. Uma notificação do status é exibida.

    ![Nova atribuição - Notificação](./media/pim-resource-roles-assign-roles/resources-new-assignment-notification.png)

## <a name="update-or-remove-an-existing-role-assignment"></a>Atualizar ou remover uma atribuição de função existente

Siga estas etapas para atualizar ou remover uma atribuição de função existente.

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **Recursos do Azure**.

1. Selecione o recurso que deseja gerenciar, como uma assinatura ou um grupo de gerenciamento.

1. Em Gerenciar, selecione **Funções** para ver a lista de funções para os recursos do Azure.

    ![Funções de recursos do Azure - Selecionar função](./media/pim-resource-roles-assign-roles/resources-update-select-role.png)

1. Selecione a função que você deseja atualizar ou remover.

1. Localize a atribuição de função nas guias **Funções qualificadas** ou **Funções ativas**.

    ![Atualizar ou remover atribuição de função](./media/pim-resource-roles-assign-roles/resources-update-remove.png)

1. Selecione **Atualizar** ou **Remover** para atualizar ou remover a atribuição de função.

    Para obter informações sobre como estender uma atribuição de função, consulte [Estender ou renovar funções de recursos do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-renew-extend.md).

## <a name="next-steps"></a>Próximas etapas

- [Estender ou renovar funções de recursos do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-renew-extend.md)
- [Configure as configurações de função de recurso do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções ad do Azure no gerenciamento de identidade privilegiado](pim-how-to-add-role-to-user.md)
