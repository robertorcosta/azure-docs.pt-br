---
title: Solucionar problemas no lado do cliente do Cache do Azure para Redis
description: Saiba como resolver problemas comuns do lado do cliente com o Azure Cache for Redis, como pressão de memória do cliente Redis, estouro de tráfego, CPU alta, largura de banda limitada, grandes solicitações ou grande tamanho de resposta.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: ace953fcb278604cb64eef463753f0f2622d3d24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277941"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Solucionar problemas no lado do cliente do Cache do Azure para Redis

Esta seção discute problemas de solução de problemas que ocorrem devido a uma condição no cliente Redis que seu aplicativo usa.

- [Pressão de memória no cliente Redis](#memory-pressure-on-redis-client)
- [Estouro de tráfego](#traffic-burst)
- [Alto nível de uso da CPU do cliente](#high-client-cpu-usage)
- [Limitação de largura de banda do lado do cliente](#client-side-bandwidth-limitation)
- [Grande solicitação ou tamanho de resposta](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Pressão de memória no cliente Redis

A pressão de memória na máquina cliente leva a todos os tipos de problemas de desempenho que podem atrasar o processamento de respostas do cache. Quando a pressão de memória bate, o sistema pode paginar dados para disco. Esse tipo de _falha de página_ faz com que o sistema fique significativamente mais lento.

Para detectar a pressão de memória no cliente:

- Monitore o uso da memória na máquina para garantir que ela não exceda a memória disponível.
- Monitore o `Page Faults/Sec` contador de desempenho do cliente. Durante o funcionamento normal, a maioria dos sistemas tem algumas falhas de página. Picos de falhas de página correspondentes com tempo sumido de solicitação podem indicar pressão de memória.

A alta pressão de memória sobre o cliente pode ser atenuada de várias maneiras:

- Aprofunde os padrões de uso da memória para reduzir o consumo de memória no cliente.
- Atualize sua VM cliente para um tamanho maior com mais memória.

## <a name="traffic-burst"></a>Estouro de tráfego

A intermitência de tráfego, combinada com configurações de `ThreadPool` ruins, podem resultar em atrasos no processamento de dados que já foram enviados pelo servidor Redis, mas ainda não foram consumidos no lado do cliente.

Monitore `ThreadPool` como suas estatísticas mudam ao longo do tempo usando [um exemplo `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs). Você pode `TimeoutException` usar mensagens do StackExchange.Redis como abaixo para investigar melhor:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

Na exceção anterior, existem várias questões que são interessantes:

- Observe que, na seção `IOCP` e na seção `WORKER`, você tem um valor `Busy` que é maior que o valor `Min`. Essa diferença `ThreadPool` significa que suas configurações precisam ser ajustadas.
- Você também pode ver `in: 64221`. Esse valor indica que 64.211 bytes foram recebidos na camada de soquete do kernel do cliente, mas não foram lidos pelo aplicativo. Essa diferença normalmente significa que seu aplicativo (por exemplo, StackExchange.Redis) não está lendo dados da rede tão rapidamente quanto o servidor está enviando para você.

Você pode [configurar `ThreadPool` suas configurações](cache-faq.md#important-details-about-threadpool-growth) para garantir que seu pool de segmentos seja dimensionado rapidamente em cenários de explosão.

## <a name="high-client-cpu-usage"></a>Alto nível de uso da CPU do cliente

O alto uso da CPU do cliente indica que o sistema não pode acompanhar o trabalho que foi solicitado a fazer. Mesmo que o cache tenha enviado a resposta rapidamente, o cliente pode não processar a resposta em tempo hábil.

Monitore o uso da CPU em todo o sistema do cliente usando métricas disponíveis no portal Azure ou através de contadores de desempenho na máquina. Tenha cuidado para não monitorar a CPU *do processo* porque um único processo pode ter baixo uso de CPU, mas a CPU em todo o sistema pode ser alta. Fique atento a picos de uso de CPU que correspondem aos tempos limite. A CPU alta `in: XXX` também `TimeoutException` pode causar altos valores em mensagens de erro, conforme descrito na seção ['Tráfego de](#traffic-burst) rajadas'.

> [!NOTE]
> O StackExchange 1.1.603 e versões posteriores incluem a métrica `local-cpu` em mensagens de erro `TimeoutException`. Certifique-se de estar usando a versão mais recente do [Pacote NuGet do StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/). Constantemente são corrigidos bugs no código para torná-lo mais robusto com relação a tempos limite, de modo que ter a versão mais recente é importante.
>

Para mitigar o alto uso da CPU de um cliente:

- Investigue o que está causando picos de CPU.
- Atualize seu cliente para um tamanho vm maior com mais capacidade de CPU.

## <a name="client-side-bandwidth-limitation"></a>Limitação de largura de banda do lado do cliente

Dependendo da arquitetura dos computadores cliente, eles poderão apresentar limitações na largura de banda de rede disponível. Se o cliente exceder a largura de banda disponível sobrecarregando a capacidade da rede, os dados não são processados no lado do cliente tão rapidamente quanto o servidor o envia. Essa situação pode resultar em tempos limite excedidos.

Monitore como o uso da largura de banda muda ao longo do tempo usando [um exemplo `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs). Esse código pode não ser executado corretamente em alguns ambientes com permissões restritas (como sites do Azure).

Para mitigar, reduza o consumo de largura de banda da rede ou aumente o tamanho da VM do cliente para uma com mais capacidade de rede.

## <a name="large-request-or-response-size"></a>Grande solicitação ou tamanho de resposta

Uma solicitação/resposta grande pode causar tempos limite. Como exemplo, suponha que seu valor de tempo definido configurado em seu cliente seja de 1 segundo. Seu aplicativo solicita duas chaves (por exemplo, "A" e "B") ao mesmo tempo (usando a mesma conexão de rede física). A maioria dos clientes solicita "pipelining", onde ambos os pedidos 'A' e 'B' são enviados um após o outro sem esperar por suas respostas. O servidor devolve as respostas na mesma ordem. Se a resposta 'A' for grande, ela pode comer a maior parte do tempo para solicitações posteriores.

No exemplo a seguir, a solicitação 'A' e 'B' são enviadas rapidamente para o servidor. O servidor começa a enviar respostas 'A' e 'B' rapidamente. Devido aos tempos de transferência de dados, a resposta 'B' deve esperar por trás dos tempos de resposta 'A' mesmo que o servidor tenha respondido rapidamente.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Essa é uma solicitação/resposta difícil de se medir. Você pode instrumentalar seu código de cliente para rastrear grandes solicitações e respostas.

As resoluções para grandes tamanhos de resposta são variadas, mas incluem:

1. Otimize sua aplicação para um grande número de pequenos valores, em vez de alguns grandes valores.
    - A solução preferida é dividir seus dados em valores menores relacionados.
    - Veja o post [Qual é a faixa de tamanho de valor ideal para redis? 100 KB é muito grande?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) para detalhes sobre por que valores menores são recomendados.
1. Aumente o tamanho da sua VM para obter recursos de largura de banda mais altos
    - Mais largura de banda em seu cliente ou vm servidor pode reduzir os tempos de transferência de dados para respostas maiores.
    - Compare o uso atual da rede em ambas as máquinas com os limites do tamanho atual da VM. Mais largura de banda apenas no servidor ou apenas no cliente pode não ser suficiente.
1. Aumente o número de objetos de conexão que seu aplicativo usa.
    - Use uma abordagem round-robin para fazer solicitações sobre diferentes objetos de conexão.

## <a name="additional-information"></a>Informações adicionais

- [Solucionar problemas no lado do servidor do Cache do Azure para Redis](cache-troubleshoot-server.md)
- [Como medir e testar o desempenho do meu cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
