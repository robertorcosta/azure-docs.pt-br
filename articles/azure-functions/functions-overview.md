---
title: Visão geral do Azure Functions
description: Saiba como o Azure Functions pode ajudar a criar aplicativos sem servidor escalonáveis.
author: craigshoemaker
ms.assetid: 01d6ca9f-ca3f-44fa-b0b9-7ffee115acd4
ms.topic: overview
ms.date: 11/20/2020
ms.author: cshoe
ms.custom: contperfq2
ms.openlocfilehash: 6713c0d45a8b5363122c726d1d31e5c479ba8fff
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/23/2020
ms.locfileid: "95504635"
---
# <a name="introduction-to-azure-functions"></a>Introdução ao Azure Functions

Muitas vezes, criamos sistemas para reagir a uma série de eventos críticos. Se você estiver criando uma API Web, respondendo a alterações no banco de dados, processando fluxos IoT ou até mesmo gerenciando filas de mensagens, cada aplicativo precisa ter uma forma de executar algum código à medida que esses eventos ocorrem.

Para atender a essa necessidade, o Azure Functions fornece "computação sob demanda" – e de duas maneiras significativas.

Primeiro, o Azure Functions permite que você implemente a lógica do seu sistema em blocos de código prontamente disponíveis. Esses blocos de código são denominados "funções". Funções diferentes poderão ser executadas sempre que você precisar responder a eventos críticos.

Em segundo lugar, à medida que as solicitações aumentam, o Azure Functions atende à demanda com tantos recursos e instâncias de função quanto necessário, mas somente enquanto for necessário. Conforme as solicitações caírem, todos os recursos adicionais e instâncias de aplicativo cairão automaticamente.

De onde vêm todos os recursos de computação? O Azure Functions [fornece o máximo ou o mínimo necessário de recursos de computação](./functions-scale.md) para atender à demanda do seu aplicativo.

Fornecer recursos de computação sob demanda é o cerne da [computação sem servidor](https://azure.microsoft.com/solutions/serverless/) no Azure Functions.

## <a name="scenarios"></a>Cenários

Em muitos casos, uma função [integra-se a uma matriz de serviços de nuvem](./functions-triggers-bindings.md) para fornecer implementações ricas em recursos.

Veja a seguir um conjunto comum, _mas não exaustivo_, de cenários para o Azure Functions.

| Se desejar... | então… |
| --- | --- |
| **Criar uma API da Web** | Implemente um ponto de extremidade para seus aplicativos Web usando o [gatilho HTTP](./functions-bindings-http-webhook.md) |
| **Processar uploads de arquivo** | Execute o código quando um arquivo for carregado ou alterado no [armazenamento de blobs](./functions-bindings-storage-blob.md) |
| **Criar um fluxo de trabalho sem servidor** | Encadeie uma série de funções usando [funções duráveis](./durable-functions-overview.md) |
| **Responder a alterações no banco de dados** | Execute a lógica personalizada quando um documento for criado ou atualizado no [Cosmos DB](./functions-bindings-cosmosdb-v2.md) |
| **Executar tarefas agendadas** | Execute o código em [horários definidos](./functions-bindings-timer.md) |
| **Criar sistemas de fila de mensagens confiáveis** | Processe filas de mensagens usando o [Armazenamento de Filas](./functions-bindings-storage-queue.md), o [Barramento de Serviço](./functions-bindings-service-bus.md) ou os [Hubs de Eventos](./functions-bindings-event-hubs.md) |
| **Analisar fluxos de dados de IoT** | Colete e processe [dados de dispositivos IoT](./functions-bindings-event-iot.md) |
| **Processar dados em tempo real** | Use o [Functions e o Signal R](./functions-bindings-signalr-service.md) para responder aos dados no momento |

Ao criar suas funções, você tem as seguintes opções e recursos disponíveis:

- **Usar a linguagem de sua preferência**: escreva funções em [C#, Java, JavaScript, PowerShell ou Python](./supported-languages.md) ou use um [manipulador personalizado](./functions-custom-handlers.md) para usar praticamente qualquer outra linguagem.

- **Automatizar a implantação**: desde uma abordagem baseada em ferramentas até o uso de pipelines externos, há uma [infinidade de opções de implantação](./functions-deployment-technologies.md) disponível.

- **Solucionar problemas de uma função**: use [ferramentas de monitoramento](./functions-monitoring.md) e [estratégias de teste](./functions-test-a-function.md) para obter informações sobre seus aplicativos.

- **Opções de preços flexíveis**: com o plano de [Consumo](./pricing.md), você só pagará enquanto suas funções estiverem em execução, enquanto os planos [Premium](./pricing.md) e do [Serviço de Aplicativo](./pricing.md) oferecerem recursos para necessidades especializadas.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Comece a usar por meio de lições, exemplos e tutoriais interativos](./functions-get-started.md)
