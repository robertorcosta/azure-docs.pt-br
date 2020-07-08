---
title: Adicionar ou remover atribuições de função do Azure usando o portal do Azure-RBAC do Azure
description: Saiba como conceder acesso aos recursos do Azure para usuários, grupos, entidades de serviço ou identidades gerenciadas usando o portal do Azure e o controle de acesso baseado em função do Azure (RBAC do Azure).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/24/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: 76f4f39e7def192b8cb97c37aefc9f67d82ad4be
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85362203"
---
# <a name="add-or-remove-azure-role-assignments-using-the-azure-portal"></a>Adicionar ou remover atribuições de função do Azure usando o portal do Azure

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control-definition-grant.md)]Este artigo descreve como atribuir funções usando o portal do Azure.

Se você precisar atribuir funções de administrador no Azure Active Directory, consulte [Exibir e atribuir funções de administrador no Azure Active Directory](../active-directory/users-groups-roles/directory-manage-roles-portal.md).

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de função, você deve ter:

- as permissões `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, como [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)

## <a name="access-control-iam"></a>Controle de acesso (IAM)

O **controle de acesso (iam)** é a página que você normalmente usa para atribuir funções para conceder acesso aos recursos do Azure. Ele também é conhecido como gerenciamento de identidade e acesso e aparece em vários locais na portal do Azure. Veja a seguir um exemplo da página de controle de acesso (IAM) para uma assinatura.

![Página de controle de acesso (IAM) para uma assinatura](./media/role-assignments-portal/access-control-subscription.png)

Para ser o mais eficaz com a página de controle de acesso (IAM), ele ajuda se você pode responder às três perguntas a seguir ao tentar atribuir uma função:

1. **Quem precisa de acesso?**

    Quem se refere a um usuário, grupo, entidade de serviço ou identidade gerenciada. Isso também é chamado de *entidade de segurança*.

1. **De que função eles precisam?**

    As permissões são agrupadas em funções. Você pode selecionar em uma lista de várias [funções internas](built-in-roles.md) ou pode usar suas próprias funções personalizadas.

1. **Onde eles precisam de acesso?**

    Em que se refere ao conjunto de recursos ao qual o acesso se aplica. Onde pode ser um grupo de gerenciamento, uma assinatura, um grupo de recursos ou um único recurso, como uma conta de armazenamento. Isso é chamado de *escopo*.

## <a name="add-a-role-assignment"></a>Adicionar uma atribuição de função

No RBAC do Azure, para conceder acesso a um recurso do Azure, você adiciona uma atribuição de função. Siga estas etapas para atribuir uma função.

1. No portal do Azure, clique em **todos os serviços** e selecione o escopo ao qual você deseja conceder acesso. Por exemplo, você pode selecionar **grupos de gerenciamento**, **assinaturas**, **grupos de recursos**, ou um recurso.

1. Clique no recurso específico para esse escopo.

1. Clique em **Controle de acesso (IAM)** .

1. Clique na guia **atribuições de função** para exibir as atribuições de função nesse escopo.

    ![Controle de acesso (IAM) e guia atribuições de função](./media/role-assignments-portal/role-assignments.png)

1. Clique em **Adicionar**  >  **Adicionar atribuição de função**.

   Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desativada.

   ![Adicionar menu de atribuição de função](./media/shared/add-role-assignment-menu.png)

    O painel Adicionar atribuição de função é aberto.

   ![Adicionar painel de atribuição de função](./media/role-assignments-portal/add-role-assignment.png)

1. Na lista suspensa **Função**, selecione uma função, por exemplo, **Colaborador da Máquina Virtual**.

1. Na lista **Selecionar**, selecione um usuário, grupo, entidade de serviço ou identidade gerenciada. Se você não vir a entidade de segurança na lista, digite na caixa **Selecionar** para pesquisar nomes de exibição, endereços de email e identificadores de objeto no diretório.

1. Clique em **Salvar** para atribuir a função.

   Após alguns instantes, a entidade de segurança é atribuída a função no escopo selecionado.

    ![Adicionar atribuição de função salva](./media/role-assignments-portal/add-role-assignment-save.png)

## <a name="assign-a-user-as-an-administrator-of-a-subscription"></a>Atribuir um usuário como um administrador de uma assinatura

Para tornar um usuário administrador de uma assinatura do Azure, atribua-o à função [Proprietário](built-in-roles.md#owner) no escopo da assinatura. A função proprietário dá ao usuário acesso completo a todos os recursos na assinatura, incluindo a permissão para conceder acesso a outras pessoas. Essas etapas são as mesmas que as de qualquer outra atribuição de função.

1. No portal do Microsoft Azure, clique em **Todos os serviços** e, em seguida, em **Assinaturas**.

1. Clique na assinatura em que você deseja conceder acesso.

1. Clique em **Controle de acesso (IAM)** .

1. Clique na guia **atribuições de função** para exibir as atribuições de função para esta assinatura.

    ![Controle de acesso (IAM) e guia atribuições de função](./media/role-assignments-portal/role-assignments.png)

1. Clique em **Adicionar**  >  **Adicionar atribuição de função**.

   Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desativada.

   ![Adicionar menu de atribuição de função](./media/shared/add-role-assignment-menu.png)

    O painel Adicionar atribuição de função é aberto.

   ![Adicionar painel de atribuição de função](./media/role-assignments-portal/add-role-assignment.png)

1. Na lista suspensa **Função**, selecione a função **Proprietário**.

1. Na lista **Selecionar**, selecione um usuário. Se o usuário não estiver na lista, digite na caixa **Selecionar** para pesquisar no diretório os nomes de exibição e os endereços de email.

1. Clique em **Salvar** para atribuir a função.

   Após alguns instantes, o usuário é atribuído à função Proprietário no escopo da assinatura.

## <a name="add-a-role-assignment-for-a-managed-identity-preview"></a>Adicionar uma atribuição de função para uma identidade gerenciada (versão prévia)

Você pode adicionar atribuições de função para uma identidade gerenciada usando a página **controle de acesso (iam)** , conforme descrito anteriormente neste artigo. Ao usar a página controle de acesso (IAM), você começa com o escopo e, em seguida, seleciona a identidade e a função gerenciadas. Esta seção descreve uma maneira alternativa de adicionar atribuições de função para uma identidade gerenciada. Usando essas etapas, você começa com a identidade gerenciada e, em seguida, seleciona o escopo e a função.

> [!IMPORTANT]
> A adição de uma atribuição de função para uma identidade gerenciada usando essas etapas alternativas está atualmente em versão prévia.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

Siga estas etapas para atribuir uma função a uma identidade gerenciada atribuída pelo sistema, começando com a identidade gerenciada.

1. No portal do Azure, abra uma identidade gerenciada atribuída pelo sistema.

1. No menu à esquerda, clique em **identidade**.

    ![Identidade gerenciada atribuída pelo sistema](./media/shared/identity-system-assigned.png)

1. Em **permissões**, clique em **atribuições de função do Azure**.

    Se as funções já estiverem atribuídas à identidade gerenciada atribuída pelo sistema selecionada, você verá a lista de atribuições de função. Essa lista inclui todas as atribuições de função que você tem permissão para ler.

    ![Atribuições de função para uma identidade gerenciada atribuída pelo sistema](./media/shared/role-assignments-system-assigned.png)

1. Para alterar a assinatura, clique na lista **assinatura** .

1. Clique em **Adicionar atribuição de função (versão prévia)**.

1. Use as listas suspensas para selecionar o conjunto de recursos que a atribuição de função aplica a, como **assinatura**, **grupo de recursos**ou recurso.

    Se você não tiver permissões de gravação de atribuição de função para o escopo selecionado, uma mensagem embutida será exibida. 

1. Na lista suspensa **Função**, selecione uma função, por exemplo, **Colaborador da Máquina Virtual**.

   ![Adicionar painel de atribuição de função](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. Clique em **Salvar** para atribuir a função.

   Depois de alguns instantes, a identidade gerenciada é atribuída à função no escopo selecionado.

### <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

Siga estas etapas para atribuir uma função a uma identidade gerenciada atribuída pelo usuário, começando com a identidade gerenciada.

1. No portal do Azure, abra uma identidade gerenciada atribuída pelo usuário.

1. No menu à esquerda, clique em **atribuições de função do Azure**.

    Se as funções já estiverem atribuídas à identidade gerenciada atribuída pelo usuário selecionada, você verá a lista de atribuições de função. Essa lista inclui todas as atribuições de função que você tem permissão para ler.

    ![Atribuições de função para uma identidade gerenciada atribuída pelo sistema](./media/shared/role-assignments-user-assigned.png)

1. Para alterar a assinatura, clique na lista **assinatura** .

1. Clique em **Adicionar atribuição de função (versão prévia)**.

1. Use as listas suspensas para selecionar o conjunto de recursos que a atribuição de função aplica a, como **assinatura**, **grupo de recursos**ou recurso.

    Se você não tiver permissões de gravação de atribuição de função para o escopo selecionado, uma mensagem embutida será exibida. 

1. Na lista suspensa **Função**, selecione uma função, por exemplo, **Colaborador da Máquina Virtual**.

   ![Adicionar painel de atribuição de função](./media/role-assignments-portal/add-role-assignment-with-scope.png)

1. Clique em **Salvar** para atribuir a função.

   Depois de alguns instantes, a identidade gerenciada é atribuída à função no escopo selecionado.

## <a name="remove-a-role-assignment"></a>Excluir uma atribuição de função

No RBAC do Azure, para remover o acesso de um recurso do Azure, você remove uma atribuição de função. Siga estas etapas para remover uma atribuição de função.

1. Abra **Controle de acesso (IAM)** em um escopo, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso, no qual você deseja remover o acesso.

1. Clique na guia **Atribuições de funções** para visualizar todas as atribuições de função para essa assinatura.

1. Na lista de atribuições de função, marque a caixa de seleção ao lado de objeto com a atribuição de função de segurança que você deseja remover.

   ![Remover mensagem de atribuição de função](./media/role-assignments-portal/remove-role-assignment-select.png)

1. Clique em **Remover**.

   ![Remover mensagem de atribuição de função](./media/role-assignments-portal/remove-role-assignment.png)

1. Na mensagem para remover a atribuição de função exibida, clique em **Sim**.

    Se você vir uma mensagem informando que as atribuições de função herdadas não podem ser removidas, você está tentando remover uma atribuição de função em um escopo filho. Você deve abrir o controle de acesso (IAM) no escopo onde a função foi atribuída e tentar novamente. Uma maneira rápida de abrir o controle de acesso (IAM) no escopo correto é examinar a coluna **escopo** e clicar no link ao lado de **(Herdado)**.

   ![Remover mensagem de atribuição de função](./media/role-assignments-portal/remove-role-assignment-inherited.png)

## <a name="next-steps"></a>Próximas etapas

- [Listar atribuições de função do Azure usando o portal do Azure](role-assignments-list-portal.md)
- [Tutorial: Conceder acesso aos recursos do Azure para um usuário usando o portal do Azure](quickstart-assign-role-user-portal.md)
- [Solucionar problemas do RBAC do Azure](troubleshooting.md)
- [Organizar seus recursos com grupos de gerenciamento do Azure](../governance/management-groups/overview.md)
