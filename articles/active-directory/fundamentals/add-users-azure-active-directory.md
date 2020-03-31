---
title: Adicionar ou excluir usuários – Azure Active Directory | Microsoft Docs
description: Instruções sobre como adicionar novos usuários ou excluir usuários existentes usando o Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 11/12/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3d72616422934501e042375edfb10a25aa27c527
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262107"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Adicionar ou excluir usuários usando o Azure Active Directory

Adicione novos usuários ou exclua os usuários existentes da organização Azure Active Directory (Azure AD). Para adicionar ou excluir usuários, você deve ser um administrador de usuário ou administrador global.

## <a name="add-a-new-user"></a>Adicione um novo usuário

Você pode criar um novo usuário usando o portal do Azure Active Directory.

Para adicionar um novo usuário, siga estas etapas:

1. Faça login no [portal Azure](https://portal.azure.com/) como administrador de usuário da organização.

1. Procure e selecione *o Diretório Ativo do Azure em* qualquer página.

1. Selecione **Usuários**e selecione **Novo usuário**.

    ![Adicionar um usuário através de Usuários - Todos os usuários no Azure AD](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. Na página **do Usuário,** digite informações para este usuário:

   - **Nome**. Obrigatórios. O primeiro e último nome do novo usuário. Por exemplo, *Mary Parker.*

   - **Nome de usuário**. Obrigatórios. O nome de usuário do novo usuário. Por exemplo, `mary@contoso.com`.

     A parte de domínio do nome de usuário deve usar o nome de domínio padrão inicial, * \<seu nome de domínio>.onmicrosoft.com*ou um nome de domínio personalizado, como *contoso.com*. Para obter mais informações sobre como criar um nome de domínio personalizado, consulte [Adicionar seu nome de domínio personalizado usando o portal Azure Active Directory](add-custom-domain.md).

   - **Grupos**. Opcionalmente, você pode adicionar o usuário a um ou mais grupos existentes. Você também pode adicionar o usuário aos grupos posteriormente. Para obter mais informações sobre como adicionar usuários a grupos, consulte [Criar um grupo básico e adicionar membros usando o Azure Active Directory](active-directory-groups-create-azure-portal.md).

   - **Função de diretório**: Se você precisar de permissões administrativas azure AD para o usuário, você pode adicioná-las a uma função Azure AD. Você pode atribuir ao usuário um administrador Global ou uma ou mais das funções de administrador limitado no Azure AD. Para mais informações sobre como atribuir funções, consulte [Como atribuir funções aos usuários](active-directory-users-assign-role-azure-portal.md).

   - **Informações do trabalho**: Você pode adicionar mais informações sobre o usuário aqui, ou fazê-lo mais tarde. Para obter mais informações sobre como adicionar informações do usuário, consulte [Como adicionar ou alterar as informações do perfil do usuário](active-directory-users-profile-azure-portal.md).

1. Copie a senha gerada automaticamente fornecida na caixa **Senha.** Você precisará dar essa senha ao usuário para fazer login pela primeira vez.

1. Selecione **Criar**.

O usuário é criado e adicionado à sua organização Azure AD.

## <a name="add-a-new-guest-user"></a>Adicione um novo usuário convidado

Você também pode convidar o novo usuário convidado a colaborar com sua organização selecionando **o usuário Convidar** da página do usuário **Novo.** Se as configurações de colaboração externa da sua organização estiverem configuradas de forma que você possa convidar convidados, o usuário receberá por e-mail um convite que deve aceitar para começar a colaborar. Para obter mais informações sobre como convidar usuários de colaboração B2B, consulte [Convidar usuários B2B para o Azure Active Directory](../b2b/add-users-administrator.md)

## <a name="add-a-consumer-user"></a>Adicionar um usuário de consumo

Pode haver cenários em que você deseja criar manualmente contas de consumidores em seu diretório ativo Do Zure B2C (Azure B2C). Para obter mais informações sobre a criação de contas de consumidores, consulte [Criar e excluir usuários de consumidores no Azure AD B2C](../../active-directory-b2c/manage-users-portal.md).

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Adicione um novo usuário em um ambiente híbrido

Se você tiver um ambiente com o Azure Active Directory (nuvem) e o Windows Server Active Directory (local), poderá adicionar novos usuários sincronizando os dados da conta de usuário existente. Para obter mais informações sobre ambientes e usuários híbridos, consulte [Integre seus diretórios locais ao Active Directory do Azure](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Excluir um usuário

Você pode excluir um usuário existente usando o portal do Azure Active Directory.

Para excluir um usuário, siga estas etapas:

1. Faça login no [portal Do Zure](https://portal.azure.com/) usando uma conta de administrador de usuário para a organização.

1. Procure e selecione *o Diretório Ativo do Azure em* qualquer página.

1. Procure e selecione o usuário que deseja excluir do seu inquilino Azure AD. Por exemplo, _Mary Parker._

1. Selecione **excluir usuário**.

    ![Usuários - página Todos os usuários com Excluir usuário destacado](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

O usuário é excluído e não aparece mais na página **Usuários - Todos os usuários**. O usuário pode ser visto na página **Usuários excluídos** pelos próximos 30 dias e pode ser restaurado durante esse período. Para obter mais informações sobre como restaurar um usuário, consulte [Restaurar ou remover um usuário recentemente excluído usando o Azure Active Directory](active-directory-users-restore.md).

Quando um usuário é excluído, quaisquer licenças consumidas pelo usuário são disponibilizadas para outros usuários.

>[!Note]
>Você deve usar o Diretório Ativo do Windows Server para atualizar a identidade, informações de contato ou informações de trabalho para usuários cuja fonte de autoridade seja o Diretório Ativo do Windows Server. Depois de concluir sua atualização, você deve aguardar a conclusão do próximo ciclo de sincronização antes de ver as alterações.

## <a name="next-steps"></a>Próximas etapas

Depois de adicionar seus usuários, você pode fazer os seguintes processos básicos:

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)

- [Atribuir funções aos usuários](active-directory-users-assign-role-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- [Trabalhar com usuários e grupos dinâmicos](../users-groups-roles/groups-create-rule.md)

Ou você pode fazer outras tarefas de gerenciamento de usuários, como [adicionar usuários convidados de outro diretório](../b2b/what-is-b2b.md) ou restaurar um usuário [excluído](active-directory-users-restore.md). Para obter mais informações sobre outras ações disponíveis, consulte [documentação de gerenciamento de usuário do Azure Active Directory](../users-groups-roles/index.yml).
