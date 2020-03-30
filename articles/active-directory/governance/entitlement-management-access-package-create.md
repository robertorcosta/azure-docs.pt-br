---
title: Criar um novo pacote de acesso em gerenciamento de direitos - Azure AD
description: Saiba como criar um novo pacote de acesso de recursos que você deseja compartilhar no gerenciamento de direitos do Azure Active Directory.
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
ms.openlocfilehash: 7c858a17d4574e6e45283df7c1276cd303f25297
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262003"
---
# <a name="create-a-new-access-package-in-azure-ad-entitlement-management"></a>Crie um novo pacote de acesso no gerenciamento de direitos ad do Azure

Um pacote de acesso permite que você faça uma configuração única de recursos e políticas que administra automaticamente o acesso durante a vida útil do pacote de acesso. Este artigo descreve como criar um novo pacote de acesso.

## <a name="overview"></a>Visão geral

Todos os pacotes de acesso devem ser colocados em um contêiner chamado catálogo. Um catálogo define quais recursos você pode adicionar ao seu pacote de acesso. Se você não especificar um catálogo, seu pacote de acesso será colocado no catálogo geral. Atualmente, você não pode mover um pacote de acesso existente para um catálogo diferente.

Se você é um gerenciador de pacotes de acesso, não poderá adicionar recursos que possui a um catálogo. Você está restrito a usar os recursos disponíveis no catálogo. Se você precisar adicionar recursos a um catálogo, você pode perguntar ao proprietário do catálogo.

Todos os pacotes de acesso devem ter pelo menos uma política. As políticas especificam quem pode solicitar o pacote de acesso e também as configurações de aprovação e ciclo de vida. Quando você cria um novo pacote de acesso, você pode criar uma política inicial para usuários em seu diretório, para usuários que não estão em seu diretório, apenas para atribuições diretas de administrador, ou você pode optar por criar a política mais tarde.

![Criar um pacote de acesso](./media/entitlement-management-access-package-create/access-package-create.png)

Aqui estão as etapas de alto nível para criar um novo pacote de acesso.

1. Em Governança de Identidade, inicie o processo para criar um novo pacote de acesso.

1. Selecione o catálogo em que deseja criar o pacote de acesso.

1. Adicione recursos do catálogo ao seu pacote de acesso.

1. Atribuir funções de recurso para cada recurso.

1. Especifique usuários que podem solicitar acesso.

1. Especifique quaisquer configurações de aprovação.

1. Especifique as configurações do ciclo de vida.

## <a name="start-new-access-package"></a>Inicie um novo pacote de acesso

**Papel pré-requisito:** Administrador global, administrador de usuário, proprietário de catálogo ou gerenciador de pacotes de acesso

1. Faça login no [portal Azure](https://portal.azure.com).

1. Clique em **Azure Active Directory** e, em seguida, clique em **Governança de Identidade**.

1. No menu esquerdo, clique em **Pacotes de acesso**.

1. Clique em **Novo pacote de acesso**.
   
    ![Gerenciamento de direitos no portal do Azure](./media/entitlement-management-shared/access-packages-list.png)

## <a name="basics"></a>Noções básicas

Na guia **Basics,** você dá um nome ao pacote de acesso e especifica qual catálogo criar o pacote de acesso.

1. Digite um nome de exibição e descrição para o pacote de acesso. Os usuários verão essas informações quando enviarem uma solicitação para o pacote de acesso.

1. Na lista de paradas **do Catálogo,** selecione o catálogo em que deseja criar o pacote de acesso. Por exemplo, você pode ter um proprietário de catálogo que gerencia todos os recursos de marketing que podem ser solicitados. Neste caso, você pode selecionar o catálogo de marketing.

    Você só verá catálogos em que tem permissão para criar pacotes de acesso. Para criar um pacote de acesso em um catálogo existente, você deve ser um administrador global ou administrador de usuário, ou você deve ser um proprietário de catálogo ou gerenciador de pacotes de acesso nesse catálogo.

    ![Pacote de acesso - Noções básicas](./media/entitlement-management-access-package-create/basics.png)

    Se você é um administrador Global, um administrador de usuário ou criador de catálogo e gostaria de criar seu pacote de acesso em um novo catálogo que não está listado, clique **em Criar novo catálogo**. Digite o nome e a descrição do catálogo e clique em **Criar**.

    O pacote de acesso que você está criando e todos os recursos incluídos nele serão adicionados ao novo catálogo. Você também pode adicionar proprietários adicionais de catálogo mais tarde.

1. Clique em **Avançar**.

## <a name="resource-roles"></a>Funções de recurso

Na guia **'Funções de recursos',** você seleciona os recursos a serem inseridos no pacote de acesso. Os usuários que solicitarem e receberem o pacote de acesso receberão todas as funções de recurso no pacote de acesso.

1. Clique no tipo de recurso que deseja adicionar **(grupos e equipes,** **aplicativos**ou **sites sharePoint).**

1. No painel Selecionar que aparece, selecione um ou mais recursos da lista.

    ![Pacote de acesso - Funções de recursos](./media/entitlement-management-access-package-create/resource-roles.png)

    Se você estiver criando o pacote de acesso no catálogo Geral ou um novo catálogo, você poderá escolher qualquer recurso do diretório que você possui. Você deve ser pelo menos um administrador Global, um administrador de usuário ou criador do Catálogo.

    Se você estiver criando o pacote de acesso em um catálogo existente, você pode selecionar qualquer recurso que já esteja no catálogo sem possuí-lo.

    Se você é um administrador Global, um administrador de usuário ou proprietário de catálogo, você tem a opção adicional de selecionar recursos que você possui que ainda não estão no catálogo. Se você selecionar recursos que não estão no catálogo selecionado, esses recursos também serão adicionados ao catálogo para que outros administradores de catálogos construam pacotes de acesso com. Se você quiser selecionar apenas os recursos que estão atualmente no catálogo selecionado, marque a **caixa 'Ver'scheck'** na parte superior do painel Selecionar.

1. Depois de selecionar os recursos, na lista **Função,** selecione a função que deseja que os usuários sejam atribuídos ao recurso.

    ![Pacote de acesso - Seleção de função de recursos](./media/entitlement-management-access-package-create/resource-roles-role.png)

1. Clique em **Avançar**.

## <a name="requests"></a>Requests

Na guia **Solicitações,** você cria a primeira política para especificar quem pode solicitar o pacote de acesso e também as configurações de aprovação. Mais tarde, você pode criar mais políticas de solicitação para permitir que grupos adicionais de usuários solicitem o pacote de acesso com suas próprias configurações de aprovação.

![Pacote de acesso - Guia solicitações](./media/entitlement-management-access-package-create/requests.png)

Dependendo de quem você deseja ser capaz de solicitar este pacote de acesso, execute as etapas em uma das seguintes seções.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

[!INCLUDE [Entitlement management lifecycle policy](../../../includes/active-directory-entitlement-management-lifecycle-policy.md)]

## <a name="review--create"></a>Examinar + criar

Na guia **'Revisar + criar',** você pode rever suas configurações e verificar se há erros de validação.

1. Revise as configurações do pacote de acesso

    ![Pacote de acesso - Política - Ativar a configuração de diretiva](./media/entitlement-management-access-package-create/review-create.png)

1. Clique **em Criar** para criar o pacote de acesso.

    O novo pacote de acesso aparece na lista de pacotes de acesso.

## <a name="creating-an-access-package-programmatically"></a>Criando um pacote de acesso programáticamente

Você também pode criar um pacote de acesso usando o Microsoft Graph.  Um usuário em uma função apropriada com `EntitlementManagement.ReadWrite.All` um aplicativo que tenha a permissão delegada pode chamar a API para

1. [Liste o acessoPackageResources no catálogo](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresources?view=graph-rest-beta) e [crie um recurso de acessoPackageResourceRequest](https://docs.microsoft.com/graph/api/accesspackageresourcerequest-post?view=graph-rest-beta) para quaisquer recursos que ainda não estejam no catálogo.
1. [Liste o acessoPackageResourceRoles](https://docs.microsoft.com/graph/api/accesspackagecatalog-list-accesspackageresourceroles?view=graph-rest-beta) de cada acessoPackageResource em um catálogo de pacotes de acesso. Essa lista de funções será usada para selecionar uma função, ao criar posteriormente um recursoDepacoteRecursoRoleScope.
1. [Crie um pacote de acesso .](https://docs.microsoft.com/graph/api/accesspackage-post?view=graph-rest-beta)
1. [Crie um accessPackageAssignmentPolicy](https://docs.microsoft.com/graph/api/accesspackageassignmentpolicy-post?view=graph-rest-beta).
1. [Crie um accessPackageResourceRoleScope](https://docs.microsoft.com/graph/api/accesspackage-post-accesspackageresourcerolescopes?view=graph-rest-beta) para cada função de recurso necessária no pacote de acesso.

## <a name="next-steps"></a>Próximas etapas

- [Link de compartilhamento para solicitar um pacote de acesso](entitlement-management-access-package-settings.md)
- [Alterar funções de recursos para um pacote de acesso](entitlement-management-access-package-resources.md)
- [Atribuir diretamente um usuário ao pacote de acesso](entitlement-management-access-package-assignments.md)
