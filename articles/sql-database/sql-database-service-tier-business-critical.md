---
title: Camada de serviço comercialmente crítica
description: Saiba mais sobre a camada Comercialmente Crítico do Banco de Dados SQL do Azure
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 12/04/2018
ms.openlocfilehash: fc328c34c1543a75fdc885087d44b28e24c0850a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268672"
---
# <a name="business-critical-tier---azure-sql-database"></a>Camada Comercialmente Crítico - Banco de Dados SQL do Azure

> [!NOTE]
> A Camada Comercialmente Crítico é chamada de Premium no modelo de compra DTU. Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [Modelos e recursos de compra do Banco de Dados SQL do Azure](sql-database-purchase-models.md).

Banco de dados SQL do Azure baseia-se na arquitetura de mecanismo de banco de dados do SQL Server que é ajustada para o ambiente de nuvem para garantir a disponibilidade de 99,99%, até mesmo no caso de falhas de infraestrutura. Há três modelos de arquitetura que são usados no Banco de Dados SQL do Azure:
- Uso Geral/Padrão 
- Comercialmente Crítico/Premium
- Hiperescala

O modelo da camada de serviço Premium/Comercialmente Crítico que se baseia em um cluster de processos do mecanismo de banco de dados. Esse modelo de arquitetura se baseia no fato de que há sempre um quorum de nós de mecanismo de banco de dados disponíveis e tem impacto mínimo no desempenho em sua carga de trabalho, mesmo durante as atividades de manutenção.

O Azure atualiza e aplica patches subjacentes ao sistema operacional, drivers e Mecanismo de BD do SQL Server de forma transparente com mínimo tempo de inatividade para usuários finais. 

A disponibilidade Premium está habilitada nas camadas de serviço Premium e Business Critical do Banco de Dados SQL do Azure e foi projetada para cargas de trabalho intensivas que não toleram qualquer impacto no desempenho devido às operações de manutenção em andamento.

No modelo Premium, o Banco de Dados SQL do Azure integra computação e armazenamento em um único nó. A alta disponibilidade neste modelo de arquitetura é obtida pela replicação de computação (processo do Mecanismo de Banco de Dados do SQL Server) e armazenamento (SSD conectado localmente) implantada em cluster de quatro nós, usando tecnologia semelhante ao SQL Server [Always On Availability Groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server).

![Cluster de nós de mecanismo de banco de dados](media/sql-database-managed-instance/business-critical-service-tier.png)

O processo do mecanismo de banco de dados SQL e os arquivos mdf / ldf subjacentes são colocados no mesmo nó com armazenamento SSD conectado localmente, fornecendo baixa latência à sua carga de trabalho. A alta disponibilidade é implementada usando tecnologia semelhante ao SQL Server [Always On Availability Groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Cada banco de dados é um cluster de nós de banco de dados com um banco de dados primário acessível para carga de trabalho do cliente e três processos secundários que contêm cópias dos dados. O nó primário efetua constantemente push das alterações para nos secundários para garantir que os dados estejam disponíveis em réplicas secundárias se o nó primário falhar por qualquer motivo. O failover é tratado pelo Mecanismo de Banco de Dados do SQL Server – uma réplica secundária se torna o nó primário e uma nova réplica secundária é criada para garantir nós suficientes no cluster. A carga de trabalho é automaticamente redirecionada para o novo nó primário.

Além disso, o cluster Business Critical possui o recurso interno [Expansão Scale-Out](sql-database-read-scale-out.md) que fornece um nó somente leitura interno gratuito que pode ser usado para executar consultas somente leitura (por exemplo, relatórios) não deve afetar o desempenho de sua carga de trabalho principal.

## <a name="when-to-choose-this-service-tier"></a>Quando escolher essa camada de serviço?

A camada de serviço Comercialmente Crítico foi projetada para os aplicativos que requerem respostas de baixa latência do armazenamento SSD subjacente (1 a 2 ms em média), recuperação rápida a infraestrutura subjacente falhar ou precisar carregar relatórios, análise e consultas somente leitura para a réplica secundária do banco de dados primário legível.

Os principais motivos pelos quais você deve escolher Comercialmente Crítico camada de serviço em vez de Uso Geral camada são:
-   Requisitos de latência de e/s baixos – a carga de trabalho que precisa da resposta rápida da camada de armazenamento (1-2 milissegundos em média) deve usar a camada de Comercialmente Crítico. 
-   Comunicação frequente entre o aplicativo e o banco de dados. O aplicativo que não pode aproveitar o cache da camada de aplicativo ou solicitar o envio [em lote](sql-database-use-batching-to-improve-performance.md) e precisa enviar muitas consultas SQL que devem ser processadas rapidamente são bons candidatos à camada de comercialmente crítico.
-   Grande número de atualizações – as operações de inserção, atualização e exclusão modificam as páginas de dados na memória (página suja) que devem ser salvas em arquivos de dados com a operação `CHECKPOINT`. A falha do processo do mecanismo de banco de dados potencial ou um failover do banco de dados com um grande número de páginas sujas pode aumentar o tempo de recuperação na camada Uso Geral. Use Comercialmente Crítico camada se você tiver uma carga de trabalho que cause muitas alterações na memória. 
-   Transações de longa execução que modificam dados. As transações que são abertas por um tempo maior impedem o truncamento do arquivo de log que pode aumentar o tamanho do log e o número de [arquivos de log virtuais (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch). O alto número de VLF pode retardar a recuperação do banco de dados após o failover.
-   Carga de trabalho com consultas analíticas e de relatório que podem ser redirecionadas para a réplica somente leitura secundária de liberação de custo.
- Maior resiliência e recuperação mais rápida a partir das falhas. Em caso de falha do sistema, o banco de dados na instância primária será desabilitado e uma das réplicas secundárias se tornará imediatamente o novo banco de dados primário de leitura/gravação que está pronto para processar as consultas. O mecanismo de banco de dados não precisa analisar e refazer transações do arquivo de log e carregar todos os dados no buffer de memória.
- Proteção de corrupção de dados avançada-a camada de Comercialmente Crítico aproveita as réplicas de banco de dado nos bastidores para fins de continuidade dos negócios e, portanto, o serviço também aproveita o reparo automático de página, que é a mesma tecnologia usada para SQL Server espelhamento de banco de dados [e grupos de disponibilidade](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring). Caso uma réplica não possa ler uma página devido a um problema de integridade de dados, uma cópia nova da página será recuperada de outra réplica, substituindo a página ilegível sem perda de dados ou tempo de inatividade do cliente. Essa funcionalidade será aplicável na camada Uso Geral se o banco de dados tiver uma réplica geográfica-secundária.
- Maior disponibilidade-a camada de Comercialmente Crítico na configuração multi-AZ garante 99,995% de disponibilidade, em comparação com 99,99% da camada Uso Geral.
- A Comercialmente Crítico camada de recuperação geográfica rápida configurada com replicação geográfica tem um RPO (objetivo de ponto de recuperação) garantido de 5 s e RTO (objetivo de tempo de recuperação) de 30 segundos para 100% de horas implantadas.

## <a name="next-steps"></a>Próximas etapas

- Encontre características de recursos (número de núcleos, e/s, memória) da camada de Comercialmente Crítico em [instância gerenciada](sql-database-managed-instance-resource-limits.md#service-tier-characteristics), banco de dados individual no modelo de [VCORE](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) ou [modelo de DTU](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)ou pool elástico no modelo de [VCORE](sql-database-vcore-resource-limits-elastic-pools.md#business-critical---provisioned-compute---gen4) e [modelo de DTU](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Saiba mais sobre as camadas [Uso Geral](sql-database-service-tier-general-purpose.md) e [Hiperescala](sql-database-service-tier-hyperscale.md).
- Saiba mais sobre o [Service Fabric](../service-fabric/service-fabric-overview.md).
- Para obter mais opções de alta disponibilidade e recuperação de desastres, consulte [Continuidade de Negócios](sql-database-business-continuity.md).
