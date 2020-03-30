---
title: Revisar o acesso aos recursos do Azure no PIM - Azure AD | Microsoft Docs
description: Saiba como revisar o acesso às funções de recursos do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: markwahl-msft
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
ms.openlocfilehash: 7ddb4d0ff1339f1427c5041528cdbe464a345b37
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "73847012"
---
# <a name="review-access-to-azure-resource-roles-in-privileged-identity-management"></a>Revise o acesso às funções de recursos do Azure no Gerenciamento de Identidade Privilegiada

As avaliações de acesso do Privileged Identity Management (PIM) podem ajudar a garantir o acesso a funções privilegiadas no Azure Active Directory (Azure AD). Este artigo é o passo para concluir uma revisão de suas atribuições de função privilegiada em uma revisão de acesso a Azure AD.

Se você for designado para uma função administrativa, você pode ser obrigado a concluir uma revisão de acesso pelo administrador para confirmar sua necessidade de uma função. A solicitação de confirmação pode vir de um e-mail que inclui um link, ou você pode confirmar no [portal Azure](https://portal.azure.com).

Se você for um administrador com privilégios de função interessado em revisões de acesso, obtenha mais detalhes em [Como iniciar uma revisão de acesso](pim-resource-roles-start-access-review.md).

## <a name="approve-or-deny-access"></a>Aprovar ou negar acesso

Você pode aprovar ou negar acesso com base em se você ainda usa esse papel ou não. Escolha **Aprovar** se você quiser manter a função ou **Negar** se não precisar mais do acesso. Seu status só muda depois que o revisor aplica os resultados.

Siga estas etapas para localizar e concluir a análise de acesso:

1. Faça login no [portal Azure](https://portal.azure.com/).
1. Selecione **o Diretório Ativo do Azure** e abra o Gerenciamento de Identidade **Privilegiada**.
1. Selecione **O acesso de revisão**.

   ![Captura de tela do aplicativo Privileged Identity Management, com a lâmina de acesso de revisão selecionada](media/pim-resource-roles-perform-access-review/rbac-access-review-complete.png)

1. Selecione a análise que deseja concluir.
1. Escolha **Aprovar** ou **Negar**. Na **caixa Fornecer uma caixa de razão,** digite uma justificativa de negócio para sua decisão, se necessário.

   ![Captura de tela Revisar detalhes](media/pim-resource-roles-perform-access-review/rbac-access-review-choice.png)

## <a name="next-steps"></a>Próximas etapas

- [Realize uma revisão de acesso das minhas funções do Azure AD no Gerenciamento de Identidade Privilegiada](pim-how-to-perform-security-review.md)
