---
title: Gerenciar o acesso de sua organização a aplicativos & grupos-Azure AD
description: Saiba como executar uma revisão de acesso para gerenciar o acesso de segurança para os aplicativos e grupos da sua organização no portal meus aplicativos.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: user-help
ms.workload: identity
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: curtand
ms.reviewer: kasimpso
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: c71195b247af6d5046d88d3e6918a660eddf09b3
ms.sourcegitcommit: db2d402883035150f4f89d94ef79219b1604c5ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2020
ms.locfileid: "77062364"
---
# <a name="perform-an-access-review-from-the-my-apps-portal"></a>Executar uma revisão de acesso no portal meus aplicativos

Você pode usar sua conta corporativa ou de estudante com o portal **meus aplicativos** baseado na Web para executar revisões de acesso para seus aplicativos e grupos. As revisões de acesso ajudam a gerenciar o acesso desatualizado ou a alterar os requisitos de acesso e garantir que eles sejam revisados e atualizados.

Se você não tiver acesso ao portal **Meus Aplicativos**, entre em contato com a assistência técnica para obter permissão.

[!INCLUDE [preview-notice](../../../includes/active-directory-end-user-my-apps-portal.md)]

>[!Important]
>Este conteúdo destina-se aos usuários dos **meus aplicativos** . Se você for administrador, poderá encontrar mais informações sobre como configurar e gerenciar seus aplicativos baseados em nuvem na [Documentação de Gerenciamento do Aplicativo](https://docs.microsoft.com/azure/active-directory/manage-apps).

## <a name="manage-access-reviews"></a>Gerenciar revisões de acesso

Se o administrador tiver concedido permissão para realizar suas próprias revisões de acesso, você poderá gerenciar o acesso de seus grupos ou aplicativos no bloco de **revisões de acesso** na página do portal **meus aplicativos** .

>[!Note]
>Se você não vir o bloco de **revisões de acesso** , isso significa que você não tem permissão para realizar revisões de acesso ou que você não tem nenhuma análise pendente aguardando sua aprovação. Se você considerar que deve ter acesso ao bloco, entre em contato com a assistência técnica para obter ajuda.

## <a name="to-perform-your-access-reviews"></a>Para executar suas revisões de acesso

1. Entre sua conta corporativa ou de estudante.

2. Abra o navegador da Web e vá para https://myapps.microsoft.comou use o link fornecido pela sua organização. Por exemplo, você pode ser direcionado para uma página personalizada para sua organização, como https://myapps.microsoft.com/contoso.com.

    A página **aplicativos** é exibida, mostrando todos os aplicativos baseados em nuvem pertencentes à sua organização e disponíveis para uso.

    ![Página de aplicativos no portal meus aplicativos](media/my-apps-portal/my-apps-portal-apps-page-access-review-tile.png)

3. Selecione o bloco **revisões de acesso** para ver uma lista de revisões de acesso aguardando sua aprovação.

    ![Página de revisões de acesso com revisões de acesso pendentes para a organização](media/my-apps-portal/my-apps-portal-access-reviews-page.png)

4. Selecione **Iniciar revisão** para iniciar sua revisão de acesso.

5. Examine seu acesso e determine se ele ainda é necessário.

    ![Página de revisão de acesso, mostrando os detalhes da revisão](media/my-apps-portal/my-apps-portal-perform-access-reviews-page.png)

    >[!Note]
    >Se você for um administrador e tiver permissão para revisar o acesso de sua organização a grupos e aplicativos, você verá uma página diferente. Para obter mais informações sobre como revisar grupos ou aplicativos para sua organização, consulte [examinar o acesso a grupos ou aplicativos nas revisões de acesso do Azure ad](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review).

6. Selecione **Sim** para manter seu acesso ou **não** para remover o acesso.

    Se você selecionar **Sim**, talvez seja necessário especificar uma justificativa na caixa **motivo** .

    ![Página de revisão de acesso, mostrando a caixa de motivo com texto de exemplo](media/my-apps-portal/my-apps-portal-perform-access-reviews-reason-box.png)

7. Selecione **Enviar**.

    Sua análise de acesso foi concluída e você retorna para o portal **meus aplicativos** .

    >[!Note]
    >Você pode alterar o acesso a qualquer momento até que o período de revisão de acesso termine. Se você remover o acesso a um aplicativo ou grupo, ele não será removido imediatamente. A remoção ocorre quando o período de revisão de acesso termina ou quando um administrador fecha a revisão.

## <a name="next-steps"></a>Próximas etapas

- [Acessar e usar aplicativos no portal meus aplicativos](my-apps-portal-end-user-access.md)
- [Alterar as informações do seu perfil](my-apps-portal-end-user-update-profile.md)
- [Exibir e atualizar suas informações relacionadas a grupos](my-apps-portal-end-user-groups.md)
