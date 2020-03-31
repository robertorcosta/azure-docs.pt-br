---
title: Notificações de e-mail para serviços de domínio do Azure AD | Microsoft Docs
description: Saiba como configurar notificações de e-mail para alertá-lo sobre problemas em um domínio gerenciado do Azure Active Directory Domain Services
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: b9af1792-0b7f-4f3e-827a-9426cdb33ba6
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: article
ms.date: 11/26/2019
ms.author: iainfou
ms.openlocfilehash: 5507579338ad0d87bc6223b56283fe7ed46af7d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77613292"
---
# <a name="configure-email-notifications-for-issues-in-azure-active-directory-domain-services"></a>Configure notificações de e-mail para problemas no Azure Active Directory Domain Services

A saúde de um domínio gerenciado do Azure Active Directory Domain Services (Azure AD DS) é monitorada pela plataforma Azure. A página de estado de saúde no portal Azure mostra quaisquer alertas para o domínio gerenciado. Para garantir que os problemas sejam respondidos em tempo hábil, as notificações de e-mail podem ser configuradas para informar sobre alertas de saúde assim que forem detectados no domínio gerenciado pelo Azure AD DS.

Este artigo mostra como configurar destinatários de notificação de e-mail para um domínio gerenciado pelo Azure AD DS.

## <a name="email-notification-overview"></a>Visão geral da notificação por e-mail

Para alertá-lo sobre problemas com um domínio gerenciado pelo Azure AD DS, você pode configurar notificações de e-mail. Essas notificações de e-mail especificam o domínio gerenciado do Azure AD DS no qual o alerta está presente, além de dar o tempo de detecção e um link para a página de saúde no portal Azure. Em seguida, você pode seguir os conselhos de solução de problemas fornecidos para resolver os problemas.

O exemplo a seguir de notificação de e-mail indica que um aviso ou alerta crítico foi gerado no domínio gerenciado pelo Azure AD DS:

![Exemplo de notificação por email](./media/active-directory-domain-services-alerts/email-alert.png)

> [!WARNING]
> Certifique-se sempre de que o e-mail vem de um remetente microsoft verificado antes de clicar nos links na mensagem. As notificações de `azure-noreply@microsoft.com` e-mail sempre vêm do endereço.

### <a name="why-would-i-receive-email-notifications"></a>Por que eu receberia notificações por email?

O Azure AD DS envia notificações de e-mail para atualizações importantes sobre o domínio gerenciado. Essas notificações são apenas para questões urgentes que impactam o serviço e devem ser tratadas imediatamente. Cada notificação de e-mail é acionada por um alerta no domínio gerenciado pelo Azure AD DS. Os alertas também aparecem no portal Azure e podem ser vistos na [página de saúde Do Azure AD DS][check-health].

O Azure AD DS não envia e-mails para anúncios, atualizações ou fins de vendas.

### <a name="when-will-i-receive-email-notifications"></a>Quando eu receberei notificações por email?

Uma notificação é enviada imediatamente quando um [novo alerta][troubleshoot-alerts] é encontrado em um domínio gerenciado pelo Azure AD DS. Se o alerta não for resolvido, notificações adicionais de e-mail são enviadas como um lembrete a cada quatro dias.

### <a name="who-should-receive-the-email-notifications"></a>Quem deve receber as notificações por email?

A lista de destinatários de e-mail do Azure AD DS deve ser composta por pessoas capazes de administrar e fazer alterações no domínio gerenciado. Esta lista de e-mails deve ser considerada como seus "socorristas" para quaisquer alertas e problemas.

Você pode adicionar até cinco destinatários de e-mails adicionais para notificações de e-mail. Se você quiser mais de cinco destinatários para notificações de e-mail, crie uma lista de distribuição e adicione-a à lista de notificação.

Você também pode optar por ter todos os *administradores globais* do diretório Azure AD e todos os membros do grupo *AAD DC Administrators* recebem notificações por e-mail. O Azure AD DS só envia notificação para até 100 endereços de e-mail, incluindo a lista de administradores globais e administradores AAD DC.

## <a name="configure-email-notifications"></a>Configurar notificações por email

Para rever os destinatários de notificação de e-mail existentes ou adicionar destinatários adicionais, complete as seguintes etapas:

1. No portal Azure, procure e selecione **Azure AD Domain Services**.
1. Selecione o domínio gerenciado pelo Azure AD DS, como *aaddscontoso.com*.
1. No lado esquerdo da janela de recursos Azure AD DS, selecione **Configurações de notificação**. Os destinatários existentes para notificações de e-mail são mostrados.
1. Para adicionar um destinatário de e-mail, digite o endereço de e-mail na tabela de destinatários adicionais.
1. Quando estiver pronto, **selecione Salvar** na navegação de cima para mão.

> [!WARNING]
> Quando você altera as configurações de notificação, as configurações de notificação para todo o domínio gerenciado pelo Azure AD DS são atualizadas, não apenas você mesmo.

## <a name="frequently-asked-questions"></a>Perguntas frequentes

### <a name="i-received-an-email-notification-for-an-alert-but-when-i-logged-on-to-the-azure-portal-there-was-no-alert-what-happened"></a>Recebi uma notificação por email para um alerta, mas quando me conectei ao Portal do Azure não havia nenhum alerta. O que aconteceu?

Se um alerta for resolvido, o alerta será liberado do portal Azure. A razão mais provável é que outra pessoa que recebe notificações de e-mail resolveu o alerta no domínio gerenciado do Azure AD DS, ou foi resolvido automaticamente pela plataforma Azure.

### <a name="why-can-i-not-edit-the-notification-settings"></a>Por que eu não posso editar as configurações de notificação?

Se você não conseguir acessar a página de configurações de notificação no portal Azure, você não terá as permissões para editar o domínio gerenciado pelo Azure AD DS. Você deve entrar em contato com um administrador global para obter permissões para editar o recurso Azure AD DS ou ser removido da lista de destinatários.

### <a name="i-dont-seem-to-be-receiving-email-notifications-even-though-i-provided-my-email-address-why"></a>Apesar de ter fornecido meu endereço de email, não estou recebendo as notificações por email. Por quê?

Verifique sua pasta de spam ou lixo eletrônico para obter a `azure-noreply@microsoft.com`notificação e certifique-se de permitir que o remetente de .

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre a solução de problemas que podem ser relatados, consulte [Alertas resolver em um domínio gerenciado pelo Azure AD DS][troubleshoot-alerts].

<!-- INTERNAL LINKS -->
[check-health]: check-health.md
[troubleshoot-alerts]: troubleshoot-alerts.md
