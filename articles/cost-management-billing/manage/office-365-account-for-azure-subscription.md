---
title: Inscrever-se no Azure com uma conta do Office 365
description: Saiba como criar uma assinatura do Azure usando uma conta do Office 365
author: JiangChen79
ms.reviewer: adwise
tags: billing,top-support-issue
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: banders
ms.openlocfilehash: b42a5b83f5442755614a3cb2ae81cffda09b4b3b
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/11/2020
ms.locfileid: "84696755"
---
# <a name="sign-up-for-an-azure-subscription-with-your-office-365-account"></a>Inscreva-se para obter uma assinatura do Azure com sua conta do Office 365
Se você tiver uma assinatura do Office 365, você pode usar sua conta do Office 365 para criar uma assinatura do Azure. Entre no [Portal do Azure](https://portal.azure.com/) usando seu nome de usuário e a senha do Office 365. No entanto, se você desejar configurar máquinas virtuais ou usar outros serviços do Azure, deverá inscrever-se para uma assinatura do Azure. Você pode compartilhar sua assinatura do Azure com outras pessoas e [usar o controle de acesso baseado em função para gerenciar o acesso à sua assinatura e recursos do Azure](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal)

Se você já tiver uma conta do Office 365 e uma assinatura do Azure, consulte [Associar um locatário do Office 365 a uma assinatura do Azure](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="get-an-azure-subscription-using-your-office-365-account"></a>Obter uma assinatura do Azure usando sua conta do Office 365

Economize tempo e evite a proliferação de conta inscrevendo-se no Azure usando seu nome de usuário e senha do Office 365.

1. Inscreva-se em [Azure.com](https://account.azure.com/signup?offer=MS-AZR-0044p&appId=docs).
2. Conecte-se usando seu nome de usuário e a senha do Office 365. A conta usada não precisa ter permissões de administrador. Se você tiver mais de uma conta do Office 365, lembre-se de usar as credenciais da conta do Office 365 que você deseja associar à sua assinatura do Azure.

   ![Captura de tela que mostra a página de conexão.](./media/office-365-account-for-azure-subscription/billing-sign-in-with-office-365-account.png)

3. Insira as informações necessárias e conclua o processo de inscrição. Algumas informações podem não ser necessárias se você já tiver uma conta do Office 365.

    ![Captura de tela que mostra o formulário de inscrição.](./media/office-365-account-for-azure-subscription/billing-azure-sign-up-fill-information.png)

- Se precisar adicionar outras pessoas de sua organização à assinatura do Azure, consulte [Introdução ao gerenciamento de acesso no Portal do Azure](../../role-based-access-control/overview.md).

## <a name=""></a><a id="more-about-subs">Mais sobre as assinaturas do Azure e do Office 365</a>
O Office 365 e o Azure usam o serviço do Azure AD para gerenciar usuários e assinaturas. O diretório do Azure é como um contêiner no qual você pode agrupar usuários e assinaturas. Para usar as mesmas contas de usuário para suas assinaturas do Azure e do Office 365, você precisa garantir que as assinaturas do Azure sejam criadas no mesmo diretório que as assinaturas do Office 365. Tenha em mente os seguintes pontos:

* Uma assinatura é criada em um diretório
* Os usuários pertencem aos diretórios
* Uma assinatura chega ao diretório do usuário que cria a assinatura. Portanto, sua assinatura do Office 365 está vinculada à mesma conta de sua assinatura do Azure.
* As assinaturas do Azure pertencem a usuários individuais no diretório
* As assinaturas do Office 365 pertencem o próprio diretório. Os usuários com as permissões corretas no diretório podem gerenciar essas assinaturas.

![Captura de tela que mostra a relação do diretório, dos usuários e das assinaturas.](./media/office-365-account-for-azure-subscription/19-background-information.png)

Para saber mais sobre, confira [Como as assinaturas do Azure são associadas ao Azure Active Directory](../../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md).

## <a name="need-help-contact-us"></a>Precisa de ajuda? Entre em contato conosco.

Caso tenha dúvidas ou precise de ajuda, [crie uma solicitação de suporte](https://go.microsoft.com/fwlink/?linkid=2083458).
