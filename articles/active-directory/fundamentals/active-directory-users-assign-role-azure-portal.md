---
title: Atribuir funções de diretório aos usuários - Azure Active Directory | Microsoft Docs
description: Instruções sobre como atribuir funções de administrador e não administrador aos usuários com Azure Active Directory.
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 09/06/2018
ms.author: ajburnle
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87b063a4d51d5d5d1e3d7949be3754ccbe74acca
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85604107"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Atribuir funções de administrador e não administrador aos usuários com Azure Active Directory
Se um usuário da sua organização precisar de permissão para gerenciar recursos do Azure AD (Azure Active Directory), você deverá atribuir ao usuário uma função apropriada no AD do Azure, com base nas ações que o usuário precisa de permissão para executar.

Para obter mais informações sobre as funções disponíveis, consulte [atribuindo funções de administrador no Azure Active Directory](../users-groups-roles/directory-assign-admin-roles.md). Para obter mais informações sobre como adicionar usuários, consulte [adicionar novos usuários ao Azure Active Directory](add-users-azure-active-directory.md).

## <a name="assign-roles"></a>Atribuir funções
Uma maneira comum de atribuir funções do Azure AD a um usuário está na **função de diretório** página para um usuário.

Você também pode atribuir funções usando o Privileged Identity Management (PIM). Para obter mais informações sobre como usar o PIM, consulte [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management).

### <a name="to-assign-a-role-to-a-user"></a>Para atribuir uma função a um usuário
1. Vá para o [portal do Azure](https://portal.azure.com/) e faça logon usando uma conta de administrador global para o diretório. 

2. Pesquise **Azure Active Directory** e selecione-o.

      ![Pesquisa do portal do Azure para o Azure Active Directory](media/active-directory-users-assign-role-azure-portal/search-azure-active-directory.png)


3. Selecione **usuários**.

4. Procure e selecione o usuário que está obtendo a atribuição de função. Por exemplo, _Alain Charon_.

      ![Página todos os usuários – selecionar o usuário](media/active-directory-users-assign-role-azure-portal/directory-role-select-user.png)

5. Na página **Alain Charon-Profile** , selecione **funções atribuídas**.

    O **Alain Charon - função de diretório** página será exibida.

6. Selecione **Adicionar atribuição**, selecione a função a ser atribuída a Alain (por exemplo, _administrador de aplicativos_) e escolha **selecionar**.

    ![Página funções atribuídas-mostrando a função selecionada](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    A função de administrador do Aplicativo é atribuída a Alain Charon e aparece na página **Alain Charon - Função de Diretório**.

## <a name="remove-a-role-assignment"></a>Excluir uma atribuição de função
Se você precisar remover a atribuição de função de um usuário, você também pode fazer isso na **Alain Charon - função de diretório** página.

### <a name="to-remove-a-role-assignment-from-a-user"></a>Para remover uma atribuição de função de um usuário

1. Selecione **Azure Active Directory**, selecione **usuários**e, em seguida, pesquise e selecione o usuário Obtendo a atribuição de função removida. Por exemplo, _Alain Charon_.

2. Selecione **funções atribuídas**, selecione **administrador do aplicativo**e, em seguida, selecione **remover atribuição**.

    ![Página funções atribuídas, mostrando a função selecionada e a opção remover](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    A função de administrador do aplicativo é removida do Alain Charon e ele não aparecerá mais na **Alain Charon - função de diretório** página.

## <a name="next-steps"></a>Próximas etapas
- [Adicionar ou excluir usuários](add-users-azure-active-directory.md)

- [Adicionar ou alterar informações de perfil](active-directory-users-profile-azure-portal.md)

- [Adicionar usuários convidados de outro diretório](../b2b/what-is-b2b.md)

Ou você pode executar outras tarefas de gerenciamento de usuários como atribuir delegados, usar políticas e compartilhar contas de usuários. Para obter mais informações sobre outras ações disponíveis, consulte [documentação de gerenciamento de usuário do Azure Active Directory](../users-groups-roles/index.yml).


