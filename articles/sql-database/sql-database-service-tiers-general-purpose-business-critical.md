---
title: Propósito geral e negócios críticos
description: O artigo discute os níveis gerais de serviços e serviços críticos para os negócios no modelo de compras baseado em vCore.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 01/30/2020
ms.openlocfilehash: 09cc9e1475616700aa77cdf92fd7ca808cd4290c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937848"
---
# <a name="azure-sql-database-service-tiers"></a>Camadas de serviço do Banco de Dados SQL do Azure

O Banco de Dados SQL do Azure é baseado na arquitetura do mecanismo de banco de dados SQL Server que é ajustada para o ambiente em nuvem para garantir 99,99% de disponibilidade, mesmo que haja uma falha na infra-estrutura. Três níveis de serviço são usados no Banco de Dados SQL do Azure, cada um com um modelo arquitetônico diferente. Essas camadas de serviço são:

- [Propósito geral,](sql-database-service-tier-general-purpose.md)que é projetado para cargas de trabalho orientadas ao orçamento.
- [Hyperscale](sql-database-service-tier-hyperscale.md), que foi projetado para a maioria das cargas de trabalho de negócios, fornecendo recursos de armazenamento altamente escalável, escala de leitura e recursos de restauração rápida de banco de dados.
- [Business critical](sql-database-service-tier-business-critical.md), que é projetado para cargas de trabalho de baixa latência com alta resiliência a falhas e failovers rápidos.

Este artigo discute diferenças entre os níveis de serviço, as considerações de armazenamento e backup para os níveis de serviço críticos de propósito geral e de negócios no modelo de compras baseado em vCore.

## <a name="service-tier-comparison"></a>Comparação de nível de serviço

A tabela a seguir descreve as principais diferenças entre os níveis de serviço para a última geração (Gen5). Observe que as características do nível de serviço podem ser diferentes no Banco de Dados Único e na Instância Gerenciada.

| | Tipo de recurso | Uso Geral |  Hiperescala | Comercialmente Crítico |
|:---:|:---:|:---:|:---:|:---:|
| **Mais adequado para** | |  Oferece opções equilibradas de computação e armazenamento orientadas ao orçamento. | A maioria das cargas de trabalho comerciais. Tamanho de armazenamento de dimensionamento automático de até 100 TB, dimensionamento de computação vertical e horizontal fluido, restauração rápida do banco de dados. | Aplicações OLTP com alta taxa de transação e baixa latência de IO. Oferece maior resiliência a falhas e failovers rápidos usando várias réplicas atualizadas sincronicamente.|
|  **Disponível no tipo de recurso:** ||Banco de dados único / Elástico pool / instância gerenciada | Banco de dados individual | Banco de dados único / Elástico pool / instância gerenciada |
| **Tamanho da computação**|Banco de dados único / Elástico pool | 1 a 80 vCores | 1 a 80 vCores | 1 a 80 vCores |
| | Instância gerenciada | 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/D | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| | Pools de instâncias gerenciadas | 2, 4, 8, 16, 24, 32, 40, 64, 80 vCores | N/D | N/D |
| **Tipo de armazenamento** | Todos | Armazenamento remoto Premium (por instância) | Armazenamento desacoplado com cache SSD local (por instância) | Armazenamento SSD local super rápido (por instância) |
| **Tamanho do banco de dados** | Banco de dados único / Elástico pool | 5 GB – 4 TB | Até 100 TB | 5 GB – 4 TB |
| | Instância gerenciada  | 32 GB A 8 TB | N/D | 32 GB – 4 TB |
| **Tamanho de armazenamento** | Banco de dados único / Elástico pool | 5 GB – 4 TB | Até 100 TB | 5 GB – 4 TB |
| | Instância gerenciada  | 32 GB A 8 TB | N/D | 32 GB – 4 TB |
| **Tamanho tempDB** | Banco de dados único / Elástico pool | [32 GB por vCore](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | [32 GB por vCore](sql-database-vcore-resource-limits-single-databases.md#hyperscale---provisioned-compute---gen5) | [32 GB por vCore](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Instância gerenciada  | [24 GB por vCore](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/D | Até 4 TB - [limitado pelo tamanho do armazenamento](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
| **Log write throughput** | Banco de dados individual | [1.875 MB/s por vCore (máx 30 MB/s)](sql-database-vcore-resource-limits-single-databases.md#general-purpose---provisioned-compute---gen4) | 100 MB/s | [6 MB/s por vCore (máx 96 MB/s)](sql-database-vcore-resource-limits-single-databases.md#business-critical---provisioned-compute---gen4) |
| | Instância gerenciada | [3 MB/s por vCore (máximo de 22 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) | N/D | [4 MB/s por vcore (máx 48 MB/s)](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) |
|**Disponibilidade**|Todos| 99,99% |  [99,95% com uma réplica secundária, 99,99% com mais réplicas](sql-database-service-tier-hyperscale-faq.md#what-slas-are-provided-for-a-hyperscale-database) | 99,99% <br/> [99,995% com base de dados única redundante da zona](https://azure.microsoft.com/blog/understanding-and-leveraging-azure-sql-database-sla/) |
|**Backups**|Todos|RA-GRS, 7-35 dias (7 dias por padrão)| RA-GRS, 7 dias, recuperação constante de ponto no tempo (PITR) | RA-GRS, 7-35 dias (7 dias por padrão) |
|**OLTP na memória** | | N/D | N/D | Disponível |
|**Réplicas somente para leitura**| | 0 embutido <br> 0 - 4 usando [geo-replicação](sql-database-active-geo-replication.md) | 0 - 4 embutidos | 1 embutido, incluído no preço <br> 0 - 4 usando [geo-replicação](sql-database-active-geo-replication.md) |
|**Preços/faturamento** | Banco de dados individual | [o vCore, o armazenamento reservado e o armazenamento de backup](https://azure.microsoft.com/pricing/details/sql-database/single/) são carregados. <br/>O IOPS não é cobrado. | [vCore para cada réplica e armazenamento usado](https://azure.microsoft.com/pricing/details/sql-database/single/) são carregados. <br/>IOPS ainda não cobrado. | [o vCore, o armazenamento reservado e o armazenamento de backup](https://azure.microsoft.com/pricing/details/sql-database/single/) são carregados. <br/>O IOPS não é cobrado. |
|| Banco de Dados SQL | [O vCore, o armazenamento reservado e o armazenamento de backup](https://azure.microsoft.com/pricing/details/sql-database/managed/) são carregados. <br/>IOPS não é cobrado| N/D | [O vCore, o armazenamento reservado e o armazenamento de backup](https://azure.microsoft.com/pricing/details/sql-database/managed/) são carregados. <br/>O IOPS não é cobrado.| 
|**Modelos de desconto**| | [Instâncias reservadas](sql-database-reserved-capacity.md)<br/>[Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (não disponível em assinaturas de dev/teste)<br/>Assinaturas de dev/teste [corporativos](https://azure.microsoft.com/offers/ms-azr-0148p/) e [pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (não disponível em assinaturas de dev/teste)<br/>Assinaturas de dev/teste [corporativos](https://azure.microsoft.com/offers/ms-azr-0148p/) e [pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0023p/)| [Instâncias reservadas](sql-database-reserved-capacity.md)<br/>[Azure Hybrid Benefit](sql-database-azure-hybrid-benefit.md) (não disponível em assinaturas de dev/teste)<br/>Assinaturas de dev/teste [corporativos](https://azure.microsoft.com/offers/ms-azr-0148p/) e [pay-as-you-go](https://azure.microsoft.com/offers/ms-azr-0023p/)|

Para obter mais informações, consulte as diferenças detalhadas entre os níveis de serviço em [banco de dados único (vCore),](sql-database-vcore-resource-limits-single-databases.md) [pools de banco de dados único (vCore),](sql-database-dtu-resource-limits-single-databases.md) [banco de dados único (DTU),](sql-database-dtu-resource-limits-single-databases.md) [pools de banco de dados único (DTU)](sql-database-dtu-resource-limits-single-databases.md)e páginas [de Instância Gerenciada.](sql-database-managed-instance-resource-limits.md)

> [!NOTE]
> Para obter informações sobre o nível de serviço de hiperescala no modelo de compras baseado em vCore, consulte [o nível de serviço de hiperescala](sql-database-service-tier-hyperscale.md). Para obter uma comparação do modelo de compra baseado em vCore com o modelo de compra baseado em DTU, consulte [Modelos e recursos de compra do Banco de Dados SQL do Azure](sql-database-purchase-models.md).

## <a name="data-and-log-storage"></a>Armazenamento de dados e de log

Os seguintes fatores afetam a quantidade de armazenamento usado para dados e arquivos de registro e se aplica a Propósito Geral e Business Critical. Para obter detalhes sobre dados e armazenamento de log em Hyperscale, consulte [nível de serviço Hyperscale](sql-database-service-tier-hyperscale.md).

- O armazenamento alocado é usado por arquivos de dados (MDF) e arquivos de log (LDF).
- Cada tamanho de cálculo de banco de dados suporta um tamanho máximo de banco de dados, com um tamanho máximo padrão de 32 GB.
- Quando você configura o tamanho único do banco de dados (o tamanho do arquivo MDF), 30% mais armazenamento adicional é adicionado automaticamente para suportar arquivos LDF.
- O tamanho do armazenamento de uma instância gerenciada do Banco de Dados SQL deve ser especificado em múltiplos de 32 GB.
- Você pode selecionar qualquer tamanho de banco de dados único entre 10 GB e o máximo suportado.
  - Para armazenamento nos níveis de serviço padrão ou de uso geral, aumente ou diminua o tamanho em incrementos de 10 GB.
  - Para armazenamento nos níveis de serviço críticos premium ou comercial, aumente ou diminua o tamanho em incrementos de 250 GB.
- No nível de serviço `tempdb` de propósito geral, usa um SSD conectado, e esse custo de armazenamento está incluído no preço vCore.
- No nível de serviço `tempdb` crítico de negócios, compartilha o SSD anexado `tempdb` com os arquivos MDF e LDF, e o custo de armazenamento está incluído no preço vCore.

> [!IMPORTANT]
> Você é cobrado pelo armazenamento total alocado para arquivos MDF e LDF.

Para monitorar o tamanho total atual dos seus arquivos MDF e LDF, use [sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql). Para monitorar o tamanho atual dos arquivos MDF e LDF individuais, use [sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [Gerenciar espaço de arquivos no Banco de Dados SQL do Azure](sql-database-file-space-management.md).

## <a name="backups-and-storage"></a>Backups e armazenamento

O armazenamento para backups de banco de dados é alocado para suportar os recursos de recuperação point-in-time (PITR) e [de retenção de longo prazo (LTR)](sql-database-long-term-retention.md) do Banco de Dados SQL. Esse armazenamento é alocado separadamente para cada banco de dados e faturado como duas cobranças separadas por banco de dados.

- **PITR**: Os backups individuais do banco de dados são copiados para [o armazenamento geo-redundante de acesso de leitura (RA-GRS)](../storage/common/storage-designing-ha-apps-with-ragrs.md) automaticamente. O tamanho do armazenamento aumenta dinamicamente à medida que novos backups são criados. O armazenamento é usado por backups completos semanais, backups diferenciais diários e backups de log de transações, que são copiados a cada 5 minutos. O consumo de armazenamento depende da taxa de alteração do banco de dados e do período de retenção para backups. É possível configurar um período de retenção separado para cada banco de dados entre 7 e 35 dias. Um valor mínimo de armazenamento igual a 100% (1x) do tamanho do banco de dados é fornecido sem custo adicional. Para a maioria dos bancos de dados, esse valor é suficiente para armazenar 7 dias de backups.
- **LTR**: SQL Database oferece a você a opção de configurar a retenção a longo prazo de backups completos por até 10 anos. Se você configurar uma política DE LTR, esses backups serão armazenados automaticamente no armazenamento RA-GRS, mas você poderá controlar com que frequência os backups são copiados. Para atender a diferentes requisitos de conformidade, você pode selecionar diferentes períodos de retenção para backups semanais, mensais e/ou anuales. A configuração escolhida determina quanto armazenamento será usado para backups LTR. Para estimar o custo do armazenamento LTR, você pode usar a calculadora de preços ltr. Para obter mais informações, consulte [sql database de retenção a longo prazo](sql-database-long-term-retention.md).

## <a name="next-steps"></a>Próximas etapas

- Para obter detalhes sobre os tamanhos de computação e tamanhos de armazenamento específicos disponíveis para um único banco de dados nos níveis de serviços críticos de propósito geral e de negócios, consulte [os limites de recursos baseados no SQL Database vCore para bancos de dados únicos](sql-database-vcore-resource-limits-single-databases.md).
- Para obter detalhes sobre os tamanhos de computação e tamanhos de armazenamento específicos disponíveis para pools elásticos nos níveis de serviço sustais e de serviços críticos para os negócios, consulte [os limites de recursos baseados no SQL Database vCore para pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md).
