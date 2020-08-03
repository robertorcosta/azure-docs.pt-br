---
title: Descobrir grupos de funções atribuíveis para gerenciar no PIM – Azure AD | Microsoft Docs
description: Saiba como descobrir grupos de função atribuíveis para gerenciar como grupos de acesso privilegiado no Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 07/27/2020
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b66a41f0f1aa48433dac77fca56d5e2877989a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/31/2020
ms.locfileid: "87505998"
---
# <a name="discover-privileged-access-groups-preview-to-manage-in-privileged-identity-management"></a>Descobrir grupos de acesso privilegiado (versão prévia) para gerenciar no Privileged Identity Management

No Azure Active Directory (AD do Azure), você pode atribuir funções internas do Azure AD a grupos de nuvem para simplificar o gerenciamento de atribuições de função. Agora você pode usar o Privileged Identity Management (PIM) para atribuir a qualificação para associação ou propriedade desses grupos, para proteger as funções do Azure AD e para proteger o acesso. Antes de gerenciar um grupo de funções do Azure AD que possa ser atribuído como um grupo de acesso privilegiado no Privileged Identity Management, você deve descobri-lo e trazê-lo sob gerenciamento no PIM.

## <a name="discover-resources"></a>Descobrir recursos

1. [Entre no Azure ad](https://aad.portal.azure.com) com permissões de função de administrador de função privilegiada.
1. Crie um grupo de funções que pode ser atribuído no Azure AD. Você deve ser um proprietário do grupo para descobrir e gerenciá-lo com Privileged Identity Management.
1. Abra **Privileged Identity Management**.
1. Selecione **acesso privilegiado (versão prévia)**.

    ![Comando Discover groups para a primeira experiência](./media/groups-discover-groups/groups-discover-groups.png)

1. Selecione **descobrir grupos**.
1. Pesquisar por nome de grupo.
1. Selecione o grupo e selecione **gerenciar grupos** para trazê-lo para o gerenciamento do PIM.

    ![Descobrir grupos sem recursos listados para a primeira experiência](./media/groups-discover-groups/groups-bring-under-management.png)

    > [!NOTE]
    > Depois que um grupo de acesso privilegiado é gerenciado, ele não pode ser retirado do gerenciamento. Isso impede que outro administrador de recursos remova Privileged Identity Management configurações.

1. Se você vir uma mensagem para confirmar a integração do recurso selecionado para gerenciamento, selecione **Sim**.

## <a name="next-steps"></a>Próximas etapas

- [Configurar atribuições de grupo de acesso privilegiado no Privileged Identity Management](pim-resource-roles-configure-role-settings.md)
- [Atribuir grupos de acesso privilegiado no Privileged Identity Management](pim-resource-roles-assign-roles.md)
