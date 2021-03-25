---
title: Visão geral da biblioteca de clientes de SMS para os Serviços de Comunicação do Azure
titleSuffix: An Azure Communication Services concept document
description: Apresenta uma visão geral da biblioteca de clientes de SMS e suas ofertas.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 11ab634ed4b25c5fd8c0079263094c393e9dcbe6
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103496585"
---
# <a name="sms-client-library-overview"></a>Visão geral da biblioteca de clientes de SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]


[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

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
| Chamada PSTN      | Adicionar funcionalidades de chamada PSTN ao seu número de chamada gratuita habilitado para SMS                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução ao envio de SMS](../../quickstarts/telephony-sms/send.md)

Os seguintes documentos podem ser do seu interesse:

- Familiarize-se com os conceitos gerais de [SMS](../telephony-sms/concepts.md)
- Obter um [número de telefone](../../quickstarts/telephony-sms/get-phone-number.md) compatível com SMS
- [Tipos de número de telefone nos Serviços de Comunicação do Azure](../telephony-sms/plan-solution.md)
