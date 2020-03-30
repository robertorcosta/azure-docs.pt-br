---
title: Listar atribuições de função usando o Azure RBAC e o portal Azure
description: Saiba como determinar quais recursos usuários, grupos, diretores de serviço ou identidades gerenciadas têm acesso ao controle de acesso baseado em função do Azure (RBAC) e do portal Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: 8078f366-a2c4-4fbb-a44b-fc39fd89df81
ms.service: role-based-access-control
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/18/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 03a3d3c7d572d7ec5b8d3ac3d527d0d59e649bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062237"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Listar atribuições de função usando o Azure RBAC e o portal Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)]Este artigo descreve como listar atribuições de função usando o portal Azure.

> [!NOTE]
> Se sua organização tiver funções de gerenciamento terceirizadas para um provedor de serviços que usa [o gerenciamento de recursos delegado do Azure,](../lighthouse/concepts/azure-delegated-resource-management.md)as atribuições de função autorizadas por esse provedor de serviços não serão mostradas aqui.

## <a name="list-role-assignments-for-a-user-or-group"></a>Listar atribuições de função para um usuário ou grupo

A maneira mais fácil de ver as funções atribuídas a um usuário ou grupo em uma assinatura é usar o painel de recursos do **Azure.**

1. No portal Azure, clique em **Todos os serviços** e selecione **Usuários** ou **Grupos**.

1. Clique no usuário ou grupo para o qual deseja listar as atribuições da função.

1. Clique em **Recursos do Azure**.

    Você vê uma lista de funções atribuídas ao usuário ou grupo selecionado em vários escopos, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso. Esta lista inclui todas as atribuições de função que você tem permissão para ler.

    ![Atribuições de função de um usuário](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Para alterar a assinatura, clique na lista **de Assinaturas.**

## <a name="list-owners-of-a-subscription"></a>Listar proprietários de uma assinatura

Os usuários que foram designados para a função [Proprietário](built-in-roles.md#owner) para uma assinatura podem gerenciar tudo na assinatura. Siga estas etapas para listar os proprietários de uma assinatura.

1. No portal do Microsoft Azure, clique em **Todos os serviços** e, em seguida, em **Assinaturas**.

1. Clique na assinatura da lista dos proprietários.

1. Clique **em Acessar controle (IAM)**.

1. Clique na guia **Atribuições de funções** para visualizar todas as atribuições de função para essa assinatura.

1. Role até a seção **Proprietários** para ver todos os usuários que foram atribuídos à função Proprietário para esta assinatura.

   ![Controle de acesso à assinatura - Guia de atribuições de função](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Listar atribuições de função em um escopo

1. No portal Azure, clique em **Todos os serviços** e selecione o escopo. Por exemplo, você pode selecionar **grupos de gerenciamento**, **assinaturas**, **grupos de recursos**, ou um recurso.

1. Clique no recurso específico.

1. Clique **em Acessar controle (IAM)**.

1. Clique na guia **Atribuições de função** para visualizar todas as atribuições de função nesse escopo.

   ![Controle de acesso – guia de atribuições de função](./media/role-assignments-list-portal/access-control-role-assignments.png)

   Na guia Atribuições de função, você pode ver quem tem acesso nesse escopo. Observe que algumas funções são definidas para **Este recurso** enquanto outras são **(Herdadas)** de outro escopo. O acesso é atribuído especificamente a esse recurso ou herdado de uma atribuição ao escopo pai.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Listar atribuições de função para um usuário em um escopo

Para listar o acesso a um usuário, grupo, diretor de serviço ou identidade gerenciada, você lista suas atribuições de função. Siga essas etapas para listar as atribuições de função para um único usuário, grupo, diretor de serviço ou identidade gerenciada em um escopo específico.

1. No portal Azure, clique em **Todos os serviços** e selecione o escopo. Por exemplo, você pode selecionar **grupos de gerenciamento**, **assinaturas**, **grupos de recursos**, ou um recurso.

1. Clique no recurso específico.

1. Clique **em Acessar controle (IAM)**.

1. Clique na guia **Verificar acesso**.

    ![Controle de acesso - verificar a guia de acesso](./media/role-assignments-list-portal/access-control-check-access.png)

1. Na lista **Localizar**, selecione o tipo de entidade de segurança para a qual você deseja verificar o acesso.

1. Na caixa de pesquisa, insira uma cadeia de caracteres para pesquisar no diretório nomes de exibição, endereços de e-mail ou identificadores de objetos.

    ![Verifique a lista de seleção de acesso](./media/role-assignments-list-portal/check-access-select.png)

1. Clique na entidade de segurança para abrir o painel **atribuições**.

    ![painel atribuições](./media/role-assignments-list-portal/check-access-assignments.png)

    Nesse painel, você pode ver as funções atribuídas à entidade de segurança selecionada e o escopo. Se houver alguma atribuição de negação nesse escopo ou herdada para esse escopo, ela será listada.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>Listar atribuições de função para uma identidade gerenciada atribuída pelo sistema

1. No portal Azure, abra uma identidade gerenciada atribuída ao sistema.

1. No menu à esquerda, clique em **Identidade**.

    ![Identidade gerenciada atribuída pelo sistema](./media/role-assignments-list-portal/identity-system-assigned.png)

1. Em **Atribuições de função,** clique em **Mostrar as funções RBAC do Azure atribuídas a essa identidade gerenciada**.

    Você vê uma lista de funções atribuídas à identidade gerenciada atribuída ao sistema selecionado em vários escopos, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso. Esta lista inclui todas as atribuições de função que você tem permissão para ler.

    ![Atribuições de função para uma identidade gerenciada atribuída ao sistema](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Listar atribuições de função para uma identidade gerenciada atribuída pelo usuário

1. No portal Azure, abra uma identidade gerenciada atribuída pelo usuário.

1. Clique em **Recursos do Azure**.

    Você vê uma lista de funções atribuídas à identidade gerenciada atribuída pelo usuário selecionada em vários escopos, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso. Esta lista inclui todas as atribuições de função que você tem permissão para ler.

    ![Atribuições de função para uma identidade gerenciada atribuída ao sistema](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Para alterar a assinatura, clique na lista **de Assinaturas.**

## <a name="list-number-of-role-assignments"></a>Número de lista de atribuições de função

Você pode ter até **2000** atribuições de função em cada assinatura. Para ajudá-lo a controlar esse limite, a guia **Atribuições de função** inclui um gráfico que lista o número de atribuições de função para a assinatura atual.

![Controle de acesso - Gráfico de número de atribuições de função](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Se você estiver chegando perto do número máximo e tentar adicionar mais atribuições de função, você verá um aviso no painel **de atribuição adicionar.** Para obter maneiras de reduzir o número de atribuições de função, consulte [Solução de problemas Do Azure RBAC](troubleshooting.md#azure-role-assignments-limit).

![Controle de acesso - Adicionar aviso de atribuição de função](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>Próximas etapas

- [Adicionar ou remover atribuições de função usando o Azure RBAC e o portal Azure](role-assignments-portal.md)
- [Solução de problemas com o RBAC para recursos do Azure](troubleshooting.md)
