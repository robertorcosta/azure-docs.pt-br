---
title: Azure SQL Database Hyperscale FAQ
description: Respostas para perguntas comuns que os clientes fazem sobre um banco de dados SQL do Azure na camada de serviço da Hiperescala - comumente chamado de banco de dados da Hiperescala.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dimitri-furman
ms.author: dfurman
ms.reviewer: ''
ms.date: 03/03/2020
ms.openlocfilehash: 9f518df02b1923513fd014be53646a9a1be8465e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268620"
---
# <a name="azure-sql-database-hyperscale-faq"></a>Azure SQL Database Hyperscale FAQ

Este artigo fornece respostas a perguntas freqüentes para clientes que consideram um banco de dados no nível de serviço Hyperscale do Banco de Dados Azure SQL, referido apenas como Hyperscale no restante desta FAQ. Este artigo descreve os cenários que o Hyperscale suporta e os recursos compatíveis com o Hyperscale.

- Esta FAQ destina-se a leitores que tenham uma breve compreensão do nível de serviço Hiperescala e que desejam ter suas dúvidas e preocupações específicas respondidas.
- Este FAQ não é destinado a ser um guia ou responder perguntas sobre como usar um banco de dados Hyperscale. Para uma introdução ao Hyperscale, recomendamos que você consulte a documentação hyperscale do [Banco de Dados Azure SQL.](sql-database-service-tier-hyperscale.md)

## <a name="general-questions"></a>Perguntas gerais

### <a name="what-is-a-hyperscale-database"></a>O que é um banco de dados Hiperescala?

Um banco de dados da Hiperescala é um banco de dados SQL do Azure na camada de serviço da Hiperescala que é apoiado pela tecnologia de armazenamento scale-out da Hiperescala. Um banco de dados Hiperescala suporta até 100 TB de dados e oferece alto rendimento e desempenho, bem como escalonamento rápido para se adaptar aos requisitos de carga de trabalho. O dimensionamento é transparente para o aplicativo – conectividade, processamento de consultas, etc. funciona como qualquer outro banco de dados SQL do Azure.

### <a name="what-resource-types-and-purchasing-models-support-hyperscale"></a>Quais tipos de recursos e modelos de compra suportam a Hiperescala?

A camada de serviço Hiperescala está disponível apenas para bancos de dados individuais usando o modelo de compra baseado em vCore no Banco de Dados SQL do Azure.  

### <a name="how-does-the-hyperscale-service-tier-differ-from-the-general-purpose-and-business-critical-service-tiers"></a>Como o nível de serviço da Hiperescala difere dos níveis de serviço de uso geral e de negócios críticos?

Os níveis de serviço baseados em vCore são diferenciados com base na disponibilidade do banco de dados e no tipo de armazenamento, desempenho e tamanho máximo, conforme descrito na tabela a seguir.

| | Tipo de recurso | Uso Geral |  Hiperescala | Comercialmente Crítico |
|:---:|:---:|:---:|:---:|:---:|
| **Mais adequado para** |Todos|Oferece opções equilibradas de computação e armazenamento orientadas ao orçamento.|A maioria das cargas de trabalho comerciais. Tamanho de armazenamento de dimensionamento automático de até 100 TB, dimensionamento rápido de computação vertical e horizontal, restauração rápida do banco de dados.|Aplicações OLTP com alta taxa de transação e baixa latência de IO. Oferece maior resiliência a falhas e failovers rápidos usando várias réplicas atualizadas sincronicamente.|
|  **Tipo de recurso** ||Banco de dados único / Elástico pool / instância gerenciada | Banco de dados individual | Banco de dados único / Elástico pool / instância gerenciada |
| **Tamanho da computação**|Banco de dados único / Elástico pool * | 1 a 80 vCores | 1 a 80 vCores * | 1 a 80 vCores |
| |Instância gerenciada | 8, 16, 24, 32, 40, 64, 80 vCores | N/D | 8, 16, 24, 32, 40, 64, 80 vCores |
| **Tipo de armazenamento** | Todos |Armazenamento remoto Premium (por instância) | Armazenamento desacoplado com cache SSD local (por instância) | Armazenamento SSD local super rápido (por instância) |
| **Tamanho de armazenamento** | Banco de dados único / Elástico pool *| 5 GB – 4 TB | Até 100 TB | 5 GB – 4 TB |
| | Instância gerenciada  | 32 GB A 8 TB | N/D | 32 GB – 4 TB |
| **IOPS** | Banco de dados individual | 500 IOPS por vCore com máximo de 7.000 IOPS | Hyperscale é uma arquitetura de várias camadas com cache em vários níveis. O IOPS eficaz dependerá da carga de trabalho. | 5000 IOPS com 200.000 IOPS máximo|
| | Instância gerenciada | Depende do tamanho do arquivo | N/D | 1375 IOPS/vCore |
|**Disponibilidade**|Todos|1 réplica, sem escala de leitura, sem cache local | Múltiplas réplicas, até 4 leitura scale-out, cache local parcial | 3 réplicas, 1 Leitura scale-out, HA redundante de zona, armazenamento local completo |
|**Backups**|Todos|RA-GRS, retenção de 7-35 dias (7 dias por padrão)| RA-GRS, retenção de 7 dias, recuperação constante de ponto no tempo (PITR) | RA-GRS, retenção de 7-35 dias (7 dias por padrão) |

\*Os pools elásticos não são suportados no nível de serviço Hyperscale

### <a name="who-should-use-the-hyperscale-service-tier"></a>Quem deve usar o nível de serviço Hiperescala

O nível de serviço Hyperscale destina-se a clientes que possuem grandes bancos de dados SQL Server no local e querem modernizar seus aplicativos movendo-se para a nuvem, ou para clientes que já estão usando o Azure SQL Database e querem expandir significativamente o potencial de crescimento de banco de dados. A Hiperescala também é destinado a clientes que buscam alto desempenho e alta escalabilidade. Com em Hiperescala, você obtém:

- Tamanho do banco de dados até 100 TB
- Backups rápidos do banco de dados, independentemente do tamanho do banco de dados (backups são baseados em instantâneos de armazenamento)
- Restaurações rápidas do banco de dados, independentemente do tamanho do banco de dados (restaurações são de instantâneos de armazenamento)
- Maior throughput de log, independentemente do tamanho do banco de dados e do número de vCores
- Leia Scale-out usando uma ou mais réplicas somente de leitura, usadas para descarregar e como esperas quentes.
- Escalabilidade rápida de computação, em tempo constante, para ser mais poderosa para acomodar a carga de trabalho pesada e, em seguida, reduzir a escala, em tempo constante. Isso é semelhante ao dimensionamento para cima e para baixo entre um P6 e um P11, por exemplo, mas muito mais rápido, pois este não é um tamanho de operação de dados.

### <a name="what-regions-currently-support-hyperscale"></a>Quais regiões atualmente dão suporte em Hiperescala

O nível de serviço Hyperscale está atualmente disponível nas regiões listadas na visão geral da escala de hiperescala do Banco de [Dados Azure SQL](sql-database-service-tier-hyperscale.md#regions).

### <a name="can-i-create-multiple-hyperscale-databases-per-logical-server"></a>Posso criar vários bancos de dados de Hiperescala por servidor lógico

Sim. Para obter mais informações e limites sobre o número de bancos de dados Hiperescala por servidor lógico, consulte [Limites de recursos do Banco de Dados SQL para bancos de dados únicos e agrupados em um servidor lógico](sql-database-resource-limits-logical-server.md).

### <a name="what-are-the-performance-characteristics-of-a-hyperscale-database"></a>Quais são as características de desempenho de um banco de dados Hyperscale

A arquitetura Hyperscale oferece alto desempenho e throughput, ao mesmo tempo em que oferece suporte a grandes tamanhos de banco de dados. 

### <a name="what-is-the-scalability-of-a-hyperscale-database"></a>O que é a escalabilidade de um banco de dados em Hiperescala

A hiperescala fornece escalabilidade rápida com base na sua demanda de carga de trabalho.

- **Dimensionamento para cima/para baixo**

  Com o Hyperscale, você pode aumentar o tamanho da computação primária em termos de recursos como CPU e memória, e depois reduzir, em tempo constante. Como o armazenamento é compartilhado, a ampliação e o dimensionamento não são um tamanho de operação de dados.  
- **Dimensionamento In/Out**

  Com o Hyperscale, você também tem a capacidade de provisionar uma ou mais réplicas adicionais de computação que você pode usar para atender às suas solicitações de leitura. Isso significa que você pode usar essas réplicas adicionais de computação como réplicas somente de leitura para descarregar sua carga de trabalho de leitura da computação principal. Além de ler somente, essas réplicas também servem como hot-standbys em caso de failover do principal.

  O provisionamento de cada uma dessas réplicas adicionais de computação pode ser feito em tempo constante e é uma operação on-line. Você pode se conectar a essas réplicas adicionais de computação somente leitura definindo o `ApplicationIntent` argumento em sua seqüência de conexões para `ReadOnly`. Quaisquer conexões `ReadOnly` com a intenção do aplicativo são automaticamente encaminhadas para uma das réplicas adicionais de computação somente leitura.

## <a name="deep-dive-questions"></a>Perguntas sobre mergulho profundo

### <a name="can-i-mix-hyperscale-and-single-databases-in-a-single-logical-server"></a>Posso misturar hiperescala e bancos de dados únicos em um único servidor lógico

 Sim, pode.

### <a name="does-hyperscale-require-my-application-programming-model-to-change"></a>O da Hiperescala requer que meu modelo de programação de aplicativo mude

Não, seu modelo de programação de aplicativo permanece como está. Você usa sua seqüência de conexões como de costume e as outras maneiras regulares de interagir com seu banco de dados Hyperscale.

### <a name="what-transaction-isolation-level-is-the-default-in-a-hyperscale-database"></a>Qual nível de isolamento de transações é o padrão em um banco de dados Hyperscale

Na réplica principal, o nível padrão de isolamento da transação é RCSI (Read Committed Snapshot Isolation). Nas réplicas secundárias de escala de leitura, o nível de isolamento padrão é Snapshot.

### <a name="can-i-bring-my-on-premises-or-iaas-sql-server-license-to-hyperscale"></a>Posso levar minha licença on-premises ou IaaS SQL Server para hyperscale

Sim, [benefício híbrido do Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) está disponível para em Hiperescala. Cada núcleo do SQL Server Standard pode mapear para 1 Hiperescala vCores. Cada núcleo do SQL Server Enterprise pode mapear para 4 vCores de Hiperescala. Você não precisa de uma licença de SQL para réplicas secundárias. O preço do Benefício Híbrido Azure será aplicado automaticamente em réplicas de Escala de Leitura (secundária).

### <a name="what-kind-of-workloads-is-hyperscale-designed-for"></a>Que tipo de cargas de trabalho é projetada para hiperescala

A hyperscale suporta todas as cargas de trabalho do SQL Server, mas é otimizada principalmente para OLTP. Você também pode trazer cargas de trabalho híbridas (HTAP) e analíticas (data mart).

### <a name="how-can-i-choose-between-azure-sql-data-warehouse-and-azure-sql-database-hyperscale"></a>Como posso escolher entre o Azure SQL Data Warehouse e o Azure SQL Database Hyperscale

Se você está executando consultas de análise interativa usando o SQL Server como um data warehouse, o Hyperscale é uma ótima opção porque você pode hospedar pequenos e médios data warehouses (como alguns TB até 100 TB) a um custo menor, e você pode migrar seus dados do SQL Server cargas de trabalho do armazém para hyperscale com alterações mínimas de código T-SQL.

Se você estiver executando análise de dados em grande escala com consultas complexas e taxas de ingestão sustentadas superiores a 100 MB/s, ou usando PDW (Parallel Data Warehouse), Teradata ou outros armazéns de dados Massively Parallel Processing (MPP), o SQL Data Warehouse pode ser a melhor escolha.
  
## <a name="hyperscale-compute-questions"></a>Perguntas sobre computação em hiperescala

### <a name="can-i-pause-my-compute-at-any-time"></a>Posso pausar meu computação a qualquer momento

Não neste momento, no entanto, você pode dimensionar seu cálculo e número de réplicas para reduzir o custo durante os horários de não-pico.

### <a name="can-i-provision-a-compute-replica-with-extra-ram-for-my-memory-intensive-workload"></a>Posso prover uma réplica de computação com RAM extra para minha carga de trabalho de memória intensiva

Não. Para obter mais RAM, você precisa atualizar para um tamanho da computação maior. Para obter mais informações, consulte [Armazenamento hiper escale e tamanhos da computação](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5).

### <a name="can-i-provision-multiple-compute-replicas-of-different-sizes"></a>Posso prover múltiplas réplicas de computação de diferentes tamanhos

Não.

### <a name="how-many-read-scale-out-replicas-are-supported"></a>Quantas réplicas de escala de leitura são suportadas

Os bancos de dados Hyperscale são criados com uma réplica de escala de leitura (duas réplicas, incluindo principal) por padrão. Você pode dimensionar o número de réplicas somente leitura entre 0 e 4 usando [o portal Azure](https://portal.azure.com) ou [a API REST](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="for-high-availability-do-i-need-to-provision-additional-compute-replicas"></a>Para alta disponibilidade, preciso prover réplicas adicionais de computação

Nos bancos de dados Hyperscale, a resiliência dos dados é fornecida no nível de armazenamento. Você só precisa de uma réplica para proporcionar resiliência. Quando a réplica de cálculo está inativa, uma nova réplica é criada automaticamente sem perda de dados.

No entanto, se houver apenas uma réplica, poderá levar algum tempo para criar o cache local na nova réplica após o failover. Durante a fase de reconstrução do cache, o banco de dados busca dados diretamente dos servidores da página, resultando em maior latência de armazenamento e desempenho de consulta degradado.

Para aplicativos de missão crítica que requerem alta disponibilidade com impacto mínimo de failover, você deve provisionar pelo menos 2 réplicas de computação, incluindo a réplica de computação principal. Essa é a configuração padrão. Dessa forma, há uma réplica hot-standby disponível que serve como um alvo de failover.

## <a name="data-size-and-storage-questions"></a>Perguntas sobre tamanho e armazenamento de dados

### <a name="what-is-the-maximum-database-size-supported-with-hyperscale"></a>Qual é o tamanho máximo do banco de dados suportado com hyperscale

100 TB.

### <a name="what-is-the-size-of-the-transaction-log-with-hyperscale"></a>Qual é o tamanho do log de transações com a Hiperescala

O log de transações com a Hiperescala é praticamente infinito. Você não precisa se preocupar com a falta de espaço de log em um sistema que tenha um alto throughput de log. No entanto, a taxa de geração de log pode ser estrangulada para escrever cargas de trabalho agressivamente contínuas. A taxa de geração de log sustentada máxima é de 100 MB/s.

### <a name="does-my-tempdb-scale-as-my-database-grows"></a>Será `tempdb` que minha escala como meu banco de dados cresce

Seu `tempdb` banco de dados está localizado no armazenamento SSD local e é dimensionado proporcionalmente ao tamanho da computação que você fornece. O `tempdb` seu é otimizado para fornecer o máximo de benefícios de desempenho. `tempdb`o tamanho não é configurável e é gerenciado para você.

### <a name="does-my-database-size-automatically-grow-or-do-i-have-to-manage-the-size-of-data-files"></a>Meu tamanho de banco de dados cresce automaticamente, ou eu tenho que gerenciar o tamanho dos arquivos de dados

O tamanho do seu banco de dados aumenta automaticamente à medida que você insere / ingressa mais dados.

### <a name="what-is-the-smallest-database-size-that-hyperscale-supports-or-starts-with"></a>Qual é o menor tamanho de banco de dados que a Hyperscale suporta ou começa com

40 GB. Um banco de dados Hyperscale é criado com um tamanho inicial de 10 GB. Então, ele começa a crescer 10 GB a cada 10 minutos, até chegar ao tamanho de 40 GB. Cada um desses mandos de 10 GB é alocado em um servidor de página diferente, a fim de fornecer mais IOPS e maior paralelismo de I/O. Por causa dessa otimização, mesmo que você escolha tamanho inicial de banco de dados menor que 40 GB, o banco de dados crescerá para pelo menos 40 GB automaticamente.

### <a name="in-what-increments-does-my-database-size-grow"></a>Em que incrementos o tamanho do meu banco de dados aumenta?

Cada arquivo de dados cresce 10 GB. Vários arquivos de dados podem crescer ao mesmo tempo.

### <a name="is-the-storage-in-hyperscale-local-or-remote"></a>É o armazenamento em Hyperscale local ou remoto

Na Hiperescala, os arquivos de dados são armazenados no armazenamento padrão do Azure. Os dados são totalmente armazenados em cache no armazenamento SSD local, em servidores de página próximos às réplicas de computação. Além disso, as réplicas computacionais possuem caches de dados no SSD local e na memória, para reduzir a frequência de buscar dados de servidores de páginas remotas.

### <a name="can-i-manage-or-define-files-or-filegroups-with-hyperscale"></a>Posso gerenciar ou definir arquivos ou grupos de arquivos com a Hiperescala

Não. Os arquivos de dados são adicionados automaticamente. As razões comuns para criar grupos de arquivos adicionais não se aplicam na arquitetura de armazenamento Hyperscale.

### <a name="can-i-provision-a-hard-cap-on-the-data-growth-for-my-database"></a>Posso provisionar um limite rígido no crescimento de dados para meu banco de dados

Não.

### <a name="how-are-data-files-laid-out-with-hyperscale"></a>Como os arquivos de dados são dispostos com a Hyperscale

Os arquivos de dados são controlados por servidores de página, com um servidor de página por arquivo de dados. À medida que o tamanho dos dados cresce, arquivos de dados e servidores de página associados são adicionados.

### <a name="is-database-shrink-supported"></a>A redução do banco de dados é suportada

Não.

### <a name="is-data-compression-supported"></a>A compactação de dados é suportada

Sim, incluindo compressão de linha, página e colunastore.

### <a name="if-i-have-a-huge-table-does-my-table-data-get-spread-out-across-multiple-data-files"></a>Se eu tiver uma tabela enorme, os dados da minha tabela serão espalhados por vários arquivos de dados

Sim. As páginas de dados associadas a uma determinada tabela podem acabar em vários arquivos de dados, que fazem parte do mesmo grupo de arquivos. O SQL Server usa [a estratégia de preenchimento proporcional](https://docs.microsoft.com/sql/relational-databases/databases/database-files-and-filegroups#file-and-filegroup-fill-strategy) para distribuir dados sobre arquivos de dados.

## <a name="data-migration-questions"></a>Perguntas sobre migração de dados

### <a name="can-i-move-my-existing-azure-sql-databases-to-the-hyperscale-service-tier"></a>Posso mover meus bancos de dados SQL do Azure existentes para a camada de serviço de Hiperescala

Sim. Você pode mover seus bancos de dados SQL do Azure existentes para a Hiperescala. Esta é uma migração unidirecional. Você não pode mover bancos de dados da Hiperescala para outro nível de serviço. Para provas de conceito (POCs), recomendamos que você faça uma cópia do seu banco de dados e migre a cópia para hyperscale.
  
### <a name="can-i-move-my-hyperscale-databases-to-other-service-tiers"></a>Posso mover meus bancos de dados Hyperscale para outros níveis de serviço

Não. Neste momento, você não pode mover um banco de dados Hyperscale para outro nível de serviço.

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Perco alguma funcionalidade ou recursos após a migração para a camada de serviço Hiperescala

Sim. Alguns dos recursos do Banco de Dados SQL do Azure ainda não são suportados no Hyperscale, incluindo, mas não se limitando à retenção de backup a longo prazo. Depois de migrar seus bancos de dados para o Hiperescala, esses recursos param de funcionar.  Esperamos que essas limitações sejam temporárias.

### <a name="can-i-move-my-on-premises-sql-server-database-or-my-sql-server-database-in-a-cloud-virtual-machine-to-hyperscale"></a>Posso mover meu banco de dados SQL Server no local ou meu banco de dados SQL Server em uma máquina virtual em nuvem para Hyperscale

Sim. Você pode usar todas as tecnologias de migração existentes para migrar para a Hyperscale, incluindo replicação transacional e quaisquer outras tecnologias de movimentação de dados (Bulk Copy, Azure Data Factory, Azure Databricks, SSIS). Consulte também o [Serviço de Migração de Banco de Dados do Azure,](../dms/dms-overview.md)que suporta muitos cenários de migração.

### <a name="what-is-my-downtime-during-migration-from-an-on-premises-or-virtual-machine-environment-to-hyperscale-and-how-can-i-minimize-it"></a>Qual é o meu tempo de inatividade durante a migração de um ambiente de máquina virtual ou local para hyperscale, e como posso minimizá-lo

O tempo de inatividade para migração para hyperscale é o mesmo que o tempo de inatividade quando você migra seus bancos de dados para outros níveis de serviço do Banco de Dados SQL do Azure. Você pode usar [replicação transacional](replication-to-sql-database.md#data-migration-scenario
) para minimizar a migração do tempo de inatividade para bancos de dados com até poucos TB de tamanho. Para bancos de dados muito grandes (10+ TB), você pode considerar migrar dados usando ADF, Spark ou outras tecnologias de movimentação de dados.

### <a name="how-much-time-would-it-take-to-bring-in-x-amount-of-data-to-hyperscale"></a>Quanto tempo levaria para trazer x quantidade de dados para hyperscale

A hyperscale é capaz de consumir 100 MB/s de dados novos/alterados, mas o tempo necessário para mover dados para bancos de dados SQL do Azure também é afetado pelo throughput de rede disponível, velocidade de leitura de fonte e o objetivo do nível de serviço do banco de dados de destino.

### <a name="can-i-read-data-from-blob-storage-and-do-fast-load-like-polybase-in-sql-data-warehouse"></a>Posso ler dados do armazenamento blob e fazer carga rápida (como polybase no SQL Data Warehouse)

Você pode ter um aplicativo cliente ler dados do Azure Storage e carregar a carga de dados em um banco de dados Hyperscale (assim como você pode com qualquer outro banco de dados SQL do Azure). Atualmente, o Polybase não é suportado no Banco de Dados SQL do Azure. Como alternativa para fornecer carga rápida, você pode usar [a Fábrica de Dados Do Azure,](https://docs.microsoft.com/azure/data-factory/)ou usar um trabalho de Faísca no [Azure Databricks](https://docs.microsoft.com/azure/azure-databricks/) com o [conector Spark para SQL](sql-database-spark-connector.md). O conector do Spark SQL dá suporte à inserção em massa.

Também é possível ler em massa dados da loja Azure Blob usando BULK INSERT ou OPENROWSET: [Exemplos de acesso em massa a dados no Armazenamento Azure Blob](https://docs.microsoft.com/sql/relational-databases/import-export/examples-of-bulk-access-to-data-in-azure-blob-storage?view=sql-server-2017#accessing-data-in-a-csv-file-referencing-an-azure-blob-storage-location).

A recuperação simples ou o modelo de log em massa não é suportada na Hiperescala. O modelo de recuperação completo é necessário para fornecer alta disponibilidade e recuperação pontual. No entanto, a arquitetura de log hyperscale fornece uma taxa de ingestão de dados melhor em comparação com outros níveis de serviço do Banco de Dados SQL do Azure.

### <a name="does-hyperscale-allow-provisioning-multiple-nodes-for-parallel-ingesting-of-large-amounts-of-data"></a>A Hyperscale permite o provisionamento de vários nódulos para a ingestão paralela de grandes quantidades de dados

Não. Hyperscale é uma arquitetura simétrica de vários processamentos (SMP) e não é um processamento massivamente paralelo (MPP) ou uma arquitetura multi-mestre. Você só pode criar várias réplicas para dimensionar cargas de trabalho somente leitura.

### <a name="what-is-the-oldest-sql-server-version-supported-for-migration-to-hyperscale"></a>Qual é a versão mais antiga do SQL Server suportada para migração para hyperscale

SQL Server 2005. Para obter mais informações, consulte [Migrar para um único banco de dados ou um banco de dados em pool](sql-database-single-database-migrate.md#migrate-to-a-single-database-or-a-pooled-database). Para problemas de compatibilidade, consulte [Resolvendo problemas de compatibilidade de migração do banco de dados](sql-database-single-database-migrate.md#resolving-database-migration-compatibility-issues).

### <a name="does-hyperscale-support-migration-from-other-data-sources-such-as-amazon-aurora-mysql-postgresql-oracle-db2-and-other-database-platforms"></a>O Hyperscale suporta a migração de outras fontes de dados, como Amazon Aurora, MySQL, PostgreSQL, Oracle, DB2 e outras plataformas de banco de dados

Sim. [O Azure Database Migration Service](../dms/dms-overview.md) suporta muitos cenários de migração.

## <a name="business-continuity-and-disaster-recovery-questions"></a>Questões de continuidade de negócios e recuperação de desastres

### <a name="what-slas-are-provided-for-a-hyperscale-database"></a>Quais SLAs são fornecidos para um banco de dados Hyperscale

Consulte [SLA para Banco de Dados SQL do Azure](https://azure.microsoft.com/support/legal/sla/sql-database/v1_4/). Réplicas adicionais de computação secundária aumentam a disponibilidade, até 99,99% para um banco de dados com duas ou mais réplicas de computação secundária.

### <a name="are-the-database-backups-managed-for-me-by-the-azure-sql-database-service"></a>Os backups de banco de dados são gerenciados para mim pelo serviço Banco de Dados SQL do Azure

Sim.

### <a name="how-often-are-the-database-backups-taken"></a>A frequência com que os backups de banco de dados são feitos

Não há backups completos, diferenciais e de log tradicionais para bancos de dados Hyperscale. Em vez disso, há instantâneos de armazenamento regulares de arquivos de dados. O registro gerado é simplesmente retido como está para o período de retenção configurado, permitindo a restauração a qualquer ponto dentro do período de retenção.

### <a name="does-hyperscale-support-point-in-time-restore"></a>O ponto de suporte de hiperescala no tempo restaura

Sim.

### <a name="what-is-the-recovery-point-objective-rporecovery-time-objective-rto-for-database-restore-in-hyperscale"></a>O que é o RpO (Recovery Point Objective, objetivo de ponto de recuperação)/RTO (Recovery Time Objective, objetivo de tempo de recuperação) para restauração de banco de dados em hiperescala

O RPO é 0 min. A meta do RTO é inferior a 10 minutos, independentemente do tamanho do banco de dados. 

### <a name="does-database-backup-affect-compute-performance-on-my-primary-or-secondary-replicas"></a>O backup do banco de dados afeta o desempenho da computação nas minhas réplicas primárias ou secundárias

Não. Os backups são gerenciados pelo subsistema de armazenamento e aproveitam os instantâneos de armazenamento. Eles não afetam as cargas de trabalho dos usuários.

### <a name="can-i-perform-geo-restore-with-a-hyperscale-database"></a>Posso realizar a georestauração com um banco de dados Hyperscale

Sim. A restauração geográfica é totalmente suportada. Ao contrário da restauração point-in-time, a georestauração requer uma operação de tamanho de dados. Os arquivos de dados são copiados em paralelo, de modo que a duração desta operação depende principalmente do tamanho do maior arquivo no banco de dados, em vez do tamanho total do banco de dados. O tempo de georestauração será significativamente menor se o banco de dados for restaurado na região do Azure, que está [emparelhada](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) com a região do banco de dados de origem.

### <a name="can-i-set-up-geo-replication-with-hyperscale-database"></a>Posso configurar a replicação geográfica com o banco de dados Hyperscale

Não no momento.

### <a name="can-i-take-a-hyperscale-database-backup-and-restore-it-to-my-on-premises-server-or-on-sql-server-in-a-vm"></a>Posso pegar um backup de banco de dados Hyperscale e restaurá-lo no meu servidor local ou no SQL Server em um VM

Não. O formato de armazenamento para bancos de dados Hyperscale é diferente de qualquer versão lançada do SQL Server, e você não controla backups ou tem acesso a eles. Para tirar seus dados de um banco de dados Hyperscale, você pode extrair dados usando quaisquer tecnologias de movimentação de dados, ou seja, Azure Data Factory, Azure Databricks, SSIS, etc.

## <a name="cross-feature-questions"></a>Perguntas sobre recursos cruzados

### <a name="do-i-lose-any-functionality-or-capabilities-after-migration-to-the-hyperscale-service-tier"></a>Perco alguma funcionalidade ou recursos após a migração para a camada de serviço Hiperescala

Sim. Alguns dos recursos do Banco de Dados SQL do Azure não são suportados em Hyperscale, incluindo, mas não se limitando à retenção de backup a longo prazo. Depois de migrar seus bancos de dados para o Hiperescala, esses recursos param de funcionar.

### <a name="will-polybase-work-with-hyperscale"></a>A Polybase funcionará com a Hyperscale

Não. O Polybase não é suportado no Banco de Dados SQL do Azure.

### <a name="does-hyperscale-have-support-for-r-and-python"></a>A Hyperscale tem suporte para R e Python

Não no momento.

### <a name="are-compute-nodes-containerized"></a>São nós computados containerizados

Não. Os processos de hiperescala são executados em um nó [de malha](https://azure.microsoft.com/services/service-fabric/) de serviço (VMs), não em contêineres.

## <a name="performance-questions"></a>Perguntas de desempenho

### <a name="how-much-write-throughput-can-i-push-in-a-hyperscale-database"></a>Quanto throughput de gravação eu posso empurrar em um banco de dados Hyperscale

O limite de throughput de log de transação é definido como 100 MB/s para qualquer tamanho de computação hyperscale. A capacidade de alcançar essa taxa depende de vários fatores, incluindo, mas não se limitando ao tipo de carga de trabalho, configuração do cliente e ter capacidade computacional suficiente na réplica de computação primária para produzir log a essa taxa.

### <a name="how-many-iops-do-i-get-on-the-largest-compute"></a>Quantos IOPS eu recebo na maior computação

A latência IOPS e IO variará dependendo dos padrões de carga de trabalho. Se os dados que estão sendo acessados forem armazenados em cache na réplica de computação, você verá desempenho semelhante de IO como o do SSD local.

### <a name="does-my-throughput-get-affected-by-backups"></a>Meu rendimento é afetado por backups

Não. A computação é dissociada da camada de armazenamento. Isso elimina o impacto de desempenho do backup.

### <a name="does-my-throughput-get-affected-as-i-provision-additional-compute-replicas"></a>Meu throughput é afetado à medida que eu provisão réplicas adicionais de computação

Como o armazenamento é compartilhado e não há replicação física direta acontecendo entre réplicas de computação primária e secundária, o throughput na réplica primária não será diretamente afetado pela adição de réplicas secundárias. No entanto, podemos acelerar a carga de trabalho contínua de gravação agressiva na principal para permitir que o log se aplique em réplicas secundárias e servidores de página para recuperar o desempenho, para evitar um desempenho de leitura ruim em réplicas secundárias.

### <a name="how-do-i-diagnose-and-troubleshoot-performance-problems-in-a-hyperscale-database"></a>Como diagnosticar e solucionar problemas de desempenho em um banco de dados Hyperscale

Para a maioria dos problemas de desempenho, particularmente aqueles que não estão enraizados no desempenho do armazenamento, as etapas comuns de diagnóstico e solução de problemas do SQL Server se aplicam. Para diagnósticos de armazenamento específicos de hiperescala, consulte diagnósticos de [solução de problemas de desempenho do SQL Hyperscale](sql-database-hyperscale-performance-diagnostics.md).

## <a name="scalability-questions"></a>Perguntas sobre escalabilidade

### <a name="how-long-would-it-take-to-scale-up-and-down-a-compute-replica"></a>Quanto tempo levaria para escalar uma réplica computacional

A dimensionamento da computação para cima ou para baixo deve levar de 5 a 10 minutos, independentemente do tamanho dos dados.

### <a name="is-my-database-offline-while-the-scaling-updown-operation-is-in-progress"></a>Meu banco de dados está off-line enquanto a operação de aumento/redução está em andamento?

Não. A ampliação e a redução estarão online.

### <a name="should-i-expect-connection-drop-when-the-scaling-operations-are-in-progress"></a>Devo esperar queda de conexão quando as operações de dimensionamento estão em andamento

A escala para cima ou para baixo resulta da queda das conexões existentes quando um failover acontece no final da operação de dimensionamento. Adicionar réplicas secundárias não resulta em quedas de conexão.

### <a name="is-the-scaling-up-and-down-of-compute-replicas-automatic-or-end-user-triggered-operation"></a>O dimensionamento para cima e para baixo das réplicas de computação é automático ou o usuário final acionado operação

Usuário final. Não é automático.  

### <a name="does-the-size-of-my-tempdb-database-also-grow-as-the-compute-is-scaled-up"></a>Será que o `tempdb` tamanho do meu banco de dados também cresce à medida que o cálculo é ampliado

Sim. O `tempdb` banco de dados aumentará automaticamente à medida que a computação crescer.  

### <a name="can-i-provision-multiple-primary-compute-replicas-such-as-a-multi-master-system-where-multiple-primary-compute-heads-can-drive-a-higher-level-of-concurrency"></a>Posso prover várias réplicas básicas de computação, como um sistema multi-mestre, onde vários chefes de computação primária podem conduzir um nível mais alto de concorrência

Não. Apenas a réplica de computação primária aceita solicitações de leitura/gravação. Réplicas de computação secundária satisfaz apenas solicitações somente de leitura.

## <a name="read-scale-out-questions"></a>Leia perguntas de escala

### <a name="how-many-secondary-compute-replicas-can-i-provision"></a>Quantas réplicas de computação secundárias eu posso propor

Criamos uma réplica secundária para bancos de dados Hyperscale por padrão. Se você quiser ajustar o número de réplicas, você pode fazê-lo usando [o portal Azure](https://portal.azure.com) ou [a API REST](https://docs.microsoft.com/rest/api/sql/databases/createorupdate).

### <a name="how-do-i-connect-to-these-secondary-compute-replicas"></a>Como me conectar a essas réplicas de computação secundárias

Você pode se conectar a essas réplicas adicionais de computação somente leitura definindo o `ApplicationIntent` argumento em sua seqüência de conexões para `ReadOnly`. Todas as conexões marcadas com `ReadOnly` são automaticamente roteadas para uma das réplicas adicionais de computação somente leitura.  

### <a name="how-do-i-validate-if-i-have-successfully-connected-to-secondary-compute-replica-using-ssms-or-other-client-tools"></a>Como validar se me conectei com sucesso à réplica de computação secundária usando SSMS ou outras ferramentas do cliente?

Você pode executar a seguinte consulta T-SQL: `SELECT DATABASEPROPERTYEX ('<database_name>', 'Updateability')`.
O resultado `READ_ONLY` é se você estiver conectado a uma `READ_WRITE` réplica secundária somente leitura e se estiver conectado à réplica primária. Observe que o contexto do banco de dados deve ser `master` definido para o nome do banco de dados Hyperscale, não para o banco de dados.

### <a name="can-i-create-a-dedicated-endpoint-for-a-read-scale-out-replica"></a>Posso criar um ponto final dedicado para uma réplica de escala de leitura

Não. Você só pode se conectar a réplicas `ApplicationIntent=ReadOnly`de escala de leitura especificando .

### <a name="does-the-system-do-intelligent-load-balancing-of-the-read-workload"></a>O sistema faz o balanceamento de carga inteligente da carga de trabalho de leitura

Não. Uma nova conexão com a intenção somente de leitura é redirecionada para uma réplica arbitrária de escala de leitura.

### <a name="can-i-scale-updown-the-secondary-compute-replicas-independently-of-the-primary-replica"></a>Posso escalar para cima/para baixo as réplicas de computação secundárias independentemente da réplica primária

Não. A réplica de computação secundária também é usada como alvos de failover de alta disponibilidade, então eles precisam ter a mesma configuração que a principal para fornecer desempenho esperado após failover.

### <a name="do-i-get-different-tempdb-sizing-for-my-primary-compute-and-my-additional-secondary-compute-replicas"></a>Eu recebo `tempdb` tamanho diferente para o meu cálculo primário e minhas réplicas secundárias adicionais de computação

Não. Seu `tempdb` banco de dados é configurado com base no provisionamento do tamanho da computação, suas réplicas de computação secundária são do mesmo tamanho da computação primária.

### <a name="can-i-add-indexes-and-views-on-my-secondary-compute-replicas"></a>Posso adicionar índices e visualizações nas minhas réplicas de computação secundária

Não. Os bancos de dados de hiperescala têm armazenamento compartilhado, o que significa que todas as réplicas computacionais veem as mesmas tabelas, índices e visualizações. Se você quiser índices adicionais otimizados para leituras no secundário, você deve adicioná-los no principal.

### <a name="how-much-delay-is-there-going-to-be-between-the-primary-and-secondary-compute-replicas"></a>Quanto atraso haverá entre as réplicas de computação primária e secundária

A latência de dados desde o momento em que uma transação é cometida no principal até o momento em que é visível em um secundário depende da taxa de geração de log atual. A latência típica dos dados é em milissegundos baixos.

## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o nível de serviço Hyperscale, consulte [o nível de serviço Hyperscale](sql-database-service-tier-hyperscale.md).
