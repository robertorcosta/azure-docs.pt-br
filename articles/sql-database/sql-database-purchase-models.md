---
title: Modelos de compra
description: Conheça os modelos de compra disponíveis para o Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 03/09/2020
ms.openlocfilehash: 97ce402045cfd2c990b457c5d4d06888cda632d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79255984"
---
# <a name="choose-between-the-vcore-and-the-dtu-purchasing-models"></a>Escolha entre os modelos de compra vCore e DTU

O Banco de Dados SQL do Azure permite que você compre facilmente uma plataforma totalmente gerenciada como um mecanismo de banco de dados de serviço (PaaS) que se adapte às suas necessidades de desempenho e custo. Dependendo do modelo de implantação que você escolheu para o Banco de Dados SQL do Azure, você pode selecionar o modelo de compra que funciona para você:

- [Modelo de compra baseado em núcleo virtual (vCore)](sql-database-service-tiers-vcore.md) (recomendado). Este modelo de compra fornece uma escolha entre um nível de computação provisionado e um nível de computação sem servidor. Com o nível de computação provisionado, você escolhe a quantidade exata de recursos computacionais que são sempre provisionados para sua carga de trabalho. Com o nível de computação sem servidor, você especifica o autodimensionamento dos recursos de computação em um intervalo de computação configurável. Com este nível de computação, você também pode pausar automaticamente e retomar o banco de dados com base na atividade de carga de trabalho. O preço unitário vCore por unidade de tempo é menor no nível de computação provisionado do que no nível de computação sem servidor.
- [Modelo de compra baseado em unidade de transação de banco de dados (DTU).](sql-database-service-tiers-dtu.md) Este modelo de compra fornece pacotes de computação e armazenamento em paciência balanceados para cargas de trabalho comuns.

Diferentes modelos de compra estão disponíveis para diferentes modelos de implantação do Azure SQL Database:

- As opções de implantação [banco de dados individual](sql-database-single-databases-manage.md) e [pool elástico](sql-database-elastic-pool.md) no [Banco de Dados SQL do Azure](sql-database-technical-overview.md) oferecem tanto o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md) quanto o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).
- A opção de implantação [de instância gerenciada](sql-database-managed-instance.md) no Banco de Dados SQL do Azure oferece apenas o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).
- O [nível de serviço Hyperscale](sql-database-service-tier-hyperscale.md) está disponível para bancos de dados únicos que estão usando o modelo de compra baseado em [vCore](sql-database-service-tiers-vcore.md).

A tabela e o gráfico a seguir comparam e contrastam os modelos de compra baseados em vCore e DTU:

|**Modelo de compra**|**Descrição**|**Mais adequado para**|
|---|---|---|
|Modelo baseado em DTU|Este modelo é baseado em uma medida empacotada de recursos de computação, armazenamento e I/O. Os tamanhos de computação são expressos em DTUs para bancos de dados únicos e em unidades de transações de banco de dados elásticas (eDTUs) para pools elásticos. Para obter mais informações sobre DTUs e eDTUs, consulte [O que são DTUs e eDTUs?](sql-database-purchase-models.md#dtu-based-purchasing-model).|Melhor para clientes que desejam opções de recursos simples e pré-configuradas.|
|Modelo baseado em vCore|Esse modelo permite escolher recursos de armazenamento e computação de maneira independente. O modelo de compra baseado em vCore também permite que você use o [Benefício Híbrido do Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para poupar custos.|Mais adequado para clientes que valorizam flexibilidade, controle e transparência.|
||||  

![comparação do modelo de preços](./media/sql-database-service-tiers/pricing-model.png)

## <a name="compute-costs"></a>Custos de computação

### <a name="provisioned-compute-costs"></a>Custos de computação provisionados

No nível de computação provisionado, o custo de computação reflete a capacidade total de computação que é provisionada para a aplicação.

Na camada de serviço Business Critical, alocamos automaticamente pelo menos três réplicas. Para refletir essa alocação adicional de recursos computacionais, o preço no modelo de compras baseado em vCore é aproximadamente 2,7 vezes maior no nível de serviço sustal business critical do que no nível de serviço sustal. Da mesma forma, o preço de armazenamento mais alto por GB no nível de serviço Business Critical reflete os limites mais altos de IO e a menor latência do armazenamento SSD.

O custo do armazenamento de backup é o mesmo para o nível de serviço Business Critical e o nível de serviço General Purpose, porque ambos os níveis usam armazenamento padrão para backups.

### <a name="serverless-compute-costs"></a>Custos de computação sem servidor

Para obter uma descrição de como a capacidade de computação é definida e os custos são calculados para o nível de computação sem servidor, consulte [SQL Database serverless](sql-database-serverless.md).

## <a name="storage-costs"></a>Custos de armazenamento

Diferentes tipos de armazenamento são cobrados de formas diferentes. Para o armazenamento de dados, você é cobrado pelo armazenamento provisionado com base no tamanho máximo do banco de dados ou do pool selecionado. O custo não muda a menos que você reduza ou aumente esse máximo. O armazenamento de backup está associado a backups automatizados de sua instância é alocado dinamicamente. Aumentar o período de retenção de backup aumenta o armazenamento de backup consumido pela sua instância.

Por padrão, 7 dias de backups automatizados de seus bancos de dados são copiados para uma conta de armazenamento padrão de armazenamento geo-redundante de acesso de leitura (RA-GRS). Esse armazenamento é usado por backups completos semanais, backups diferenciais diários e backups de log de transações, que são copiados a cada 5 minutos. O tamanho dos registros de transações depende da taxa de alteração do banco de dados. Um valor mínimo de armazenamento igual a 100% do tamanho do banco de dados é fornecido sem custo adicional. O consumo adicional de armazenamento de backup é cobrado em GB por mês.

Para obter mais informações sobre preços de armazenamento, confira a página de [preços](https://azure.microsoft.com/pricing/details/sql-database/single/).

## <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

Um núcleo virtual (vCore) representa uma CPU lógica e oferece a opção de escolher entre gerações de hardware e as características físicas do hardware (por exemplo, o número de núcleos, a memória e o tamanho do armazenamento). O modelo de compra baseado em vCore oferece flexibilidade, controle, transparência do consumo de recursos individuais e uma maneira simples de traduzir os requisitos de carga de trabalho no local para a nuvem. Este modelo permite que você escolha os recursos de computação, memória e armazenamento com base em suas necessidades de carga de trabalho.

No modelo de compras baseado em vCore, você pode escolher entre os níveis de serviço [General Purpose](sql-database-high-availability.md#basic-standard-and-general-purpose-service-tier-availability) e [Business Critical](sql-database-high-availability.md#premium-and-business-critical-service-tier-availability) para [bancos de dados únicos,](sql-database-single-database-scale.md) [pools elásticos](sql-database-elastic-pool.md)e [instâncias gerenciadas.](sql-database-managed-instance.md) Para bancos de dados únicos, você também pode escolher o [nível de serviço Hyperscale](sql-database-service-tier-hyperscale.md).

O modelo de compra baseado em vCore permite que você escolha independentemente os recursos de computação e armazenamento, combine o desempenho no local e otimize o preço. No modelo de compra baseado em vCore, você paga por:

- Compute recursos (o nível de serviço + o número de vCores e a quantidade de memória + a geração de hardware).
- O tipo e a quantidade de dados e armazenamento de registro.
- Armazenamento de backup (RA-GRS).

> [!IMPORTANT]
> Os recursos de cálculo, I/O e armazenamento de dados e registros são cobrados por banco de dados ou pool elástico. O armazenamento de backup é cobrado por cada banco de dados. Para obter mais informações sobre encargos de instância gerenciada, confira [instâncias gerenciadas](sql-database-managed-instance.md).
> **Limitações da região:** Para a lista atual de regiões suportadas, consulte [os produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Para criar uma instância gerenciada em uma região que atualmente não é suportada, [envie uma solicitação de suporte através do portal Azure](quota-increase-request.md).

Se o seu banco de dados único ou pool elástico consome mais de 300 DTUs, a conversão para o modelo de compra baseado em vCore pode reduzir seus custos. Você pode converter usando sua API de escolha ou usando o portal Azure, sem tempo de inatividade. No entanto, a conversão não é necessária e não é feita automaticamente. Se o modelo de compra baseado em DTU atender aos seus requisitos de desempenho e de negócios, você deverá continuar utilizando-o.

Para converter do modelo de compra baseado em DTU para o modelo de compra baseado em vCore, selecione o tamanho da computação usando as seguintes regras de ouro:

- Cada 100 DTUs no nível padrão exigem pelo menos 1 vCore no nível de serviço Do Propósito Geral.
- Cada 125 DTUs no nível premium exigem pelo menos 1 vCore no nível de serviço Business Critical.

> [!NOTE]
> As diretrizes de dimensionamento DTU para vCore são aproximadas e são fornecidas para ajudar na estimativa inicial do objetivo de serviço de banco de dados de destino. A configuração ideal do banco de dados de destino é dependente da carga de trabalho. 
> 
> Alcançar a relação preço/desempenho ideal pode exigir aproveitar a flexibilidade do modelo vCore para ajustar o número de vCores, a geração de [hardware,](sql-database-service-tiers-vcore.md#hardware-generations)os níveis de [serviço](sql-database-service-tiers-vcore.md#service-tiers) e [computação,](sql-database-service-tiers-vcore.md#compute-tiers) bem como a sintonia de outros parâmetros de configuração de banco de dados, como [o grau máximo de paralelismo.](https://docs.microsoft.com/sql/relational-databases/query-processing-architecture-guide#parallel-query-processing)

## <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

Uma unidade de transação de banco de dados (DTU) representa uma medida combinada de CPU, memória, leituras e gravações. O modelo de compra baseado em DTU oferece um conjunto de pacotes pré-configurados de recursos de computação e inclui armazenamento para impulsionar diferentes níveis de desempenho do aplicativo. Se você preferir a simplicidade de um pacote pré-configurado e pagamentos fixos a cada mês, o modelo baseado em DTU pode ser mais adequado para suas necessidades.

No modelo de compras baseado em DTU, você pode escolher entre os níveis básico, padrão e de serviço premium para [bancos de dados únicos](sql-database-single-database-scale.md) e [pools elásticos.](sql-database-elastic-pool.md) O modelo de compra baseado em DTU não está disponível para [instâncias gerenciadas](sql-database-managed-instance.md).

### <a name="database-transaction-units-dtus"></a>DTUs (Unidades de Transação de Banco de Dados)

Para um único banco de dados em um tamanho de computação específico dentro de um nível de [serviço,](sql-database-single-database-scale.md)a Microsoft garante um certo nível de recursos para esse banco de dados (independente de qualquer outro banco de dados na nuvem DoZure). Essa garantia proporciona um nível previsível de desempenho. A quantidade de recursos alocados para um banco de dados é calculada como um número de DTUs e é uma medida empacotada de recursos de computação, armazenamento e I/O.

A proporção entre esses recursos é originalmente determinada por uma carga de trabalho de referência de [processamento de transações on-line (OLTP)](sql-database-benchmark-overview.md) projetada para ser típica de cargas de trabalho OLTP do mundo real. Quando sua carga de trabalho excede a quantidade de qualquer um desses recursos, seu rendimento é estrangulado, resultando em desempenho e intervalos mais lentos.

Os recursos usados pela sua carga de trabalho não afetam os recursos disponíveis para outros bancos de dados SQL na nuvem do Azure. Da mesma forma, os recursos usados por outras cargas de trabalho não afetam os recursos disponíveis para o seu banco de dados SQL.

![caixa delimitadora](./media/sql-database-what-is-a-dtu/bounding-box.png)

As DTUs são mais úteis para entender os recursos relativos que são alocados para bancos de dados SQL do Azure em diferentes tamanhos de computação e níveis de serviço. Por exemplo: 

- Dobrar os DTUs aumentando o tamanho da computação de um banco de dados equivale a dobrar o conjunto de recursos disponíveis para esse banco de dados.
- Um banco de dados P11 de nível de serviço premium com 1750 DTUs fornece 350x mais poder de computação DTU do que um banco de dados de nível de serviço básico com 5 DTUs.  

Para obter uma visão mais profunda sobre o consumo de recursos (DTU) de sua carga de trabalho, use [insights de desempenho de consulta](sql-database-query-performance.md) para:

- Identifique as principais consultas por CPU/duração/execução que podem ser potencialmente ajustadas para um melhor desempenho. Por exemplo, uma consulta intensiva em I/O pode se beneficiar de [técnicas de otimização na memória](sql-database-in-memory.md) para fazer melhor uso da memória disponível em um determinado nível de serviço e tamanho de computação.
- Aprofunde-se nos detalhes de uma consulta para visualizar seu texto e seu histórico de uso de recursos.
- Acesse recomendações de ajuste de desempenho que mostram ações tomadas pelo [SQL Database Advisor](sql-database-advisor.md).

### <a name="elastic-database-transaction-units-edtus"></a>Unidades de transação de banco de dados elásticos (eDTUs)

Para bancos de dados SQL que estão sempre disponíveis, em vez de fornecer um conjunto dedicado de recursos (DTUs) que podem nem sempre ser necessários, você pode colocar esses bancos de dados em um [pool elástico](sql-database-elastic-pool.md). Os bancos de dados em um pool elástico estão em um único servidor de banco de dados Azure SQL e compartilham um pool de recursos.

Os recursos compartilhados em um pool elástico são medidos por unidades de transação de banco de dados elásticos (eDTUs). Os pools elásticos fornecem uma solução simples e econômica para gerenciar metas de desempenho para vários bancos de dados que têm padrões de uso amplamente variados e imprevisíveis. Um pool elástico garante que todos os recursos não podem ser consumidos por um banco de dados no pool, ao mesmo tempo em que garante que cada banco de dados no pool tenha sempre uma quantidade mínima de recursos necessários disponíveis.

Um pool é fornecido com um número definido de eDTUs por um preço definido. No pool elástico, bancos de dados individuais podem fazer escala automática dentro dos limites configurados. Um banco de dados uma carga mais pesada consumirá mais eDTUs para atender à demanda. Bancos de dados cargas mais leves consumirão menos eDTUs. Bancos de dados sem carga não consumirão eDTUs. Como os recursos são provisionados para todo o pool, em vez de por banco de dados, os pools elásticos simplificam suas tarefas de gerenciamento e fornecem um orçamento previsível para o pool.

Você pode adicionar eDTUs adicionais a um pool existente sem tempo de inatividade do banco de dados e sem impacto nos bancos de dados no pool. Da mesma forma, se você não precisar mais de eDTUs extras, remova-os de uma piscina existente a qualquer momento. Você também pode adicionar bancos de dados ou subtrair bancos de dados de um pool a qualquer momento. Para reservar eDTUs para outras bases de dados, limite o número de eDTUs que um banco de dados pode usar uma carga pesada. Se um banco de dados subutilizar consistentemente recursos, mova-o para fora do pool e configure-o como um único banco de dados com uma quantidade previsível de recursos necessários.

### <a name="determine-the-number-of-dtus-needed-by-a-workload"></a>Determinar o número de DTUs necessárias para uma carga de trabalho

Se você quiser migrar uma carga de trabalho de máquina virtual existente no local ou sql server para o Banco de Dados SQL do Azure, use a [calculadora DTU](https://dtucalculator.azurewebsites.net/) para aproximar o número de DTUs necessários. Para obter uma carga de trabalho existente no Banco de Dados Azure SQL, use [insights sobre o desempenho da consulta](sql-database-query-performance.md) para entender o consumo de recursos de banco de dados (DTUs) e obtenha insights mais profundos para otimizar sua carga de trabalho. O [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database) dynamic management view (Detran) permite visualizar o consumo de recursos na última hora. A visualização do catálogo [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database) exibe o consumo de recursos nos últimos 14 dias, mas com uma fidelidade menor de médias de cinco minutos.

### <a name="determine-dtu-utilization"></a>Determinar a utilização do DTU

Para determinar a porcentagem média de utilização do DTU/eDTU em relação ao limite de DTU/eDTU de um banco de dados ou de um pool elástico, utilize a seguinte fórmula:

`avg_dtu_percent = MAX(avg_cpu_percent, avg_data_io_percent, avg_log_write_percent)`

Os valores de entrada para esta fórmula podem ser obtidos em [sys.dm_db_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database), [sys.resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)e [sys.elastic_pool_resource_stats](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database) DMVs. Em outras palavras, para determinar o percentual de utilização do DTU/eDTU em direção ao limite DeD/eDTU de um banco de dados ou de um pool elástico, escolha o maior valor percentual do seguinte: `avg_cpu_percent`, `avg_data_io_percent`e `avg_log_write_percent` em um determinado momento.

> [!NOTE]
> O limite de DTU de um banco de dados é determinado pela CPU, leituras, gravações e memória disponíveis para o banco de dados. No entanto, como o mecanismo de banco de dados SQL Server `avg_memory_usage_percent` normalmente usa toda a memória disponível para seu cache de dados para melhorar o desempenho, o valor geralmente será próximo de 100% independentemente da carga atual do banco de dados. Portanto, embora a memória influencie indiretamente o limite de DTU, ela não é usada na fórmula de utilização do DTU.
>

### <a name="workloads-that-benefit-from-an-elastic-pool-of-resources"></a>Cargas de trabalho que se beneficiam de um pool elástico de recursos

Os pools são adequados para bancos de dados com uma média de utilização de recursos baixa e picos de utilização relativamente pouco freqüentes. Para obter mais informações, consulte [Quando você deve considerar um pool elástico do Banco de Dados SQL?](sql-database-elastic-pool.md).

### <a name="hardware-generations-in-the-dtu-based-purchasing-model"></a>Gerações de hardware no modelo de compras baseado em DTU

No modelo de compras baseado em DTU, os clientes não podem escolher a geração de hardware usada para seus bancos de dados. Embora um determinado banco de dados geralmente permaneça em uma geração de hardware específica por um longo tempo (geralmente por vários meses), existem certos eventos que podem fazer com que um banco de dados seja movido para outra geração de hardware.

Por exemplo, um banco de dados pode ser movido para uma geração de hardware diferente se for dimensionado para um objetivo de serviço diferente, ou se a infra-estrutura atual em um data center está se aproximando de seus limites de capacidade, ou se o hardware usado atualmente está sendo desativado devido ao seu fim de vida.

Se um banco de dados for movido para diferentes hardwares, o desempenho da carga de trabalho pode mudar. O modelo DTU garante que o tempo de throughput e resposta da carga de trabalho de referência do [DTU](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-dtu#dtu-benchmark) permanecerá substancialmente idêntico à medida que o banco de dados se move para uma geração de hardware diferente, desde que seu objetivo de serviço (o número de DTUs) permaneça o mesmo. 

No entanto, em todo o amplo espectro de cargas de trabalho dos clientes em execução no Banco de Dados SQL do Azure, o impacto do uso de hardware diferente para o mesmo objetivo de serviço pode ser mais acentuado. Diferentes cargas de trabalho se beneficiarão de diferentes configurações de hardware e recursos. Portanto, para cargas de trabalho diferentes do benchmark DTU, é possível ver diferenças de desempenho se o banco de dados passar de uma geração de hardware para outra.

Por exemplo, um aplicativo sensível à latência de rede pode ver melhor desempenho no hardware Gen5 vs. Gen4 devido ao uso de Rede Acelerada no Gen5, mas um aplicativo usando IO de leitura intensiva pode ver melhor desempenho no hardware Gen4 vs. Gen5 devido ao maior relação de memória por núcleo em Gen4.

Clientes com cargas de trabalho sensíveis a alterações de hardware ou clientes que desejam controlar a escolha da geração de hardware para seu banco de dados podem usar o modelo [vCore](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore) para escolher sua geração de hardware preferida durante a criação e o dimensionamento do banco de dados. No modelo vCore, os limites de recursos de cada objetivo de serviço em cada geração de hardware são [documentados,](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-single-databases) tanto para bancos de dados únicos quanto para [piscinas elásticas.](https://docs.microsoft.com/azure/sql-database/sql-database-vcore-resource-limits-elastic-pools) Para obter mais informações sobre as gerações de hardware no modelo vCore, consulte [Hardware Generations](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers-vcore#hardware-generations).

## <a name="frequently-asked-questions-faqs"></a>Perguntas frequentes (FAQs)

### <a name="do-i-need-to-take-my-application-offline-to-convert-from-a-dtu-based-service-tier-to-a-vcore-based-service-tier"></a>Preciso tirar meu aplicativo offline para converter de um nível de serviço baseado em DTU para um nível de serviço baseado em vCore?

Não. Você não precisa tirar o aplicativo offline. Os novos níveis de serviço oferecem um método simples de conversão on-line que é semelhante ao processo existente de atualização de bancos de dados do padrão para o nível de serviço premium e o contrário. Você pode iniciar esta conversão usando o portal Azure, PowerShell, a CLI do Azure, O T-SQL ou a API REST. Consulte [Gerenciar bancos de dados únicos](sql-database-single-database-scale.md) e gerenciar [piscinas elásticas](sql-database-elastic-pool.md).

### <a name="can-i-convert-a-database-from-a-service-tier-in-the-vcore-based-purchasing-model-to-a-service-tier-in-the-dtu-based-purchasing-model"></a>Posso converter um banco de dados de um nível de serviço no modelo de compras baseado em vCore para um nível de serviço no modelo de compras baseado em DTU?

Sim, você pode facilmente converter seu banco de dados para qualquer objetivo de desempenho suportado usando o portal Azure, PowerShell, a Cli do Azure, o T-SQL ou a API REST. Consulte [Gerenciar bancos de dados únicos](sql-database-single-database-scale.md) e gerenciar [piscinas elásticas](sql-database-elastic-pool.md).

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre o modelo de compras baseado em vCore, consulte o [modelo de compra baseado em vCore](sql-database-service-tiers-vcore.md).
- Para obter mais informações sobre o modelo de compras baseado em DTU, consulte o [modelo de compra baseado em DTU](sql-database-service-tiers-dtu.md).
