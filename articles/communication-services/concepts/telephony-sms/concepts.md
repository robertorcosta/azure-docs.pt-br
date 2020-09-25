---
title: Conceitos de SMS nos Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre os conceitos de SMS dos Serviços de Comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e5cfc1e27bae10a1c67e4506afe9db825664785f
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943126"
---
# <a name="sms-concepts"></a>Conceitos de SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

Os Serviços de Comunicação do Azure permitem que você envie e receba mensagens de texto SMS usando as bibliotecas de cliente SMS dos Serviços de Comunicação. Essas bibliotecas de cliente podem ser usadas para dar suporte a cenários de atendimento ao cliente, lembretes de compromissos, autenticação de dois fatores e outras necessidades de comunicação em tempo real. O recurso Serviços de Comunicação de SMS permite que você envie mensagens de maneira confiável enquanto expõe as informações de taxa de envio e resposta relativas às suas campanhas.

Os principais recursos das bibliotecas de clientes de SMS dos Serviços de Comunicação do Azure incluem:

-  Experiência de configuração **simples** para adicionar funcionalidade de SMS aos seus aplicativos.
- Suporte para mensagem de **alta velocidade** em números de chamadas gratuitas para casos de uso A2P (aplicativo para pessoa) no Estados Unidos.
- Conversas **de duas vias** para oferecer suporte a cenários como suporte ao cliente, alertas e lembretes de compromissos.
- **Entrega confiável** com relatórios de entrega em tempo real para mensagens enviadas do seu aplicativo.
- **Análises** para acompanhar seus padrões de uso e a participação do cliente.
- Suporte para processamento de **recusa** para detectar e respeitar automaticamente as aceitações de números de chamadas gratuitas. Os Serviços de Comunicação detectam mensagens STOP e START e envia as seguintes respostas padrão para os usuários finais: 
  - STOP – *"Você cancelou com sucesso a assinatura de mensagens deste número. Responda START para assinar novamente."*
  - START – *"Você assinou novamente as mensagens deste número. Responda STOP para cancelar a assinatura."*
  - As mensagens STOP e START serão retransmitidas para você. Os Serviços de Comunicação do Azure incentivam você a monitorar e implementar essas recusas para garantir que mais nenhuma mensagem seja enviada aos destinatários que recusaram suas comunicações.


## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução ao envio de SMS](../../quickstarts/telephony-sms/send.md)

Os seguintes documentos podem ser do seu interesse:

- Familiarize-se com a [biblioteca de clientes de SMS](../telephony-sms/sdk-features.md)
- Obter um [número de telefone](../../quickstarts/telephony-sms/get-phone-number.md) compatível com SMS
- [Planejar sua solução SMS](../telephony-sms/plan-solution.md)
