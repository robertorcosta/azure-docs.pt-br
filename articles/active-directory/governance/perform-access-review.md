---
title: Revisar o acesso a grupos & aplicativos em avaliações de acesso - Azure AD
description: Saiba como revisar o acesso de membros do grupo ou o acesso ao aplicativo nas avaliações de acesso do Azure Active Directory.
services: active-directory
author: barclayn
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee4125e82dd5176f01de294011e22a1d66005094
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128463"
---
# <a name="review-access-to-groups-and-applications-in-azure-ad-access-reviews"></a>Revise o acesso a grupos e aplicativos em avaliações de acesso ao Azure AD

O Azure Active Directory (Azure AD) simplifica a forma como as empresas gerenciam o acesso a grupos e aplicativos no Azure AD e outros Serviços Online da Microsoft com um recurso chamado acessações de acesso Azure AD.

Este artigo descreve como um revisor designado realiza uma revisão de acesso para membros de um grupo ou usuários com acesso a um aplicativo.

## <a name="open-the-access-review"></a>Abra a revisão de acesso

O primeiro passo para realizar uma revisão de acesso é encontrar e abrir a revisão de acesso.

1. Procure um e-mail da Microsoft que peça para você revisar o acesso. Aqui está um exemplo de e-mail para revisar o acesso a um grupo.

    ![Exemplo de e-mail da Microsoft para revisar o acesso a um grupo](./media/perform-access-review/access-review-email.png)

1. Clique no link **Iniciar para** abrir a revisão de acesso.

Se você não tiver o e-mail, você pode encontrar suas avaliações de acesso pendentes seguindo estas etapas.

1. Faça login no portal [https://myapps.microsoft.com](https://myapps.microsoft.com)MyApps em .

    ![Aplicativos de listagem de portal do MyApps para os seus aplicativos de lista](./media/perform-access-review/myapps-access-panel.png)

1. No canto superior direito da página, clique no símbolo do usuário para exibir seu nome e a organização padrão. Se houver mais de uma organização listada, selecione a organização que solicitou uma análise de acesso.

1. Clique no **bloco de avaliações** do Access para ver uma lista das avaliações de acesso pendentes.

    Se o bloco não estiver visível, não haverá revisões de acesso para executar nessa organização e nenhuma ação será necessária no momento.

    ![Lista de avaliações de acesso pendentes para aplicativos e grupos](./media/perform-access-review/access-reviews-list.png)

1. Clique no link **Iniciar revisão** para a revisão de acesso que deseja realizar.

## <a name="perform-the-access-review"></a>Realize a revisão de acesso

Depois de abrir a revisão de acesso, você verá os nomes dos usuários que precisam ser revisados.

Se a solicitação for para rever seu próprio acesso, a página será diferente. Para obter mais informações, consulte [Revisar acesso para si mesmo a grupos ou aplicativos](review-your-access.md).

![Revisão de acesso aberto listando os usuários que precisam ser revisados](./media/perform-access-review/perform-access-review.png)

Há duas maneiras que você pode aprovar ou negar o acesso:

- Você pode aprovar ou negar acesso a um ou mais usuários, ou
- Você pode aceitar as recomendações do sistema, que é a maneira mais fácil e rápida.

### <a name="approve-or-deny-access-for-one-or-more-users"></a>Aprovar ou negar acesso a um ou mais usuários

1. Revise a lista de usuários para decidir se aprovam ou negam seu acesso contínuo.

1. Para aprovar ou negar o acesso a um único usuário, clique na linha para abrir uma janela para especificar a ação a ser tomada. Para aprovar ou negar acesso a vários usuários, adicione marcas de verificação ao lado dos usuários e clique no botão **De ré Do usuário Review X** para abrir uma janela para especificar a ação a ser tomada.

1. Clique **em Aprovar** ou **Negar**. Se você não tiver certeza, você pode clicar **em Não Saber**. Isso resultará na manutenção do seu acesso pelo usuário, mas a seleção será refletida nos registros de auditoria.

    ![Janela de ação que inclui aprovar, negar e não saber opções](./media/perform-access-review/approve-deny.png)

1. Se necessário, digite uma razão na caixa **Razão.**

    O administrador da revisão de acesso pode exigir que você forneça uma razão para aprovar o acesso contínuo ou a adesão ao grupo.

1. Depois de especificar a ação a ser tomada, clique **em Salvar**.

    Se você quiser alterar sua resposta, selecione a linha e atualize a resposta. Por exemplo, você pode aprovar um usuário previamente negado ou negar um usuário previamente aprovado. Você pode alterar sua resposta a qualquer momento até que a revisão de acesso tenha terminado.

    Se houver vários revisores, a última resposta enviada será gravada. Considere um exemplo onde um administrador designa dois revisores – Alice e Bob. Alice abre a revisão de acesso primeiro e aprova o acesso. Antes do término da revisão, Bob abre a revisão de acesso e nega o acesso. A última resposta de negação é o que é gravado.

    > [!NOTE]
    > Se um usuário é negado acesso, ele não é removido imediatamente. Eles são removidos quando a revisão termina ou quando um administrador interrompe a revisão.

### <a name="approve-or-deny-access-based-on-recommendations"></a>Aprovar ou negar acesso com base em recomendações

Para tornar as avaliações de acesso mais fáceis e rápidas para você, também fornecemos recomendações que você pode aceitar com um único clique. As recomendações são geradas com base na atividade de login do usuário.

1. Na barra azul na parte inferior da página, clique em **Aceitar recomendações**.

    ![Listagem de revisão de acesso aberto mostrando o botão Aceitar recomendações](./media/perform-access-review/accept-recommendations.png)

    Você vê um resumo das ações recomendadas.

    ![Janela que exibe um resumo das ações recomendadas](./media/perform-access-review/accept-recommendations-summary.png)

1. Clique **em Ok** para aceitar as recomendações.

## <a name="next-steps"></a>Próximas etapas

- [Complete uma revisão de acesso de grupos ou aplicativos](complete-access-review.md)
