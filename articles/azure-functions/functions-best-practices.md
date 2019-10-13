---
title: Práticas recomendadas para o Azure Functions | Microsoft Docs
description: Aprenda as práticas recomendadas e padrões para o Azure Functions.
author: ggailey777
manager: gwallace
ms.assetid: 9058fb2f-8a93-4036-a921-97a0772f503c
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/16/2017
ms.author: glenga
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ad2f56388b49692d799202d06ed3dc0123f272e5
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/13/2019
ms.locfileid: "72294366"
---
# <a name="optimize-the-performance-and-reliability-of-azure-functions"></a>Melhore o desempenho e a confiabilidade do Azure Functions

Este artigo fornece orientações para melhorar o desempenho e confiabilidade de seus aplicativos de funções [sem servidor](https://azure.microsoft.com/solutions/serverless/). 

## <a name="general-best-practices"></a>Práticas recomendadas gerais

A seguir, estão as melhores práticas para criar e projetar soluções sem servidor usando o Azure Functions.

### <a name="avoid-long-running-functions"></a>Evite funções grandes de longa duração

As funções grandes de longa duração podem causar problemas de tempo limite inesperados. Para saber mais sobre os tempos limite de um determinado plano de hospedagem, confira [duração do tempo limite do aplicativo de funções](functions-scale.md#timeout). 

Uma função pode ser grande devido a muitas dependências de Node.js. A importação dessas dependências pode causar aumento no tempo de carregamento resultando em tempos limite inesperados. As dependências são carregadas explícita e implicitamente. Um único módulo carregado pelo seu código pode carregar seus próprios módulos adicionais. 

Sempre que possível, refatore funções grandes em conjuntos menores de funções que funcionem juntos e retornem respostas rápidas. Por exemplo, um webhook ou a função do gatilho HTTP pode exigir uma resposta de confirmação dentro de um certo limite de tempo; é comum de webhooks exigirem uma resposta imediata. Você pode passar o conteúdo do gatilho HTTP para uma fila para ser processado por uma função de gatilho de fila. Essa abordagem permite adiar o trabalho real e retornar uma resposta imediata.


### <a name="cross-function-communication"></a>Comunicação entre funções

As [Durable Functions](durable/durable-functions-overview.md) e os [Aplicativos Lógicos do Azure](../logic-apps/logic-apps-overview.md) são projetados para gerenciar transições de estado e comunicação entre diversas funções.

Quando as Funções Duráveis ou os Aplicativos Lógicos não são usados para integrar com várias funções, geralmente é uma melhor prática usar filas de armazenamento para comunicação entre funções.  O principal motivo é que as filas de armazenamento são mais baratas e mais fáceis de provisionar. 

Mensagens individuais em uma fila de armazenamento estão limitadas ao tamanho de 64 KB. Se você precisar passar mensagens maiores entre as funções, uma fila do Barramento de Serviço do Azure poderá ser usada para dar suporte a mensagens com tamanhos de até 256 KB na camada Standard e até 1 MB na camada Premium.

Tópicos de barramento de serviço são úteis se você precisar de filtragem de mensagens antes do processamento.

Hubs de eventos são úteis para oferecer suporte a comunicações de alto volume.


### <a name="write-functions-to-be-stateless"></a>Grave funções para serem sem estado 

As funções devem ser sem estado e idempotentes se possível. Associe quaisquer informações de estado necessárias a seus dados. Por exemplo, um pedido sendo processado provavelmente teria um membro `state` associado. Uma função pode processar um pedido com base no estado enquanto a função em si permanece sem estado. 

Funções de idempotentes são recomendadas especialmente com gatilhos de timer. Por exemplo, se você tiver algo que absolutamente deve ser executado uma vez por dia, grave-o para que possa ser executado a qualquer momento durante o dia com os mesmos resultados. A função pode ser encerrada quando não houver nenhum trabalho para um dia específico. Também se uma execução anterior tiver falhado ao concluir, a próxima execução deve continuar de onde a anterior parou.


### <a name="write-defensive-functions"></a>Grave funções defensivas

Suponha que sua função pode encontrar uma exceção a qualquer momento. Projete suas funções com a capacidade de continuar de um ponto de falha anterior durante a próxima execução. Considere um cenário que requeira as seguintes ações:

1. Consulta de 10.000 linhas em um banco de dados.
2. Crie uma mensagem de fila para cada uma das linhas para processar ainda mais adiante na linha.
 
Dependendo de quão complexo é o sistema, você pode ter: serviços posteriores envolvidos se comportando mal, interrupções de rede ou limites de cota alcançados, etc. Tudo isso pode afetar sua função a qualquer momento. Você precisa para projetar suas funções para estarem preparadas para isso.

Como o seu código reage se ocorrer uma falha após a inserção de 5.000 desses itens em uma fila para processamento? Controle itens em um conjunto concluído. Caso contrário, você pode inseri-los de novo posteriormente. Isso pode ter um sério impacto em seu fluxo de trabalho. 

Se um item da fila já tiver sido processado, permita que sua função seja no-op.

Tire proveito de medidas defensivas já fornecidas para componentes usados na plataforma Azure Functions. Por exemplo, consulte **Tratamento de mensagens suspeitas na fila** na documentação de [gatilhos e associações de fila de Armazenamento do Microsoft Azure](functions-bindings-storage-queue.md#trigger---poison-messages). 

## <a name="scalability-best-practices"></a>Melhores práticas de escalabilidade

Há uma série de fatores que afetam como as instâncias do aplicativo de funções são escaladas. Os detalhes são fornecidos na documentação de [dimensionamento de função](functions-scale.md).  A seguir, estão algumas das melhores práticas para garantir a escalabilidade ideal para um aplicativo de funções.

### <a name="share-and-manage-connections"></a>Gerenciar e compartilhar conexões

Reutilize conexões para recursos externos, sempre que possível.  Veja [como gerenciar conexões no Azure Functions](./manage-connections.md).

### <a name="dont-mix-test-and-production-code-in-the-same-function-app"></a>Não misture códigos de teste e de produção no mesmo aplicativo de funções

As funções em um aplicativo de funções compartilham recursos. Por exemplo, a memória é compartilhada. Se você estiver usando um aplicativo de funções em produção, não adicione recursos e funções de teste nele. Ele pode causar sobrecarga inesperada durante a execução de código de produção.

Cuidado com o que você carrega em seus aplicativos de funções de produção. A memória é dividida igualmente entre cada função no aplicativo.

Se você tiver um assembly compartilhado referenciado em várias funções .NET, coloque-o em uma pasta compartilhada comum. Faça referência ao assembly com uma instrução semelhante ao exemplo a seguir, caso esteja usando scripts de C# (.csx): 

    #r "..\Shared\MyAssembly.dll". 

Caso contrário, é fácil de implantar acidentalmente várias versões de teste do mesmo binário que se comportam diferentemente entre as funções.

Não use o log detalhado no código de produção. Ele tem um impacto negativo no desempenho.

### <a name="use-async-code-but-avoid-blocking-calls"></a>Usar o código assíncrono, mas evitar chamadas de bloqueio

A programação assíncrona é uma prática recomendada. No entanto, sempre evite fazer referência à propriedade `Result` ou chamar o método `Wait` em um instância `Task`. Essa abordagem pode levar ao esgotamento de thread.

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

### <a name="receive-messages-in-batch-whenever-possible"></a>Receber mensagens em lote sempre que possível

Alguns gatilhos, como o Hub de Eventos, habilitam o recebimento de um lote de mensagens em uma única invocação.  As mensagens em lote têm um desempenho melhor.  É possível configurar o tamanho máximo do lote no arquivo `host.json`, conforme detalhado na [documentação de referência do host.json](functions-host-json.md)

Em funções do C#, é possível alterar o tipo para uma matriz fortemente tipada.  Por exemplo, em vez de `EventData sensorEvent`, a assinatura do método pode ser `EventData[] sensorEvent`.  Em outras linguagens, será necessário definir explicitamente a propriedade de cardinalidade no `function.json` como `many`, a fim de habilitar o envio em lote, [conforme mostrado aqui](https://github.com/Azure/azure-webjobs-sdk-templates/blob/df94e19484fea88fc2c68d9f032c9d18d860d5b5/Functions.Templates/Templates/EventHubTrigger-JavaScript/function.json#L10).

### <a name="configure-host-behaviors-to-better-handle-concurrency"></a>Configurar comportamentos de host para lidar melhor com a simultaneidade

O arquivo `host.json` no aplicativo de funções permite configurar os comportamentos do tempo de execução e do gatilho do host.  Além do envio em lote de comportamentos, é possível gerenciar a simultaneidade de diversos gatilhos.  Geralmente, ajustar os valores nessas opções pode ajudar a escalar cada instância adequadamente para as demandas das funções invocadas.

As configurações no arquivo dos hosts se aplicam a todas as funções do aplicativo, em uma *única instância* da função. Por exemplo, se você tiver um aplicativo de funções com duas funções HTTP e as solicitações simultâneas definidas como 25, uma solicitação para qualquer gatilho HTTP será contada como uma das 25 solicitações simultâneas compartilhadas.  Se esse aplicativo de funções for escalado para dez instâncias, as duas funções permitirão efetivamente 250 solicitações simultâneas (10 instâncias * 25 solicitações simultâneas por instância).

**Opções de host de simultaneidade HTTP**

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

Outras opções de configuração do host podem ser encontradas [no documento de configuração de host](functions-host-json.md).

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações, consulte os seguintes recursos:

* [Como gerenciar conexões no Azure Functions](manage-connections.md)
* [Práticas recomendadas do Serviço de Aplicativo do Azure](../app-service/app-service-best-practices.md)
