---
title: Monitore e ajuste-hiperescala (Citus)-banco de dados do Azure para PostgreSQL
description: Este artigo descreve os recursos de monitoramento e ajuste no banco de dados do Azure para PostgreSQL-Citus (hiperescala)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: d2e9fcd6f6292c1da76e725e90deda4547b3682d
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975509"
---
# <a name="monitor-and-tune-azure-database-for-postgresql---hyperscale-citus"></a>Monitorar e ajustar o banco de dados do Azure para PostgreSQL-Citus (hiperescala)

Monitorar os dados dos seus servidores ajuda a solucionar problemas e otimizar sua carga de trabalho. O Citus (hiperescala) fornece várias opções de monitoramento para fornecer informações sobre o comportamento de nós em um grupo de servidores.

## <a name="metrics"></a>Métricas

O Citus (hiperescala) fornece métricas para cada nó em um grupo de servidores. As métricas fornecem informações sobre o comportamento dos recursos de suporte. Cada métrica é emitida em uma frequência de um minuto e tem até 30 dias de histórico.

Além de exibir grafos das métricas, você pode configurar alertas. Para obter diretrizes passo a passo, consulte [How to set up alerts](howto-hyperscale-alert-on-metric.md) (Como configurar alertas).  Outras tarefas incluem a configuração de ações automatizadas, a execução de análises avançadas e o histórico de arquivamento. Para obter mais informações, consulte a [Visão geral das métricas no Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas

Essas métricas estão disponíveis para nós de hiperescala (Citus):

|Métrica|Nome de exibição da métrica|Unidade|Descrição|
|---|---|---|---|
|active_connections|Conexões ativas|Contagem|O número de conexões ativas com o servidor.|
|cpu_percent|Porcentagem de CPU|Porcentagem|O percentual de CPU em uso.|
|IOPS|IOPS|Contagem|Consulte a [definição de IOPS](../virtual-machines/linux/premium-storage-performance.md#iops) e a [taxa de transferência de hiperescala](concepts-hyperscale-configuration-options.md)|
|memory_percent|Porcentagem de memória|Porcentagem|O percentual de memória em uso.|
|network_bytes_ingress|Entrada na rede|Bytes|Entrada de rede em conexões ativas.|
|network_bytes_egress|Saída da rede|Bytes|Rede-Out em conexões ativas.|
|storage_percent|Porcentagem de armazenamento|Porcentagem|O percentual de armazenamento usado fora do máximo do servidor.|
|storage_used|Armazenamento usado|Bytes|A quantidade de armazenamento em uso. O armazenamento usado pelo serviço pode incluir os arquivos de banco de dados, os logs de transação e os logs do servidor.|

O Azure não fornece métricas agregadas para o cluster como um todo, mas as métricas para vários nós podem ser colocadas no mesmo grafo.

## <a name="next-steps"></a>Próximos passos

- Confira [como configurar alertas](howto-hyperscale-alert-on-metric.md) para obter orientação sobre como criar um alerta em uma métrica.
