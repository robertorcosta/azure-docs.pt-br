---
title: Notificações do Azure Active Directory Identity Protection
description: Saiba como as notificações dão suporte às suas atividades de investigação.
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: conceptual
ms.date: 10/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0c83aa6e476bbd898999fb6efe490c7847a809ff
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120120"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notificações do Azure Active Directory Identity Protection

O Azure AD Identity Protection envia dois tipos de e-mails de notificação automatizados para ajudá-lo a gerenciar detecções de risco e risco do usuário:

- Email de usuários em risco detectado
- Email de resumo semanal

Este artigo fornece uma visão geral de ambos os emails de notificação.

## <a name="users-at-risk-detected-email"></a>Email de usuários em risco detectado

Em resposta a uma conta detectada em risco, o Azure AD Identity Protection gera um alerta de email com **Usuários em risco detectado** como assunto. O email inclui um link para o relatório **[Usuários sinalizados por risco](../reports-monitoring/concept-user-at-risk.md)**. Como prática recomendada, você deve investigar imediatamente os usuários em risco.

A configuração para este alerta permite especificar em que nível de risco do usuário deseja que o alerta seja gerado. O e-mail será gerado quando o nível de risco do usuário atingir o que você especificou; no entanto, você não receberá novos usuários em risco detectados alertas de e-mail para este usuário depois que eles passarem para este nível de risco do usuário. Por exemplo, se você definir a política para alertar sobre o risco médio do usuário e seu usuário John passar para o risco médio, você receberá os usuários em risco detectado e-mail para John. No entanto, você não receberá um segundo usuário em alerta detectado de risco se John então se mover para alto risco ou tiver detecções adicionais de risco.

![Email de usuários em risco detectado](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Configure usuários em alertas detectados de risco

Como administrador, você pode definir:

- **O nível de risco do usuário que desencadeia a geração deste e-mail** - Por padrão, o nível de risco é definido como risco "Alto".
- **Os destinatários deste email** -Por padrão, os destinatários incluem todos os administradores globais. Os administradores globais também podem adicionar outros administradores globais, administradores de segurança, leitores de segurança como destinatários.
   - Opcionalmente, você pode **adicionar e-mails adicionais para receber notificações de alerta** este recurso é uma visualização e os usuários definidos devem ter as permissões apropriadas para visualizar os relatórios vinculados no portal Azure.

Configure os usuários em e-mail de risco no **portal Azure** **o Azure Active Directory** > **Security** > **Identity Protection** > **Usuários em alertas detectados de risco**.

## <a name="weekly-digest-email"></a>Email de resumo semanal

O e-mail de digestão semanal contém um resumo de novas detecções de risco.  
 Ele inclui:

- Usuários em risco
- Atividades suspeitas
- Vulnerabilidades detectadas
- Links para os relatórios relacionados no Identity Protection

![Email de resumo semanal](./media/howto-identity-protection-configure-notifications/400.png)

Por padrão, os destinatários incluem todos os Admins Globais. Os administradores globais também podem adicionar outros administradores globais, administradores de segurança, leitores de segurança como destinatários.

### <a name="configure-weekly-digest-email"></a>Configure o e-mail de digestão semanal

Como administrador, você pode alternar o envio de um e-mail digerir semanalmente e escolher os usuários designados para receber o e-mail.

Configure o e-mail de digestão semanal no **portal Azure** em **azure Active Directory** > **Security** > **Identity Protection** > **Weekly digest**.

## <a name="see-also"></a>Confira também

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
