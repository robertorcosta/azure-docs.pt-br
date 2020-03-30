---
title: Crie uma revisão de acesso das funções de recursos do Azure no PIM - Azure AD | Microsoft Docs
description: Saiba como criar uma revisão de acesso das funções de recursos do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ae70b8386b1dc3ebd570d2651cded3eda75dfc53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847082"
---
# <a name="create-an-access-review-of-azure-resource-roles-in-privileged-identity-management"></a>Crie uma revisão de acesso das funções de recurso do Azure no Gerenciamento de Identidade Privilegiada

O acesso a funções privilegiadas de recursos do Azure para funcionários muda ao longo do tempo. Para reduzir o risco associado a atribuições de funções obsoletas, você deve revisar regularmente o acesso. Você pode usar o Azure Active Directory (Azure AD) Privileged Identity Management (PIM) para criar avaliações de acesso para funções de recursos privilegiadas do Azure. Você também pode configurar revisões de acesso recorrentes que ocorrem automaticamente.

Este artigo descreve como criar uma ou mais avaliações de acesso para funções de recursos privilegiadas do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[Administrador de funções privilegiado](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Avaliações de acesso aberto

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função Administrador de Funções com Privilégios.

1. Abra **o Azure AD Privileged Identity Management**.

1. No menu à esquerda, selecione **recursos do Azure**.

1. Selecione o recurso que deseja gerenciar, como uma assinatura ou um grupo de gerenciamento.

1. Em Gerenciar, selecione **'Acessar'**

    ![Recursos do Azure - Lista de avaliações de acesso mostrando o status de todas as avaliações](./media/pim-resource-roles-start-access-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Inicie a revisão de acesso

Depois de especificar as configurações para uma revisão de acesso, clique em **Iniciar**. A revisão de acesso aparecerá em sua lista com um indicador de seu status.

![Lista de avaliações de acesso mostrando o status da revisão iniciada](./media/pim-resource-roles-start-access-review/access-reviews-list.png)

Por padrão, o Azure AD envia um email para os revisores logo após o início da análise. Se você optar pelo não envio do email pelo Azure AD, certifique-se de informar aos revisores que eles devem concluir uma análise de acesso pendente. Você pode mostrar-lhes as instruções de como revisar o [acesso às funções de recursos do Azure](pim-resource-roles-perform-access-review.md).

## <a name="manage-the-access-review"></a>Gerenciar a análise de acesso

Você pode acompanhar o progresso à medida que os revisores completam suas avaliações na página **Visão Geral** da revisão de acesso. Nenhum direito de acesso é alterado no diretório até que a [revisão seja concluída](pim-resource-roles-complete-access-review.md).

![Página de visão geral do access reviews mostrando os detalhes da revisão](./media/pim-resource-roles-start-access-review/access-review-overview.png)

Se esta for uma revisão única, depois que o período de revisão de acesso terminar ou o administrador parar a revisão de acesso, siga as etapas em [Complete uma revisão de acesso das funções de recurso do Azure](pim-resource-roles-complete-access-review.md) para ver e aplicar os resultados.  

Para gerenciar uma série de avaliações de acesso, navegue até a revisão de acesso e você encontrará as próximas ocorrências em Revisões programadas e edite a data final ou adicione/remova os revisores de acordo.

Com base em suas seleções em **Configurações de conclusão,** a aplicação automática será executada após a data de término da revisão ou quando você parar manualmente a revisão. O status da revisão mudará de **Concluído** para estados intermediários, como **a aplicação** e, finalmente, para o estado **aplicado.** Você deve esperar ver usuários negados, se houver, sendo removidos das funções em poucos minutos.

## <a name="next-steps"></a>Próximas etapas

- [Revisar o acesso às funções de recursos do Azure](pim-resource-roles-perform-access-review.md)
- [Complete uma revisão de acesso das funções de recursos do Azure](pim-resource-roles-complete-access-review.md)
- [Crie uma revisão de acesso das funções do Azure AD](pim-how-to-start-security-review.md)
