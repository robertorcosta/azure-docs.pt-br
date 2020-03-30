---
title: Práticas recomendadas para o Azure Functions
description: Aprenda as práticas recomendadas e padrões para o Azure Functions.
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.topic: conceptual
ms.date: 12/17/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a41a5828a82d81c5e7e8749fee70cd15e17bb9d0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277772"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Melhore o desempenho e a confiabilidade do Azure Functions

Este artigo fornece orientações para melhorar o desempenho e confiabilidade de seus aplicativos de funções [sem servidor](https://azure.microsoft.com/solutions/serverless/).  

## <a name="general-best-practices"></a>Práticas recomendadas gerais

A seguir, estão as melhores práticas para criar e projetar soluções sem servidor usando o Azure Functions.

### <a name="avoid-long-running-functions"></a>Evite funções grandes de longa duração

As funções grandes de longa duração podem causar problemas de tempo limite inesperados. Para saber mais sobre os intervalos para um determinado plano de hospedagem, consulte a [duração do tempo de tempo do aplicativo de função](functions-scale.md#timeout). 

Uma função pode se tornar grande por causa de muitas dependências do Node.js. A importação dessas dependências pode causar aumento no tempo de carregamento resultando em tempos limite inesperados. As dependências são carregadas explícita e implicitamente. Um único módulo carregado pelo seu código pode carregar seus próprios módulos adicionais. 

Sempre que possível, refatore funções grandes em conjuntos menores de funções que funcionem juntos e retornem respostas rápidas. Por exemplo, uma função de webhook ou gatilho HTTP pode exigir uma resposta de reconhecimento dentro de um determinado limite de tempo; é comum que os webhooks exijam uma resposta imediata. Você pode passar o conteúdo do gatilho HTTP para uma fila para ser processado por uma função de gatilho de fila. Esta abordagem permite que você adie o trabalho real e retorne uma resposta imediata.


### <a name="cross-function-communication"></a>Comunicação entre funções

As [Durable Functions](durable/durable-functions-overview.md) e os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) são projetados para gerenciar transições de estado e comunicação entre diversas funções.

Se não usar funções duráveis ou aplicativos lógicos para se integrar a várias funções, é melhor usar filas de armazenamento para comunicação entre funções. A principal razão é que as filas de armazenamento são mais baratas e muito mais fáceis de provisão do que outras opções de armazenamento. 

Mensagens individuais em uma fila de armazenamento estão limitadas ao tamanho de 64 KB. Se você precisar passar mensagens maiores entre as funções, uma fila do Barramento de Serviço do Azure poderá ser usada para dar suporte a mensagens com tamanhos de até 256 KB na camada Standard e até 1 MB na camada Premium.

Tópicos de barramento de serviço são úteis se você precisar de filtragem de mensagens antes do processamento.

Hubs de eventos são úteis para oferecer suporte a comunicações de alto volume.


### <a name="write-functions-to-be-stateless"></a>Grave funções para serem sem estado 

As funções devem ser sem estado e idempotentes se possível. Associe quaisquer informações de estado necessárias a seus dados. Por exemplo, um pedido sendo processado provavelmente teria um membro `state` associado. Uma função pode processar um pedido com base no estado enquanto a função em si permanece sem estado. 

Funções de idempotentes são recomendadas especialmente com gatilhos de timer. Por exemplo, se você tem algo que absolutamente deve ser executado uma vez por dia, escreva-o para que ele possa ser executado a qualquer momento durante o dia com os mesmos resultados. A função pode sair quando não há trabalho para um dia específico. Também se uma execução anterior tiver falhado ao concluir, a próxima execução deve continuar de onde a anterior parou.


### <a name="write-defensive-functions"></a>Grave funções defensivas

Suponha que sua função pode encontrar uma exceção a qualquer momento. Projete suas funções com a capacidade de continuar de um ponto de falha anterior durante a próxima execução. Considere um cenário que requeira as seguintes ações:

1. Consulta por 10.000 linhas em um banco de dados.
2. Crie uma mensagem de fila para cada uma das linhas para processar ainda mais adiante na linha.
 
Dependendo do quão complexo o seu sistema é, você pode ter: serviços a jusante envolvidos se comportando mal, paralisações de rede ou limites de cotas atingidos, etc. Tudo isso pode afetar sua função a qualquer momento. Você precisa para projetar suas funções para estarem preparadas para isso.

Como o seu código reage se ocorrer uma falha após a inserção de 5.000 desses itens em uma fila para processamento? Controle itens em um conjunto concluído. Caso contrário, você pode inseri-los de novo posteriormente. Essa dupla inserção pode ter um impacto sério no seu fluxo de trabalho, por isso [torne suas funções idempotentes.](functions-idempotent.md) 

Se um item da fila já tiver sido processado, permita que sua função seja no-op.

Tire proveito de medidas defensivas já fornecidas para componentes usados na plataforma Azure Functions. Por exemplo, consulte **Tratamento de mensagens suspeitas na fila** na documentação de [gatilhos e associações de fila de Armazenamento do Microsoft Azure](functions-bindings-storage-queue-trigger.md#poison-messages). 

## <a name="scalability-best-practices"></a>Melhores práticas de escalabilidade

Há uma série de fatores que impactam a forma como as instâncias do seu aplicativo de função escalam. Os detalhes são fornecidos na documentação de [dimensionamento de função](functions-scale.md).  A seguir, estão algumas das melhores práticas para garantir a escalabilidade ideal para um aplicativo de funções.

### <a name="share-and-manage-connections"></a>Gerenciar e compartilhar conexões

Reutilizar conexões com recursos externos sempre que possível. Veja [como gerenciar conexões no Azure Functions](./manage-connections.md).

### <a name="avoid-sharing-storage-accounts"></a>Evite compartilhar contas de armazenamento

Quando você cria um aplicativo de função, você deve associá-lo a uma conta de armazenamento. A conexão de conta de armazenamento é mantida na configuração do [aplicativo AzureWebJobsStorage](./functions-app-settings.md#azurewebjobsstorage). 

[!INCLUDE [functions-shared-storage](../../includes/functions-shared-storage.md)]

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Não misture códigos de teste e de produção no mesmo aplicativo de funções

As funções em um aplicativo de funções compartilham recursos. Por exemplo, a memória é compartilhada. Se você estiver usando um aplicativo de funções em produção, não adicione recursos e funções de teste nele. Ele pode causar sobrecarga inesperada durante a execução de código de produção.

Cuidado com o que você carrega em seus aplicativos de funções de produção. A memória é dividida igualmente entre cada função no aplicativo.

Se você tiver um conjunto compartilhado referenciado em várias funções .NET, coloque-o em uma pasta compartilhada comum. Caso contrário, você poderia acidentalmente implantar várias versões do mesmo binário que se comportam de forma diferente entre as funções.

Não use o registro verbose no código de produção, o que tem um impacto negativo no desempenho.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Usar o código assíncrono, mas evitar chamadas de bloqueio

A programação assíncrona é uma prática recomendada, especialmente quando as operações de I/O de bloqueio estão envolvidas.

Em C#, evite sempre `Result` referenciar `Wait` a `Task` propriedade ou chamar o método em uma instância. Essa abordagem pode levar ao esgotamento de thread.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="use-multiple-worker-processes"></a>Use vários processos de trabalhadores

Por padrão, qualquer instância host para Funções usa um processo de um único trabalhador. Para melhorar o desempenho, especialmente com tempos de execução de threadúnico como python, use o [FUNCTIONS_WORKER_PROCESS_COUNT](functions-app-settings.md#functions_worker_process_count) para aumentar o número de processos de trabalhadores por host (até 10). As funções do Azure, então, tentam distribuir uniformemente invocações de funções simultâneas entre esses trabalhadores. 

O FUNCTIONS_WORKER_PROCESS_COUNT se aplica a cada host que as Funções criam ao dimensionar seu aplicativo para atender à demanda. 

### <a name="receive-messages-in-batch-whenever-possible"></a>Receber mensagens em lote sempre que possível

Alguns gatilhos, como o Hub de Eventos, habilitam o recebimento de um lote de mensagens em uma única invocação.  As mensagens em lote têm um desempenho melhor.  É possível configurar o tamanho máximo do lote no arquivo `host.json`, conforme detalhado na [documentação de referência do host.json](functions-host-json.md)

Para funções C#, você pode alterar o tipo para uma matriz fortemente digitada.  Por exemplo, em vez de `EventData sensorEvent`, a assinatura do método pode ser `EventData[] sensorEvent`.  Para outras línguas, você precisará explicitamente definir a `function.json` propriedade `many` cardinalidade em seu para, a fim de habilitar o [loteamento como mostrado aqui](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Configurar comportamentos de host para lidar melhor com a simultaneidade

O arquivo `host.json` no aplicativo de funções permite configurar os comportamentos do runtime e do gatilho do host.  Além do envio em lote de comportamentos, é possível gerenciar a simultaneidade de diversos gatilhos. Geralmente, ajustar os valores nessas opções pode ajudar a escalar cada instância adequadamente para as demandas das funções invocadas.

As configurações no arquivo host.json aplicam-se em todas as funções dentro do aplicativo, dentro de uma *única instância* da função. Por exemplo, se você tivesse um aplicativo [`maxConcurrentRequests`](functions-bindings-http-webhook-output.md#hostjson-settings) de função com duas funções HTTP e solicitações definidas como 25, uma solicitação para qualquer gatilho HTTP contaria para as 25 solicitações simultâneas compartilhadas.  Quando esse aplicativo de função é dimensionado para 10 instâncias, as duas funções efetivamente permitem 250 solicitações simultâneas (10 instâncias * 25 solicitações simultâneas por instância). 

Outras opções de configuração do host são encontradas no [artigo de configuração host.json](functions-host-json.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais, consulte os recursos a seguir:

* [Como gerenciar conexões no Azure Functions](manage-connections.md)
* [Práticas recomendadas do Serviço de Aplicativo do Azure](../app-service/app-service-best-practices.md)
