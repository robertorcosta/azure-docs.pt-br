---
title: Exibir, adicionar e remover atribuições para um pacote de acesso no gerenciamento de direitos Azure AD - Azure Active Directory
description: Saiba como visualizar, adicionar e remover atribuições de um pacote de acesso no gerenciamento de direitos do Azure Active Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262016"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Exibir, adicionar e remover atribuições para um pacote de acesso no gerenciamento de direitos Azure AD

Na gestão de direitos do Azure AD, você pode ver quem foi designado para acessar pacotes, sua política e status. Se um pacote de acesso tiver uma política apropriada, você também pode atribuir diretamente o usuário a um pacote de acesso. Este artigo descreve como visualizar, adicionar e remover atribuições para pacotes de acesso.

## <a name="view-who-has-an-assignment"></a>Veja quem tem uma tarefa

**Papel pré-requisito:** Administrador global, administrador de usuário, proprietário de catálogo ou gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **Acessar pacotes** e, em seguida, abra o pacote de acesso.

1. Clique **em Atribuições** para ver uma lista de atribuições ativas.

    ![Lista de atribuições para um pacote de acesso](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Clique em uma atribuição específica para ver detalhes adicionais.

1. Para ver uma lista de atribuições que não tinham todas as funções de recurso devidamente provisionadas, clique no status do filtro e selecione **Entregar**.

    Você pode ver detalhes adicionais sobre erros de entrega localizando a solicitação correspondente do usuário na página **Solicitações.**

1. Para ver as atribuições expiradas, clique no status do filtro e **selecione Expirado**.

1. Para baixar um arquivo CSV da lista filtrada, clique em **Baixar**.

### <a name="viewing-assignments-programmatically"></a>Visualização de atribuições programáticas

Você também pode recuperar atribuições em um pacote de acesso usando o Microsoft Graph.  Um usuário em uma função apropriada com `EntitlementManagement.ReadWrite.All` um aplicativo que tenha a permissão delegada pode chamar a API para [listar o acessoPackageAssignments](https://docs.microsoft.com/graph/api/accesspackageassignment-list?view=graph-rest-beta).

## <a name="directly-assign-a-user"></a>Atribuir diretamente um usuário

Em alguns casos, você pode querer atribuir diretamente usuários específicos a um pacote de acesso para que os usuários não precisem passar pelo processo de solicitação do pacote de acesso. Para atribuir diretamente aos usuários, o pacote de acesso deve ter uma política que permita atribuições diretas do administrador.

**Papel pré-requisito:** Administrador global, administrador de usuário, proprietário de catálogo ou gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **Acessar pacotes** e, em seguida, abra o pacote de acesso.

1. No menu à esquerda, clique **em Atribuições**.

1. Clique **em Nova atribuição** para abrir Adicionar usuário para acessar o pacote.

    ![Atribuições - Adicionar usuário ao pacote de acesso](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1. Clique **em Adicionar usuários** para selecionar os usuários aos os que você deseja atribuir este pacote de acesso.

1. Na lista **de diretiva Selecionar,** selecione uma diretiva pela quais as solicitações e o ciclo de vida futuros dos usuários serão regidos e rastreados. Se você quiser que os usuários selecionados tenham configurações de diretiva diferentes, você pode clicar em **Criar nova diretiva** para adicionar uma nova diretiva.

1. Defina a data e a hora que deseja que a atribuição dos usuários selecionados comece e termine. Se uma data final não for fornecida, as configurações do ciclo de vida da diretiva serão usadas.

1. Opcionalmente, forneça uma justificativa para sua atribuição direta para manter o registro.

1. Clique **em Adicionar** para atribuir diretamente os usuários selecionados ao pacote de acesso.

    Após alguns momentos, clique **em Atualizar** para ver os usuários na lista Demissões.

### <a name="directly-assigning-users-programmatically"></a>Atribuindo diretamente aos usuários de forma programática

Você também pode atribuir diretamente um usuário a um pacote de acesso usando o Microsoft Graph.  Um usuário em uma função apropriada com `EntitlementManagement.ReadWrite.All` um aplicativo que tenha a permissão delegada pode chamar a API para [criar um accessPackageAssignmentRequest](https://docs.microsoft.com/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta).

## <a name="remove-an-assignment"></a>Remova uma atribuição

**Papel pré-requisito:** Administrador global, administrador de usuário, proprietário de catálogo ou gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **Acessar pacotes** e, em seguida, abra o pacote de acesso.

1. No menu à esquerda, clique **em Atribuições**.
 
1. Clique na caixa de seleção ao lado do usuário cuja atribuição você deseja remover do pacote de acesso. 

1. Clique no botão **Remover** perto da parte superior do painel esquerdo. 
 
    ![Atribuições - Remover o usuário do pacote de acesso](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Uma notificação aparecerá informando que a atribuição foi removida. 

## <a name="next-steps"></a>Próximas etapas

- [Alterar solicitação e configurações para um pacote de acesso](entitlement-management-access-package-request-policy.md)
- [Exibir relatórios e logs](entitlement-management-reports.md)
