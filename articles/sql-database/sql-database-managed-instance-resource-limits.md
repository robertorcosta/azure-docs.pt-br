---
title: Limites de recursos - instância gerenciada
description: Este artigo fornece uma visão geral dos limites de recursos do Banco de Dados SQL do Azure para instâncias gerenciadas.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop, sachinp, sstein
ms.date: 02/25/2020
ms.openlocfilehash: b2871ec87e4d7f337c26b3ff3de83c1c3c88aea2
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365379"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Visão geral Azure SQL Database gerenciou limites de recursos de instância gerenciada

Este artigo fornece uma visão geral das características técnicas e limites de recursos para a instância gerenciada do Azure SQL Database e fornece informações sobre como solicitar um aumento a esses limites.

> [!NOTE]
> Para diferenças em recursos suportados e instruções T-SQL, consulte [Diferenças de recursos](sql-database-features.md) e [Suporte à instrução T-SQL](sql-database-managed-instance-transact-sql-information.md). Para diferenças gerais entre os níveis de serviço em banco de dados único e instância gerenciada, consulte [comparação de nível de serviço](sql-database-service-tiers-general-purpose-business-critical.md#service-tier-comparison).

## <a name="hardware-generation-characteristics"></a>Características de geração de hardware

A instância gerenciada tem características e limites de recursos que dependem da infra-estrutura e arquitetura subjacentes. A instância gerenciada do Banco de Dados SQL do Azure pode ser implantada em duas gerações de hardware: Gen4 e Gen5. As gerações de hardware têm características diferentes, como descrito na tabela a seguir:

|   | **Gen4** | **Gen5** |
| --- | --- | --- |
| Hardware | Processadores Intel E5-2673 v3 (Haswell) de 2,4 GHz, SSD ligado vCore = 1 PP (núcleo físico) | Processadores Intel E5-2673 v4 (Broadwell) 2.3-GHz e Intel SP-8160 (Skylake), SSD NVMe rápido, vCore=1 LP (hiper-thread) |
| Número de vCores | 8, 16, 24 vCores | 4, 8, 16, 24, 32, 40, 64, 80 vCores |
| Memória máxima (relação memória/núcleo) | 7 GB por vCore<br/>Adicione mais vCores para obter mais memória. | 5.1 GB por vCore<br/>Adicione mais vCores para obter mais memória. |
| Memória OLTP max | Limite de instância: 1-1,5 GB por vCore| Limite de instância: 0,8 - 1,65 GB por vCore |
| Armazenamento reservado em instância máxima |  Propósito Geral: 8 TB<br/>Business Critical: 1 TB | Propósito Geral: 8 TB<br/> Business Critical 1 TB, 2 TB ou 4 TB, dependendo do número de núcleos |

> [!IMPORTANT]
> - O hardware Gen4 está sendo eliminado e não está mais disponível para as novas implantações. Todas as novas instâncias gerenciadas devem ser implantadas no hardware Gen5.
> - Considere [mover suas instâncias gerenciadas para o](sql-database-service-tiers-vcore.md) hardware Gen 5 para experimentar uma gama mais ampla de escalabilidade de vCore e armazenamento, rede acelerada, melhor desempenho de IO e latência mínima.

### <a name="in-memory-oltp-available-space"></a>Espaço disponível oLTP na memória 

A quantidade de espaço OLTP na memória no nível de serviço [Business Critical](sql-database-service-tier-business-critical.md) depende do número de vCores e geração de hardware. Na tabela a seguir estão listados limites de memória que podem ser usados para objetos OLTP na memória.

| Espaço OLTP na memória  | **Gen5** | **Gen4** |
| --- | --- | --- |
| 4 vCores  | 3,14 GB | |   
| 8 vCores  | 6,28 GB | 8 GB |
| 16 vCores | 15,77 GB | 20 GB |
| 24 vCores | 25,25 GB | 36 GB |
| 32 vCores | 37,94 GB | |
| 40 vCores | 52,23 GB | |
| 64 vCores | 99,9 GB    | |
| 80 vCores | 131,68 GB| |

## <a name="service-tier-characteristics"></a>Características de camada de serviço

A instância gerenciada tem dois níveis de serviço: [General Purpose](sql-database-service-tier-general-purpose.md) e [Business Critical](sql-database-service-tier-business-critical.md). Esses níveis fornecem [diferentes capacidades,](sql-database-service-tiers-general-purpose-business-critical.md)conforme descrito na tabela abaixo.

> [!Important]
> O nível de serviço Business Critical fornece cópia adicional de instância incorporada (réplica secundária) que pode ser usada para carga de trabalho somente leitura. Se você puder separar consultas de leitura e leitura/consultas analíticas/de relatórios, você obterá duas vezes vCores e memória pelo mesmo preço. A réplica secundária pode atrasar alguns segundos atrás da instância primária, por isso foi projetada para descarregar relatórios/cargas de trabalho analíticas que não precisam do estado exato dos dados. Na tabela abaixo, **as consultas somente leitura** são as consultas que são executadas na réplica secundária.

| **Recurso** | **Propósito Geral** | **Crítica de negócios** |
| --- | --- | --- |
| Número de vCores\* | Gen4: 8, 16, 24<br/>Gen5: 4, 8, 16, 24, 32, 40, 64, 80 | Gen4: 8, 16, 24 <br/> Gen5: 4, 8, 16, 24, 32, 40, 64, 80 <br/>\*O mesmo número de vCores é dedicado para consultas somente leitura. |
| Memória máxima | Gen4: 56 GB - 168 GB (7GB/vCore)<br/>Gen5: 20,4 GB - 408 GB (5.1GB/vCore)<br/>Adicione mais vCores para obter mais memória. | Gen4: 56 GB - 168 GB (7GB/vCore)<br/>Gen5: 20,4 GB - 408 GB (5.1GB/vCore) para consultas de leitura-gravação<br/>+ 20,4 GB adicionais - 408 GB (5,1GB/vCore) para consultas somente leitura.<br/>Adicione mais vCores para obter mais memória. |
| Tamanho de armazenamento de exemplo máximo (reservado) | - 2 TB para 4 vCores (somente Gen5)<br/>- 8 TB para outros tamanhos | Gen4: 1 TB <br/> Gen5: <br/>- 1 TB para 4, 8, 16 vCores<br/>- 2 TB para 24 vCores<br/>- 4 TB para 32, 40, 64, 80 vCores |
| Tamanho máximo do banco de dados | Até o tamanho da ocorrência disponível atualmente (máximo de 2 TB - 8 TB, dependendo do número de vCores). | Até o tamanho da ocorrência disponível atualmente (máximo de 1 TB - 4 TB, dependendo do número de vCores). |
| Tamanho máximo de TempDB | Limitado a 24 GB/vCore (96 - 1.920 GB) e atualmente disponível tamanho de armazenamento de instância.<br/>Adicione mais vCores para obter mais espaço TempDB.<br/> O tamanho do arquivo de log é limitado a 120 GB.| Até o tamanho de armazenamento de instância atualmente disponível. |
| Número máximo de bancos de dados por instância | 100, a menos que o limite de tamanho de armazenamento de instância tenha sido atingido. | 100, a menos que o limite de tamanho de armazenamento de instância tenha sido atingido. |
| Número máximo de arquivos de banco de dados por instância | Até 280, a menos que o tamanho de armazenamento de exemplo ou o limite de [espaço de alocação de armazenamento do Disco Premium do Azure](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files) tenha sido atingido. | 32.767 arquivos por banco de dados, a menos que o limite de tamanho de armazenamento de instância tenha sido atingido. |
| Tamanho máximo do arquivo de dados | Limitado ao tamanho de armazenamento de instância atualmente disponível (máximo de 2 TB - 8 TB) e [ao espaço de alocação de armazenamento em disco Premium Do Zure](sql-database-release-notes.md#exceeding-storage-space-with-small-database-files). | Limitado ao tamanho de armazenamento de instância atualmente disponível (até 1 TB - 4 TB). |
| Tamanho do arquivo de log max | Limitado a 2 TB e atualmente disponível tamanho de armazenamento de instância. | Limitado a 2 TB e atualmente disponível tamanho de armazenamento de instância. |
| Dados/Log IOPS (aproximado) | Até 30-40 K IOPS por instância*, 500 - 7500 por arquivo<br/>\*[Aumente o tamanho do arquivo para obter mais IOPS](#file-io-characteristics-in-general-purpose-tier)| 10 K - 200 K (2500 IOPS/vCore)<br/>Adicione mais vCores para obter melhor desempenho de IO. |
| Limite de throughput de gravação de log (por instância) | 3 MB/s por vCore<br/>Max 22 MB/s | 4 MB/s por vCore<br/>Máx. de 48 MB/s |
| Taxa de transferência de dados (aproximada) | 100 – 250 MB/s por arquivo<br/>\*[Aumente o tamanho do arquivo para obter melhor desempenho de IO](#file-io-characteristics-in-general-purpose-tier) | Não é limitado. |
| Latência de IO de armazenamento (aproximada) | 5-10 ms | 1-2 ms |
| OLTP na memória | Sem suporte | Disponível, [o tamanho depende do número de vCore](#in-memory-oltp-available-space) |
| Sessões máximas | 30000 | 30000 |
| [Réplicas somente para leitura](sql-database-read-scale-out.md) | 0 | 1 (incluído no preço) |

> [!NOTE]
> - **O tamanho de armazenamento de instância atualmente disponível** é a diferença entre o tamanho da instância reservada e o espaço de armazenamento usado.
> - O tamanho do arquivo de log e de dados nos bancos de dados de sistema e de usuário são incluídos no tamanho de armazenamento de instância que é comparado ao limite de tamanho de armazenamento máximo. Usar a exibição do sistema <a href="https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-master-files-transact-sql">sys. master_files</a> para determinar o total o espaço usado pelos bancos de dados. Logs de erros não são persistentes e não são incluídos no tamanho. Backups não são incluídos no tamanho de armazenamento.
> - O throughput e o IOPS no nível de Propósito Geral também dependem do tamanho do [arquivo](#file-io-characteristics-in-general-purpose-tier) que não é explicitamente limitado pela instância gerenciada.
> - Você pode criar outra réplica legível em diferentes regiões do Azure usando grupos de failover automático.
> - O IOPS de exemplo máximo depende do layout do arquivo e da distribuição da carga de trabalho. Como exemplo, se você criar arquivos de 7 x 1TB com iOPS máximo de 5K cada e 7 arquivos pequenos (menores que 128 GB) com 500 IOPS cada, você pode obter 38500 IOPS por instância (7x5000+7x500) se sua carga de trabalho pode usar todos os arquivos. Observe que alguma quantidade de IOPS também é usada para backups automáticos.

> [!NOTE]
> Encontre mais informações sobre os [limites de recursos em pools de instâncias gerenciadas neste artigo](sql-database-instance-pools.md#instance-pools-resource-limitations).

### <a name="file-io-characteristics-in-general-purpose-tier"></a>Características do Arquivo IO no nível de Propósito Geral

No nível de serviço do General Purpose, cada arquivo de banco de dados está recebendo IOPS dedicado e throughput que depende do tamanho do arquivo. Arquivos maiores estão recebendo mais IOPS e throughput. As características de IO dos arquivos do banco de dados são mostradas na tabela a seguir:

| Tamanho de arquivo | >=0 e <=128 GiB | >128 e <=256 GiB | >256 e <= 512 GiB | >0,5 e <=1 TiB    | >1 e <=2 TiB    | >2 e <=4 TiB | >4 e <=8 TiB |
|---------------------|-------|-------|-------|-------|-------|-------|-------|
| IOPS por arquivo       | 500   | 1100 | 2300              | 5.000              | 7500              | 7500              | 12.500   |
| Throughput por arquivo | 100 MiB/s | 125 MiB/s | 150 MiB/s | 200 MiB/s | 250 MiB/s | 250 MiB/s | 480 MiB/s | 

Se você notar alta latência de IO em algum arquivo de banco de dados ou ver que o IOPS/throughput está chegando ao limite, você pode melhorar o desempenho [aumentando o tamanho do arquivo](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/Increase-data-file-size-to-improve-HammerDB-workload-performance/ba-p/823337).

Há também limites de nível de instância, como o throughput de gravação de log máximo de 22 MB/s, então você pode não ser capaz de alcançar o arquivo em todo o arquivo de log porque você está atingindo o limite de throughput da instância.

## <a name="supported-regions"></a>Regiões com suporte

As instâncias gerenciadas só podem ser criadas em [regiões suportadas](https://azure.microsoft.com/global-infrastructure/services/?products=sql-database&regions=all). Para criar uma instância gerenciada em uma região que atualmente não é suportada, você pode [enviar uma solicitação de suporte através do portal Azure](quota-increase-request.md).

## <a name="supported-subscription-types"></a>Tipos de assinatura com suporte

A tualmente, a instância gerenciada suporta a implantação apenas nos seguintes tipos de assinaturas:

- [EA (Enterprise Agreement)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Pague como você-vai](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Provedor de Serviços em Nuvem (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)
- [Desenvolvimento/Teste Enterprise](https://azure.microsoft.com/offers/ms-azr-0148p/)
- [Desenvolvimento/Teste pré-pago](https://azure.microsoft.com/offers/ms-azr-0023p/)
- [Assinaturas com crédito mensal do Azure para assinantes do Visual Studio](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/)

## <a name="regional-resource-limitations"></a>Limitações de recursos regionais

Os tipos de assinatura suportados podem conter um número limitado de recursos por região. A instância gerenciada tem dois limites padrão por região do Azure (que podem ser aumentados demanda, criando uma solicitação de suporte especial [no portal Azure,](quota-increase-request.md) dependendo de um tipo de tipo de assinatura:

- **Limite de sub-rede**: o número máximo de sub-redes nas quais as instâncias gerenciadas são implantadas em uma única região.
- **limite da unidade vCore**: O número máximo de unidades vCore que podem ser implantadas em todas as instâncias em uma única região. Um GP vCore usa uma unidade vCore e um BC vCore leva 4 unidades vCore. O número total de instâncias não é limitado enquanto estiver dentro do limite da unidade vCore.

> [!Note]
> Esses limites são configurações padrão e não limitações técnicas. Os limites podem ser aumentados demanda, criando uma solicitação de suporte especial [no portal Azure](quota-increase-request.md) se você precisar de instâncias mais gerenciadas na região atual. Como alternativa, você pode criar novas instâncias gerenciadas em outra região do Azure sem enviar solicitações de suporte.

A tabela a seguir mostra os **limites regionais padrão** para tipos de assinatura suportados (os limites padrão podem ser estendidos usando a solicitação de suporte descrita abaixo):

|Tipo de assinatura| Número máximo de sub-redes de instância gerenciada | Número máximo de unidades vCore* |
| :---| :--- | :--- |
|Pago conforme o uso|3|320|
|CSP |8 (15 em algumas regiões**)|960 (1440 em algumas regiões**)|
|Desenvolvimento/Teste pago conforme o uso|3|320|
|Desenvolvimento/Teste Enterprise|3|320|
|EA|8 (15 em algumas regiões**)|960 (1440 em algumas regiões**)|
|Visual Studio Enterprise|2 |64|
|Plataformas Visual Studio Professional e MSDN|2|32|

\*No planejamento de implantações, leve em consideração que o nível de serviço Business Critical (BC) requer quatro (4) vezes mais capacidade vCore do que o nível de serviço do General Purpose (GP). Por exemplo: 1 GP vCore = 1 unidade vCore e 1 BC vCore = 4 unidades vCore. Para simplificar sua análise de consumo em relação aos limites padrão, siperte as unidades vCore em todas as sub-redes da região onde as instâncias gerenciadas são implantadas e compare os resultados com os limites da unidade de instância para o seu tipo de assinatura. **O número máximo de unidades vCore** se aplica a cada assinatura em uma região. Não há limite por sub-redes individuais, exceto que a soma de todos os vCores implantados em várias sub-redes deve ser menor ou igual ao **número máximo de unidades vCore**.

\*\*Limites maiores de sub-rede e vCore estão disponíveis nas seguintes regiões: Austrália Leste, Leste dos EUA, Leste dos EUA 2, Norte da Europa, Centro-Sul dos EUA, Sudeste Asiático, Reino Unido Sul, Europa Ocidental, Oeste dos EUA 2.

## <a name="request-a-quota-increase-for-sql-managed-instance"></a>Solicite um aumento de cota para a instância gerenciada pelo SQL

Se você precisar de instâncias mais gerenciadas em suas regiões atuais, envie uma solicitação de suporte para estender a cota usando o portal Azure. Para obter mais informações, consulte [Solicitar aumentos de cotas para o Banco de Dados SQL do Azure](quota-increase-request.md).

## <a name="next-steps"></a>Próximas etapas

- Para obter mais informações sobre a instância gerenciada, [consulte O que é uma instância gerenciada?](sql-database-managed-instance.md).
- Para obter informações sobre preços, consulte o preço de [instância gerenciada do SQL Database](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Para saber como criar sua primeira instância gerenciada, consulte [o guia quickstart](sql-database-managed-instance-get-started.md).
