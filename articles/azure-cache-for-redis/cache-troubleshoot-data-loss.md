---
title: Solucionar problemas de perda de dados do Cache do Azure para Redis
description: Saiba como resolver problemas de perda de dados com o Cache Do Azure para Redis, como perda parcial de chaves, expiração de chaves ou perda completa de chaves.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d54506b94f076f0a3d967f88bd4e2960a1ca6396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530894"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Solucionar problemas de perda de dados do Cache do Azure para Redis

Este artigo discute como diagnosticar perdas de dados reais ou percebidas que podem ocorrer no Azure Cache for Redis.

> [!NOTE]
> Várias das etapas de solução de problemas neste guia incluem instruções para executar comandos do Redis e monitorar diversas métricas de desempenho. Para obter mais informações, consulte os artigos na seção [Informações adicionais](#additional-information) .
>

## <a name="partial-loss-of-keys"></a>Perda parcial das chaves

O Cache do Azure para Redis não exclui aleatoriamente as chaves depois de armazenadas na memória. No entanto, ele remove chaves em resposta às políticas de expiração ou despejo e a comandos explícitos de exclusão de chaves. As teclas que foram escritas para o nó mestre em uma instância Premium ou Standard Azure Cache for Redis também podem não estar disponíveis em uma réplica imediatamente. Os dados são replicados do mestre para a réplica de forma assíncrona e não bloqueada.

Se você descobrir que as chaves desapareceram do seu cache, verifique as seguintes possíveis causas:

| Causa | Descrição |
|---|---|
| [Expiração da chave](#key-expiration) | As chaves são removidas por causa dos intervalos definidos nelas. |
| [Despejo de chaves](#key-eviction) | As chaves são removidas pressão de memória. |
| [Exclusão da chave](#key-deletion) | As teclas são removidas por comandos de exclusão explícitas. |
| [Replicação assíncrona](#async-replication) | As chaves não estão disponíveis em uma réplica devido a atrasos na replicação de dados. |

### <a name="key-expiration"></a>Expiração da chave

O Cache do Azure para Redis remove uma chave automaticamente se a chave tiver um tempo de intervalo e esse período tiver passado. Para obter mais informações sobre o vencimento da chave Redis, consulte a documentação do comando [EXPIRE.](https://redis.io/commands/expire) Os valores de tempo de tempo também podem ser definidos usando os comandos [SET,](https://redis.io/commands/set) [SETEX,](https://redis.io/commands/setex) [GETSET](https://redis.io/commands/getset)e outros ** \*comandos STORE.**

Para obter estatísticas sobre quantas teclas expiraram, use o comando [INFO.](https://redis.io/commands/info) A `Stats` seção mostra o número total de chaves expiradas. A `Keyspace` seção fornece mais informações sobre o número de chaves com intervalos de tempo e o valor médio de tempo de saída.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Você também pode olhar para métricas de diagnóstico para o seu cache, para ver se há uma correlação entre quando a chave desapareceu e um pico nas teclas expiradas. Consulte o apêndice de [Depuração Redis Keyspace Misses](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) para obter informações sobre o uso de notificações de espaço-chave ou **MONITOR** para depurar esses tipos de problemas.

### <a name="key-eviction"></a>Despejo de chaves

O Azure Cache for Redis requer espaço de memória para armazenar dados. Ele limpa as teclas para liberar a memória disponível quando necessário. Quando os valores **used_memory** ou **used_memory_rss** no comando [INFO](https://redis.io/commands/info) se aproximam da configuração de **memória maxconfigurada** configurada, o Azure Cache for Redis inicia a despejo de chaves de memória com base na [diretiva cache](https://redis.io/topics/lru-cache).

Você pode monitorar o número de chaves despejadas usando o comando [INFO:](https://redis.io/commands/info)

```
# Stats

evicted_keys:13224
```

Você também pode olhar para métricas de diagnóstico para o seu cache, para ver se há uma correlação entre quando a chave desapareceu e um pico nas teclas despejadas. Consulte o apêndice de [Depuração Redis Keyspace Misses](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) para obter informações sobre o uso de notificações de espaço-chave ou **MONITOR** para depurar esses tipos de problemas.

### <a name="key-deletion"></a>Exclusão da chave

Os clientes Redis podem emitir o comando [DEL](https://redis.io/commands/del) ou [HDEL](https://redis.io/commands/hdel) para remover explicitamente as chaves do Cache Azure para Redis. Você pode rastrear o número de operações de exclusão usando o comando [INFO.](https://redis.io/commands/info) Se os comandos **DEL** ou **HDEL** forem chamados, eles serão listados na seção. `Commandstats`

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Replicação assíncrona

Qualquer instância do Azure Cache for Redis no nível Padrão ou Premium é configurada com um nó mestre e pelo menos uma réplica. Os dados são copiados do mestre para uma réplica de forma assíncrona usando um processo de fundo. O site [redis.io](https://redis.io/topics/replication) descreve como a replicação de dados Redis funciona em geral. Para cenários em que os clientes escrevem para redis com freqüência, a perda parcial de dados pode ocorrer porque essa replicação é garantida como instantânea. Por exemplo, se o mestre cair *depois que* um cliente escrever uma chave para ele, mas *antes* que o processo de fundo tenha a chance de enviar essa chave para a réplica, a chave será perdida quando a réplica assumir como o novo mestre.

## <a name="major-or-complete-loss-of-keys"></a>Perda maior ou completa de chaves

Se a maioria ou todas as chaves desapareceram do seu cache, verifique as seguintes possíveis causas:

| Causa | Descrição |
|---|---|
| [Flushing de chave](#key-flushing) | As chaves foram limpas manualmente. |
| [Seleção incorreta de banco de dados](#incorrect-database-selection) | O Azure Cache for Redis está definido para usar um banco de dados não padrão. |
| [Falha de instância de Redis](#redis-instance-failure) | O servidor Redis não está disponível. |

### <a name="key-flushing"></a>Flushing de chave

Os clientes podem ligar para o comando [FLUSHDB](https://redis.io/commands/flushdb) para remover todas as chaves em um *único* banco de dados ou [FLUSHALL](https://redis.io/commands/flushall) para remover todas as chaves de *todos os* bancos de dados em um cache Redis. Para descobrir se as teclas foram lavadas, use o comando [INFO.](https://redis.io/commands/info) A `Commandstats` seção mostra se o comando **FLUSH** foi chamado:

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Seleção incorreta de banco de dados

O Azure Cache for Redis usa o banco de dados **db0** por padrão. Se você mudar para outro banco de dados (por exemplo, **db1**) e tentar ler as chaves dele, o Cache do Azure para Redis não as encontrará lá. Cada banco de dados é uma unidade logicamente separada e possui um conjunto de dados diferente. Use o comando [SELECT](https://redis.io/commands/select) para usar outros bancos de dados disponíveis e procure chaves em cada um deles.

### <a name="redis-instance-failure"></a>Falha de instância de Redis

Redis é um armazenamento de dados na memória. Os dados são mantidos nas máquinas físicas ou virtuais que hospedam o cache Redis. Uma ocorrência de Cache Azure para Redis no nível Basic é executada em apenas uma única máquina virtual (VM). Se essa VM estiver em baixa, todos os dados armazenados no cache serão perdidos. 

Os caches nos níveis Standard e Premium oferecem uma resiliência muito maior contra a perda de dados usando duas VMs em uma configuração replicada. Quando o nó mestre em tal cache falha, o nó de réplica assume para servir dados automaticamente. Essas VMs estão localizadas em domínios separados para falhas e atualizações, para minimizar a chance de ambos ficarem indisponíveis simultaneamente. Se uma grande paralisação do data center acontecer, no entanto, as VMs ainda podem cair juntas. Seus dados serão perdidos nesses raros casos.

Considere usar a persistência e [a replicação de](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) [dados Redis](https://redis.io/topics/persistence) para melhorar a proteção de seus dados contra essas falhas de infra-estrutura.

## <a name="additional-information"></a>Informações adicionais

- [Solucionar problemas no lado do servidor do Cache do Azure para Redis](cache-troubleshoot-server.md)
- [Qual oferta de Cache do Azure para Redis e tamanho eu devo usar?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Como monitorar o Cache do Azure para Redis](cache-how-to-monitor.md)
- [Como posso executar comandos do Redis?](cache-faq.md#how-can-i-run-redis-commands)
