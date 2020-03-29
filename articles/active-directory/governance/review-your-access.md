---
title: Revise seu acesso a grupos & aplicativos em avaliações de acesso - Azure AD
description: Saiba como revisar seu próprio acesso a grupos ou aplicativos nas avaliações de acesso do Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 05/21/2019
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d75d7ffe28fa126ee5e359cba19e66e3c0f36e2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422398"
---
# <a name="review-access-for-yourself-to-groups-or-applications-in-azure-ad-access-reviews"></a>Revise o acesso para si mesmo a grupos ou aplicativos em avaliações de acesso ao Azure AD

O Azure Active Directory (Azure AD) simplifica a forma como as empresas gerenciam o acesso a grupos ou aplicativos no Azure AD e outros Serviços Online da Microsoft com um recurso chamado acessações de acesso Azure AD.

Este artigo descreve como rever seu próprio acesso a um grupo ou a um aplicativo.

## <a name="open-the-access-review"></a>Abra a revisão de acesso

O primeiro passo para realizar uma revisão de acesso é encontrar e abrir a revisão de acesso.

1. Procure um e-mail da Microsoft que peça para você revisar o acesso. Aqui está um e-mail de exemplo para revisar seu acesso a um grupo.

    ![Exemplo de e-mail da Microsoft para revisar seu acesso a um grupo](./media/review-your-access/access-review-email.png)

1. Clique no link **de acesso** à Revisão para abrir a revisão de acesso.

Se você não tiver o e-mail, você pode encontrar suas avaliações de acesso pendentes seguindo estas etapas.

1. Faça login no portal [https://myapps.microsoft.com](https://myapps.microsoft.com)MyApps em .

    ![Aplicativos de listagem de portal do MyApps para os seus aplicativos de lista](./media/review-your-access/myapps-access-panel.png)

1. No canto superior direito da página, clique no símbolo do usuário para exibir seu nome e a organização padrão. Se houver mais de uma organização listada, selecione a organização que solicitou uma análise de acesso.

1. No lado direito da página, clique no bloco **de avaliações** do Access para ver uma lista das avaliações de acesso pendentes.

    Se o bloco não estiver visível, não haverá revisões de acesso para executar nessa organização e nenhuma ação será necessária no momento.

    ![Lista de avaliações de acesso pendentes para seus aplicativos e grupos](./media/review-your-access/access-reviews-list.png)

1. Clique no link **Iniciar revisão** para a revisão de acesso que deseja realizar.

## <a name="perform-the-access-review"></a>Realize a revisão de acesso

Depois de abrir a revisão de acesso, você pode ver o seu acesso.

1. Revise seu acesso e decida se você ainda precisa de acesso.

    Se a solicitação for para revisar o acesso para outros, a página será diferente. Para obter mais informações, consulte [Revisar acesso a grupos ou aplicativos](perform-access-review.md).

    ![Revisão de acesso aberto perguntando se você ainda precisa de acesso a um grupo](./media/review-your-access/perform-access-review.png)

1. Clique **em Sim** para manter seu acesso ou clique em **Não** para remover seu acesso.

1. Se você clicar em **Sim,** talvez seja necessário especificar uma justificativa na caixa **Razão.**

    ![Revisão de acesso concluída perguntando se você ainda precisa de acesso a um grupo](./media/review-your-access/perform-access-review-submit.png)

1. Clique em **Enviar**.

    Sua seleção é submetida e você retorna ao portal MyApps.

    Se você quiser alterar sua resposta, reabra a página de comentários de acesso e atualize sua resposta. Você pode alterar sua resposta a qualquer momento até que a revisão de acesso tenha terminado.

    > [!NOTE]
    > Se você indicou que não precisa mais de acesso, você não será removido imediatamente. Você é removido quando a revisão termina ou quando um administrador interrompe a revisão.

## <a name="next-steps"></a>Próximas etapas

- [Complete uma revisão de acesso de grupos ou aplicativos](complete-access-review.md)
