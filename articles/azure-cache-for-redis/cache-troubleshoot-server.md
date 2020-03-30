---
title: Solucionar problemas no lado do servidor do Cache do Azure para Redis
description: Aprenda a resolver problemas comuns do lado do servidor com o Azure Cache for Redis, como pressão de memória, CPU alta, comandos de longa duração ou limitações de largura de banda.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: a68c27de304a0da6470745ee4abf69590d9bf78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277928"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Solucionar problemas no lado do servidor do Cache do Azure para Redis

Esta seção discute problemas de solução de problemas que ocorrem devido a uma condição em um Cache Azure para Redis ou na máquina virtual que o hospeda.

- [Demanda de memória do servidor Redis](#memory-pressure-on-redis-server)
- [Alto uso de CPU ou carga do servidor](#high-cpu-usage-or-server-load)
- [Comandos de execução longa](#long-running-commands)
- [Limitação de largura de banda do lado do servidor](#server-side-bandwidth-limitation)

> [!NOTE]
> Várias das etapas de solução de problemas neste guia incluem instruções para executar comandos do Redis e monitorar diversas métricas de desempenho. Para obter mais informações, consulte os artigos na seção [Informações adicionais](#additional-information) .
>

## <a name="memory-pressure-on-redis-server"></a>Demanda de memória do servidor Redis

Uma grande demanda da memória do servidor acarreta todo tipo de problemas de desempenho, que podem atrasar o processamento de solicitações. Quando a pressão de memória bate, o sistema pode paginar dados para disco. Esse tipo de _falha de página_ faz com que o sistema fique significativamente mais lento. Há diversas causas possíveis para essa demanda de memória:

- O cache é preenchido com dados próximos de sua capacidade máxima.
- Redis está vendo alta fragmentação de memória. Essa fragmentação é mais frequentemente causada pelo armazenamento de objetos grandes, uma vez que Redis é otimizado para pequenos objetos.

Redis expõe duas estatísticas através do comando [INFO](https://redis.io/commands/info) que podem ajudá-lo a identificar esse problema: "used_memory" e "used_memory_rss". Você pode [visualizar essas métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) usando o portal.

Existem várias mudanças possíveis que você pode fazer para ajudar a manter o uso da memória saudável:

- [Configurar uma política de memória](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) e definir tempos de expiração em suas chaves. Esta política pode não ser suficiente se você tiver fragmentação.
- [Configurar um valor para maxmemory-reserved](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) que seja grande o suficiente para compensar pela fragmentação da memória.
- Dividir os objetos grandes armazenados em cache em objetos menores relacionados.
- [Crie alertas](cache-how-to-monitor.md#alerts) em métricas como memória usada para ser notificado precocemente sobre possíveis impactos.
- [Dimensione](cache-how-to-scale.md) para um tamanho de cache maior com mais capacidade de memória.

## <a name="high-cpu-usage-or-server-load"></a>Alto uso de CPU ou carga do servidor

Uma alta carga de servidor ou uso da CPU significa que o servidor não pode processar solicitações em tempo hábil. O servidor pode demorar a responder e não consegue acompanhar as taxas de solicitação.

[Monitore métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) como CPU ou carga de servidor. Fique atento a picos de uso de CPU que correspondem aos tempos limite.

Existem várias mudanças que você pode fazer para mitigar a alta carga do servidor:

- Investigue o que está causando picos de CPU, como [comandos de longa duração anotados](#long-running-commands) abaixo ou falhas de página devido à alta pressão de memória.
- [Crie alertas](cache-how-to-monitor.md#alerts) em métricas como CPU ou carga de servidor para ser notificado antecipadamente sobre possíveis impactos.
- [Dimensione](cache-how-to-scale.md) para um tamanho de cache maior com mais capacidade de CPU.

## <a name="long-running-commands"></a>Comandos de execução longa

Alguns comandos Redis são mais caros de serem executados do que outros. A [documentação dos comandos Redis](https://redis.io/commands) mostra a complexidade do tempo de cada comando. Como o processamento de comando Redis é de um thread único, um comando que leva tempo para ser executado bloqueará todos os outros que vierem atrás dele. Você deve rever os comandos que está emitindo para o servidor Redis para entender os impactos de desempenho. Por exemplo, o comando [KEYS](https://redis.io/commands/keys) é frequentemente usado sem saber que é uma operação O(N). Você pode evitar KEYS usando [scan](https://redis.io/commands/scan) para reduzir picos de CPU.

Usando o comando [SLOWLOG,](https://redis.io/commands/slowlog) você pode medir comandos caros sendo executados contra o servidor.

## <a name="server-side-bandwidth-limitation"></a>Limitação de largura de banda do lado do servidor

Diferentes tamanhos de cache têm diferentes capacidades de largura de banda de rede. Se o servidor exceder a largura de banda disponível, os dados não serão enviados ao cliente tão rapidamente. As solicitações dos clientes podem ficar sem tempo porque o servidor não pode empurrar dados para o cliente rápido o suficiente.

As métricas "Leitura de cache" e "Gravação de cache" podem ser usadas para ver quanta largura de banda do lado do servidor está sendo usada. Você pode [visualizar essas métricas](cache-how-to-monitor.md#view-metrics-with-azure-monitor) no portal.

Para mitigar situações em que o uso da largura de banda da rede esteja próximo da capacidade máxima:

- Alterar o comportamento de chamada do cliente para reduzir a demanda de rede.
- [Crie alertas](cache-how-to-monitor.md#alerts) em métricas como leitura de cache ou gravação de cache para ser notificado antecipadamente sobre possíveis impactos.
- [Dimensione](cache-how-to-scale.md) para um tamanho de cache maior com mais capacidade de largura de banda da rede.

## <a name="additional-information"></a>Informações adicionais

- [Solucionar problemas no lado do cliente do Cache do Azure para Redis](cache-troubleshoot-client.md)
- [Qual oferta de Cache do Azure para Redis e tamanho eu devo usar?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Como medir e testar o desempenho do meu cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Como monitorar o Cache do Azure para Redis](cache-how-to-monitor.md)
- [Como posso executar comandos do Redis?](cache-faq.md#how-can-i-run-redis-commands)
