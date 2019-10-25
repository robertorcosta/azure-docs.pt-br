---
title: Atribuir funções do Azure AD no Privileged Identity Management-Azure Active Directory | Microsoft Docs
description: Saiba como atribuir funções do Azure AD no Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 10/22/2019
ms.author: curtand
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ad03e69df4ca03d58bba98b912ade354f90042e
ms.sourcegitcommit: ec2b75b1fc667c4e893686dbd8e119e7c757333a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/23/2019
ms.locfileid: "72809207"
---
# <a name="assign-azure-ad-roles-in-privileged-identity-management"></a>Atribuir funções do Azure AD no Privileged Identity Management

Com o Azure Active Directory (AD do Azure), um administrador global pode fazer atribuições de função de administrador do Azure AD **permanentes** . Essas atribuições de função podem ser criadas usando o [portal do Azure](../users-groups-roles/directory-assign-admin-roles.md) ou usando [comandos do PowerShell](/powershell/module/azuread#directory_roles).

O serviço PIM (Azure AD Privileged Identity Management) também permite que administradores de função com privilégios façam atribuições de função de administrador permanentes. Além disso, os administradores de função com privilégios podem tornar os usuários **qualificados** para as funções de administrador do Azure AD. Um administrador qualificado pode ativar a função quando necessário e suas permissões expirarão assim que forem feitas.

## <a name="make-a-user-eligible-for-a-role"></a>Qualificar um usuário para uma função

Siga estas etapas para tornar um usuário qualificado para uma função de administrador do Azure AD.

1. Entre no [portal do Azure](https://portal.azure.com/) com um usuário que seja membro da função [Administrador de Funções com Privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator).

    Para obter informações sobre como conceder a outro administrador acesso para gerenciar Privileged Identity Management, consulte [conceder acesso a outros administradores para gerenciar Privileged Identity Management](pim-how-to-give-access-to-pim.md).

1. Abra o **Azure AD Privileged Identity Management**.

    Se você ainda não tiver iniciado Privileged Identity Management no portal do Azure, vá para [começar a usar Privileged Identity Management](pim-getting-started.md).

1. Selecione **funções do Azure ad**.

1. Selecione **funções** ou **Membros**.

    ![Opções do menu funções do Azure AD com funções e membros realçadas](./media/pim-how-to-add-role-to-user/pim-directory-roles.png)

1. Selecione **Adicionar membro** para abrir adicionar membros gerenciados.

1. Selecione **selecionar uma função**, selecione uma função que você deseja gerenciar e selecione **selecionar**.

    ![Selecione um painel de função listando funções do Azure AD](./media/pim-how-to-add-role-to-user/pim-select-a-role.png)

1. Selecione **selecionar Membros**, selecione os usuários que você deseja atribuir à função e, em seguida, selecione **selecionar**.

    ![Selecione o painel Membros onde você pode selecionar um usuário](./media/pim-how-to-add-role-to-user/pim-select-members.png)

1. Em adicionar membros gerenciados, selecione **OK** para adicionar o usuário à função.

1. Na lista de funções, selecione a função que você acabou de atribuir para ver a lista de membros.

     Quando a função for atribuída, o usuário que você selecionou aparecerá na lista de membros como **Qualificado** para a função.

    ![Os membros de uma função são listados junto com seu estado de ativação](./media/pim-how-to-add-role-to-user/pim-directory-role-eligible.png)

1. Agora que o usuário está qualificado para a função, avise-o de que ele pode ativá-lo de acordo com as instruções em [Ativar minhas funções do Azure AD no Privileged Identity Management](pim-how-to-activate-role.md).

    Os administradores qualificados são solicitados a registrar na MFA (Autenticação Multifator do Microsoft Azure) durante a ativação. Se um usuário não puder se registrar na MFA ou estiver usando uma conta da Microsoft (geralmente, @outlook.com), será preciso torná-lo permanente em todas as funções.

## <a name="make-a-role-assignment-permanent"></a>Tornar uma atribuição de função permanente

Por padrão, novos usuários só estão qualificados para uma função de administrador do Azure AD. Siga estas etapas se quiser tornar uma atribuição de função permanente.

1. Abra o **Azure AD Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **Membros**.

    ![Funções do Azure AD – lista de membros mostrando a função e o estado de ativação](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecione uma função **qualificada** que você deseja tornar permanente.

1. Selecione **mais** e, em seguida, selecione **tornar Perm**.

    ![Painel que lista um usuário qualificado para uma função com mais opções de menu abertas](./media/pim-how-to-add-role-to-user/pim-make-perm.png)

    A função agora está listada como **permanente**.

    ![Lista de membros mostrando a função e o estado de ativação que agora é permanente](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members-permanent.png)

## <a name="remove-a-user-from-a-role"></a>Remover um usuário de uma função

É possível remover usuários de atribuições de função, mas certifique-se de que sempre haja pelo menos um usuário que seja um Administrador Global permanente. Se não tiver certeza de quais os usuários ainda precisam das suas atribuições de função, você poderá [iniciar uma revisão de acesso para a função](pim-how-to-start-security-review.md).

Siga estas etapas para remover um usuário específico de uma função de administrador do Azure AD.

1. Abra o **Azure AD Privileged Identity Management**.

1. Selecione **funções do Azure ad**.

1. Selecione **Membros**.

    ![Funções do Azure AD – lista de membros mostrando a função e a estatística de ativação](./media/pim-how-to-add-role-to-user/pim-directory-role-list-members.png)

1. Selecione a atribuição de função que você deseja remover.

1. Selecione **mais** e, em seguida, **remover**.

    ![Painel que lista um usuário que tem uma função permanente com mais opções de menu abertas](./media/pim-how-to-add-role-to-user/pim-remove-role.png)

1. Quando for solicitado a confirmar a ação, selecione **Sim**.

    ![Mensagem perguntando se você deseja remover o membro da função](./media/pim-how-to-add-role-to-user/pim-remove-role-confirm.png)

    A atribuição de função será removida.

## <a name="authorization-error-when-assigning-roles"></a>Erro de autorização ao atribuir funções

Cenário: como um proprietário ativo ou administrador de acesso de usuário para um recurso do Azure, você pode ver seu recurso dentro de Privileged Identity Management mas não pode executar nenhuma ação, como fazer uma atribuição qualificada ou exibir uma lista de atribuições de função do página Visão geral do recurso. Qualquer uma dessas ações resulta em um erro de autorização.

Para atribuir funções, a entidade de serviço MS-PIM deve ser atribuída à [função Administrador de acesso do usuário](../../role-based-access-control/built-in-roles.md#user-access-administrator) no controle de acesso baseado em função do Azure para acesso de recursos do Azure (em oposição às funções de administração do Azure AD). Em vez de esperar até que o MS-PIM receba a função de Administrador de Acesso do Usuário, atribua-a manualmente.

As etapas a seguir atribuem a função de administrador de acesso do usuário à entidade de serviço do MS-PIM para uma assinatura.

1. Entre no [portal do Azure](https://portal.azure.com) como um administrador global em sua organização do Azure AD.

1. Escolha **Todos os serviços** e **Assinaturas**.

1. Escolha sua assinatura.

1. Clique em **Controle de acesso (IAM)** .

1. Escolha **Atribuições de função** para ver a lista atual de atribuições de função no escopo da assinatura.

   ![Folha IAM (controle) de acesso para uma assinatura](./media/pim-how-to-add-role-to-user/ms-pim-access-control.png)

1. Verifique se a entidade de serviço **MS-PIM** tem a função **Administrador de Acesso do Usuário**.

1. Caso contrário, escolha **Adicionar atribuição de função** para abrir o painel **Adicionar atribuição de função**.

1. Na lista suspensa **Função**, selecione a função **Administrador de Acesso do Usuário**.

1. Na lista **Selecionar**, localize e selecione a entidade de serviço **MS-PIM**.

   ![Adicionar painel de atribuição de função-adicionar permissões para entidade de serviço do Microsoft PIM](./media/pim-how-to-add-role-to-user/ms-pim-add-permissions.png)

1. Escolha **Salvar** para atribuir a função.

   Após alguns momentos, verifique se a entidade de serviço do MS-PIM tem a função Administrador de Acesso do Usuário no escopo da assinatura.

   ![Folha de controle de acesso (IAM) mostrando a atribuição de função de administrador de acesso do usuário para MS-PIM](./media/pim-how-to-add-role-to-user/ms-pim-user-access-administrator.png)

## <a name="next-steps"></a>Próximos passos

- [Definir as configurações de função de administrador do Azure AD no Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Atribuir funções de recurso do Azure no Privileged Identity Management](pim-resource-roles-assign-roles.md)
