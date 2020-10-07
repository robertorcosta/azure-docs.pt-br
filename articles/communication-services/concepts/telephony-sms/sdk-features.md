---
title: Visão geral da biblioteca de clientes de SMS para os Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Apresenta uma visão geral da biblioteca de clientes de SMS e suas ofertas.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 4077bbe0a98f6b7788af9b6c44f73abbc936c6aa
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332231"
---
# <a name="sms-client-library-overview"></a>Visão geral da biblioteca de clientes de SMS

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

As bibliotecas de clientes de SMS dos Serviços de Comunicação do Azure podem ser usadas para adicionar mensagens SMS aos aplicativos.

## <a name="sms-client-library-capabilities"></a>Funcionalidades da biblioteca de clientes de SMS

A lista a seguir apresenta o conjunto de recursos que estão disponíveis atualmente em nossas bibliotecas de cliente.

| Grupo de recursos | Funcionalidade                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Principais funcionalidades | Enviar e receber mensagens SMS </br> *Emojis Unicode com suporte*                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Receber relatórios de entrega para mensagens enviadas                                            | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Todos os conjuntos de caracteres (suporte a idioma/Unicode)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Suporte para mensagens longas (até 2.048 caracteres)                                           | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Concatenação automática de mensagens longas                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
| Eventos            | Usar a Grade de Eventos para configurar webhooks para receber mensagens de entrada e relatórios de entrega | ✔️   | ✔️    | ✔️    | ✔️      |
| Número do telefone      | Números de chamada gratuita                                                                     | ✔️   | ✔️    | ✔️    | ✔️      |
| Regulatório        | Tratamento de recusa                                                                      | ✔️   | ✔️    | ✔️    | ✔️      |
| Monitoramento        | Monitorar o uso de mensagens enviadas e recebidas                                          | ✔️   | ✔️    | ✔️    | ✔️      |
| Chamada PSTN      | Adicionar funcionalidades de chamada de PSTN ao seu número gratuito habilitado para SMS (versão prévia privada)                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução ao envio de SMS](../../quickstarts/telephony-sms/send.md)

Os seguintes documentos podem ser do seu interesse:

- Familiarize-se com os conceitos gerais de [SMS](../telephony-sms/concepts.md)
- Obter um [número de telefone](../../quickstarts/telephony-sms/get-phone-number.md) compatível com SMS
- [Planejar sua solução SMS](../telephony-sms/plan-solution.md)
