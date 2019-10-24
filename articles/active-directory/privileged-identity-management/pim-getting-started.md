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
ms.topic: conceptual
ms.workload: identity
ms.date: 04/09/2019
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: b0ba7846b60ca6649b4342d5096e92dfd8c96601
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756306"
---
# <a name="start-using-privileged-identity-management"></a>Começar usando o Privileged Identity Management

Com o PIM (Privileged Identity Management), você pode gerenciar, controlar e monitorar o acesso dentro de sua organização do Azure Active Directory (Azure AD). Esse escopo inclui acesso aos recursos do Azure, ao Azure AD e a outros serviços online da Microsoft, como o Office 365 ou Microsoft Intune.

Este artigo descreve como habilitar e começar a usar o Privileged Identity Management.

## <a name="prerequisites"></a>Pré-requisitos

Para usar Privileged Identity Management, você deve ter uma das seguintes licenças:

- Azure AD Premium P2
- Enterprise Mobility + Security (EMS) E5

Para obter mais informações, consulte [requisitos de licença para usar Privileged Identity Management](subscription-requirements.md).

## <a name="first-person-to-use-pim"></a>Primeira pessoa a usar o PIM

Se você for a primeira pessoa a usar Privileged Identity Management em seu diretório, receberá automaticamente as funções [administrador de segurança](../users-groups-roles/directory-assign-admin-roles.md#security-administrator) e [administrador de função com privilégios](../users-groups-roles/directory-assign-admin-roles.md#privileged-role-administrator) no diretório. Somente administradores de função com privilégios podem gerenciar atribuições de função de usuários do Azure AD. Além disso, você pode optar por executar o [assistente de segurança](pim-security-wizard.md) que o orientará na experiência inicial de descoberta e atribuição.

## <a name="enable-pim"></a>Habilita o PIM

Para começar a usar Privileged Identity Management em seu diretório, primeiro você deve habilitar Privileged Identity Management.

1. Faça login no [portal do Microsoft Azure](https://portal.azure.com/) como administrador global do seu diretório.

    Você deve ser um administrador global com uma conta organizacional (por exemplo, @yourdomain.com), e não uma conta Microsoft (por exemplo, @outlook.com), para habilitar o Privileged Identity Management para um diretório.

1. Clique em **Todos os serviços** e encontre o serviço **Azure AD Privileged Identity Management**.

    ![Azure AD Privileged Identity Management em Todos os serviços](./media/pim-getting-started/pim-all-services-find.png)

1. Clique para abrir o guia de início rápido do Privileged Identity Management.

1. Na lista, clique em **Consentimento para o PIM**.

    ![Consentimento para Privileged Identity Management para habilitar Privileged Identity Management](./media/pim-getting-started/consent-pim.png)

1. Clique em **verificar minha identidade** para verificar sua identidade com o Azure MFA. Você será solicitado a escolher uma conta.

    ![Escolha uma janela de conta para verificar sua identidade](./media/pim-getting-started/pick-account.png)

1. Se mais informações forem necessárias para a verificação, você será guiado pelo processo. Para mais informações, consulte [Obter ajuda para a confirmação em dois passos](https://go.microsoft.com/fwlink/p/?LinkId=708614).

    ![Mais informações sobre a janela necessária se sua organização precisar de mais informações](./media/pim-getting-started/more-information-required.png)

    Por exemplo, você pode ser solicitado a fornecer confirmação por telefone.

    ![Página de verificação de segurança adicional perguntando como entrar em contato com você](./media/pim-getting-started/additional-security-verification.png)

1. Depois de concluir o processo de verificação, clique no botão **consentimento**.

1. Na mensagem que aparece, clique em **Sim** para dar consentimento ao serviço de Privileged Identity Management.

    ![Consentimento para Privileged Identity Management mensagem para concluir o processo de consentimento](./media/pim-getting-started/consent-pim-message.png)

## <a name="sign-up-pim-for-azure-ad-roles"></a>Inscreva-se o PIM para funções do Microsoft Azure Active Directory

Depois de habilitar Privileged Identity Management para seu diretório, você precisará inscrever Privileged Identity Management para gerenciar funções do Azure AD.

1. Abra o **Azure AD Privileged Identity Management**.

1. Clique em **funções do Microsoft Azure Active Directory**.

    ![Inscrever Privileged Identity Management para funções do Azure AD](./media/pim-getting-started/sign-up-pim-azure-ad-roles.png)

1. Clique em **Inscreva-se**.

1. Na mensagem que aparece, clique em **Sim** para se inscrever Privileged Identity Management para gerenciar funções do Azure AD.

    ![Inscrever Privileged Identity Management para mensagem de funções do Azure AD](./media/pim-getting-started/sign-up-pim-message.png)

    Quando a inscrição for concluída, as opções do Microsoft Azure Active Directory serão ativadas. Talvez você precise atualizar o portal.

    Para obter informações sobre como descobrir e selecionar os recursos do Azure para proteger com Privileged Identity Management, consulte [descobrir recursos do Azure para gerenciar no Privileged Identity Management](pim-resource-roles-discover-resources.md).

## <a name="navigate-to-your-tasks"></a>Navegue até as tarefas

Quando Privileged Identity Management estiver configurado, você poderá iniciar suas tarefas de gerenciamento de identidade.

![Janela de navegação no Privileged Identity Management mostrando as opções tarefas e gerenciar](./media/pim-getting-started/pim-quickstart-tasks.png)

| Tarefa + Gerenciar | Descrição |
| --- | --- |
| **Minhas Funções**  | Exibe uma lista de funções qualificadas e ativas atribuídas a você. É aqui que você pode ativar as funções qualificadas atribuídas. |
| **Minhas solicitações** | Exibe as solicitações pendentes para ativar atribuições de função qualificadas. |
| **Aprovar solicitações** | Exibe uma lista de solicitações de usuários para ativar funções qualificadas em seu diretório, que você pode aprovar. |
| **Análise de acesso** | Lista as revisões de acesso ativas atribuídas a você para completar, esteja você revisando o acesso para si mesmo ou para outra pessoa. |
| **Funções do Microsoft Azure Active Directory** | Exibe um painel e configurações para administradores de função com privilégios para gerenciar atribuições de função do Azure AD. Esse painel é desabilitado para todos que não forem administradores de função com privilégios. Esses usuários têm acesso a um painel especial denominado Minha exibição. O painel Minha exibição exibe somente informações sobre o usuário que acessa o painel, não o locatário inteiro. |
| **Recursos do Azure** | Exibe um painel e configurações para que administradores com função com privilégios gerenciem atribuições de função de recurso do Azure. Esse painel é desabilitado para todos que não forem administradores de função com privilégios. Esses usuários têm acesso a um painel especial denominado Minha exibição. O painel Minha exibição exibe somente informações sobre o usuário que acessa o painel, não o locatário inteiro. |

## <a name="add-a-pim-tile-to-the-dashboard"></a>Adicionar um bloco PIM ao painel

Para facilitar a abertura de Privileged Identity Management, você deve adicionar um bloco de Privileged Identity Management ao seu painel de portal do Azure.

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Clique em **Todos os serviços** e encontre o serviço **Azure AD Privileged Identity Management**.

    ![Azure AD Privileged Identity Management em Todos os serviços](./media/pim-getting-started/pim-all-services-find.png)

1. Clique para abrir o guia de início rápido do Privileged Identity Management.

1. Marque a **folha fixar no painel** para fixar a folha Privileged Identity Management início rápido no painel.

    ![Ícone de pino para fixar Privileged Identity Management folha no painel](./media/pim-getting-started/pim-quickstart-pin-to-dashboard.png)

    No painel do Azure, você verá um bloco como este:

    ![Bloco Privileged Identity Management início rápido no painel](./media/pim-getting-started/pim-quickstart-dashboard-tile.png)

## <a name="next-steps"></a>Próximos passos

- [Atribuir funções do Azure AD no Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Descubra os recursos do Azure para gerenciar no Privileged Identity Management](pim-resource-roles-discover-resources.md)
