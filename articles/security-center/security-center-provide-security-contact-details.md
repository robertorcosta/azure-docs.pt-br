---
title: Fornecer detalhes de contato de segurança na Central de Segurança do Azure | Microsoft Docs
description: Este documento mostra como fornecer detalhes de contato de segurança na Central de Segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2019
ms.author: memildin
ms.openlocfilehash: 64a9600a3014f7e85a7f924d38882bfadaf631db
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387811"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Fornecer detalhes de contato de segurança na Central de segurança do Azure
A Central de Segurança do Azure recomendará que você forneça detalhes de contato de segurança para sua assinatura do Azure se ainda não fez isso. Essas informações serão usadas pela Microsoft para contatá-lo se o MSRC (Microsoft Security Response Center) descobrir que os dados do cliente têm sido acessados por uma pessoa não autorizada ou ilegal. O MSRC executa determinado monitoramento de segurança da rede e da infraestrutura do Azure e recebe reclamações de inteligência e abuso de ameaça de terceiros.

Uma notificação por email é enviada na primeira ocorrência diária de um alerta e apenas para alertas de gravidade alta. As preferências de email só podem ser configuradas para políticas de assinatura. Grupos de recursos dentro de uma assinatura herdarão essas configurações. Os alertas estão disponíveis apenas no nível Padrão do Azure Security Center.

Notificações de email de alerta são enviadas:
- Para um único destinatário de email por tipo de alerta por dia  
- Não mais do que 3 mensagens de email são enviadas para um único destinatário em um único dia
- Cada mensagem de email contém um único alerta, não uma agregação de alertas
- Somente para alertas de gravidade alta

> [!TIP]
> Para alertas com outros níveis de gravidade, crie uma [automação de fluxo de trabalho](workflow-automation.md) para usar um App Lógico que enviará e-mails para o pessoal relevante.
 
Por exemplo, se uma mensagem de email já foi enviada para alertá-lo sobre um ataque de RDP, você não receberá outra mensagem de email sobre um ataque de RDP no mesmo dia, mesmo se outro alerta for disparado. 

> [!NOTE]
> Este documento apresenta o serviço usando uma implantação de exemplo.  Ela não é um guia passo a passo.

## <a name="set-up-email-notifications-for-alerts"></a>Configure notificações de e-mail para alertas<a name="email"></a>

1. Abra a página **de notificações de e-mail:**

    - Para alertas, abra **as configurações de preços &,** selecione a assinatura relevante e selecione **notificações de e-mail**.

    - Se você estiver implementando uma recomendação, então **recomendações,** selecione **Fornecer detalhes de contato de segurança,** selecione a assinatura do Azure para fornecer informações de contato. Isso abre **Notificações por email**.

   ![Fornecer detalhes de contato de segurança][2]

1. Insira o endereço de email de contato de segurança ou endereços separados por vírgulas. Não há limite para o número de endereços de e-mail que você pode inserir.

1. Para receber emails sobre alertas de gravidade alta, ative a opção **Enviar-me emails sobre alertas**. Para outros níveis de gravidade, use um App Lógico conforme explicado na [automação do fluxo de trabalho.](workflow-automation.md)

1. Você pode enviar notificações de e-mail para proprietários de assinaturas (administrador de serviços clássicos e co-administradores, além da função de proprietário do RBAC no escopo da assinatura).

1. Para aplicar as informações de contato de segurança à sua assinatura, **selecione Salvar**.

## <a name="see-also"></a>Confira também
Para saber mais sobre a Central de Segurança, confira o seguinte:

* [Definindo políticas de segurança no Azure Security Center](tutorial-security-policy.md) -- Saiba como configurar políticas de segurança para suas assinaturas e grupos de recursos do Azure.
* [Gerenciamento de recomendações de segurança no Azure Security Center](security-center-recommendations.md) -- Saiba como as recomendações ajudam você a proteger seus recursos do Azure.
* [Monitoramento de segurança no Azure Security Center](security-center-monitoring.md) -- Saiba como monitorar a saúde de seus recursos do Azure.
* [Gerenciamento e resposta a alertas de segurança no Azure Security Center](security-center-managing-and-responding-alerts.md) -- Saiba como gerenciar e responder a alertas de segurança.
* [Monitoramento de soluções de parceiros com a Central de Segurança do Azure](security-center-partner-solutions.md) – saiba como monitorar o status de integridade de suas soluções de parceiro.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
