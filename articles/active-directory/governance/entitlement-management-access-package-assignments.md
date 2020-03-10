---
title: Exibir, adicionar e remover atribuições para um pacote de acesso no gerenciamento de direitos do Azure AD-Azure Active Directory
description: Saiba como exibir, adicionar e remover atribuições de um pacote de acesso no gerenciamento de direitos Azure Active Directory.
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5a2107974cd63c0d02aaeb555430453c39990bd
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2020
ms.locfileid: "78376543"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Exibir, adicionar e remover atribuições para um pacote de acesso no gerenciamento de direitos do Azure AD

No gerenciamento de direitos do Azure AD, você pode ver quem foi atribuído a pacotes de acesso, sua política e status. Se um pacote do Access tiver uma política apropriada, você também poderá atribuir diretamente o usuário a um pacote do Access. Este artigo descreve como exibir, adicionar e remover atribuições de pacotes do Access.

## <a name="view-who-has-an-assignment"></a>Exibir quem tem uma atribuição

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. Clique em **atribuições** para ver uma lista de atribuições ativas.

    ![Lista de atribuições para um pacote de acesso](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Clique em uma atribuição específica para ver detalhes adicionais.

1. Para ver uma lista de atribuições que não tenham todas as funções de recurso provisionadas corretamente, clique no status do filtro e selecione **entrega**.

    Você pode ver detalhes adicionais sobre erros de entrega, localizando a solicitação correspondente do usuário na página **solicitações** .

1. Para ver as atribuições expiradas, clique no status do filtro e selecione **expirado**.

1. Para baixar um arquivo CSV da lista filtrada, clique em **baixar**.

### <a name="viewing-assignments-programmatically"></a>Exibindo atribuições programaticamente

Você também pode recuperar atribuições em um pacote do Access usando Microsoft Graph.  Um usuário em uma função apropriada com um aplicativo que tem a permissão `EntitlementManagement.ReadWrite.All` delegada pode chamar a API para [listar accessPackageAssignments](https://docs.microsoft.com/graph/api/accesspackageassignment-list?view=graph-rest-beta).

## <a name="directly-assign-a-user"></a>Atribuir um usuário diretamente

Em alguns casos, talvez você queira atribuir diretamente usuários específicos a um pacote do Access para que os usuários não precisem passar pelo processo de solicitar o pacote de acesso. Para atribuir usuários diretamente, o pacote de acesso deve ter uma política que permita atribuições diretas de administrador.

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. No menu à esquerda, clique em **atribuições**.

1. Clique em **nova atribuição** para abrir Adicionar usuário ao pacote do Access.

    ![Atribuições-Adicionar usuário ao pacote de acesso](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Clique em **Adicionar usuários** para selecionar os usuários aos quais você deseja atribuir este pacote de acesso.

1. Na lista **selecionar política** , selecione uma política que o ciclo de vida e as solicitações futuras dos usuários serão governadas e controladas pelo. Se desejar que os usuários selecionados tenham configurações de política diferentes, você poderá clicar em **criar nova política** para adicionar uma nova política.

1. Defina a data e a hora em que você deseja que a atribuição de usuários selecionados inicie e termine. Se uma data de término não for fornecida, as configurações do ciclo de vida da política serão usadas.

1. Opcionalmente, forneça uma justificativa para sua atribuição direta para manutenção de registros.

1. Clique em **Adicionar** para atribuir diretamente os usuários selecionados ao pacote de acesso.

    Depois de alguns instantes, clique em **Atualizar** para ver os usuários na lista atribuições.

### <a name="directly-assigning-users-programmatically"></a>Atribuição direta de usuários programaticamente

Você também pode atribuir diretamente um usuário a um pacote do Access usando Microsoft Graph.  Um usuário em uma função apropriada com um aplicativo que tem a permissão `EntitlementManagement.ReadWrite.All` delegada pode chamar a API para [criar um accessPackageAssignmentRequest](https://docs.microsoft.com/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta).

## <a name="remove-an-assignment"></a>Remover uma atribuição

**Função de pré-requisito:** Administrador global, administrador de usuário, proprietário do catálogo ou Gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **pacotes de acesso** e abra o pacote de acesso.

1. No menu à esquerda, clique em **atribuições**.
 
1. Clique na caixa de seleção ao lado do usuário cuja atribuição você deseja remover do pacote do Access. 

1. Clique no botão **remover** próximo à parte superior do painel esquerdo. 
 
    ![Atribuições – remover usuário do pacote de acesso](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Uma notificação será exibida informando que a atribuição foi removida. 

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

- [Solicitações de alteração e configurações de um pacote de acesso](entitlement-management-access-package-request-policy.md)
- [Exibir relatórios e logs](entitlement-management-reports.md)
