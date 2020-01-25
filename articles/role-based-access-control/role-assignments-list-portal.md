---
title: Listar atribuições de função usando o RBAC do Azure e o portal do Azure
description: Saiba como determinar quais recursos os usuários, grupos, entidades de serviço ou identidades gerenciadas têm acesso ao uso do RBAC (controle de acesso baseado em função) do Azure e o portal do Azure.
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
ms.date: 01/23/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 099cf74dd27f39a4289397d5178511125d9ebf6f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720686"
---
# <a name="list-role-assignments-using-azure-rbac-and-the-azure-portal"></a>Listar atribuições de função usando o RBAC do Azure e o portal do Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] este artigo descreve como listar atribuições de função usando o portal do Azure.

> [!NOTE]
> Se sua organização tiver funções de gerenciamento terceirizadas para um provedor de serviços que usa o [Gerenciamento de recursos delegado do Azure](../lighthouse/concepts/azure-delegated-resource-management.md), as atribuições de função autorizadas por esse provedor de serviços não serão mostradas aqui.

## <a name="list-role-assignments-for-a-user-or-group"></a>Listar atribuições de função para um usuário ou grupo

A maneira mais fácil de ver as funções atribuídas a um usuário ou grupo em uma assinatura é usar o painel **recursos do Azure** .

1. No portal do Azure, clique em **todos os serviços** e selecione **usuários** ou **grupos**.

1. Clique no usuário ou grupo para o qual você deseja listar as atribuições de função.

1. Clique em **Recursos do Azure**.

    Você vê uma lista de funções atribuídas ao usuário ou grupo selecionado em vários escopos, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso. Essa lista inclui todas as atribuições de função que você tem permissão para ler.

    ![Atribuições de função de um usuário](./media/role-assignments-list-portal/azure-resources-user.png)    

1. Para alterar a assinatura, clique na lista **assinaturas** .

## <a name="list-owners-of-a-subscription"></a>Listar os proprietários de uma assinatura

Os usuários aos quais foi atribuída a função de [proprietário](built-in-roles.md#owner) de uma assinatura podem gerenciar tudo na assinatura. Siga estas etapas para listar os proprietários de uma assinatura.

1. No portal do Microsoft Azure, clique em **Todos os serviços** e, em seguida, em **Assinaturas**.

1. Clique na assinatura da qual você deseja listar os proprietários.

1. Clique em **Controle de acesso (IAM)** .

1. Clique na guia **Atribuições de funções** para visualizar todas as atribuições de função para essa assinatura.

1. Role até a seção **proprietários** para ver todos os usuários aos quais foi atribuída a função de proprietário para esta assinatura.

   ![Controle de acesso à assinatura – guia atribuições de função](./media/role-assignments-list-portal/access-control-role-assignments-subscription.png)

## <a name="list-role-assignments-at-a-scope"></a>Listar atribuições de função em um escopo

1. No portal do Azure, clique em **todos os serviços** e, em seguida, selecione o escopo. Por exemplo, você pode selecionar **grupos de gerenciamento**, **assinaturas**, **grupos de recursos**, ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controle de acesso (IAM)** .

1. Clique na guia **Atribuições de função** para visualizar todas as atribuições de função nesse escopo.

   ![Controle de acesso – guia de atribuições de função](./media/role-assignments-list-portal/access-control-role-assignments.png)

   Na guia Atribuições de função, você pode ver quem tem acesso nesse escopo. Observe que algumas funções são definidas para **Este recurso** enquanto outras são **(Herdadas)** de outro escopo. O acesso é atribuído especificamente a esse recurso ou herdado de uma atribuição ao escopo pai.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>Listar atribuições de função para um usuário em um escopo

Para listar o acesso de um usuário, grupo, entidade de serviço ou identidade gerenciada, você lista suas atribuições de função. Siga estas etapas para listar as atribuições de função para um único usuário, grupo, entidade de serviço ou identidade gerenciada em um escopo específico.

1. No portal do Azure, clique em **todos os serviços** e, em seguida, selecione o escopo. Por exemplo, você pode selecionar **grupos de gerenciamento**, **assinaturas**, **grupos de recursos**, ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controle de acesso (IAM)** .

1. Clique na guia **Verificar acesso**.

    ![Controle de acesso - verificar a guia de acesso](./media/role-assignments-list-portal/access-control-check-access.png)

1. Na lista **Localizar**, selecione o tipo de entidade de segurança para a qual você deseja verificar o acesso.

1. Na caixa de pesquisa, insira uma cadeia de caracteres para pesquisar no diretório nomes de exibição, endereços de e-mail ou identificadores de objetos.

    ![Verifique a lista de seleção de acesso](./media/role-assignments-list-portal/check-access-select.png)

1. Clique na entidade de segurança para abrir o painel **atribuições**.

    ![painel atribuições](./media/role-assignments-list-portal/check-access-assignments.png)

    Nesse painel, você pode ver as funções atribuídas à entidade de segurança selecionada e o escopo. Se houver alguma atribuição de negação nesse escopo ou herdada para esse escopo, ela será listada.

## <a name="list-role-assignments-for-a-system-assigned-managed-identity"></a>Listar atribuições de função para uma identidade gerenciada atribuída pelo sistema

1. No portal do Azure, abra uma identidade gerenciada atribuída pelo sistema.

1. No menu à esquerda, clique em **identidade**.

    ![Identidade gerenciada atribuída pelo sistema](./media/role-assignments-list-portal/identity-system-assigned.png)

1. Em **atribuições de função**, clique em **Mostrar as funções de RBAC do Azure atribuídas a essa identidade gerenciada**.

    Você verá uma lista de funções atribuídas à identidade gerenciada atribuída pelo sistema selecionada em vários escopos, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso. Essa lista inclui todas as atribuições de função que você tem permissão para ler.

    ![Atribuições de função para uma identidade gerenciada atribuída pelo sistema](./media/role-assignments-list-portal/azure-resources-system-assigned.png)

## <a name="list-role-assignments-for-a-user-assigned-managed-identity"></a>Listar atribuições de função para uma identidade gerenciada atribuída pelo usuário

1. No portal do Azure, abra uma identidade gerenciada atribuída pelo sistema.

1. Clique em **Recursos do Azure**.

    Você verá uma lista de funções atribuídas à identidade gerenciada atribuída pelo usuário selecionada em vários escopos, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso. Essa lista inclui todas as atribuições de função que você tem permissão para ler.

    ![Atribuições de função para uma identidade gerenciada atribuída pelo sistema](./media/role-assignments-list-portal/azure-resources-user-assigned.png)

1. Para alterar a assinatura, clique na lista **assinaturas** .

## <a name="list-number-of-role-assignments"></a>Listar número de atribuições de função

Você pode ter até **2000** atribuições de função em cada assinatura. Para ajudá-lo a manter o controle desses limites, a guia **atribuições de função** inclui um gráfico que lista o número atual de atribuições de função.

![Controle de acesso – número do gráfico de atribuições de função](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Se estiver ficando próximo do número máximo e tentar adicionar mais atribuições de função, você verá um aviso no painel **Adicionar atribuição de função** . Você pode reduzir o número de atribuições de função excluindo atribuições de função que não são mais necessárias ou atribuindo funções a grupos em vez de usuários individuais.

![Controle de acesso-Adicionar aviso de atribuição de função](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="next-steps"></a>Próximos passos

- [Adicionar ou remover atribuições de função usando o RBAC do Azure e o portal do Azure](role-assignments-portal.md)
- [Solução de problemas com o RBAC para recursos do Azure](troubleshooting.md)
