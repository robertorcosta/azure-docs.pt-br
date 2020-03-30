---
title: Azure AD Connect em nuvem provisionamento de nova configuração de agente
description: Este artigo descreve como instalar provisionamento em nuvem.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c086227d23901cb7904d8cc0a768e9f4b5ad43db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77620982"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Crie uma nova configuração para o provisionamento em nuvem baseado no Azure AD Connect

Depois de instalar o agente, você precisa entrar no portal Dozure e configurar o provisionamento em nuvem do Azure Active Directory (Azure AD). Siga estas etapas para habilitar o agente.

## <a name="configure-provisioning"></a>Configurar provisionamento
Para configurar o provisionamento, siga estas etapas.

1.  No portal Azure, selecione **O Diretório Ativo do Azure**.
1.  Selecione **Azure AD Connect**.
1.  Selecione **Gerenciar provisionamento (Visualização)**.

    ![Gerenciar provisionamento (Visualização)](media/how-to-configure/manage1.png)

1.  Selecione **Nova configuração**.
1.  Na tela de configuração, o domínio no local é prepovoado.
1.  Digite um **e-mail de notificação**. Este e-mail será notificado quando o provisionamento não estiver saudável.
1.  Mova o seletor para **Habilitar**e selecione **Salvar**.

    ![Provisionamento Azure AD (Visualização)](media/tutorial-single-forest/configure2.png)

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Provisionamento de escopo para usuários e grupos específicos
Você pode escopo do agente para sincronizar usuários e grupos específicos usando grupos de diretórios ativos ou unidades organizacionais no local. Você não pode configurar grupos e unidades organizacionais dentro de uma configuração. 

1.  No portal Azure, selecione **O Diretório Ativo do Azure**.
1.  Selecione **Azure AD Connect**.
1.  Selecione **Gerenciar provisionamento (Visualização)**.
1.  Em **Configuração,** selecione sua configuração.

    ![Seção de configuração](media/how-to-configure/scope1.png)

1.  Em **Configurar**, selecione **Todos os usuários** para alterar o escopo da regra de configuração.

    ![Todas as opções de usuários](media/how-to-configure/scope2.png)

1. À direita, você pode alterar o escopo para incluir apenas grupos de segurança. Digite o nome distinto do grupo e selecione **Adicionar**.

    ![Opção de grupos de segurança selecionados](media/how-to-configure/scope3.png)

1.  Ou você pode alterar o escopo para incluir apenas unidades organizacionais específicas. Selecione **Feito** e **Salve**.  
2.  Depois de alterar o escopo, você deve reiniciar o [provisionamento](#restart-provisioning) para iniciar uma sincronização imediata das alterações.

    ![Opção de unidades organizacionais selecionadas](media/how-to-configure/scope4.png)


## <a name="restart-provisioning"></a>Reinicie o provisionamento 
Se você não quiser esperar pela próxima execução programada, acione o provisionamento executado usando o botão **Dereinicialização.** 
1.  No portal Azure, selecione **O Diretório Ativo do Azure**.
1.  Selecione **Azure AD Connect**.
1.  Selecione **Gerenciar provisionamento (Visualização)**.
1.  Em **Configuração,** selecione sua configuração.

    ![Seleção de configuração para reiniciar o provisionamento](media/how-to-configure/scope1.png)

1.  Na parte superior, selecione **Recomeçar provisionamento**.

## <a name="remove-a-configuration"></a>Remova uma configuração
Para excluir uma configuração, siga estas etapas.

1.  No portal Azure, selecione **O Diretório Ativo do Azure**.
1.  Selecione **Azure AD Connect**.
1.  Selecione **Gerenciar provisionamento (Visualização)**.
1.  Em **Configuração,** selecione sua configuração.

    ![Seleção de configuração para remover configuração](media/how-to-configure/scope1.png)

1.  Na parte superior da tela de configuração, **selecione Excluir**.

    ![Botão Excluir](media/how-to-configure/remove1.png)

>[!IMPORTANT]
>Não há confirmação antes de excluir uma configuração. Certifique-se de que esta é a ação que deseja tomar antes de selecionar **Excluir**.


## <a name="next-steps"></a>Próximas etapas 

- [O que é provisionamento?](what-is-provisioning.md)
- [O que é o provisionamento em nuvem do Azure AD Connect?](what-is-cloud-provisioning.md)
