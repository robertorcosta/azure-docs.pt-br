---
title: Descoberta e insights de funções do Azure AD (visualização) no assistente de segurança do Privileged Identity Management anterior-Azure Active Directory
description: A descoberta e as informações (anteriormente conhecido como assistente de segurança) ajudam a converter atribuições de função do Azure AD permanentes em atribuições just-in-time com Privileged Identity Management.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.subservice: pim
ms.date: 08/07/2020
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef1de7e98a9cb57f83b87589ceddedc3cdd80927
ms.sourcegitcommit: 98854e3bd1ab04ce42816cae1892ed0caeedf461
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/07/2020
ms.locfileid: "88005883"
---
# <a name="discovery-and-insights-preview-for-azure-ad-roles-formerly-security-wizard"></a>Descoberta e insights (visualização) para funções do Azure AD (anteriormente conhecido como assistente de segurança)

Se você estiver começando com o PIM (Privileged Identity Management) em sua organização do Azure Active Directory (Azure AD), você poderá usar a página **descoberta e informações (versão prévia)** para começar. Este recurso mostra quem está atribuído a funções privilegiadas em sua organização e como usar o PIM para alterar rapidamente as atribuições de função permanentes em atribuições just-in-time. Você pode exibir ou fazer alterações em suas atribuições de função com privilégios permanentes em **descoberta e ideias (versão prévia)**. É uma ferramenta de análise e uma ferramenta de ação.

## <a name="discovery-and-insights-preview"></a>Descoberta e ideias (versão prévia)

Antes que sua organização comece a usar Privileged Identity Management, todas as atribuições de função são permanentes. Os usuários sempre estão em suas funções atribuídas mesmo quando não precisam de seus privilégios. A descoberta e as informações (versão prévia), que substitui o antigo assistente de segurança, mostra uma lista de funções privilegiadas e quantos usuários estão atualmente nessas funções. Você pode listar as atribuições de uma função para saber mais sobre os usuários atribuídos se um ou mais deles não forem familiares.

: heavy_check_mark: a **Microsoft recomenda** que você mantenha duas contas de interrupção que são atribuídas permanentemente à função de administrador global que não requer autenticação multifator ao entrar. Você pode usá-los em qualquer cenário de interrupção ou, caso nenhum seja capaz de elevar sua função de administrador global.

Além disso, mantenha as atribuições de função permanentes se um usuário tiver um conta Microsoft (em outras palavras, uma conta que use para entrar nos serviços da Microsoft, como o Skype ou Outlook.com). Se você precisar que a autenticação multifator seja ativada nessa função, o usuário será bloqueado.

## <a name="open-discovery-and-insights-preview"></a>Abrir descoberta e insights (versão prévia)

1. Entre no [portal do Azure](https://portal.azure.com/).

1. Abra **Azure ad Privileged Identity Management**.

1. Selecione **funções do Azure ad** e, em seguida, selecione **descoberta e insights (versão prévia)**. Abrir a página inicia o processo de descoberta para localizar as atribuições de função relevantes.

    ![Funções do Azure AD – página de descoberta e informações mostrando as 3 opções](./media/pim-security-wizard/new-preview-link.png)

1. Selecione **reduzir administradores globais**.

    ![Reduzir os administradores globais-painel de funções mostrando todos os membros](./media/pim-security-wizard/new-preview-page.png)

1. Examine a lista de atribuições de função de administrador global.

    ![Reduzir os administradores globais-painel de funções mostrando todos os membros](./media/pim-security-wizard/new-global-administrator-list.png)

1. Selecione **Avançar** para selecionar os usuários ou grupos que você deseja tornar qualificados e, em seguida, selecione **tornar qualificado** ou **remover atribuição**.

    ![Converter Membros em uma página qualificada com opções para selecionar membros que você deseja tornar qualificados para funções](./media/pim-security-wizard/new-global-administrator-buttons.png)

1. Você também pode exigir que todos os administradores globais revisem seu próprio acesso.

    ![Página de administradores globais mostrando a seção de revisões de acesso](./media/pim-security-wizard/new-global-administrator-access-review.png)

1. Depois de selecionar qualquer uma dessas alterações, você verá uma notificação do Azure.

1. Em seguida, você pode selecionar **eliminar o acesso** ou **examinar entidades de serviço** para repetir as etapas acima em outras funções privilegiadas e nas atribuições de função da entidade de serviço. Para atribuições de função de entidade de serviço, você só pode remover atribuições de função.

    ![Opções adicionais de informações para eliminar o acesso e a análise das entidades de serviço ](./media/pim-security-wizard/new-preview-page-service-principals.png)

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções do Azure AD no Privileged Identity Management](pim-how-to-add-role-to-user.md)
- [Conceder acesso a outros administradores para gerenciar Privileged Identity Management](pim-how-to-give-access-to-pim.md)
