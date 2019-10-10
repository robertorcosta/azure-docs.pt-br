---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/23/2019
ms.author: glenga
ms.openlocfilehash: 64a1062a8b73768a334277eafb663a7d2d5dd59a
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71838950"
---
Atributos de associação são definidos diretamente no arquivo function.json. Dependendo do tipo de associação, outras propriedades podem ser necessárias. A [configuração de saída da fila](../articles/azure-functions/functions-bindings-storage-queue.md#output---configuration) descreve os campos obrigatórios para uma associação de fila do Armazenamento do Azure. A extensão facilita a inclusão de associações no arquivo function.json. 

Para criar uma associação, clique com o botão direito do mouse (Ctrl+clique no macOS) no arquivo `function.json` na pasta HttpTrigger e escolha **Adicionar associação...** . Siga os prompts para definir as seguintes propriedades de associação para a nova associação:

| Prompt | Valor | DESCRIÇÃO |
| -------- | ----- | ----------- |
| **Selecionar direção de associação** | `out` | A associação é uma associação de saída. |
| **Selecionar associação com direção...** | `Azure Queue Storage` | A associação é uma associação de fila do Armazenamento do Azure. |
| **O nome usado para identificar essa associação em seu código** | `msg` | Nome que identifica o parâmetro de associação referenciado em seu código. |
| **A fila à qual a mensagem será enviada** | `outqueue` | O nome da fila na qual a associação escreve. Quando o *queueName* não existe, a associação o cria no primeiro uso. |
| **Selecione a configuração de "local.setting.json"** | `AzureWebJobsStorage` | O nome de uma configuração de aplicativo que contém a cadeia de conexão da Conta de armazenamento. A configuração `AzureWebJobsStorage` contém a cadeia de conexão para a Conta de armazenamento criada com o aplicativo de funções. |

Uma associação é incluída na matriz `bindings` no seu arquivo function.json, que agora deve se parecer com o seguinte exemplo:

```json
{
   ...

  "bindings": [
    {
      "authLevel": "function",
      "type": "httpTrigger",
      "direction": "in",
      "name": "req",
      "methods": [
        "get",
        "post"
      ]
    },
    {
      "type": "http",
      "direction": "out",
      "name": "$return"
    },
    {
      "type": "queue",
      "direction": "out",
      "name": "msg",
      "queueName": "outqueue",
      "connection": "AzureWebJobsStorage"
    }
  ]
}
```