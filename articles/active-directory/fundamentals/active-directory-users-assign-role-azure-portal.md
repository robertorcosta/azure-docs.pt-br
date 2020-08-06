---
title: Atribuir funções do Azure AD a usuários-Azure Active Directory | Microsoft Docs
description: Instruções sobre como atribuir funções de administrador e não administrador aos usuários com Azure Active Directory.
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 06/15/2020
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8a468b5f43e8b024dd1da6e382049f8cce6de295
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/05/2020
ms.locfileid: "87797358"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Atribuir funções de administrador e não administrador aos usuários com Azure Active Directory

No Azure Active Directory (AD do Azure), se um dos seus usuários precisar de permissão para gerenciar os recursos do Azure AD, você deverá atribuí-los a uma função que forneça as permissões necessárias. Para obter informações sobre quais funções gerenciam recursos do Azure e quais funções gerenciam recursos do Azure AD, consulte [funções de administrador de assinatura clássica, funções do Azure e funções do Azure ad](../../role-based-access-control/rbac-and-directory-admin-roles.md).

Para obter mais informações sobre as funções disponíveis do Azure AD, consulte [atribuindo funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Para adicionar usuários, consulte [Adicionar novos usuários ao Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Atribuir funções

Uma maneira comum de atribuir funções do Azure AD a um usuário está na página **funções atribuídas** para um usuário. Você também pode ter a elegibilidade para ser elevado just-in-time em uma função usando Privileged Identity Management (PIM). Para obter mais informações sobre como usar o PIM, consulte [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

> [!Note]
> Se você tiver um plano de licença Azure AD Premium P2 e já usar o PIM, todas as tarefas de gerenciamento de função serão executadas na [experiência de Privileged Identity Management](../users-groups-roles/directory-manage-roles-portal.md).
>
> ![Funções do Azure AD gerenciadas no PIM para usuários que já usam o PIM e que têm uma licença Premium P2](./media/active-directory-users-assign-role-azure-portal/pim-manages-roles-for-p2.png)

## <a name="assign-a-role-to-a-user"></a>Atribuir uma função a um usuário

1. Vá para a [portal do Azure](https://portal.azure.com/) e entre usando uma conta de administrador global para o diretório.

2. Pesquise **Azure Active Directory** e selecione-o.

      ![Pesquisa do portal do Azure para o Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)

3. Selecione **Usuários**.

4. Procure e selecione o usuário que está obtendo a atribuição de função. Por exemplo, _Alain Charon_.

      ![Página todos os usuários – selecionar o usuário](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Na página **Alain Charon-Profile** , selecione **funções atribuídas**.

    A página **Alain Charon-funções administrativas** é exibida.

6. Selecione **Adicionar atribuições**, selecione a função a ser atribuída ao Alain (por exemplo, _administrador do aplicativo_) e escolha **selecionar**.

    ![Página funções atribuídas-mostrando a função selecionada](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    A função Administrador de aplicativos é atribuída a Alain Charon e aparece na página **Alain Charon-funções administrativas** .

## <a name="remove-a-role-assignment"></a>Excluir uma atribuição de função

Se você precisar remover a atribuição de função de um usuário, também poderá fazer isso na página **Alain Charon-funções administrativas** .

### <a name="to-remove-a-role-assignment-from-a-user"></a>Para remover uma atribuição de função de um usuário

1. Selecione **Azure Active Directory**, selecione **usuários**e, em seguida, pesquise e selecione o usuário Obtendo a atribuição de função removida. Por exemplo, _Alain Charon_.

2. Selecione **funções atribuídas**, selecione **administrador do aplicativo**e, em seguida, selecione **remover atribuição**.

    ![Página funções atribuídas, mostrando a função selecionada e a opção remover](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    A função Administrador de aplicativos é removida de Alain Charon e não aparece mais na página **Alain Charon-funções administrativas** .

## <a name="next-steps"></a>Próximas etapas

- [Adicionar ou excluir usuários](add-users-azure-active-directory.md)

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)

- [Adicionar usuários convidados de outro diretório](../b2b/what-is-b2b.md)

Outras tarefas de gerenciamento de usuário que você pode fazer check-out estão disponíveis em [Azure Active Directory documentação de gerenciamento de usuário](../users-groups-roles/index.yml).
