---
title: Descubra os recursos do Azure para gerenciar no PIM - Azure AD | Microsoft Docs
description: Saiba como descobrir recursos do Azure para gerenciar no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87da43100c7494937ddc842e0f903ba3a360959e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74022884"
---
# <a name="discover-azure-resources-to-manage-in-privileged-identity-management"></a>Descubra os recursos do Azure para gerenciar no Gerenciamento de Identidade Privilegiada

Usando o Azure Active Directory (Azure AD) Privileged Identity Management (PIM), você pode melhorar a proteção dos seus recursos do Azure. Isso é útil para organizações que já usam o Gerenciamento de Identidade Privilegiada para proteger as funções do Azure AD e para os proprietários de grupo de gerenciamento e assinaturas que estão procurando garantir recursos de produção.

Quando você configura o Gerenciamento de Identidade Privilegiada para recursos do Azure, você precisa descobrir e selecionar os recursos para proteger com o Gerenciamento de Identidade Privilegiado. Não há limite para o número de recursos que você pode gerenciar com o Gerenciamento de Identidade Privilegiada. No entanto, é recomendável começar com seus recursos  mais importantes (produção).

## <a name="discover-resources"></a>Descobrir recursos

1. Faça login no [portal Azure](https://portal.azure.com/).

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **Recursos do Azure**.

    Se esta é a primeira vez que usa o Gerenciamento de Identidade Privilegiada para recursos do Azure, você verá uma página **de recursos do Discover.**

    ![Descubra o painel de recursos sem recursos listados para a primeira experiência](./media/pim-resource-roles-discover-resources/discover-resources-first-run.png)

    Se outro administrador em sua organização já estiver gerenciando os recursos do Azure no Gerenciamento de Identidade Privilegiada, você verá uma lista dos recursos que estão sendo gerenciados no momento.

    ![Descubra recursos de listagem de pai que estão sendo gerenciados](./media/pim-resource-roles-discover-resources/discover-resources.png)

1. Selecione **os recursos do Discover** para iniciar a experiência de descoberta.

    ![Recursos de listagem de pai e de si maquinamento do Discovery que podem ser gerenciados, como assinaturas e grupos de gerenciamento](./media/pim-resource-roles-discover-resources/discovery-pane.png)

1. Na página **''Detecção',** use **filtro de estado de recursos** e **selecione o tipo de recurso** para filtrar os grupos de gerenciamento ou assinaturas para os quais você tem permissão de gravação. Ele provavelmente é mais fácil para começar **todos** os inicialmente.

    Você só pode pesquisar e selecionar recursos de gerenciamento ou assinatura para gerenciar usando o Gerenciamento de Identidade Privilegiada. Quando você gerencia um grupo de gerenciamento ou uma assinatura no Gerenciamento de Identidade Privilegiada, você também pode gerenciar seus recursos filhos.

1. Selecione a caixa de seleção ao lado de quaisquer recursos não gerenciados que você deseja gerenciar.

1. Selecione **Gerenciar recurso** para começar a gerenciar os recursos selecionados.

    > [!NOTE]
    > Uma vez gerenciado um grupo de gerenciamento ou assinatura, ele não pode ser gerenciado. Isso impede que outro administrador de recursos removesse configurações de gerenciamento de identidade privilegiado.

    ![Painel de detecção com um recurso selecionado e a opção Gerenciar recurso destacada](./media/pim-resource-roles-discover-resources/discovery-manage-resource.png)

1. Se você vir uma mensagem para confirmar o onboarding do recurso selecionado para gerenciamento, selecione **Sim**.

    ![Mensagem confirmando os recursos selecionados para gestão](./media/pim-resource-roles-discover-resources/discovery-manage-resource-message.png)

## <a name="next-steps"></a>Próximas etapas

- [Configure as configurações de função de recurso do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-configure-role-settings.md)
- [Atribuir funções de recurso do Azure no Gerenciamento de Identidade Privilegiada](pim-resource-roles-assign-roles.md)
