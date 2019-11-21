---
title: Práticas recomendadas para o Azure Functions
description: Aprenda as práticas recomendadas e padrões para o Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 10/16/2017
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fa85f636233a067713d127938d674b359bd03696
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227377"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Melhore o desempenho e a confiabilidade do Azure Functions

Este artigo fornece orientações para melhorar o desempenho e confiabilidade de seus aplicativos de funções [sem servidor](https://azure.microsoft.com/solutions/serverless/).  

## <a name="general-best-practices"></a>Práticas recomendadas gerais

A seguir, estão as melhores práticas para criar e projetar soluções sem servidor usando o Azure Functions.

### <a name="avoid-long-running-functions"></a>Evite funções grandes de longa duração

As funções grandes de longa duração podem causar problemas de tempo limite inesperados. To learn more about the timeouts for a given hosting plan, see [function app timeout duration](functions-scale.md#timeout). 

A function can become large because of many Node.js dependencies. A importação dessas dependências pode causar aumento no tempo de carregamento resultando em tempos limite inesperados. As dependências são carregadas explícita e implicitamente. Um único módulo carregado pelo seu código pode carregar seus próprios módulos adicionais. 

Sempre que possível, refatore funções grandes em conjuntos menores de funções que funcionem juntos e retornem respostas rápidas. For example, a webhook or HTTP trigger function might require an acknowledgment response within a certain time limit; it's common for webhooks to require an immediate response. Você pode passar o conteúdo do gatilho HTTP para uma fila para ser processado por uma função de gatilho de fila. This approach lets you defer the actual work and return an immediate response.


### <a name="cross-function-communication"></a>Comunicação entre funções

As [Durable Functions](durable/durable-functions-overview.md) e os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) são projetados para gerenciar transições de estado e comunicação entre diversas funções.

If not using Durable Functions or Logic Apps to integrate with multiple functions, it's best to use storage queues for cross-function communication. The main reason is that storage queues are cheaper and much easier to provision than other storage options. 

Mensagens individuais em uma fila de armazenamento estão limitadas ao tamanho de 64 KB. Se você precisar passar mensagens maiores entre as funções, uma fila do Barramento de Serviço do Azure poderá ser usada para dar suporte a mensagens com tamanhos de até 256 KB na camada Standard e até 1 MB na camada Premium.

Tópicos de barramento de serviço são úteis se você precisar de filtragem de mensagens antes do processamento.

Hubs de eventos são úteis para oferecer suporte a comunicações de alto volume.


### <a name="write-functions-to-be-stateless"></a>Grave funções para serem sem estado 

As funções devem ser sem estado e idempotentes se possível. Associe quaisquer informações de estado necessárias a seus dados. Por exemplo, um pedido sendo processado provavelmente teria um membro `state` associado. Uma função pode processar um pedido com base no estado enquanto a função em si permanece sem estado. 

Funções de idempotentes são recomendadas especialmente com gatilhos de timer. For example, if you have something that absolutely must run once a day, write it so it can run anytime during the day with the same results. The function can exit when there's no work for a particular day. Também se uma execução anterior tiver falhado ao concluir, a próxima execução deve continuar de onde a anterior parou.


### <a name="write-defensive-functions"></a>Grave funções defensivas

Suponha que sua função pode encontrar uma exceção a qualquer momento. Projete suas funções com a capacidade de continuar de um ponto de falha anterior durante a próxima execução. Considere um cenário que requeira as seguintes ações:

1. Query for 10,000 rows in a database.
2. Crie uma mensagem de fila para cada uma das linhas para processar ainda mais adiante na linha.
 
Depending on how complex your system is, you may have: involved downstream services behaving badly, networking outages, or quota limits reached, etc. All of these can affect your function at any time. Você precisa para projetar suas funções para estarem preparadas para isso.

Como o seu código reage se ocorrer uma falha após a inserção de 5.000 desses itens em uma fila para processamento? Controle itens em um conjunto concluído. Caso contrário, você pode inseri-los de novo posteriormente. This double-insertion can have a serious impact on your work flow, so [make your functions idempotent](functions-idempotent.md). 

Se um item da fila já tiver sido processado, permita que sua função seja no-op.

Tire proveito de medidas defensivas já fornecidas para componentes usados na plataforma Azure Functions. Por exemplo, consulte **Tratamento de mensagens suspeitas na fila** na documentação de [gatilhos e associações de fila de Armazenamento do Microsoft Azure](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="scalability-best-practices"></a>Melhores práticas de escalabilidade

There are a number of factors that impact how instances of your function app scale. Os detalhes são fornecidos na documentação de [dimensionamento de função](functions-scale.md).  A seguir, estão algumas das melhores práticas para garantir a escalabilidade ideal para um aplicativo de funções.

### <a name="share-and-manage-connections"></a>Gerenciar e compartilhar conexões

Reuse connections to external resources whenever possible.  Veja [como gerenciar conexões no Azure Functions](./manage-connections.md).

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Não misture códigos de teste e de produção no mesmo aplicativo de funções

As funções em um aplicativo de funções compartilham recursos. Por exemplo, a memória é compartilhada. Se você estiver usando um aplicativo de funções em produção, não adicione recursos e funções de teste nele. Ele pode causar sobrecarga inesperada durante a execução de código de produção.

Cuidado com o que você carrega em seus aplicativos de funções de produção. A memória é dividida igualmente entre cada função no aplicativo.

If you have a shared assembly referenced in multiple .NET functions, put it in a common shared folder. Otherwise, you could accidentally deploy multiple versions of the same binary that behave differently between functions.

Don't use verbose logging in production code, which has a negative performance impact.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Usar o código assíncrono, mas evitar chamadas de bloqueio

A programação assíncrona é uma prática recomendada. No entanto, sempre evite fazer referência à propriedade `Result` ou chamar o método `Wait` em um instância `Task`. Essa abordagem pode levar ao esgotamento de thread.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>Receber mensagens em lote sempre que possível

Alguns gatilhos, como o Hub de Eventos, habilitam o recebimento de um lote de mensagens em uma única invocação.  As mensagens em lote têm um desempenho melhor.  É possível configurar o tamanho máximo do lote no arquivo `host.json`, conforme detalhado na [documentação de referência do host.json](functions-host-json.md)

For C# functions, you can change the type to a strongly-typed array.  Por exemplo, em vez de `EventData sensorEvent`, a assinatura do método pode ser `EventData[] sensorEvent`.  For other languages, you'll need to explicitly set the cardinality property in your `function.json` to `many` in order to enable batching [as shown here](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Configurar comportamentos de host para lidar melhor com a simultaneidade

O arquivo `host.json` no aplicativo de funções permite configurar os comportamentos do runtime e do gatilho do host.  Além do envio em lote de comportamentos, é possível gerenciar a simultaneidade de diversos gatilhos. Geralmente, ajustar os valores nessas opções pode ajudar a escalar cada instância adequadamente para as demandas das funções invocadas.

Settings in the host.json file apply across all functions within the app, within a *single instance* of the function. For example, if you had a function app with two HTTP functions and [`maxConcurrentRequests`](functions-bindings-http-webhook.md#hostjson-settings) requests set to 25, a request to either HTTP trigger would count towards the shared 25 concurrent requests.  When that function app is scaled to 10 instances, the two functions effectively allow 250 concurrent requests (10 instances * 25 concurrent requests per instance). 

Other host configuration options are found in the [host.json configuration article](functions-host-json.md).

## <a name="next-steps"></a>Próximos passos

Para saber mais, consulte os recursos a seguir:

* [Como gerenciar conexões no Azure Functions](manage-connections.md)
* [Práticas recomendadas do Serviço de Aplicativo do Azure](../app-service/app-service-best-practices.md)
