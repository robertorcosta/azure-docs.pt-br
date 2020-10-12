---
title: Listar atribuições de função do Azure usando o portal do Azure-RBAC do Azure
description: Saiba como determinar quais recursos os usuários, grupos, entidades de serviço ou identidades gerenciadas têm acesso ao uso do portal do Azure e do Azure RBAC (controle de acesso baseado em função).
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/24/2020
ms.author: rolyon
ms.reviewer: bagovind
ms.openlocfilehash: f7d56ecc7fc6bd850fced33c2c1cf20902bb2df4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "85361824"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>Listar atribuições de função do Azure usando o portal do Azure

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control-definition-list.md)] Este artigo descreve como listar atribuições de função usando o portal do Azure.

> [!NOTE]
> Se sua organização tiver funções de gerenciamento terceirizadas para um provedor de serviços que usa o [Gerenciamento de recursos delegado do Azure](../lighthouse/concepts/azure-delegated-resource-management.md), as atribuições de função autorizadas por esse provedor de serviços não serão mostradas aqui.

## <a name="list-role-assignments-for-a-user-or-group"></a>Listar atribuições de função para um usuário ou grupo

A maneira mais fácil de ver as funções atribuídas a um usuário ou grupo em uma assinatura é usar o painel **atribuições de função do Azure** .

1. No portal do Azure, selecione **todos os serviços** no menu portal do Azure.

1. Selecione **Azure Active Directory** e, em seguida, selecione **usuários** ou **grupos**.

1. Clique no usuário ou grupo para o qual você deseja listar as atribuições de função.

1. Clique em **atribuições de função do Azure**.

    Você vê uma lista de funções atribuídas ao usuário ou grupo selecionado em vários escopos, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso. Essa lista inclui todas as atribuições de função que você tem permissão para ler.

    ![Atribuições de função de um usuário](./media/role-assignments-list-portal/azure-role-assignments-user.png)    

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

## <a name="list-role-assignments-for-a-managed-identity"></a>Listar atribuições de função para uma identidade gerenciada

Você pode listar as atribuições de função para identidades gerenciadas atribuídas pelo sistema e atribuídas pelo usuário em um determinado escopo usando a folha de **controle de acesso (iam)** , conforme descrito anteriormente. Esta seção descreve como listar atribuições de função apenas para a identidade gerenciada.

### <a name="system-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo sistema

1. No portal do Azure, abra uma identidade gerenciada atribuída pelo sistema.

1. No menu à esquerda, clique em **identidade**.

    ![Identidade gerenciada atribuída pelo sistema](./media/shared/identity-system-assigned.png)

1. Em **permissões**, clique em **atribuições de função do Azure**.

    Você verá uma lista de funções atribuídas à identidade gerenciada atribuída pelo sistema selecionada em vários escopos, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso. Essa lista inclui todas as atribuições de função que você tem permissão para ler.

    ![Atribuições de função para uma identidade gerenciada atribuída pelo sistema](./media/shared/role-assignments-system-assigned.png)

1. Para alterar a assinatura, clique na lista **assinatura** .

### <a name="user-assigned-managed-identity"></a>Identidade gerenciada atribuída pelo usuário

1. No portal do Azure, abra uma identidade gerenciada atribuída pelo usuário.

1. Clique em **atribuições de função do Azure**.

    Você verá uma lista de funções atribuídas à identidade gerenciada atribuída pelo usuário selecionada em vários escopos, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso. Essa lista inclui todas as atribuições de função que você tem permissão para ler.

    ![Atribuições de função para uma identidade gerenciada atribuída pelo sistema](./media/shared/role-assignments-user-assigned.png)

1. Para alterar a assinatura, clique na lista **assinatura** .

## <a name="list-number-of-role-assignments"></a>Listar número de atribuições de função

Em cada assinatura, você pode ter até **2 mil** atribuições de função. Esse limite inclui atribuições de função na assinatura, no grupo de recursos e nos escopos de recursos. Para ajudá-lo a manter o controle desse limite, a guia **atribuições de função** inclui um gráfico que lista o número de atribuições de função para a assinatura atual.

![Controle de acesso – número do gráfico de atribuições de função](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

Se estiver ficando próximo do número máximo e tentar adicionar mais atribuições de função, você verá um aviso no painel **Adicionar atribuição de função** . Para obter maneiras de reduzir o número de atribuições de função, consulte [solucionar problemas do RBAC do Azure](troubleshooting.md#azure-role-assignments-limit).

![Controle de acesso-Adicionar aviso de atribuição de função](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="download-role-assignments-preview"></a>Baixar atribuições de função (versão prévia)

Você pode baixar atribuições de função em um escopo em formatos CSV ou JSON. Isso pode ser útil se você precisar inspecionar a lista em uma planilha ou fazer um inventário ao migrar uma assinatura.

> [!IMPORTANT]
> O download das atribuições de função está atualmente em visualização pública.
> Essa versão prévia é fornecida sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Alguns recursos podem não ter suporte ou podem ter restrição de recursos.
> Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Ao baixar atribuições de função, você deve ter em mente os seguintes critérios:

- Se você não tiver permissões para ler o diretório, como a função leitores de diretório, as colunas DisplayName, SignInName e ObjectType ficarão em branco.
- As atribuições de função cuja entidade de segurança foi excluída não estão incluídas.
- O acesso concedido aos administradores clássicos não está incluído.

Siga estas etapas para baixar atribuições de função em um escopo.

1. No portal do Azure, clique em **todos os serviços** e selecione o escopo onde você deseja baixar as atribuições de função. Por exemplo, você pode selecionar **grupos de gerenciamento**, **assinaturas**, **grupos de recursos**, ou um recurso.

1. Clique no recurso específico.

1. Clique em **Controle de acesso (IAM)** .

1. Clique em **baixar atribuições de função (versão prévia)** para abrir o painel baixar atribuições de função.

    ![Controle de acesso-baixar atribuições de função](./media/role-assignments-list-portal/download-role-assignments.png)

1. Use as caixas de seleção para selecionar as atribuições de função que você deseja incluir no arquivo baixado.

    - **Herdado** – incluir atribuições de função herdadas para o escopo atual.
    - **No escopo atual** – inclua atribuições de função para o escopo atual.
    - **Filhos** – inclua atribuições de função em níveis abaixo do escopo atual. Esta caixa de seleção está desabilitada para o escopo do grupo de gerenciamento.

1. Selecione o formato de arquivo, que pode ser CSV (valores separados por vírgula) ou JavaScript Object Notation (JSON).

1. Especifique o nome do arquivo.

1. Clique em **Iniciar** para iniciar o download.

    Os exemplos a seguir mostram a saída de cada formato de arquivo.

    ![Baixar atribuições de função como CSV](./media/role-assignments-list-portal/download-role-assignments-csv.png)

    ![Baixar atribuições de função como CSV](./media/role-assignments-list-portal/download-role-assignments-json.png)

## <a name="next-steps"></a>Próximas etapas

- [Adicionar ou remover atribuições de função do Azure usando o portal do Azure](role-assignments-portal.md)
- [Solucionar problemas do RBAC do Azure](troubleshooting.md)
