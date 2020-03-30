---
title: Atribuir ou remover licenças - Azure Active Directory | Microsoft Docs
description: Instruções sobre como atribuir ou remover licenças do Azure Active Directory de seus usuários ou grupos.
services: active-directory
author: msaburnley
manager: daveba
ms.assetid: f8b932bc-8b4f-42b5-a2d3-f2c076234a78
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20ec1d1909a53a9de29e12be33957acfd1643698
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128834"
---
# <a name="assign-or-remove-licenses-in-the-azure-active-directory-portal"></a>Atribuir ou remover licenças no portal do Diretório Ativo do Azure

Muitos serviços do Azure Active Directory (Azure AD) exigem que você licencie cada um de seus usuários ou grupos (e membros associados) para esse serviço. Somente usuários com licenças ativas poderão acessar e usar os serviços AD licenciados do Azure para os quais isso é verdade. As licenças são aplicadas por inquilino e não são transferidas para outros inquilinos. 

## <a name="available-license-plans"></a>Planos de licença disponíveis

Existem vários planos de licença disponíveis para o serviço Azure AD, incluindo:

- AD do Azure Gratuito

- Azure AD Premium P1

- Azure AD Premium P2

Para obter informações específicas sobre cada plano de licença e os detalhes de licenciamento associados, consulte [de que licença eu preciso?](https://azure.microsoft.com/pricing/details/active-directory/).

Nem todos os serviços da Microsoft estão disponíveis em todos os locais. Antes que uma licença possa ser atribuída a um grupo, você deve especificar o **Local de uso** para todos os membros. Você pode definir esse valor **Azure Active Directory &gt; os usuários &gt; perfil &gt; configurações** área no Azure AD. Qualquer usuário cujo local de uso não seja especificado herda a localização da organização Azure AD.

## <a name="view-license-plans-and-plan-details"></a>Ver planos de licença e detalhes do plano

Você pode visualizar seus planos de serviço disponíveis, incluindo as licenças individuais, verificar datas de validade pendentes e visualizar o número de atribuições disponíveis.

### <a name="to-find-your-service-plan-and-plan-details"></a>Para encontrar seu plano de serviço e detalhes do plano

1. Faça login no [portal Azure](https://portal.azure.com/) usando uma conta de administrador de licenças em sua organização Azure AD.

1. Selecione **Active Directory do Azure** e, em seguida, selecione **Licenças**.

    ![Página de licenças, com número de serviços adquiridos e licenças atribuídas](media/license-users-groups/license-details-blade.png)

1. Selecione o link **Comprado** para visualizar a página **Produtos** e ver os **números atribuídos**, **disponíveis**e **expirando em breve** para seus planos de licença.

    ![página de serviços, com planos de licença de serviço e informações de licença associadas](media/license-users-groups/license-products-blade-with-products.png)

1. Selecione um nome de plano para ver seus usuários e grupos licenciados.

## <a name="assign-licenses-to-users-or-groups"></a>Atribuir licenças a usuários ou grupos

Certifique-se de que qualquer pessoa que precise usar um serviço licenciado do Azure AD tenha a licença apropriada. Você pode adicionar os direitos de licenciamento aos usuários ou a um grupo inteiro.

### <a name="to-assign-a-license-to-a-user"></a>Para atribuir uma licença a um usuário

1. Na página **Produtos,** selecione o nome do plano de licença que deseja atribuir ao usuário.

    ![página de serviços, com plano de licença de serviço destacado](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Na página de visão geral do plano de licença, selecione **Atribuir**.

    ![página de serviços, com opção Deatribuição destacada](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Na página **Atribuir**, selecione **Usuários e grupos** e pesquise e selecione o usuário que você está atribuindo a licença.

    ![Atribuir página de licença, com pesquisa realçada e opções de seleção](media/license-users-groups/assign-license-blade-with-highlight.png)

1. Selecione **Opções de atribuição**, verifique se você tem as opções de licença apropriadas ativadas e, em seguida, selecione **OK**.

    ![Página de opção de licença, com todas as opções disponíveis no plano de licença](media/license-users-groups/license-option-blade-assignments.png)

    A página **Atribuir licença** é atualizada para mostrar que um usuário está selecionado e que as atribuições estão configuradas.

    > [!NOTE]
    > Nem todos os serviços da Microsoft estão disponíveis em todos os locais. Antes que uma licença possa ser atribuída a um usuário, você deve especificar o **Local de uso**. Você pode definir esse valor **Azure Active Directory &gt; os usuários &gt; perfil &gt; configurações** área no Azure AD. Qualquer usuário cujo local de uso não seja especificado herda a localização da organização Azure AD.

1. Selecione **Atribuir**.

    O usuário é adicionado à lista de usuários licenciados e tem acesso aos serviços incluídos do Azure AD.
    > [!NOTE]
    > As licenças também podem ser atribuídas diretamente a um usuário na página **Licenças** do usuário. Se um usuário tiver uma licença atribuída através de uma associação de grupo e você quiser atribuir a mesma licença diretamente ao usuário, ela só poderá ser feita a partir da página **Produtos** mencionado apenas na etapa 1.

### <a name="to-assign-a-license-to-a-group"></a>Para atribuir uma licença a um grupo

1. Na página **Produtos,** selecione o nome do plano de licença que deseja atribuir ao usuário.

    ![Lâmina de produtos, com plano de licença de produto destacado](media/license-users-groups/license-products-blade-with-product-highlight.png)

1. Na página **Plano 2 do Active Directory do Azure 2**, selecione **Atribuir**.

    ![Página de produtos, com a opção de atribuir realçada](media/license-users-groups/license-products-blade-with-assign-option-highlight.png)

1. Na página **Atribuir**, selecione **Usuários e grupos** e pesquise e selecione o grupo que você está atribuindo a licença.

    ![Atribuir página de licença, com pesquisa realçada e opções de seleção](media/license-users-groups/assign-group-license-blade-with-highlight.png)

1. Selecione **Opções de atribuição**, verifique se você tem as opções de licença apropriadas ativadas e, em seguida, selecione **OK**.

    ![Página de opção de licença, com todas as opções disponíveis no plano de licença](media/license-users-groups/license-option-blade-group-assignments.png)

    A página **Atribuir licença** é atualizada para mostrar que um usuário está selecionado e que as atribuições estão configuradas.

1. Selecione **Atribuir**.

    O grupo é adicionado à lista de grupos licenciados e todos os membros têm acesso aos serviços incluídos do Azure AD.

## <a name="remove-a-license"></a>Remover uma licença

Você pode remover uma licença da página de usuário Azure AD de um usuário, da página de visão geral do grupo para uma atribuição de grupo ou a partir da página Azure AD **Licenses** para ver os usuários e grupos para obter uma licença.

### <a name="to-remove-a-license-from-a-user"></a>Para remover uma licença de um usuário

1. Na página **de usuários licenciados** para o plano de serviço, selecione o usuário que não deve mais ter a licença. Por exemplo, _Alain Charon_.

1. Selecione **remover licença**.

    ![Página de usuários licenciados com a opção de licença de remover realçada](media/license-users-groups/license-products-user-blade-with-remove-option-highlight.png)

> [!IMPORTANT]
> Licenças que um usuário herda de um grupo não podem ser removidas diretamente. Em vez disso, você precisa remover o usuário do grupo do qual ele está herdando a licença.

### <a name="to-remove-a-license-from-a-group"></a>Para remover uma licença de um grupo

1. Na página **de grupos licenciados** para o plano de licença, selecione o grupo que não deve mais ter a licença.

1. Selecione **remover licença**.

    ![Página Grupos de Licenciados com a opção Remover Licença Destacada](media/license-users-groups/license-products-group-blade-with-remove-option-highlight.png)
    
    > [!NOTE]
    > Quando uma conta de usuário no local sincronizada com o Azure AD fica fora de escopo para a sincronização ou quando a sincronização é removida, o usuário é excluído suavemente no Azure AD. Quando isso ocorrer, as licenças atribuídas ao usuário diretamente ou via licenciamento em grupo serão **marcadas** como suspensas em vez de **excluídas**.

## <a name="next-steps"></a>Próximas etapas

Depois de atribuir suas licenças, você pode executar os seguintes processos:

- [Identifique e resolva problemas de atribuição de licenças](../users-groups-roles/licensing-groups-resolve-problems.md)

- [Adicione usuários licenciados a um grupo para licenciamento](../users-groups-roles/licensing-groups-migrate-users.md)

- [Cenários, limitações e problemas conhecidos usando grupos para gerenciar o licenciamento no Azure Active Directory](../users-groups-roles/licensing-group-advanced.md)

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)
