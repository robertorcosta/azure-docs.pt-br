---
title: Azure AD funde assistente de segurança no PIM - Azure Active Directory | Microsoft Docs
description: Descreve o assistente de segurança que você pode usar para converter atribuição de função privilegiadas do Azure Active Directory para qualificados usando o Azure Active Directory Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 11/08/2019
ms.author: curtand
ms.custom: pim ; H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04bd0993873568ba7cce368ddd9277ed356b636c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266566"
---
# <a name="azure-ad-roles-security-wizard-in-privileged-identity-management"></a>Azure AD funde assistente de segurança no Gerenciamento de Identidade Privilegiada

Se você é a primeira pessoa a usar o PIM (Privileged Identity Management, gerenciamento de identidade privilegiado) em sua organização Azure Active Directory (Azure AD),, você será apresentado a um assistente para começar. O assistente ajuda você a entender os riscos de segurança de identidades privilegiadas e como usar o Gerenciamento de Identidade Privilegiada para reduzir esses riscos. Você não precisará fazer nenhuma alteração nas atribuições de função existentes no assistente, se preferir fazer isso posteriormente.

## <a name="wizard-overview"></a>Visão geral do assistente

Antes que sua organização comece a usar o Gerenciamento de Identidade Privilegiada, todas as atribuições de função são permanentes: os usuários estão sempre nessas funções, mesmo que não precisem de seus privilégios atualmente. A primeira etapa do assistente mostra uma lista de funções com privilégios altos e quantos usuários atualmente estão nessas funções. Você poderá analisar uma função específica para saber mais sobre os usuários se um ou mais não forem familiares.

A segunda etapa do assistente lhe fornece a oportunidade de alterar as atribuições de função do administrador.  

> [!WARNING]
> É importante que você tenha pelo menos um administrador Global e mais de um administrador de funções privilegiado com uma conta organizacional (não uma conta Microsoft). Se houver apenas um administrador de funções privilegiado, a organização não poderá gerenciar o Gerenciamento de Identidade Privilegiada se essa conta for excluída.
> Além disso, mantenha as atribuições de função permanentes se um usuário tiver uma conta Microsoft (em outras palavras, uma conta que eles usam para fazer login em serviços da Microsoft como Skype e Outlook.com). Se você planeja exigir autenticação multifatorial para ativação para essa função, esse usuário será bloqueado.

## <a name="run-the-wizard"></a>Executar o assistente

1. Faça login no [portal Azure](https://portal.azure.com/).

1. Abra **o Azure AD Privileged Identity Management**.

1. Selecione **as funções azure AD** e, em seguida, selecione **Assistente**.

    ![Funções Azure AD - Página do assistente mostrando os 3 passos para executar o assistente](./media/pim-security-wizard/wizard-start.png)

1. Selecione **1 Descubra funções privilegiadas**.

1. Examine a lista de funções com privilégios para ver quais usuários são permanentes ou qualificados.

    ![Descubra funções privilegiadas - Painel de papéis mostrando membros permanentes e elegíveis](./media/pim-security-wizard/discover-privileged-roles-users.png)

1. Selecione **Ao lado** para selecionar os usuários ou grupos que deseja tornar elegíveis.

    ![Converta membros em página elegível com opções para selecionar membros que você deseja tornar elegíveis para funções](./media/pim-security-wizard/convert-members-eligible.png)

1. Depois de selecionar os usuários ou grupos, selecione **Next**.

    ![Revisar a página de alterações mostrando membros com atribuições de função permanente que serão convertidas](./media/pim-security-wizard/review-changes.png)

1. Selecione **OK** para converter as atribuições permanentes para elegíveis.

    Quando a conversão for concluída, você verá uma notificação.

    ![Notificação mostrando o status de uma conversão](./media/pim-security-wizard/notification-completion.png)

Se você precisar converter outras atribuições de função com privilégios para qualificados, você pode executar o assistente novamente. Se você quiser usar a interface de gerenciamento de identidade privilegiada em vez do assistente, consulte Atribuir a [azure ad roles no Gerenciamento de Identidade Privilegiado](pim-how-to-add-role-to-user.md).

## <a name="next-steps"></a>Próximas etapas

- [Atribuir funções ad do Azure no gerenciamento de identidade privilegiado](pim-how-to-add-role-to-user.md)
- [Conceder acesso a outros administradores para gerenciar o Gerenciamento de Identidade Privilegiada](pim-how-to-give-access-to-pim.md)
