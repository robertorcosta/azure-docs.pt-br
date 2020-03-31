---
title: Tipos de funções em funções duráveis do Azure
description: Conheça os tipos de funções e funções que suportam a comunicação função-a-função em uma orquestração de funções duráveis em Funções Azure.
author: cgillum
ms.topic: conceptual
ms.date: 08/22/2019
ms.author: azfuncdf
ms.openlocfilehash: 35ef9d8731e169e890f5985ce01215fec5d6e3de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277876"
---
# <a name="durable-functions-types-and-features"></a>Tipos e recursos de funções duráveis

As Durable Functions são uma extensão do [Azure Functions](../functions-overview.md). Você pode usar Funções Duráveis para orquestração estatal de execução de funções. Um aplicativo de função durável é uma solução que é composta de diferentes funções do Azure. As funções podem desempenhar diferentes funções em uma orquestração de função durável. 

Atualmente, existem quatro tipos de funções duráveis nas funções do Azure: atividade, orquestrador, entidade e cliente. O resto desta seção entra em mais detalhes sobre os tipos de funções envolvidas em uma orquestração.

## <a name="orchestrator-functions"></a>Funções de orquestrador

As funções do orquestrador descrevem como as ações são executadas e a ordem em que as ações são executadas. As funções do orquestrador descrevem a orquestração em código (C# ou JavaScript) como mostrado nos [padrões de aplicação de Funções Duráveis](durable-functions-overview.md#application-patterns). Uma orquestração pode ter muitos tipos diferentes de ações, incluindo [funções](#activity-functions)de atividade, [suborquestrações,](durable-functions-orchestrations.md#sub-orchestrations) [espera por eventos externos,](durable-functions-orchestrations.md#external-events) [HTTP](durable-functions-http-features.md)e [timers.](durable-functions-orchestrations.md#durable-timers) As funções orquestradoras também podem interagir com [as funções da entidade.](#entity-functions)

> [!NOTE]
> As funções do orquestrador são escritas usando código comum, mas há requisitos rigorosos sobre como escrever o código. Especificamente, o código de função do orquestrador deve ser *determinístico.* Não seguir esses requisitos de determinismo pode fazer com que as funções orquestradoras não sejam executadas corretamente. Informações detalhadas sobre esses requisitos e como contorná-los podem ser encontradas no tópico [de restrições de código.](durable-functions-code-constraints.md)

Para obter informações mais detalhadas sobre funções orquestradoras e suas características, consulte o artigo [de orquestrações duráveis.](durable-functions-orchestrations.md)

## <a name="activity-functions"></a>Funções de atividade

As funções de atividade são a unidade básica de trabalho em uma orquestração de função durável. Funções de atividade são as funções e tarefas que são orquestradas no processo. Por exemplo, você pode criar uma função orquestradora para processar uma ordem. As tarefas envolvem verificar o inventário, cobrar o cliente e criar uma remessa. Cada tarefa seria uma função de atividade separada. Essas funções de atividade podem ser executadas em série, em paralelo, ou alguma combinação de ambas.

Ao contrário das funções orquestradoras, as funções de atividade não são restritas no tipo de trabalho que você pode fazer nelas. As funções de atividade são frequentemente usadas para fazer chamadas de rede ou executar operações intensivas de CPU. Uma função de atividade também pode retornar os dados de volta à função orquestradora. O Quadro de Tarefas Duráveis garante que cada função de atividade chamada será executada *pelo menos uma vez* durante a execução de uma orquestração.

> [!NOTE]
> Como as funções de atividade só garantem *pelo menos uma vez* a execução, recomendamos que você torne sua lógica de função de atividade *impotente* sempre que possível.

Use um [gatilho de atividade](durable-functions-bindings.md#activity-trigger) para definir uma função de atividade. As funções .NET recebem um `DurableActivityContext` parâmetro. Você também pode vincular o gatilho a qualquer outro objeto serializador JSON para passar em entradas para a função. Em JavaScript, você pode acessar `<activity trigger binding name>` uma entrada através da propriedade no [ `context.bindings` objeto](../functions-reference-node.md#bindings). As funções de atividade só podem ter um único valor passado para elas. Para passar vários valores, você deve usar tuplas, matrizes ou tipos complexos.

> [!NOTE]
> Você pode ativar uma função de atividade apenas a partir de uma função orquestradora.

## <a name="entity-functions"></a>Funções de entidade

As funções da entidade definem operações para leitura e atualização de pequenos pedaços de estado. Muitas vezes nos referimos a essas entidades estatais como *entidades duráveis.* Como as funções de orquestrador, as funções de entidade são funções com um tipo de gatilho especial, o *gatilho de entidade*. Eles também podem ser invocados a partir de funções do cliente ou de funções orquestradoras. Ao contrário das funções orquestradoras, as funções da entidade não possuem restrições específicas de código. As funções de entidade também gerenciam o estado explicitamente, em vez de representar implicitamente o estado por meio do fluxo de controle.

> [!NOTE]
> As funções de entidade e a funcionalidade relacionada estão disponíveis apenas nas Durable Functions 2.0 e superior.

Para obter mais informações sobre as funções da entidade, consulte o artigo [Entidades Duráveis.](durable-functions-entities.md)

## <a name="client-functions"></a>Funções do cliente

As funções orquestradoras são acionadas por uma [vinculação do gatilho de orquestração](durable-functions-bindings.md#orchestration-trigger) e as funções da entidade são acionadas por uma [vinculação de gatilho de entidade](durable-functions-bindings.md#entity-trigger). Ambos os gatilhos funcionam reagindo a mensagens que são enfileiradas em um [centro de tarefas](durable-functions-task-hubs.md). A principal maneira de entregar essas mensagens é usando um [vinculação de cliente orquestrador](durable-functions-bindings.md#orchestration-client) ou uma [vinculação](durable-functions-bindings.md#entity-client) de cliente de entidade dentro de uma *função do cliente*. Qualquer função não orquestradora pode ser uma *função do cliente.* Por exemplo, você pode acionar o orquestrador a partir de uma função acionada pelo HTTP, uma função ativada pelo Azure Event Hub, etc. O que torna uma função uma *função cliente* é o uso da vinculação de saída de cliente durável.

> [!NOTE]
> Ao contrário de outros tipos de funções, as funções orquestradora e de entidade não podem ser acionadas diretamente usando os botões no Portal Azure. Se você quiser testar uma função orquestradorou ou entidade no Portal Azure, você deve executar uma *função de cliente* que inicia uma função orquestradora ou entidade como parte de sua implementação. Para a experiência de teste mais simples, recomenda-se uma função *de gatilho manual.*

Além de desencadear funções orquestradoras ou de entidade, a vinculação *de cliente durável* pode ser usada para interagir com orquestrações e entidades em execução. Por exemplo, orquestrações podem ser consultadas, encerradas e podem ter eventos levantados a eles. Para obter mais informações sobre a gestão de orquestrações e entidades, consulte o artigo [de gerenciamento de instâncias.](durable-functions-instance-management.md)

## <a name="next-steps"></a>Próximas etapas

Para começar, crie sua primeira função durável em [C#](durable-functions-create-first-csharp.md) ou [JavaScript](quickstart-js-vscode.md).

> [!div class="nextstepaction"]
> [Leia mais sobre orquestrações de Funções Duráveis](durable-functions-orchestrations.md)