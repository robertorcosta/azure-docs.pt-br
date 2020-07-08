---
title: Começar a usar o PIM-Azure Active Directory | Microsoft Docs
description: Saiba como ativar e começar a usar o PIM (Gerenciamento de Identidades Privilegiadas) do Azure Active Directory Privileged Identity Management no portal do Azure.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: pim
ms.topic: how-to
ms.workload: identity
ms.date: 04/23/2020
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: cad6c41b465e14b11d4fa7b8e7fa6037d49e8eae
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84742893"
---
# <a name="start-using-privileged-identity-management"></a>Começar usando o Privileged Identity Management

Este artigo descreve como habilitar o PIM (Privileged Identity Management) e começar a usá-lo.

Use o Privileged Identity Management (PIM) para gerenciar, controlar e monitorar o acesso em sua organização do Azure Active Directory (AD do Azure). Com o PIM, você pode fornecer acesso necessário e Just-in-time aos recursos do Azure, aos recursos do Azure AD e a outros serviços online da Microsoft, como o Office 365 ou Microsoft Intune.

## <a name="prerequisites"></a>Pré-requisitos

Para usar Privileged Identity Management, você deve ter uma das seguintes licenças:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Para obter mais informações, consulte [requisitos de licença para usar Privileged Identity Management](subscription-requirements.md).

## <a name="prepare-pim-for-azure-ad-roles"></a>Preparar o PIM para funções do Azure AD

Depois de habilitar Privileged Identity Management para seu diretório, você pode preparar Privileged Identity Management para gerenciar funções do Azure AD.

Aqui estão as tarefas que recomendamos para você se preparar para as funções do Azure AD, na ordem:

1. [Defina as configurações de função do Azure ad](pim-how-to-change-default-settings.md).
1. [Dê atribuições qualificadas](pim-how-to-add-role-to-user.md).
1. [Permitir que usuários qualificados ativem sua função do Azure ad just-in-time](pim-how-to-activate-role.md).

## <a name="prepare-pim-for-azure-roles"></a>Preparar o PIM para funções do Azure

Depois de habilitar Privileged Identity Management para seu diretório, você pode preparar Privileged Identity Management para gerenciar funções do Azure para o acesso a recursos do Azure em uma assinatura.

Aqui estão as tarefas que recomendamos para você se preparar para as funções do Azure, na ordem:

1. [Recursos de descoberta do Azure](pim-resource-roles-discover-resources.md)
1. [Defina as configurações de função do Azure](pim-resource-roles-configure-role-settings.md).
1. [Dê atribuições qualificadas](pim-resource-roles-assign-roles.md).
1. [Permitir que usuários qualificados ativem suas funções do Azure just-in-time](pim-resource-roles-activate-your-roles.md).

## <a name="navigate-to-your-tasks"></a>Navegue até as tarefas

Quando Privileged Identity Management estiver configurado, você pode aprender o seu caminho.

![Janela de navegação no Privileged Identity Management mostrando as opções tarefas e gerenciar](./media/pim-getting-started/pim-quickstart-tasks.png)

| Tarefa + Gerenciar | Descrição |
| --- | --- |
| **Minhas Funções**  | Exibe uma lista de funções qualificadas e ativas atribuídas a você. É aqui que você pode ativar as funções qualificadas atribuídas. |
| **Minhas solicitações** | Exibe as solicitações pendentes para ativar atribuições de função qualificadas. |
| **Aprovar solicitações** | Exibe uma lista de solicitações de usuários para ativar funções qualificadas em seu diretório, que você pode aprovar. |
| **Examinar acesso** | Lista as revisões de acesso ativas atribuídas a você para completar, esteja você revisando o acesso para si mesmo ou para outra pessoa. |
| **Funções do Azure AD** | Exibe um painel e configurações para administradores de função com privilégios para gerenciar atribuições de função do Azure AD. Esse painel é desabilitado para todos que não forem administradores de função com privilégios. Esses usuários têm acesso a um painel especial denominado Minha exibição. O painel minha exibição exibe apenas informações sobre o usuário que está acessando o painel, e não toda a organização. |
| **Recursos do Azure** | Exibe um painel e configurações para administradores de função com privilégios para gerenciar atribuições de função de recurso do Azure. Esse painel é desabilitado para todos que não forem administradores de função com privilégios. Esses usuários têm acesso a um painel especial denominado Minha exibição. O painel minha exibição exibe apenas informações sobre o usuário que está acessando o painel, e não toda a organização. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Adicionar um bloco PIM ao painel

Para facilitar a abertura de Privileged Identity Management, adicione um bloco do PIM ao seu painel de portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Selecione **todos os serviços** e localize o serviço de **Azure ad Privileged Identity Management** .

    ![Azure AD Privileged Identity Management em Todos os serviços](./media/pim-getting-started/pim-all-services-find.png)

1. Selecione o guia de início rápido do Privileged Identity Management.

1. Marque a **folha fixar no painel** para fixar a folha Privileged Identity Management início rápido no painel.

    ![Ícone de pino para fixar Privileged Identity Management folha no painel](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    No painel do Azure, você verá um bloco como este:

    ![Bloco Privileged Identity Management início rápido no painel](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções do Azure AD no Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Gerenciar o acesso a recursos do Azure no Privileged Identity Management](pim-resource-roles-discover-resources.md)
