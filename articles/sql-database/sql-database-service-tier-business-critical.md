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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
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

As principais razões pelas quais você deve escolher o nível de serviço Business Critical em vez do nível de Propósito Geral são:
-   Requisitos de latência de IO baixos – a carga de trabalho que precisa da resposta rápida da camada de armazenamento (1-2 milissegundos em média) deve usar o nível Business Critical. 
-   Comunicação frequente entre aplicativo e banco de dados. O aplicativo que não pode aproveitar o cache da camada de aplicativo ou [solicitar loteamento](sql-database-use-batching-to-improve-performance.md) e precisa enviar muitas consultas SQL que devem ser rapidamente processadas são bons candidatos para o nível Business Critical.
-   Grande número de atualizações – insira, atualize e exclua as operações modifique `CHECKPOINT` as páginas de dados na memória (página suja) que devem ser salvas em arquivos de dados com operação. Uma possível falha no processo do mecanismo de banco de dados ou um failover do banco de dados com um grande número de páginas sujas pode aumentar o tempo de recuperação no nível de Propósito Geral. Use o nível Business Critical se você tiver uma carga de trabalho que causa muitas alterações na memória. 
-   Transações de longa duração que modificam dados. As transações abertas por mais tempo impedem a truncação do arquivo log que pode aumentar o tamanho do registro e o número de [arquivos de log virtuais (VLF)](https://docs.microsoft.com/sql/relational-databases/sql-server-transaction-log-architecture-and-management-guide#physical_arch). O alto número de VLF pode retardar a recuperação do banco de dados após o failover.
-   Carga de trabalho com relatórios e consultas analíticas que podem ser redirecionadas para a réplica somente leitura secundária gratuita.
- Maior resiliência e recuperação mais rápida das falhas. Em caso de falha no sistema, o banco de dados na instância primária será desativado e uma das réplicas secundárias será imediatamente se tornar um novo banco de dados principal de leitura e gravação que está pronto para processar as consultas. O mecanismo do banco de dados não precisa analisar e refazer transações do arquivo de log e carregar todos os dados no buffer de memória.
- Proteção avançada contra corrupção de dados - O nível Business Critical aproveita as réplicas de banco de dados nos bastidores para fins de continuidade de negócios e, portanto, o serviço também aproveita o reparo automático de páginas, que é a mesma tecnologia usada para [espelhamento de](https://docs.microsoft.com/sql/sql-server/failover-clusters/automatic-page-repair-availability-groups-database-mirroring)banco de dados sql server e grupos de disponibilidade . Caso uma réplica não possa ler uma página devido a um problema de integridade de dados, uma nova cópia da página será recuperada de outra réplica, substituindo a página ilegível sem perda de dados ou tempo de inatividade do cliente. Essa funcionalidade é aplicável no nível De propósito geral se o banco de dados tiver uma réplica geosecundária.
- Maior disponibilidade - O nível Business Critical na configuração Multi-AZ garante 99,995% de disponibilidade, em comparação com 99,99% do nível de Uso Geral.
- Geo-recuperação rápida - O nível Business Critical configurado com georeplicação tem um RPO (Recovery Point Objective, objetivo de recuperação) garantido de 5 seg e Objetivo de Tempo de Recuperação (RTO) de 30 seg para 100% das horas implantadas.

## <a name="next-steps"></a>Próximas etapas

- Encontre características de recursos (número de núcleos, IO, memória) do nível Business Critical em [Instância Gerenciada,](sql-database-managed-instance-resource-limits.md#service-tier-characteristics)Banco de dados único no [modelo vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) ou [modelo DTU](sql-database-dtu-resource-limits-single-databases.md#premium-service-tier)ou pool Elástico no [modelo vCore](sql-database-vcore-resource-limits-elastic-pools.md#business-critical---provisioned-compute---gen4) e [modelo DTU](sql-database-dtu-resource-limits-elastic-pools.md#premium-elastic-pool-limits).
- Saiba mais sobre as camadas [Uso Geral](sql-database-service-tier-general-purpose.md) e [Hiperescala](sql-database-service-tier-hyperscale.md).
- Saiba mais sobre [malha de serviço](../service-fabric/service-fabric-overview.md).
- Para obter mais opções de alta disponibilidade e recuperação de desastres, consulte [Continuidade de Negócios](sql-database-business-continuity.md).
