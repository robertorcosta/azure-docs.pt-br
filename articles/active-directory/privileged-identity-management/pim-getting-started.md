---
title: Comece a usar o PIM - Azure Active Directory | Microsoft Docs
description: Saiba como ativar e começar a usar o PIM (Gerenciamento de Identidades Privilegiadas) do Azure Active Directory Privileged Identity Management no portal do Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: conceptual
ms.workload: identity
ms.date: 03/13/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cbb9b4340a7cdb9be5039722a8f75e09288ec48
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472853"
---
# <a name="start-using-privileged-identity-management"></a>Começar usando o Privileged Identity Management

Com o PIM (Privileged Identity Management, gerenciamento de identidade privilegiado), você pode gerenciar, controlar e monitorar o acesso dentro da organização Azure Active Directory (Azure AD). Este escopo inclui acesso aos recursos do Azure, Azure AD e outros serviços online da Microsoft, como o Office 365 ou o Microsoft Intune.

Este artigo descreve como habilitar e começar a usar o Gerenciamento de Identidade Privilegiada.

## <a name="prerequisites"></a>Pré-requisitos

Para usar o Gerenciamento de Identidade Privilegiada, você deve ter uma das seguintes licenças:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Para obter mais informações, consulte [os requisitos de licença para usar o Gerenciamento de Identidade Privilegiado](subscription-requirements.md).

## <a name="sign-up-pim-for-azure-ad-roles"></a>Inscreva-se o PIM para funções do Microsoft Azure Active Directory

Depois de habilitar o Gerenciamento de Identidade Privilegiada para o seu diretório, você precisará assinar o Gerenciamento de Identidade Privilegiada para gerenciar as funções do Azure AD.

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **funções Azure AD**.

    ![Inscreva-se no Gerenciamento de Identidade Privilegiada para funções AD do Azure](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Selecione **Inscreva-se**.

1. Na mensagem que aparece, clique **em Sim** para assinar o Gerenciamento de Identidade Privilegiada para gerenciar as funções do Azure AD.

    ![Inscreva-se gerenciamento de identidade privilegiado para a mensagem de funções ad do Azure](./media/pim-getting-started/sign-up-pim-message.png)

    Quando a inscrição for concluída, as opções do Microsoft Azure Active Directory serão ativadas. Talvez você precise atualizar o portal.

    Para obter informações sobre como descobrir e selecionar os recursos do Azure para proteger com o Gerenciamento de Identidade Privilegiado, consulte [Discover Azure resources to manage in Privileged Identity Management](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Navegue até as tarefas

Uma vez configurado o Gerenciamento de Identidade Privilegiado, você pode iniciar suas tarefas de gerenciamento de identidade.

![Janela de navegação no Gerenciamento de Identidade Privilegiada mostrando tarefas e gerenciar opções](./media/pim-getting-started/pim-quickstart-tasks.png)

| Tarefa + Gerenciar | Descrição |
| --- | --- |
| **Minhas Funções**  | Exibe uma lista de funções qualificadas e ativas atribuídas a você. É aqui que você pode ativar as funções qualificadas atribuídas. |
| **Minhas solicitações** | Exibe as solicitações pendentes para ativar atribuições de função qualificadas. |
| **Aprovar solicitações** | Exibe uma lista de solicitações de usuários para ativar funções qualificadas em seu diretório, que você pode aprovar. |
| **Examinar acesso** | Lista as revisões de acesso ativas atribuídas a você para completar, esteja você revisando o acesso para si mesmo ou para outra pessoa. |
| **Funções do Microsoft Azure Active Directory** | Exibe um painel de controle e configurações para administradores de função privilegiadas para gerenciar as atribuições de função Azure AD. Esse painel é desabilitado para todos que não forem administradores de função com privilégios. Esses usuários têm acesso a um painel especial denominado Minha exibição. O painel Minha exibição exibe somente informações sobre o usuário que acessa o painel, não o locatário inteiro. |
| **Recursos do Azure** | Exibe um painel e configurações para que administradores com função com privilégios gerenciem atribuições de função de recurso do Azure. Esse painel é desabilitado para todos que não forem administradores de função com privilégios. Esses usuários têm acesso a um painel especial denominado Minha exibição. O painel Minha exibição exibe somente informações sobre o usuário que acessa o painel, não o locatário inteiro. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Adicionar um bloco PIM ao painel

Para facilitar a abertura do Gerenciamento de Identidade Privilegiada, adicione um bloco de gerenciamento de identidade privilegiado ao painel do portal Azure.

1. Faça login no [portal Azure](https://portal.azure.com/).

1. Selecione **Todos os serviços** e encontre o serviço **de Gerenciamento de Identidade Privilegiada AD do Azure.**

    ![Azure AD Privileged Identity Management em Todos os serviços](./media/pim-getting-started/pim-all-services-find.png)

1. Selecione o Quickstart de gerenciamento de identidade privilegiado.

1. Verifique **a lâmina pin para o painel de instrumentos** para fixar a lâmina Quickstart de gerenciamento de identidade privilegiada no painel.

    ![Ícone pushpin para fixar lâmina de gerenciamento de identidade privilegiada no painel](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    No painel do Azure, você verá um bloco como este:

    ![Gerenciamento de identidade privilegiado ladrilho Quickstart no painel](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções ad do Azure no gerenciamento de identidade privilegiado](pim-how-to-add-role-to-user.md)
- [Descubra os recursos do Azure para gerenciar no Gerenciamento de Identidade Privilegiada](pim-resource-roles-discover-resources.md)
