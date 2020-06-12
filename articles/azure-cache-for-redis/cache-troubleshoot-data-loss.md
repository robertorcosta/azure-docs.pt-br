---
title: Solucionar problemas de perda de dados do Cache do Azure para Redis
description: Saiba como resolver problemas de perda de dados com o Cache do Azure para Redis, como perda parcial de chaves, término de chave ou perda completa de chaves.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: ef7824640dcd2b9dbae1d27f385e5334ba9875ff
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83699228"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Solucionar problemas de perda de dados do Cache do Azure para Redis

Este artigo discute como diagnosticar perdas de dados reais ou percebidas que podem ocorrer no Cache do Azure para Redis.

> [!NOTE]
> Várias das etapas de solução de problemas neste guia incluem instruções para executar comandos do Redis e monitorar diversas métricas de desempenho. Para obter mais informações, consulte os artigos na seção [Informações adicionais](#additional-information) .
>

## <a name="partial-loss-of-keys"></a>Perda parcial de chaves

O Cache do Azure para Redis não exclui as chaves aleatoriamente após elas terem sido armazenadas na memória. No entanto, ele remove as chaves em resposta às políticas de término ou remoção e aos comandos explícitos de exclusão de chaves. As chaves que foram gravadas no nó mestre em uma instância de Cache do Azure para Redis Premium ou Standard também podem não estar disponíveis em uma réplica imediatamente. Os dados são replicados do mestre para a réplica de maneira assíncrona e sem bloqueio.

Se você descobrir que as chaves desapareceram do cache, verifique as seguintes causas possíveis:

| Causa | Descrição |
|---|---|
| [Expiração da chave](#key-expiration) | As chaves são removidas devido aos tempos limite definidos. |
| [Remoção de chave](#key-eviction) | As chaves são removidas sob pressão de memória. |
| [Exclusão de chave](#key-deletion) | As chaves são removidas por comandos de exclusão explícitos. |
| [Replicação assíncrona](#async-replication) | As chaves não estão disponíveis em uma réplica devido a atrasos na replicação de dados. |

### <a name="key-expiration"></a>Expiração da chave

O Cache do Azure para Redis removerá uma chave automaticamente se a chave tiver um tempo limite atribuído e esse período tiver passado. Para obter mais informações sobre o término da chave Redis, confira a documentação do comando [EXPIRE](https://redis.io/commands/expire). Os valores de tempo limite também podem ser definidos usando o conjunto de comandos [SET](https://redis.io/commands/set), [SETEX](https://redis.io/commands/setex), [GETSET](https://redis.io/commands/getset) e outros comandos **\*STORE**.

Para obter estatísticas sobre quantas chaves expiraram, use o comando [INFO](https://redis.io/commands/info). A seção `Stats` mostra o número total de chaves expiradas. A seção `Keyspace` fornece mais informações sobre o número de chaves com tempos limite e o valor de tempo limite médio.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

Você também pode examinar as métricas de diagnóstico para o cache para ver se há uma correlação entre quando a chave ficou ausente e um pico nas chaves expiradas. Confira o apêndice de [Depuração de falhas de keyspace do Redis](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) para obter informações sobre como usar notificações de keyspace ou **MONITORAR** a depurar desses tipos de problemas.

### <a name="key-eviction"></a>Remoção de chave

O Cache do Azure para Redis requer espaço de memória para armazenar dados. Ele limpa as chaves para liberar a memória disponível quando necessário. Quando os valores **used_memory** ou **used_memory_rss valores** no comando [INFO](https://redis.io/commands/info) aproximam-se da configuração **MaxMemory** definida, o Cache do Azure para Redis inicia a remoção de chaves da memória com base na [política de cache](https://redis.io/topics/lru-cache).

Você pode monitorar o número de chaves removidas usando o comando [INFO](https://redis.io/commands/info):

```
# Stats

evicted_keys:13224
```

Você também pode examinar as métricas de diagnóstico para o cache para ver se há uma correlação entre quando a chave ficou ausente e um pico nas chaves removidas. Confira o apêndice de [Depuração de falhas de keyspace do Redis](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) para obter informações sobre como usar notificações de keyspace ou **MONITORAR** a depurar desses tipos de problemas.

### <a name="key-deletion"></a>Exclusão de chave

Os clientes do Redis podem emitir o comando [DEL](https://redis.io/commands/del) ou [HDEL](https://redis.io/commands/hdel) para remover explicitamente as chaves do Cache do Azure para Redis. Você pode acompanhar o número de operações de exclusão usando o comando [INFO](https://redis.io/commands/info). Se os comandos **DEL** ou **HDEL** tiverem sido chamados, eles serão listados na seção `Commandstats`.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Replicação assíncrona

Qualquer instância de Cache do Azure para Redis na camada Standard ou Premium é configurada com um nó mestre e pelo menos uma réplica. Os dados são copiados do mestre para uma réplica de maneira assíncrona usando um processo em segundo plano. O site [redis.io](https://redis.io/topics/replication) descreve como a replicação de dados do Redis funciona em geral. Para cenários em que os clientes gravam em Redis com frequência, a perda de dados parcial pode ocorrer porque essa replicação não tem garantia de ser instantânea. Por exemplo, se o mestre ficar inativo *depois* de um cliente gravar uma chave nele, mas *antes* de o processo em segundo plano ter a chance de enviar essa chave para a réplica, a chave será perdida quando a réplica assumir o controle como o novo mestre.

## <a name="major-or-complete-loss-of-keys"></a>Perda grande ou completa de chaves

Se a maioria das chaves ou todas elas tiverem desaparecido do seu cache, verifique as seguintes causas possíveis:

| Causa | Descrição |
|---|---|
| [Liberação de chave](#key-flushing) | As chaves foram limpas manualmente. |
| [Seleção de banco de dados incorreta](#incorrect-database-selection) | O Cache do Azure para Redis é definido para usar um banco de dados não padrão. |
| [Falha na instância do Redis](#redis-instance-failure) | O servidor Redis não está disponível. |

### <a name="key-flushing"></a>Liberação de chave

Os clientes podem chamar o comando [FLUSHDB](https://redis.io/commands/flushdb) para remover todas as chaves em um banco de dados *individual* ou [FLUSHALL](https://redis.io/commands/flushall) para remover todas as chaves de *todos* os bancos de dados em um Cache Redis. Para descobrir se as chaves foram liberadas, use o comando [INFO](https://redis.io/commands/info). A seção `Commandstats` mostra se o comando **FLUSH** foi chamado:

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Seleção de banco de dados incorreta

O Cache do Azure para Redis usa o banco de dados **db0** por padrão. Se você alternar para outro banco de dados (por exemplo, **db1**) e tentar ler chaves dele, o Cache do Azure para Redis não as encontrará. Cada banco de dados é uma unidade separada de maneira lógica e mantém um conjunto de dados diferente. Use o comando [SELECT](https://redis.io/commands/select) para usar outros bancos de dados disponíveis e procurar chaves em cada um deles.

### <a name="redis-instance-failure"></a>Falha na instância do Redis

Redis é um armazenamento de dados na memória. Os dados são mantidos nas máquinas virtuais ou físicas que hospedam o Cache Redis. Uma instância do Cache do Azure para Redis na camada básica é executada somente em uma VM (máquina virtual). Se essa VM estiver inativa, todos os dados que você armazenou no cache serão perdidos. 

Os caches nas camadas Standard e Premium oferecem resiliência muito maior contra perda de dados usando duas VMs em uma configuração replicada. Quando o nó mestre nesse cache falha, o nó da réplica assume para atender aos dados automaticamente. Essas VMs estão localizadas em domínios separados para falhas e atualizações para minimizar a chance de ambas ficarem não disponíveis simultaneamente. No entanto, se ocorrer uma interrupção de datacenter principal, as VMs ainda poderão ficar inativas ao mesmo tempo. Seus dados serão perdidos nesses casos raros.

Considere usar [Persistência de dados do Redis](https://redis.io/topics/persistence) e [replicação geográfica](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) para aprimorar a proteção de seus dados contra essas falhas de infraestrutura.

## <a name="additional-information"></a>Informações adicionais

- [Solucionar problemas no lado do servidor do Cache do Azure para Redis](cache-troubleshoot-server.md)
- [Qual oferta de Cache do Azure para Redis e tamanho eu devo usar?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Como monitorar o Cache do Azure para Redis](cache-how-to-monitor.md)
- [Como posso executar comandos do Redis?](cache-faq.md#how-can-i-run-redis-commands)
