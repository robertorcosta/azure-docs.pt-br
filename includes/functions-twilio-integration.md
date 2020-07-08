---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: 467e09f9bd46df6d888d82f2961c5aed9cca4ab5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "67171723"
---
Este exemplo envolve o uso do serviço [Twilio](https://www.twilio.com/) para enviar mensagens SMS a um telefone celular. O Azure Functions já tem suporte para Twilio por meio da [Associação com o Twilio](https://docs.microsoft.com/azure/azure-functions/functions-bindings-twilio) e o exemplo usa esse recurso.

A primeira coisa de que você precisa é uma conta do Twilio. É possível criar uma gratuitamente em https://www.twilio.com/try-twilio. Quando tiver uma conta, adicione as três seguintes **configurações de aplicativo** ao seu aplicativo de função.

| Nome da configuração do aplicativo | Descrição do valor |
| - | - |
| **TwilioAccountSid**  | A SID de sua conta do Twilio |
| **TwilioAuthToken**   | O token de autenticação de sua conta do Twilio |
| **TwilioPhoneNumber** | O número de telefone associado à sua conta do Twilio. Ele é usado para enviar mensagens SMS. |