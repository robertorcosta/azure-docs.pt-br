---
title: Alterar as configurações de solicitação e aprovação para um pacote de acesso no gerenciamento de direitos Azure AD - Azure Active Directory
description: Saiba como alterar as configurações de solicitação e aprovação de um pacote de acesso no gerenciamento de direitos do Azure Active Directory.
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
ms.openlocfilehash: 493ba6396a7ceb11b917fbda5dd6c37c070f2fee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261925"
---
# <a name="change-request-and-approval-settings-for-an-access-package-in-azure-ad-entitlement-management"></a>Alterar as configurações de solicitação e aprovação para um pacote de acesso no gerenciamento de direitos Ad do Azure

Como gerenciador de pacotes de acesso, você pode alterar os usuários que podem solicitar um pacote de acesso a qualquer momento editando a diretiva ou adicionando uma nova diretiva. Você também pode alterar as configurações de aprovação.

Este artigo descreve como alterar as configurações de solicitação e aprovação de um pacote de acesso existente.

## <a name="choose-between-one-or-multiple-polices"></a>Escolha entre uma ou várias polícias

A maneira como você especifica quem pode solicitar um pacote de acesso é com uma política. Quando você cria um pacote de acesso, você especifica a configuração de solicitação e aprovação que cria uma diretiva. A maioria dos pacotes de acesso terá uma única diretiva, mas um único pacote de acesso pode ter várias políticas. Você criaria várias políticas para um pacote de acesso se desejasse permitir que diferentes conjuntos de usuários fossem concedidos atribuições com diferentes configurações de solicitação e aprovação. Por exemplo, uma única diretiva não pode ser usada para atribuir usuários internos e externos ao mesmo pacote de acesso. No entanto, você pode criar duas políticas no mesmo pacote de acesso - uma para usuários internos e outra para usuários externos. Se houver várias políticas que se aplicam a um usuário, elas serão solicitadas no momento de sua solicitação para selecionar a política a que gostariam de ser atribuída. O diagrama a seguir mostra um pacote de acesso com duas políticas.

![Várias políticas em um pacote de acesso](./media/entitlement-management-access-package-request-policy/access-package-policy.png)

| Cenário | Número de políticas |
| --- | --- |
| Quero que todos os usuários do meu diretório tenham as mesmas configurações de solicitação e aprovação para um pacote de acesso | Um |
| Quero que todos os usuários de certas organizações conectadas possam solicitar um pacote de acesso | Um |
| Quero permitir que usuários em meu diretório e também usuários fora do meu diretório solicitem um pacote de acesso | Vários |
| Eu quero especificar diferentes configurações de aprovação para alguns usuários | Vários |
| Quero que alguns usuários acessem as atribuições do pacote para expirar, enquanto outros usuários podem estender seu acesso | Vários |

Para obter informações sobre a lógica prioritária usada quando várias políticas se aplicam, consulte [Várias políticas](entitlement-management-troubleshoot.md#multiple-policies
).

### <a name="open-an-existing-policy-of-request-and-approval-settings"></a>Abra uma política existente de configurações de solicitação e aprovação

Para alterar as configurações de solicitação e aprovação de um pacote de acesso, você precisa abrir a diretiva correspondente. Siga estas etapas para abrir as configurações de solicitação e aprovação de um pacote de acesso.

**Papel pré-requisito:** Administrador global, administrador de usuário, proprietário de catálogo ou gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **Acessar pacotes** e, em seguida, abra o pacote de acesso.

1. Clique **em Políticas** e clique na diretiva que deseja editar.

    O painel de detalhes da Política é aberto na parte inferior da página.

    ![Pacote de acesso - Painel de detalhes da política](./media/entitlement-management-shared/policy-details.png)

1. Clique **em Editar** para editar a diretiva.

    ![Pacote de acesso - Editar política](./media/entitlement-management-shared/policy-edit.png)

1. Clique na guia **Solicitações** para abrir as configurações de solicitação e aprovação.

1. Execute as etapas em uma das seguintes seções de solicitação.

### <a name="add-a-new-policy-of-request-and-approval-settings"></a>Adicionar uma nova política de configurações de solicitação e aprovação

Se você tiver um conjunto de usuários que devem ter diferentes configurações de solicitação e aprovação, provavelmente precisará criar uma nova política. Siga essas etapas para começar a adicionar uma nova diretiva a um pacote de acesso existente.

**Papel pré-requisito:** Administrador global, administrador de usuário, proprietário de catálogo ou gerenciador de pacotes de acesso

1. No portal do Azure, clique em **Azure Active Directory** e, em seguida, em **Governança de Identidade**.

1. No menu à esquerda, clique em **Acessar pacotes** e, em seguida, abra o pacote de acesso.

1. Clique **em Políticas** e, em seguida, Adicione **política**.

1. Digite um nome e uma descrição para a apólice.

    ![Criar política com nome e descrição](./media/entitlement-management-access-package-request-policy/policy-name-description.png)

1. Clique **Avançar** para abrir a guia **Solicitações**.

1. Execute as etapas em uma das seguintes seções de solicitação.

[!INCLUDE [Entitlement management request policy](../../../includes/active-directory-entitlement-management-request-policy.md)]

Se você estiver editando uma diretiva clique em **Atualizar**. Se você estiver adicionando uma nova diretiva, clique em **Criar**.

## <a name="next-steps"></a>Próximas etapas

- [Alterar as configurações do ciclo de vida de um pacote de acesso](entitlement-management-access-package-lifecycle-policy.md)
- [Exibir solicitações para um pacote de acesso](entitlement-management-access-package-requests.md)