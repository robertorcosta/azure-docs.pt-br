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
ms.date: 04/01/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: e98b09d8b07c625613e3be149e64ac8f06adc089
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/08/2019
ms.locfileid: "73805557"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Adicionar ou excluir usuários usando o Azure Active Directory
Adicione novos usuários ou exclua usuários existentes da organização do Azure Active Directory (Azure AD). Para adicionar ou excluir usuários, você deve ser um administrador de usuário ou um administrador global. 

## <a name="add-a-new-user"></a>Adicione um novo usuário
Você pode criar um novo usuário usando o portal do Azure Active Directory.

### <a name="to-add-a-new-user"></a>Para adicionar um novo usuário
1. Entre no [portal do Azure](https://portal.azure.com/) como um administrador de usuário para a organização.

2. Selecione **Active Directory do Azure**, selecione **Usuários** e, em seguida, selecione **Novo usuário**.

    ![Usuários - página Todos os usuários com o novo usuário destacado](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. Na página **Usuário**, preencha as informações necessárias.

    ![Adicionar novo usuário, página do usuário com informações do usuário](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **Nome (obrigatório).** O primeiro e último nome do novo usuário. Por exemplo, Mary Parker.

   - **Nome de usuário (obrigatório).** O nome de usuário do novo usuário. Por exemplo: mary@contoso.com.
    
       A parte do domínio do nome de usuário deve usar o nome de domínio padrão inicial, <_yourdomainname_>. Onmicrosoft.com ou um nome de domínio personalizado, como contoso.com. Para obter mais informações sobre como criar um nome de domínio personalizado, consulte [Como adicionar um nome de domínio personalizado ao Active Directory do Azure](add-custom-domain.md).

   - **Perfil.** Opcionalmente, você pode adicionar mais informações sobre o usuário. Você também pode adicionar informações do usuário posteriormente. Para obter mais informações sobre como adicionar informações do usuário, consulte [Como adicionar ou alterar as informações do perfil do usuário](active-directory-users-profile-azure-portal.md).

   - **Grupos.** Opcionalmente, você pode adicionar o usuário a um ou mais grupos existentes. Você também pode adicionar o usuário aos grupos posteriormente. Para obter mais informações sobre como adicionar usuários a grupos, consulte [Como criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md).

   - **Função de diretório.** Opcionalmente, você pode adicionar o usuário a uma função de administrador do Azure AD. Você pode atribuir o usuário para ser um administrador global ou uma ou mais das funções de administrador limitadas no Azure AD. Para mais informações sobre como atribuir funções, consulte [Como atribuir funções aos usuários](active-directory-users-assign-role-azure-portal.md).

4. Copie a senha gerada automaticamente fornecida na caixa **Senha**. Você precisará fornecer essa senha ao usuário para o processo de logon inicial.

5. Selecione **Criar**.

    O usuário é criado e adicionado ao seu locatário do Azure AD.

## <a name="add-a-new-user-within-a-hybrid-environment"></a>Adicione um novo usuário em um ambiente híbrido
Se você tiver um ambiente com o Azure Active Directory (nuvem) e o Windows Server Active Directory (local), poderá adicionar novos usuários sincronizando os dados da conta de usuário existente. Para obter mais informações sobre ambientes e usuários híbridos, consulte [Integre seus diretórios locais ao Active Directory do Azure](../hybrid/whatis-hybrid-identity.md).

## <a name="delete-a-user"></a>Excluir um usuário
Você pode excluir um usuário existente usando o portal do Azure Active Directory.

### <a name="to-delete-a-user"></a>Para excluir um usuário
1. Entre no [portal do Azure](https://portal.azure.com/) usando uma conta de administrador de usuário para a organização.

2. Selecione **Active Directory do Azure**, selecione **Usuários** e pesquise e selecione o usuário que você deseja excluir do locatário do Azure AD. Por exemplo, _Mary Parker_.

3. Selecione **excluir usuário**.

    ![Usuários - página Todos os usuários com Excluir usuário destacado](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    O usuário é excluído e não aparece mais na página **Usuários - Todos os usuários**. O usuário pode ser visto na página **Usuários excluídos** pelos próximos 30 dias e pode ser restaurado durante esse período. Para obter mais informações sobre como restaurar um usuário, consulte [Como restaurar ou remover permanentemente um usuário excluído recentemente](active-directory-users-restore.md). Quando um usuário é excluído, todas as licenças consumidas pelo usuário são disponibilizadas para que outros usuários sejam consumidos.

    >[!Note]
    >Você deve usar o Windows Server Active Directory para atualizar a identidade, as informações de contato ou as informações do trabalho para os usuários cuja fonte de autoridade é o Windows Server Active Directory. Depois de concluir sua atualização, você deve aguardar a conclusão do próximo ciclo de sincronização antes de ver as alterações.

## <a name="next-steps"></a>Próximas etapas

Depois de adicionar seus usuários, você pode executar os seguintes processos básicos:

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)

- [Atribuir funções a usuários](active-directory-users-assign-role-azure-portal.md)

- [Criar um grupo básico e adicionar membros](active-directory-groups-create-azure-portal.md)

- [Trabalhar com usuários e grupos dinâmicos](../users-groups-roles/groups-create-rule.md)

Ou você pode executar outras tarefas de gerenciamento de usuário, como [adicionando usuários convidados de outro diretório](../b2b/what-is-b2b.md) ou [restaurar um usuário excluído](active-directory-users-restore.md). Para obter mais informações sobre outras ações disponíveis, consulte [Documentação de gerenciamento de usuários do Active Directory do Azure](../users-groups-roles/index.yml).
