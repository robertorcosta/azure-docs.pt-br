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
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/22/2019
ms.locfileid: "74382164"
---
# <a name="azure-active-directory-identity-protection-notifications"></a>Notificações do Azure Active Directory Identity Protection

Azure AD Identity Protection sends two types of automated notification emails to help you manage user risk and risk detections:

- Email de usuários em risco detectado
- Email de resumo semanal

Este artigo fornece uma visão geral de ambos os emails de notificação.

## <a name="users-at-risk-detected-email"></a>Email de usuários em risco detectado

Em resposta a uma conta detectada em risco, o Azure AD Identity Protection gera um alerta de email com **Usuários em risco detectado** como assunto. O email inclui um link para o relatório **[Usuários sinalizados por risco](../reports-monitoring/concept-user-at-risk.md)** . Como prática recomendada, você deve investigar imediatamente os usuários em risco.

The configuration for this alert allows you to specify at what user risk level you want the alert to be generated. The email will be generated when the user's risk level reaches what you have specified; however, you will not receive new users at risk detected email alerts for this user after they move to this user risk level. For example, if you set the policy to alert on medium user risk and your user John moves to medium risk, you will receive the users at risk detected email for John. However, you will not receive a second user at risk detected alert if John then moves to high risk or has additional risk detections.

![Email de usuários em risco detectado](./media/howto-identity-protection-configure-notifications/01.png)

### <a name="configure-users-at-risk-detected-alerts"></a>Configure users at risk detected alerts

Como administrador, você pode definir:

- **The user risk level that triggers the generation of this email** - By default, the risk level is set to “High” risk.
- **Os destinatários deste email** -Por padrão, os destinatários incluem todos os administradores globais. Os administradores globais também podem adicionar outros administradores globais, administradores de segurança, leitores de segurança como destinatários.
   - Optionally you can **Add additional emails to receive alert notifications** this feature is a preview and users defined must have the appropriate permissions to view the linked reports in the Azure portal.

Configure the users at risk email in the **Azure portal** under **Azure Active Directory** > **Security** > **Identity Protection** > **Users at risk detected alerts**.

## <a name="weekly-digest-email"></a>Email de resumo semanal

The weekly digest email contains a summary of new risk detections.  
Ele inclui:

- Usuários em risco
- Atividades suspeitas
- Vulnerabilidades detectadas
- Links para os relatórios relacionados no Identity Protection

![Email de resumo semanal](./media/howto-identity-protection-configure-notifications/400.png)

By default, recipients include all Global Admins. Os administradores globais também podem adicionar outros administradores globais, administradores de segurança, leitores de segurança como destinatários.

### <a name="configure-weekly-digest-email"></a>Configure weekly digest email

As an administrator, you can switch sending a weekly digest email on or off and choose the users assigned to receive the email.

Configure the weekly digest email in the **Azure portal** under **Azure Active Directory** > **Security** > **Identity Protection** > **Weekly digest**.

## <a name="see-also"></a>Consulte

- [Azure Active Directory Identity Protection](../active-directory-identityprotection.md)
