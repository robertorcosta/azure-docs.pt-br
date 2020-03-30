---
title: Notificações de e-mail no PIM - Azure Active Directory | Microsoft Docs
description: Descreve as notificações por email no Azure AD PIM (Privileged Identity Management).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 01/05/2019
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: ee5f2edbae28276f8485ae774a5b1c52e1af2fd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72756395"
---
# <a name="email-notifications-in-pim"></a>Notificações por email no PIM

O PIM (Privileged Identity Management, gerenciamento de identidade privilegiado) permite saber quando eventos importantes ocorrem na organização Azure Active Directory (Azure AD), como quando uma função é atribuída ou ativada. O Gerenciamento de Identidade Privilegiada mantém você informado enviando notificações de e-mail para você e outros participantes. Adicionalmente, esses emails podem incluir links para tarefas relevantes como ativar ou renovar uma função. Este artigo descreve a aparência desses emails, quando são enviados e quem recebe os emails.

## <a name="sender-email-address-and-subject-line"></a>Endereço de email do remetente e linha do assunto

Os e-mails enviados do Gerenciamento de Identidade Privilegiada para as funções de recursos do Azure AD e do Azure têm o seguinte endereço de e-mail do remetente:

- Endereço de **e-mail:\@azure-no-reply microsoft.com**
- Nome de exibição: Microsoft Azure

Esses emails incluem um prefixo **PIM** na linha de assunto. Aqui está um exemplo:

- PIM: Alain Charon foi permanentemente designado para a função de Leitor de Backup

## <a name="notifications-for-azure-ad-roles"></a>Notificações para funções ad do Azure

O Gerenciamento de Identidade Privilegiada envia e-mails quando ocorrem os seguintes eventos para as funções do Azure AD:

- Quando uma ativação de função com privilégios está com aprovação pendente
- Quando uma solicitação de ativação de função com privilégios é concluída
- Quando o Azure AD Privileged Identity Management é habilitado

Quem recebe esses emails para as funções do Azure AD depende da função, do evento e da configuração de notificações:

| Usuário | Ativação de função está pendente de aprovação | A solicitação de ativação de função está concluída | O PIM está habilitado |
| --- | --- | --- | --- |
| Administrador de função com privilégios</br>(Ativado/Qualificado) | Sim</br>(somente se nenhum aprovador explícito for especificado) | Sim* | Sim |
| Administrador de segurança</br>(Ativado/Qualificado) | Não | Sim* | Sim |
| Administrador global</br>(Ativado/Qualificado) | Não | Sim* | Sim |

\*Se as [**configurações de** Notificações](pim-how-to-change-default-settings.md#notifications) estiver definida como **Habilitar**.

A seguir, é mostrado um email de exemplo enviado quando um usuário ativa uma função do Azure AD para a organização fictícia Contoso.

![Novo e-mail de gerenciamento de identidade privilegiado para funções AD do Azure](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Semanalmente, o e-mail de digerir gerenciamento de identidade privilegiado para as funções do Azure AD

Um e-mail de resumo semanal do Privileged Identity Management para funções Ad do Azure é enviado para administradores de funções privilegiadas, administradores de segurança e administradores globais que habilitaram o Gerenciamento de Identidade Privilegiado. Este e-mail semanal fornece um instantâneo das atividades de Gerenciamento de Identidade Privilegiada para a semana, bem como atribuições de função privilegiadas. É disponibilizado apenas para locatários na nuvem pública. Aqui está um exemplo de e-mail:

![Semanalmente, o e-mail de digerir gerenciamento de identidade privilegiado para as funções do Azure AD](./media/pim-email-notifications/email-directory-weekly.png)

O email inclui quatro blocos:

| Bloco | Descrição |
| --- | --- |
| **Usuários ativados** | Número de vezes que os usuários ativaram a função qualificada no locatário. |
| **Usuários tornados permanentes** | Número de vezes que usuários com uma atribuição qualificada tornam-se permanentes. |
| **Atribuições de função no Gerenciamento de Identidade Privilegiada** | Número de vezes que os usuários são atribuídos a uma função elegível dentro do Gerenciamento de Identidade Privilegiada. |
| **Atribuições de função fora do PIM** | Número de vezes que os usuários são atribuídos a uma função permanente fora do Gerenciamento de Identidade Privilegiada (dentro do Azure AD). |

A seção **Visão geral das principais funções** lista as cinco principais funções no locatário com base no número total de administradores permanentes e qualificados para cada função. O link **Executar ação** abre o [assistente do PIM](pim-security-wizard.md), onde é possível converter administradores permanentes em administradores qualificados em lotes.

## <a name="pim-emails-for-azure-resource-roles"></a>Emails do PIM para funções de recurso do Azure

O Gerenciamento de Identidade Privilegiada envia e-mails para proprietários e administradores de acesso ao usuário quando ocorrem os seguintes eventos para funções de recurso do Azure:

- Quando uma atribuição de função estiver com aprovação pendente
- Quando uma função for atribuída
- Quando uma função estiver prestes a expirar
- Quando uma função for qualificada para estender
- Quando uma função estiver sendo renovada por um usuário final
- Quando uma solicitação de ativação de função for concluída

O Gerenciamento de Identidade Privilegiada envia e-mails para usuários finais quando ocorrem os seguintes eventos para funções de recurso do Azure:

- Quando uma função for atribuída ao usuário
- Quando a função de um usuário expirar
- Quando a função do usuário for estendida
- Quando a solicitação de ativação de função de um usuário for concluída

A seguir, é mostrado um email de exemplo enviado quando um usuário recebe uma função de recurso do Azure para a organização fictícia Contoso.

![Novo e-mail de gerenciamento de identidade privilegiado para funções de recursos do Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Próximas etapas

- [Configure as configurações de função AD do Azure no Gerenciamento de Identidade Privilegiada](pim-how-to-change-default-settings.md)
- [Aprovar ou negar pedidos para funções do Azure AD no Gerenciamento de Identidade Privilegiada](azure-ad-pim-approval-workflow.md)
