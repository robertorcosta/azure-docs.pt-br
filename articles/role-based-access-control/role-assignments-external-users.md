---
title: Adicionar ou remover atribuições de função para usuários externos com RBAC e o portal Azure
description: Saiba como conceder acesso aos recursos do Azure para usuários externos a uma organização usando o RBAC (Role-Based Access Control, controle de acesso baseado em função do Azure).
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 11/25/2019
ms.author: rolyon
ms.reviewer: skwan
ms.custom: it-pro
ms.openlocfilehash: 1c440b85f792ac5bb1336f4d20f930aafc38ad7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79245636"
---
# <a name="add-or-remove-role-assignments-for-external-guest-users-using-azure-rbac-and-the-azure-portal"></a>Adicionar ou remover atribuições de função para usuários convidados externos usando o Azure RBAC e o portal Azure

[O RBAC (Role-Based Access Control, controle de acesso baseado em função do Azure)](overview.md) permite uma melhor gestão de segurança para grandes organizações e para pequenas e médias empresas que trabalham com colaboradores externos, fornecedores ou freelancers que precisam de acesso a recursos específicos em seu ambiente, mas não necessariamente a toda a infra-estrutura ou quaisquer escopos relacionados ao faturamento. Você pode usar os recursos no [Azure Active Directory B2B](../active-directory/b2b/what-is-b2b.md) para colaborar com usuários convidados externos e você pode usar o RBAC para conceder apenas as permissões que os usuários convidados precisam em seu ambiente.

## <a name="prerequisites"></a>Pré-requisitos

Para adicionar ou remover atribuições de função, você deve ter:

- as permissões `Microsoft.Authorization/roleAssignments/write` e `Microsoft.Authorization/roleAssignments/delete`, como [Administrador de Acesso do Usuário](built-in-roles.md#user-access-administrator) ou [Proprietário](built-in-roles.md#owner)

## <a name="when-would-you-invite-guest-users"></a>Quando você convidaria os usuários convidados?

Aqui estão alguns exemplos de cenários em que você pode convidar usuários convidados para sua organização e conceder permissões:

- Permitir que um fornecedor externo autônomo tenha apenas uma conta de email para acessar os recursos do Azure para um projeto.
- Permita que um parceiro externo gerencie certos recursos ou uma assinatura inteira.
- Permitir que os engenheiros de suporte que não são da sua organização (como o suporte da Microsoft) acessem temporariamente o recurso do Azure para solucionar problemas.

## <a name="permission-differences-between-member-users-and-guest-users"></a>Diferenças de permissão entre usuários membros e usuários convidados

Os membros nativos de um diretório (usuários membros) têm permissões diferentes dos usuários convidados de outro diretório como convidados de colaboração B2B (usuários convidados). Por exemplo, o usuário de membros pode ler quase todas as informações do diretório, enquanto os usuários convidados têm permissões de diretório restritas. Para obter mais informações sobre usuários membros e usuários [convidados, consulte Quais são as permissões de usuário padrão no Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

## <a name="add-a-guest-user-to-your-directory"></a>Adicionar um usuário convidado ao diretório

Siga estas etapas para adicionar um usuário convidado ao seu diretório usando a página Diretório Ativo do Azure.

1. Certifique-se de que as configurações de colaboração externa da sua organização estejam configuradas de forma que você possa convidar convidados. Para obter mais informações, consulte [Ativar colaboração externa B2B e gerenciar quem pode convidar os convidados](../active-directory/b2b/delegate-invitations.md).

1. No portal Azure, clique em **Azure Active Directory** > **Users** > **Novo usuário convidado**.

    ![Novo recurso de usuário convidado no portal Azure](./media/role-assignments-external-users/invite-guest-user.png)

1. Siga os passos para adicionar um novo usuário convidado. Para obter mais informações, consulte [Adicionar usuários de colaboração B2B do Active Directory no portal Azure](../active-directory/b2b/add-users-administrator.md#add-guest-users-to-the-directory).

Depois de adicionar um usuário convidado ao diretório, você poderá enviar ao usuário convidado um link direto para um aplicativo compartilhado, ou o usuário convidado poderá clicar na URL de resgate no email de convite.

![E-mail de convite do usuário convidado](./media/role-assignments-external-users/invite-email.png)

Para que o usuário convidado possa acessar seu diretório, ele deve concluir o processo de convite.

![Permissões de revisão de convite saqueado para usuário convidado](./media/role-assignments-external-users/invite-review-permissions.png)

Para obter mais informações sobre o processo de convite, consulte [o resgate do convite de colaboração B2B do Azure Active Directory](../active-directory/b2b/redemption-experience.md).

## <a name="add-a-role-assignment-for-a-guest-user"></a>Adicione uma atribuição de função para um usuário convidado

No RBAC, para conceder acesso, você atribui uma função. Para adicionar uma atribuição de função para um usuário convidado, você segue [os mesmos passos](role-assignments-portal.md#add-a-role-assignment) que seguiria para um usuário membro, grupo, diretor de serviço ou identidade gerenciada. Siga essas etapas adicione uma atribuição de função para um usuário convidado em diferentes escopos.

1. No portal Azure, clique em **Todos os serviços**.

1.  Selecione o conjunto de recursos aos quais o acesso se aplica, também conhecido como escopo. Por exemplo, você pode selecionar **grupos de gerenciamento**, **assinaturas**, **grupos de recursos**, ou um recurso.

1. Clique no recurso específico.

1. Clique **em Acessar controle (IAM)**.

    A captura de tela a seguir mostra um exemplo da lâmina de controle de acesso (IAM) para um grupo de recursos. Se você fizer qualquer alteração de controle de acesso aqui, elas se aplicarão apenas ao grupo de recursos.

    ![Lâmina de controle de acesso (IAM) para um grupo de recursos](./media/role-assignments-external-users/access-control-resource-group.png)

1. Clique na guia **Atribuições de função** para visualizar todas as atribuições de função nesse escopo.

1. Clique em **Adicionar** > **atribuição de função** para abrir o painel de atribuição adicionar função.

    Se você não tiver permissões para atribuir funções, a opção Adicionar atribuição de função será desativada.

    ![Adicionar menu](./media/role-assignments-external-users/add-menu.png)

1. Na lista suspensa **Função**, selecione uma função, por exemplo, **Colaborador da Máquina Virtual**.

1. Na lista **Selecionar,** selecione o usuário convidado. Se você não ver o usuário na lista, você pode digitar na caixa **Selecionar** para procurar nomes de exibição, endereços de e-mail e identificadores de objetos.

   ![Adicionar painel de atribuição de função](./media/role-assignments-external-users/add-role-assignment.png)

1. Clique **em Salvar** para atribuir a função no escopo selecionado.

    ![Atribuição de função para contribuinte de máquina virtual](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="add-a-role-assignment-for-a-guest-user-not-yet-in-your-directory"></a>Adicione uma atribuição de função para um usuário convidado ainda não em seu diretório

Para adicionar uma atribuição de função para um usuário convidado, você segue [os mesmos passos](role-assignments-portal.md#add-a-role-assignment) que seguiria para um usuário membro, grupo, diretor de serviço ou identidade gerenciada.

Se o usuário convidado ainda não estiver em seu diretório, você pode convidar o usuário diretamente do painel de atribuição adicionar função.

1. No portal Azure, clique em **Todos os serviços**.

1.  Selecione o conjunto de recursos aos quais o acesso se aplica, também conhecido como escopo. Por exemplo, você pode selecionar **grupos de gerenciamento**, **assinaturas**, **grupos de recursos**, ou um recurso.

1. Clique no recurso específico.

1. Clique **em Acessar controle (IAM)**.

1. Clique na guia **Atribuições de função** para visualizar todas as atribuições de função nesse escopo.

1. Clique em **Adicionar** > **atribuição de função** para abrir o painel de atribuição adicionar função.

    ![Adicionar menu](./media/role-assignments-external-users/add-menu.png)

1. Na lista suspensa **Função**, selecione uma função, por exemplo, **Colaborador da Máquina Virtual**.

1. Na lista **Selecionar,** digite o endereço de e-mail da pessoa que deseja convidar e selecione essa pessoa.

   ![Convidar o usuário convidado em Adicionar painel de atribuição de função](./media/role-assignments-external-users/add-role-assignment-new-guest.png)

1. Clique **em Salvar** para adicionar o usuário convidado ao seu diretório, atribuir a função e enviar um convite.

    Depois de alguns momentos, você verá uma notificação da atribuição da função e informações sobre o convite.

    ![Atribuição de função e notificação do usuário convidado](./media/role-assignments-external-users/invited-user-notification.png)

1. Para convidar manualmente o usuário convidado, clique com o botão direito do mouse e copie o link do convite na notificação. Não clique no link de convite porque ele inicia o processo de convite.

    O link de convite terá o seguinte formato:

    `https://invitations.microsoft.com/redeem/...`

1. Envie o link do convite para o usuário convidado para concluir o processo de convite.

    Para obter mais informações sobre o processo de convite, consulte [o resgate do convite de colaboração B2B do Azure Active Directory](../active-directory/b2b/redemption-experience.md).

## <a name="remove-a-guest-user-from-your-directory"></a>Remova um usuário convidado do seu diretório

Antes de remover um usuário convidado de um diretório, você deve primeiro remover quaisquer atribuições de função para esse usuário convidado. Siga estas etapas para remover um usuário convidado de um diretório.

1. Controle **de acesso aberto (IAM)** em um escopo, como grupo de gerenciamento, assinatura, grupo de recursos ou recurso, onde o usuário convidado tem uma atribuição de função.

1. Clique na guia **Atribuições de função** para exibir todas as atribuições da função.

1. Na lista de atribuições de função, adicione uma marca de seleção ao lado do usuário convidado com a atribuição de função que deseja remover.

   ![Remover atribuição de função](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. Clique em **Remover**.

   ![Remover mensagem de atribuição de função](./media/role-assignments-external-users/remove-role-assignment.png)

1. Na mensagem para remover a atribuição de função exibida, clique em **Sim**.

1. Na barra de navegação à esquerda, clique em **Usuários ativos do Azure** > **.**

1. Clique no usuário convidado que deseja remover.

1. Clique em **Excluir**.

   ![Excluir usuário convidado](./media/role-assignments-external-users/delete-guest-user.png)

1. Na mensagem de exclusão que aparece, clique **em Sim**.

## <a name="troubleshoot"></a>Solução de problemas

### <a name="guest-user-cannot-browse-the-directory"></a>O usuário convidado não pode navegar pelo diretório

Usuários convidados têm permissões de diretório restritas. Por exemplo, os usuários convidados não podem navegar pelo diretório e não podem procurar grupos ou aplicativos. Para obter mais informações, consulte [Quais são as permissões de usuário padrão no Azure Active Directory?](../active-directory/fundamentals/users-default-permissions.md).

![O usuário convidado não pode navegar usuários em um diretório](./media/role-assignments-external-users/directory-no-users.png)

Se um usuário convidado precisar de privilégios adicionais no diretório, você pode atribuir uma função de diretório ao usuário convidado. Se você realmente quiser que um usuário convidado tenha acesso completo à leitura ao seu diretório, você pode adicionar o usuário convidado à função [Leitores](../active-directory/users-groups-roles/directory-assign-admin-roles.md) do Diretório no Azure AD. Para obter mais informações, consulte [as permissões de subvenção aos usuários de organizações parceiras no seu inquilino do Azure Active Directory](../active-directory/b2b/add-guest-to-role.md).

![Atribuir papel leitores de diretório](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>O usuário convidado não pode navegar usuários, grupos ou diretores de serviço para atribuir funções

Usuários convidados têm permissões de diretório restritas. Mesmo que um usuário convidado seja um [Proprietário](built-in-roles.md#owner) em um escopo, se ele tentar adicionar uma atribuição de função para conceder acesso a outra pessoa, ele não poderá navegar na lista de usuários, grupos ou diretores de serviço.

![O usuário convidado não pode navegar por princípios de segurança para atribuir funções](./media/role-assignments-external-users/directory-no-browse.png)

Se o usuário convidado souber o nome exato de login de alguém no diretório, ele poderá conceder acesso. Se você realmente quiser que um usuário convidado tenha acesso completo à leitura ao seu diretório, você pode adicionar o usuário convidado à função [Leitores](../active-directory/users-groups-roles/directory-assign-admin-roles.md) do Diretório no Azure AD. Para obter mais informações, consulte [as permissões de subvenção aos usuários de organizações parceiras no seu inquilino do Azure Active Directory](../active-directory/b2b/add-guest-to-role.md).

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>O usuário convidado não pode registrar aplicativos ou criar diretores de serviços

Usuários convidados têm permissões de diretório restritas. Se um usuário convidado precisar ser capaz de registrar aplicativos ou criar diretores de serviço, você pode adicionar o usuário convidado à função [desenvolvedor de aplicativos](../active-directory/users-groups-roles/directory-assign-admin-roles.md) no Azure AD. Para obter mais informações, consulte [as permissões de subvenção aos usuários de organizações parceiras no seu inquilino do Azure Active Directory](../active-directory/b2b/add-guest-to-role.md).

![O usuário convidado não pode registrar aplicativos](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>O usuário convidado não vê o novo diretório

Se um usuário convidado tiver acesso a um diretório, mas não ver o novo diretório listado no portal Azure quando tentar trocar em seu painel **de assinatura Directory + ,** certifique-se de que o usuário convidado tenha concluído o processo de convite. Para obter mais informações sobre o processo de convite, consulte [o resgate do convite de colaboração B2B do Azure Active Directory](../active-directory/b2b/redemption-experience.md).

### <a name="guest-user-does-not-see-resources"></a>O usuário convidado não vê recursos

Se um usuário convidado tiver acesso a um diretório, mas não ver os recursos a que foi concedido acesso no portal do Azure, certifique-se de que o usuário convidado tenha selecionado o diretório correto. Um usuário convidado pode ter acesso a vários diretórios. Para alternar diretórios, no canto superior esquerdo, clique em **Diretório + assinatura**e clique no diretório apropriado.

![Diretórios + Painel de Assinaturas no portal Azure](./media/role-assignments-external-users/directory-subscription.png)

## <a name="next-steps"></a>Próximas etapas

- [Adicionar usuários de colaboração B2B do Azure Active Directory no Portal do Azure](../active-directory/b2b/add-users-administrator.md)
- [Propriedades de um usuário de colaboração B2B do Azure Active Directory](../active-directory/b2b/user-properties.md)
- [Os elementos do email de convite para colaboração B2B – Azure Active Directory](../active-directory/b2b/invitation-email-elements.md)
- [Adicione um usuário convidado como co-administrador](classic-administrators.md#add-a-guest-user-as-a-co-administrator)