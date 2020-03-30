---
title: Crie uma revisão de acesso das funções do Azure AD no PIM - Azure AD | Microsoft Docs
description: Saiba como criar uma revisão de acesso das funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2f7e9ef503a9a3469ecbc835be8d9229fbd0167f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847104"
---
# <a name="create-an-access-review-of-azure-ad-roles-in-privileged-identity-management"></a>Crie uma revisão de acesso das funções do Azure AD no Gerenciamento de Identidade Privilegiada

Para reduzir o risco associado a atribuições de funções obsoletas, você deve revisar regularmente o acesso. Você pode usar o Azure AD Privileged Identity Management (PIM) para criar avaliações de acesso para funções ad privilegiadas do Azure. Você também pode configurar revisões de acesso recorrentes que ocorrem automaticamente.

Este artigo descreve como criar uma ou mais avaliações de acesso para funções ad privilegiadas do Azure.

## <a name="prerequisites"></a>Pré-requisitos

[Administrador de funções privilegiado](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator)

## <a name="open-access-reviews"></a>Avaliações de acesso aberto

1. Faça login no [portal Azure](https://portal.azure.com/) com um usuário que é membro da função de administrador de funções privilegiadas.

1. Abra **o Azure AD Privileged Identity Management**.

1. No menu à esquerda, selecione **as funções Azure AD** e selecione **As avaliações de acesso**.

1. Em Gerenciar, selecione **'Acessar'**

    ![Funções azure AD - Lista de avaliações de acesso mostrando o status de todas as avaliações](./media/pim-how-to-start-security-review/access-reviews.png)

[!INCLUDE [Privileged Identity Management access reviews](../../../includes/active-directory-privileged-identity-management-access-reviews.md)]

## <a name="start-the-access-review"></a>Inicie a revisão de acesso

Depois de especificar as configurações para uma revisão de acesso, selecione **Iniciar**. A revisão de acesso aparecerá em sua lista com um indicador de seu status.

![Lista de avaliações de acesso mostrando o status das avaliações iniciadas](./media/pim-how-to-start-security-review/access-reviews-list.png)

Por padrão, o Azure AD envia um email para os revisores logo após o início da análise. Se você optar pelo não envio do email pelo Azure AD, certifique-se de informar aos revisores que eles devem concluir uma análise de acesso pendente. Você pode mostrar-lhes as instruções de como revisar o [acesso às funções ad do Azure](pim-how-to-perform-security-review.md).

## <a name="manage-the-access-review"></a>Gerenciar a análise de acesso

Você pode acompanhar o progresso à medida que os revisores completam suas avaliações na página **Visão Geral** da revisão de acesso. Nenhum direito de acesso é alterado no diretório até que a [revisão seja concluída](pim-how-to-complete-review.md).

![Página de visão geral do access reviews mostrando os detalhes da revisão](./media/pim-how-to-start-security-review/access-review-overview.png)

Se esta for uma revisão única, depois que o período de revisão de acesso terminar ou o administrador parar a revisão de acesso, siga as etapas em [Complete uma revisão de acesso das funções Ad do Azure](pim-how-to-complete-review.md) para ver e aplicar os resultados.  

Para gerenciar uma série de avaliações de acesso, navegue até a revisão de acesso e você encontrará as próximas ocorrências em Revisões programadas e edite a data final ou adicione/remova os revisores de acordo.

Com base em suas seleções em **Configurações de conclusão,** a aplicação automática será executada após a data de término da revisão ou quando você parar manualmente a revisão. O status da revisão mudará de **Concluído** para estados intermediários, como **a aplicação** e, finalmente, para o estado **aplicado.** Você deve esperar ver usuários negados, se houver, sendo removidos das funções em poucos minutos.

## <a name="next-steps"></a>Próximas etapas

- [Revisar o acesso às funções do Azure AD](pim-how-to-perform-security-review.md)
- [Complete uma revisão de acesso das funções do Azure AD](pim-how-to-complete-review.md)
- [Crie uma revisão de acesso das funções de recursos do Azure](pim-resource-roles-start-access-review.md)
