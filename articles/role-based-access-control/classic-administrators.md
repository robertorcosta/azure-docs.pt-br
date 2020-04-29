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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "79243478"
---
# <a name="azure-classic-subscription-administrators"></a>Administradores da assinatura clássica do Azure

A Microsoft recomenda que você gerencie o acesso aos recursos do Azure usando o RBAC (controle de acesso baseado em função). No entanto, se você ainda estiver usando o modelo de implantação clássico, precisará usar uma função de administrador de assinatura clássica: administrador de serviços e coadministrador. Para obter mais informações, consulte [Azure Resource Manager versus implantação clássica](../azure-resource-manager/management/deployment-models.md).

Este artigo descreve como adicionar ou alterar as funções Administrador de Serviços e Coadministrador e como exibir o Administrador da Conta.

## <a name="add-a-co-administrator"></a>Adicionar um Coadministrador

> [!TIP]
> É necessário adicionar um Coadministrador somente se o usuário precisar gerenciar implantações clássicas do Azure usando o [Módulo do PowerShell para Gerenciamento de Serviços do Azure](https://docs.microsoft.com/powershell/module/servicemanagement/azure). Se o usuário usa apenas o portal do Azure para gerenciar os recursos clássicos, não é preciso adicionar o administrador clássico para ele.

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador ou coadministrador de serviço.

1. Abra [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione a assinatura.

    Coadministradores somente podem ser atribuídos no escopo da assinatura.

1. Clique em **controle de acesso (iam)**.

1. Clique na guia **Administradores clássicos**.

    ![Captura de tela que abre Administradores clássicos](./media/classic-administrators/classic-administrators.png)

1. Clique em **Adicionar** > **Adicionar coadministrador** para abrir o painel Adicionar co-administradores.

    Se a opção Adicionar coadministrador estiver desabilitada, você não terá permissões.

1. Selecione o usuário que você deseja adicionar e clique em **Adicionar**.

    ![Captura de tela que adiciona o coadministrador](./media/classic-administrators/add-coadmin.png)

## <a name="add-a-guest-user-as-a-co-administrator"></a>Adicionar um usuário convidado como um coadministrador

Para adicionar um usuário convidado como um coadministrador, siga as mesmas etapas da seção [Adicionar um coadministrador](#add-a-co-administrator) anterior. O usuário convidado deve atender aos seguintes critérios:

- O usuário convidado deve ter uma presença em seu diretório. Isso significa que o usuário foi convidado para seu diretório e aceitou o convite.

Para obter mais informações, sobre como adicionar um usuário convidado ao seu diretório, consulte [adicionar Azure Active Directory usuários de colaboração B2B no portal do Azure](../active-directory/b2b/add-users-administrator.md).

### <a name="differences-for-guest-users"></a>Diferenças para usuários convidados

Os usuários convidados que receberam a função de coadministrador podem ver algumas diferenças em comparação com os usuários Membros com a função de coadministrador. Considere o cenário a seguir.

- O usuário A com uma conta do Azure AD (conta corporativa ou de estudante) é um administrador de serviço para uma assinatura do Azure.
- O usuário B tem uma conta da Microsoft.
- O usuário A atribui a função Coadministrador ao usuário B.
- O usuário B pode fazer quase tudo, mas não pode registrar aplicativos ou pesquisar usuários no diretório do Azure AD.

Você esperava que o usuário B pudesse gerenciar tudo. O motivo para essa diferença é que a conta da Microsoft é adicionada à assinatura como um usuário convidado, em vez de um usuário membro. Os usuários convidados têm permissões padrão diferentes no Azure AD em comparação com os usuários membros. Por exemplo, usuários membros podem ler outros usuários no Azure AD, e os usuários convidados não podem. Os usuários membros podem registrar novas entidades de serviço no Azure AD, e os usuários convidados não podem.

Se houver a necessidade de um usuário convidado executar essas tarefas, uma solução possível é atribuir as funções de administrador específicas do Azure AD que o usuário convidado precisa. Por exemplo, no cenário anterior, você pode atribuir a função [Leitores de Diretório](../active-directory/users-groups-roles/directory-assign-admin-roles.md#directory-readers) para ler outros usuários e atribuir a função [Desenvolvedor de Aplicativos](../active-directory/users-groups-roles/directory-assign-admin-roles.md#application-developer) para criar entidades de serviço. Para saber mais sobre os usuários membros e convidados e suas permissões, consulte [Quais são as permissões de usuário padrão no Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md). Para obter mais informações sobre como conceder acesso para usuários convidados, consulte [gerenciar o acesso aos recursos do Azure para usuários convidados externos usando o RBAC](role-assignments-external-users.md).

Observe que as [funções internas para recursos do Azure](../role-based-access-control/built-in-roles.md) são diferentes das [funções de administrador do Azure AD](../active-directory/users-groups-roles/directory-assign-admin-roles.md). As funções internas não concedem acesso ao Azure AD. Para saber mais, consulte [Compreender as diferentes funções](../role-based-access-control/rbac-and-directory-admin-roles.md).

Para obter informações que comparam usuários Membros e usuários convidados, consulte [quais são as permissões de usuário padrão no Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="remove-a-co-administrator"></a>Remover um Coadministrador

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador ou coadministrador de serviço.

1. Abra [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione a assinatura.

1. Clique em **controle de acesso (iam)**.

1. Clique na guia **Administradores clássicos**.

1. Adicione uma marca de seleção ao lado do Coadministrador que você quer remover.

1. Clique em **Remover**.

1. Na caixa de mensagem que é exibida, clique em **Sim**.

    ![Captura de tela que remove o coadministrador](./media/classic-administrators/remove-coadmin.png)

## <a name="change-the-service-administrator"></a>Alterar o administrador do serviço

Somente o Administrador da Conta pode alterar o Administrador de Serviços de uma assinatura. Por padrão, ao inscrever-se em uma assinatura do Azure, o Administrador de Serviços é o mesmo que o Administrador da Conta. O usuário com a função Administrador da Conta não tem acesso ao portal do Azure. O usuário com a função Administrador de Serviços tem acesso total ao portal do Azure. Se o Administrador da Conta e o Administrador de Serviços forem o mesmo usuário e você alterar o Administrador de Serviços para um usuário diferente, o Administrador da Conta perderá o acesso ao portal do Azure. No entanto, o Administrador da Conta sempre poderá usar o Centro de Contas para alterar o Administrador de Serviços de volta para si mesmo.

Siga estas etapas para alterar o administrador de serviços no **centro de contas**.

### <a name="account-center"></a>Centro de Contas

1. Verifique se o cenário tem suporte verificando as [limitações para alterar o administrador de serviços](#limitations-for-changing-the-service-administrator).

1. Entre no [centro de contas](https://account.windowsazure.com/subscriptions) como o administrador da conta.

1. Clique em uma assinatura.

1. No lado direito, clique em **Editar detalhes da assinatura**.

    ![Captura de tela mostrando o botão Editar assinatura no Centro de Contas](./media/classic-administrators/editsub.png)

1. Na caixa **ADMINISTRADOR DE SERVIÇOS** , insira o endereço de email do novo administrador de serviços.

    ![Captura de tela mostrando a caixa para alterar o email do Administrador de Serviços](./media/classic-administrators/change-service-admin.png)

1. Clique na marca de seleção para salvar a alteração.

### <a name="limitations-for-changing-the-service-administrator"></a>Limitações para alterar o Administrador de Serviços

Só pode haver um administrador de serviços por assinatura do Azure. A alteração do administrador de serviços se comportará de maneira diferente, dependendo se o administrador da conta for um conta Microsoft ou se é uma conta do Azure AD (conta corporativa ou de estudante).

| Conta de administrador da conta | Pode alterar o administrador de serviços para um conta Microsoft diferente? | Pode alterar o administrador de serviço para uma conta do Azure AD no mesmo diretório? | Pode alterar o administrador de serviço para uma conta do Azure AD em um diretório diferente? |
| --- | --- | --- | --- |
| Conta da Microsoft | Sim | Não | Não |
| Conta do Azure AD | Sim | Sim | Não |

Se o administrador da conta for uma conta do Azure AD, você poderá alterar o administrador do serviço para uma conta do Azure AD no mesmo diretório, mas não em um diretório diferente. Por exemplo, abby@contoso.com o pode alterar o administrador de bob@contoso.comserviços para o, mas não pode alterar john@notcontoso.com o john@notcontoso.com administrador de serviços para, a menos que tenha uma presença no diretório contoso.com

Para obter mais informações sobre contas da Microsoft e contas do Azure AD, consulte [o que é Azure Active Directory?](../active-directory/fundamentals/active-directory-whatis.md).

## <a name="view-the-account-administrator"></a>Exibir o Administrador da Conta

O Administrador da Conta é o usuário que inscreveu-se inicialmente para a assinatura do Azure e é responsável como o proprietário de cobrança da assinatura. Para alterar o administrador da conta de uma assinatura, consulte [transferir a propriedade de uma assinatura do Azure para outra conta](../cost-management-billing/manage/billing-subscription-transfer.md).

Siga estas etapas para exibir o Administrador da Conta.

1. Entre no [portal do Azure](https://portal.azure.com).

1. Abra [Assinaturas](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) e selecione a assinatura.

1. Clique em **Propriedades**.

    O administrador da conta da assinatura é exibido na caixa **administrador da conta** .

    ![Captura de tela mostrando o Administrador da Conta](./media/classic-administrators/account-admin.png)

## <a name="next-steps"></a>Próximas etapas

* [Entender as diferentes funções no Azure](../role-based-access-control/rbac-and-directory-admin-roles.md)
* [Gerenciar o acesso aos recursos do Azure usando o RBAC e o portal do Azure](../role-based-access-control/role-assignments-portal.md)
* [Adicionar ou alterar administradores de assinatura do Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)
