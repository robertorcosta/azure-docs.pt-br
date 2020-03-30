---
title: Administradores da assinatura clássica do Azure | Microsoft Docs
description: Descreve como adicionar ou alterar as funções Administrador de Serviços e Coadministrador do Azure e como exibir o Administrador da Conta.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/22/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 2493e893f9afda0642bd838c94538dd0b984bce5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243478"
---
# <a name="azure-classic-subscription-administrators"></a>Administradores da assinatura clássica do Azure

A Microsoft recomenda que você gerencie o acesso aos recursos do Azure usando o RBAC (controle de acesso baseado em função). No entanto, se você ainda estiver usando o modelo de implantação clássico, você precisará usar uma função clássica de administrador de assinatura: Administrador de serviços e co-administrador. Para obter mais informações, consulte [Azure Resource Manager versus implantação clássica](../azure-resource-manager/management/deployment-models.md).

Este artigo descreve como adicionar ou alterar as funções Administrador de Serviços e Coadministrador e como exibir o Administrador da Conta.

## <a name="add-a-co-administrator"></a>Adicionar um Coadministrador

> [!TIP]
> É necessário adicionar um Coadministrador somente se o usuário precisar gerenciar implantações clássicas do Azure usando o [Módulo do PowerShell para Gerenciamento de Serviços do Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Se o usuário usa apenas o portal do Azure para gerenciar os recursos clássicos, não é preciso adicionar o administrador clássico para ele.

1. Faça login no [portal Azure](https://portal.azure.com) como administrador de serviços ou co-administrador.

1. Abra [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione a assinatura.

    Coadministradores somente podem ser atribuídos no escopo da assinatura.

1. Clique **em Acessar controle (IAM)**.

1. Clique na guia **Administradores clássicos**.

    ![Captura de tela que abre Administradores clássicos](./media/classic-administrators/classic-administrators.png)

1. Clique **em Adicionar** > **co-administrador** para abrir o painel Adicionar co-administradores.

    Se a opção Adicionar coadministrador estiver desabilitada, você não terá permissões.

1. Selecione o usuário que deseja adicionar e clique **em Adicionar**.

    ![Captura de tela que adiciona o coadministrador](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Adicione um usuário convidado como co-administrador

Para adicionar um usuário convidado como co-administrador, siga os mesmos passos da seção [Adicionar um Co-Administrador](#add-a-co-administrator) anterior. O usuário convidado deve atender aos seguintes critérios:

- O usuário convidado deve ter uma presença em seu diretório. Isso significa que o usuário foi convidado para o seu diretório e aceitou o convite.

Para obter mais informações sobre como adicionar um usuário convidado ao seu diretório, consulte Adicionar usuários de [colaboração B2B do Active Directory no portal Azure](../active-directory/b2b/add-users-administrator.md).

### <a name="differences-for-guest-users"></a>Diferenças para usuários convidados

Os usuários convidados que foram designados para a função de Co-Administrador podem ver algumas diferenças em comparação com os usuários membros com a função de Co-Administrador. Considere o cenário a seguir.

- O Usuário A com uma conta AD do Azure (conta de trabalho ou escola) é um administrador de serviços para uma assinatura do Azure.
- O usuário B tem uma conta da Microsoft.
- O usuário A atribui a função Coadministrador ao usuário B.
- O usuário B pode fazer quase tudo, mas não pode registrar aplicativos ou pesquisar usuários no diretório do Azure AD.

Você esperava que o usuário B pudesse gerenciar tudo. O motivo para essa diferença é que a conta da Microsoft é adicionada à assinatura como um usuário convidado, em vez de um usuário membro. Os usuários convidados têm permissões padrão diferentes no Azure AD em comparação com os usuários membros. Por exemplo, usuários membros podem ler outros usuários no Azure AD, e os usuários convidados não podem. Os usuários membros podem registrar novas entidades de serviço no Azure AD, e os usuários convidados não podem.

Se houver a necessidade de um usuário convidado executar essas tarefas, uma solução possível é atribuir as funções de administrador específicas do Azure AD que o usuário convidado precisa. Por exemplo, no cenário anterior, você pode atribuir a função [Leitores de Diretório](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) para ler outros usuários e atribuir a função [Desenvolvedor de Aplicativos](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) para criar entidades de serviço. Para saber mais sobre os usuários membros e convidados e suas permissões, consulte [Quais são as permissões de usuário padrão no Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md). Para obter mais informações sobre a concessão de acesso para usuários convidados, consulte [Gerenciar o acesso aos recursos do Azure para usuários convidados externos usando o RBAC](role-assignments-external-users.md).

Observe que as [funções internas para recursos do Azure](../role-based-access-control/built-in-roles.md) são diferentes das [funções de administrador do Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md). As funções internas não concedem acesso ao Azure AD. Para saber mais, consulte [Compreender as diferentes funções](../role-based-access-control/rbac-and-directory-admin-roles.md).

Para obter informações que comparem usuários membros e usuários [convidados, consulte quais são as permissões de usuário padrão no Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md)

## <a name="remove-a-co-administrator"></a>Remover um Coadministrador

1. Faça login no [portal Azure](https://portal.azure.com) como administrador de serviços ou co-administrador.

1. Abra [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione a assinatura.

1. Clique **em Acessar controle (IAM)**.

1. Clique na guia **Administradores clássicos**.

1. Adicione uma marca de seleção ao lado do Coadministrador que você quer remover.

1. Clique em **Remover**.

1. Na caixa de mensagem que é exibida, clique em **Sim**.

    ![Captura de tela que remove o coadministrador](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Alterar o administrador do serviço

Somente o Administrador da Conta pode alterar o Administrador de Serviços de uma assinatura. Por padrão, ao inscrever-se em uma assinatura do Azure, o Administrador de Serviços é o mesmo que o Administrador da Conta. O usuário com a função Administrador da Conta não tem acesso ao portal do Azure. O usuário com a função Administrador de Serviços tem acesso total ao portal do Azure. Se o Administrador da Conta e o Administrador de Serviços forem o mesmo usuário e você alterar o Administrador de Serviços para um usuário diferente, o Administrador da Conta perderá o acesso ao portal do Azure. No entanto, o Administrador da Conta sempre poderá usar o Centro de Contas para alterar o Administrador de Serviços de volta para si mesmo.

Siga estas etapas para alterar o Administrador de Serviços no **Centro de Contas**.

### <a name="account-center"></a>Centro de Contas

1. Certifique-se de que seu cenário é suportado verificando as [limitações para alterar o Administrador do Serviço](#limitations-for-changing-the-service-administrator).

1. Faça login no [Centro de Contas](https://account.windowsazure.com/subscriptions) como administrador de contas.

1. Clique em uma assinatura.

1. No lado direito, clique em **Editar detalhes da assinatura**.

    ![Captura de tela mostrando o botão Editar assinatura no Centro de Contas](./media/classic-administrators/editsub.png)

1. Na caixa **ADMINISTRADOR DE SERVIÇOS** , insira o endereço de email do novo administrador de serviços.

    ![Captura de tela mostrando a caixa para alterar o email do Administrador de Serviços](./media/classic-administrators/change-service-admin.png)

1. Clique na marca de seleção para salvar a alteração.

### <a name="limitations-for-changing-the-service-administrator"></a>Limitações para alterar o Administrador de Serviços

Só pode haver um administrador de serviços por assinatura do Azure. Alterar o administrador de serviços se comportará de forma diferente dependendo se o administrador da conta é uma conta da Microsoft ou se é uma conta AD do Azure (conta de trabalho ou escola).

| Conta do administrador da conta | Pode alterar o administrador de serviços para uma conta Microsoft diferente? | O Administrador de Serviços pode alterar a conta Azure AD no mesmo diretório? | O Administrador de Serviços pode alterar a conta Ad do Azure em um diretório diferente? |
| --- | --- | --- | --- |
| Conta da Microsoft | Sim | Não | Não |
| Conta do AD do Azure | Sim | Sim | Não |

Se o administrador de conta for uma conta Ad do Azure, você poderá alterar o Administrador de Serviços para uma conta AD do Azure no mesmo diretório, mas não em um diretório diferente. Por abby@contoso.com exemplo, pode alterar bob@contoso.como administrador de serviços john@notcontoso.com john@notcontoso.com para , mas não pode alterar o administrador de serviço para, a menos que tenha uma presença no diretório contoso.com.

Para obter mais informações sobre contas Microsoft e contas AD do Azure, consulte [O que é o Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md)

## <a name="view-the-account-administrator"></a>Exibir o Administrador da Conta

O Administrador da Conta é o usuário que inscreveu-se inicialmente para a assinatura do Azure e é responsável como o proprietário de cobrança da assinatura. Para alterar o administrador de contas de uma assinatura, consulte [Transferir a propriedade de uma assinatura do Azure para outra conta](../cost-management-billing/manage/billing-subscription-transfer.md).

Siga estas etapas para exibir o Administrador da Conta.

1. Faça login no [portal Azure](https://portal.azure.com).

1. Abra [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione a assinatura.

1. Clique em **Propriedades**.

    O administrador da conta da assinatura é exibido na caixa **Administrador da conta.**

    ![Captura de tela mostrando o Administrador da Conta](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Próximas etapas

* [Entender as diferentes funções no Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Gerenciar o acesso aos recursos do Azure usando o RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md)
* [Adicionar ou alterar administradores de assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)
