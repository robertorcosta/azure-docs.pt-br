---
title: Atribuir funções de recursos do Azure aos hóspedes no PIM - Azure AD | Microsoft Docs
description: Saiba como convidar usuários convidados externos e atribuir funções de recursos do Azure no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2efcf77d65fa2f9e203ed805cd7d78b9802ee3aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74021931"
---
# <a name="invite-guest-users-and-assign-azure-resource-roles-in-privileged-identity-management"></a>Convide os usuários convidados e atribua funções de recurso do Azure no Gerenciamento de Identidade Privilegiada

Os usuários convidados do Azure Active Directory (Azure AD) fazem parte dos recursos de colaboração business-to-business (B2B) dentro do Azure AD para que você possa gerenciar usuários e fornecedores convidados externos como convidados no Azure AD. Quando você combina a colaboração B2B com o Pim (AD Privileged Identity Management, gerenciamento de identidade privilegiado) do Azure, você pode estender seus requisitos de conformidade e governança aos hóspedes. Por exemplo, você pode usar esses recursos de gerenciamento de identidade privilegiado para tarefas de identidade do Azure com convidados:

- Atribuir acesso a recursos específicos do Azure
- Habilitar acesso Just-In-Time
- Especifique a data de duração e de término da atribuição
- Exigir autenticação multifatorial em atribuição ativa ou ativação
- Realizar as revisões de acesso
- Usar alertas e logs de auditoria

Este artigo descreve como convidar um convidado para sua organização e gerenciar seu acesso aos recursos do Azure usando o Gerenciamento de Identidade Privilegiada.

## <a name="when-would-you-invite-guests"></a>Quando convidaria convidados?

Aqui estão alguns exemplos de quando você pode convidar convidados para sua organização:

- Permitir que um fornecedor externo autônomo tenha apenas uma conta de email para acessar os recursos do Azure para um projeto.
- Permitir que um parceiro externo em uma grande organização que usa Serviços de Federação do Active Directory (AD FS) local para acessar o aplicativo de despesas.
- Permitir que os engenheiros de suporte que não são da sua organização (como o suporte da Microsoft) acessem temporariamente o recurso do Azure para solucionar problemas.

## <a name="how-does-collaboration-using-b2b-guests-work"></a>Como funciona a colaboração usando convidados B2B?

Quando você usa a colaboração B2B, você pode convidar um usuário externo para sua organização como convidado. O hóspede pode ser gerenciado como usuário em sua organização, mas um hóspede tem que ser autenticado em sua organização doméstica e não em sua organização Azure AD. Isso significa que se o hóspede não tiver mais acesso à sua organização doméstica, ele também perderá o acesso à sua organização. Por exemplo, se o hóspede deixar sua organização, ele automaticamente perde o acesso a todos os recursos que você compartilhou com eles no Azure AD sem que você tenha que fazer nada. Para obter mais informações sobre a colaboração B2B, consulte [O que é acesso do usuário convidado no Azure Active Directory B2B?](../b2b/what-is-b2b.md).

![Diagrama mostrando como um usuário convidado é autenticado em seu diretório doméstico](./media/pim-resource-roles-external-users/b2b-external-user.png)

## <a name="check-guest-collaboration-settings"></a>Verifique as configurações de colaboração dos hóspedes

Para ter certeza de que você pode convidar os convidados para sua organização, você deve verificar as configurações de colaboração dos convidados.

1. Faça login no [portal Azure](https://portal.azure.com/).

1. Selecione as > **configurações do usuário**do **diretório ativo do Azure**.

1. Selecione **Gerenciar configurações de colaboração externa**.

    ![Página de configurações de colaboração externa mostrando as configurações de restrição de permissão, convite e colaboração](./media/pim-resource-roles-external-users/external-collaboration-settings.png)

1. Certifique-se de que os **admins e os usuários na função de convidado convidado podem convidar** o switch está definido como **Sim**.

## <a name="invite-a-guest-and-assign-a-role"></a>Convide um convidado e atribua um papel

Usando o Gerenciamento de Identidade Privilegiado, você pode convidar um convidado e torná-lo elegível para uma função de recurso do Azure.

1. Faça login no [portal Azure](https://portal.azure.com/) com um usuário que seja membro da [função privilegiada administrador](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) ou administrador de [usuário.](../users-groups-roles/directory-assign-admin-roles.md#user-administrator)

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **Recursos do Azure**.

1. Use o **Filtro de recurso** para filtrar a lista de recursos gerenciados.

1. Selecione o recurso que deseja gerenciar, como um grupo de recursos, grupo de recursos, assinatura ou grupo de gerenciamento.

    Você deve definir o escopo apenas para o que o convidado precisa.

1. Em Gerenciar, selecione **Funções** para ver a lista de funções para os recursos do Azure.

    ![Lista de funções de recursos do Azure mostrando número de usuários ativos e elegíveis](./media/pim-resource-roles-external-users/resources-roles.png)

1. Selecione a função mínima que o usuário precisará.

    ![Página de função selecionada listando os membros atuais dessa função](./media/pim-resource-roles-external-users/selected-role.png)

1. Na página de função, selecione **Adicionar membro** para abrir o painel de atribuição Novo.

1. Clique em **Selecionar um membro ou grupo**.

    ![Nova atribuição - Selecione um membro ou painel de grupo listando usuários e grupos, juntamente com uma opção Convidar](./media/pim-resource-roles-external-users/select-member-group.png)

1. Para convidar um convidado, clique em **Convidar**.

    ![Convide uma página de convidados com caixas para inserir um endereço de e-mail e especificar uma mensagem pessoal](./media/pim-resource-roles-external-users/invite-guest.png)

1. Depois de selecionar um convidado, clique **em Convidar**.

    O convidado deve ser adicionado como um membro selecionado.

1. No **Selecionar um membro ou painel** de grupo, clique em **Selecionar**.

1. No painel **Desfiliação,** selecione o tipo de atribuição e a duração.

    ![Nova atribuição - Página de configurações de membros com opções para especificar o tipo de atribuição, a data de início e a data de término](./media/pim-resource-roles-external-users/membership-settings.png)

1. Para concluir a atribuição, selecione **Concluído** e, em seguida, **Adicione**.

    A atribuição de papel de convidado aparecerá em sua lista de papéis.

    ![Página de função listando o convidado como elegível](./media/pim-resource-roles-external-users/role-assignment.png)

## <a name="activate-role-as-a-guest"></a>Ativar função como convidado

Se você é um usuário externo, você deve aceitar o convite para ser um convidado na organização Azure AD e possivelmente ativar sua atribuição de função.

1. Abra o e-mail com seu convite. O email se parecerá com o seguinte.

    ![Convite por e-mail com nome do diretório, mensagem pessoal e um link Get Started](./media/pim-resource-roles-external-users/email-invite.png)

1. Selecione o link **'Iniciar'no** e-mail.

1. Depois de revisar as permissões, clique em **Aceitar**.

    ![Revisar a página de permissões em um navegador com uma lista de permissões que a organização gostaria que você revisasse](./media/pim-resource-roles-external-users/invite-accept.png)

1. Você pode ser solicitado a aceitar um termo de uso e especificar se deseja permanecer conectado. No portal Azure, se você for *elegível* para um papel, você ainda não terá acesso aos recursos.

1. Para ativar sua atribuição de função, abra o e-mail com seu link de ativação de função. O email se parecerá com o seguinte.

    ![E-mail indicando que você é elegível para uma função com um link de função Ativar](./media/pim-resource-roles-external-users/email-role-assignment.png)

1. Selecione **Ativar função** para abrir suas funções elegíveis no Gerenciamento de Identidade Privilegiada.

    ![Minha página de funções no Gerenciamento de Identidade Privilegiada listando suas funções elegíveis](./media/pim-resource-roles-external-users/my-roles-eligible.png)

1. Em Ação, selecione o link **Ativar.**

    Dependendo das configurações de função, você precisará especificar algumas informações para ativar a função.

1. Depois de especificar as configurações para a função, clique em **Ativar** para ativar a função.

    ![Ativar escopo de listagem de página e opções para especificar a hora de início, duração e razão](./media/pim-resource-roles-external-users/activate-role.png)

    A menos que o administrador seja necessário para aprovar sua solicitação, você deve ter acesso aos recursos especificados.

## <a name="view-activity-for-a-guest"></a>Ver atividade para um convidado

Você pode visualizar registros de auditoria para acompanhar o que os hóspedes estão fazendo.

1. Como administrador, abra o Gerenciamento de Identidade Privilegiada e selecione o recurso que foi compartilhado.

1. Selecione **a auditoria Resource** para visualizar a atividade desse recurso. O exemplo a seguir mostra um exemplo da atividade para um grupo de recursos.

    ![Recursos do Azure - Página de auditoria de recursos listando a hora, solicitante e ação](./media/pim-resource-roles-external-users/audit-resource.png)

1. Para exibir a atividade do convidado, selecione O*nome de convidado*dos**Usuários** > ativos do >  **Azure**.

1. Selecione **registros de auditoria** para ver os registros de auditoria da organização. Se necessário, você pode especificar os filtros.

    ![Logs de auditoria de diretório saem, alvo, iniciados por e atividade](./media/pim-resource-roles-external-users/audit-directory.png)

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções de administrador admin do Azure AD no Gerenciamento de Identidade Privilegiada](pim-how-to-add-role-to-user.md)
- [O que é acesso do usuário convidado na colaboração Azure AD B2B?](../b2b/what-is-b2b.md)
