---
title: Limites de recursos vCore de banco de dados individual
description: Esta página descreve alguns limites de recursos vCore comuns para um único banco de dados no banco de dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 07/09/2020
ms.openlocfilehash: add2e0cc2852f9ab0b63565841f670ed6c53d9a7
ms.sourcegitcommit: 3541c9cae8a12bdf457f1383e3557eb85a9b3187
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/09/2020
ms.locfileid: "86206124"
---
# <a name="resource-limits-for-single-databases-using-the-vcore-purchasing-model"></a>Limites de recursos para bancos de dados individuais usando o modelo de compra vCore
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Este artigo fornece os limites de recursos detalhados para bancos de dados individuais no banco de dados SQL do Azure usando o modelo de compra vCore.

Para os limites de modelo de compra de DTU para bancos de dados individuais em um servidor, consulte [visão geral dos limites de recursos em um servidor](resource-limits-logical-server.md).

Você pode definir a camada de serviço, o tamanho da computação (objetivo do serviço) e a quantidade de armazenamento para um único banco de dados usando o [portal do Azure](single-database-manage.md#the-azure-portal), [Transact-SQL](single-database-manage.md#transact-sql-t-sql), [PowerShell](single-database-manage.md#powershell), o [CLI do Azure](single-database-manage.md#the-azure-cli)ou a [API REST](single-database-manage.md#rest-api).

> [!IMPORTANT]
> Para obter diretrizes e considerações sobre o dimensionamento, consulte [dimensionar um banco de dados individual](single-database-scale.md).

## <a name="general-purpose---serverless-compute---gen5"></a>Computação de uso geral sem servidor-Gen5

A [camada de computação sem servidor](serverless-tier-overview.md) está disponível atualmente somente em hardware Gen5.

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Tamanho da computação (objetivo do serviço)|GP_S_Gen5_1|GP_S_Gen5_2|GP_S_Gen5_4|GP_S_Gen5_6|GP_S_Gen5_8|
|:--- | --: |--: |--: |--: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|
|VCores mín. máx.|0.5-1|0.5-2|0,5-4|0,75 a 6|1.0-8|
|Memória mín. máx. (GB)|2.02-3|2.05-6|2.10-12|2,25-18|3,00-24|
|Atraso de pausa automática mín. máx. (minutos)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|512|1024|1024|1024|1536|
|Tamanho máximo de log (GB)|154|307|307|307|461|
|Tamanho máximo de dados de TempDB (GB)|32|64|128|192|256|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|320|640|1280|1920|2560|
|Taxa máxima de logs (MBps)|3.8|7,5|15|22,5|30|
|Máximo de trabalhos simultâneos (solicitações)|75|150|300|450|600|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\*O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho da computação (objetivo do serviço)|GP_S_Gen5_10|GP_S_Gen5_12|GP_S_Gen5_14|GP_S_Gen5_16|
|:--- | --: |--: |--: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|
|VCores mín. máx.|1,25-10|1,50-12|1,75-14|2,00-16|
|Memória mín. máx. (GB)|3,75-30|4.50-36|5,25-42|6.00-48|
|Atraso de pausa automática mín. máx. (minutos)|60-10080|60-10080|60-10080|60-10080|
|Suporte de columnstore|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1536|3072|3072|3072|
|Tamanho máximo de log (GB)|461|461|461|922|
|Tamanho máximo de dados de TempDB (GB)|320|384|448|512|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|3200|3840|4480|5120|
|Taxa máxima de logs (MBps)|30|30|30|30|
|Máximo de trabalhos simultâneos (solicitações)|750|900|1.050|1200|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\*O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-3"></a>Geração de computação Gen5 (parte 3)

|Tamanho da computação (objetivo do serviço)|GP_S_Gen5_18|GP_S_Gen5_20|GP_S_Gen5_24|GP_S_Gen5_32|GP_S_Gen5_40|
|:--- | --: |--: |--: |--: |--:|
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|
|VCores mín. máx.|2,25-18|2,5 a 20|3-24|4-32|5-40|
|Memória mín. máx. (GB)|6.75-54|7.5-60|9-72|12-96|15-120|
|Atraso de pausa automática mín. máx. (minutos)|60-10080|60-10080|60-10080|60-10080|60-10080|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|3072|3072|4096|4096|4096|
|Tamanho máximo de log (GB)|922|922|1229|1229|1229|
|Tamanho máximo de dados de TempDB (GB)|576|640|768|1024|1280|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|5760|6400|7680|10240|12800|
|Taxa máxima de logs (MBps)|30|30|30|30|30|
|Máximo de trabalhos simultâneos (solicitações)|1350|1500|1800|2400|3000|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\*O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).


## <a name="hyperscale---provisioned-compute---gen4"></a>Hiperescala – computação provisionada-Gen4

### <a name="gen4-compute-generation-part-1"></a>Geração de computação Gen4 (parte 1)

|Tamanho da computação (objetivo do serviço)|HS_Gen4_1|HS_Gen4_2|HS_Gen4_3|HS_Gen4_4|HS_Gen4_5|HS_Gen4_6|
|:--- | --: |--: |--: |---: | --: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|[RBPEX](service-tier-hyperscale.md#compute) Tamanho|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100|
|Tamanho máximo do log (TB)|Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |
|Tamanho máximo de dados de TempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento| [Observação 1](#notes) |[Observação 1](#notes)|[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |
|IOPS máximo de SSD local *|4000 |8000 |12000 |16000 |20000 |24.000 |
|Taxa máxima de logs (MBps)|100 |100 |100 |100 |100 |100 |
|Latência de E/S (aproximada)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\*Além da e/s SSD local, as cargas de trabalho usarão a es [do servidor de paginação](service-tier-hyperscale.md#page-server) remota. O IOPS efetivo dependerá da carga de trabalho. Para obter detalhes, consulte [governança de e](resource-limits-logical-server.md#resource-governance)/s de dados e [Io de dados nas estatísticas de utilização de recursos](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen4-compute-generation-part-2"></a>Geração de computação Gen4 (parte 2)

|Tamanho da computação (objetivo do serviço)|HS_Gen4_7|HS_Gen4_8|HS_Gen4_9|HS_Gen4_10|HS_Gen4_16|HS_Gen4_24|
|:--- | ---: |--: |--: | --: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|[RBPEX](service-tier-hyperscale.md#compute) Tamanho|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |
|Tamanho máximo do log (TB)|Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |
|Tamanho máximo de dados de TempDB (GB)|224|256|288|320|512|768|
|Tipo de armazenamento| [Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |
|IOPS máximo de SSD local *|28000 |32000 |36000 |40000 |64000 |76800 |
|Taxa máxima de logs (MBps)|100 |100 |100 |100 |100 |100 |
|Latência de E/S (aproximada)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|
|Máximo de trabalhos simultâneos (solicitações)|1.400|1600|1800|2000|3200|4800|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\*Além da e/s SSD local, as cargas de trabalho usarão a es [do servidor de paginação](service-tier-hyperscale.md#page-server) remota. O IOPS efetivo dependerá da carga de trabalho. Para obter detalhes, consulte [governança de e](resource-limits-logical-server.md#resource-governance)/s de dados e [Io de dados nas estatísticas de utilização de recursos](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

## <a name="hyperscale---provisioned-compute---gen5"></a>Hiperescala – computação provisionada-Gen5

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Tamanho da computação (objetivo do serviço)|HS_Gen5_2|HS_Gen5_4|HS_Gen5_6|HS_Gen_8|HS_Gen5_10|HS_Gen5_12|HS_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|[RBPEX](service-tier-hyperscale.md#compute) Tamanho|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |100|
|Tamanho máximo do log (TB)|Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|320|384|448|
|Tipo de armazenamento| [Observação 1](#notes) |[Observação 1](#notes)|[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |
|IOPS máximo de SSD local *|8000 |16000 |24.000 |32000 |40000 |48000 |56000 |
|Taxa máxima de logs (MBps)|100 |100 |100 |100 |100 |100 |100 |
|Latência de E/S (aproximada)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|1.400|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\*Além da e/s SSD local, as cargas de trabalho usarão a es [do servidor de paginação](service-tier-hyperscale.md#page-server) remota. O IOPS efetivo dependerá da carga de trabalho. Para obter detalhes, consulte [governança de e](resource-limits-logical-server.md#resource-governance)/s de dados e [Io de dados nas estatísticas de utilização de recursos](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho da computação (objetivo do serviço)|HS_Gen5_16|HS_Gen5_18|HS_Gen5_20|HS_Gen_24|HS_Gen5_32|HS_Gen5_40|HS_Gen5_80|
|:--- | --: |--: |--: |--: |---: |--: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|[RBPEX](service-tier-hyperscale.md#compute) Tamanho|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|3X de memória|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (TB)|100 |100 |100 |100 |100 |100 |100 |
|Tamanho máximo do log (TB)|Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |Ilimitado |
|Tamanho máximo de dados de TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento| [Observação 1](#notes) |[Observação 1](#notes)|[Observação 1](#notes)|[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |[Observação 1](#notes) |
|IOPS máximo de SSD local *|64000 |72000 |80000 |96000 |160000 |192000 |204800 |
|Taxa máxima de logs (MBps)|100 |100 |100 |100 |100 |100 |100 |
|Latência de E/S (aproximada)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|[Observação 2](#notes)|
|Máximo de trabalhos simultâneos (solicitações)|1600|1800|2000|2400|3200|4000|8000|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Réplicas secundárias|0-4|0-4|0-4|0-4|0-4|0-4|0-4|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Retenção de armazenamento de backup|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|7 dias|
|||

\*Além da e/s SSD local, as cargas de trabalho usarão a es [do servidor de paginação](service-tier-hyperscale.md#page-server) remota. O IOPS efetivo dependerá da carga de trabalho. Para obter detalhes, consulte [governança de e](resource-limits-logical-server.md#resource-governance)/s de dados e [Io de dados nas estatísticas de utilização de recursos](hyperscale-performance-diagnostics.md#data-io-in-resource-utilization-statistics).

#### <a name="notes"></a>Observações

**Observação 1**: o hiperescala é uma arquitetura de várias camadas com componentes de armazenamento e computação separados: [arquitetura de camada de serviço de hiperescala](service-tier-hyperscale.md#distributed-functions-architecture)

**Observação 2**: a latência é 1-2 ms para dados no SSD da réplica de computação local, que armazena em cache as páginas de dados mais usadas. Maior latência de dados recuperados de servidores de páginas.

## <a name="general-purpose---provisioned-compute---gen4"></a>Computação provisionada de uso geral-Gen4

> [!IMPORTANT]
> Novos bancos de dados Gen4 não têm mais suporte nas regiões leste da Austrália ou sul do Brasil.

### <a name="gen4-compute-generation-part-1"></a>Geração de computação Gen4 (parte 1)

|Tamanho da computação (objetivo do serviço)|GP_Gen4_1|GP_Gen4_2|GP_Gen4_3|GP_Gen4_4|GP_Gen4_5|GP_Gen4_6
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|
|Tamanho máximo de log (GB)|307|307|461|461|461|922|
|Tamanho máximo de dados de TempDB (GB)|32|64|96|128|160|192|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|320|640|960|1280|1600|1920|
|Taxa máxima de logs (MBps)|3,75|7,5|11,25|15|18,75|22,5|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\*O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Geração de computação Gen4 (parte 2)

|Tamanho da computação (objetivo do serviço)|GP_Gen4_7|GP_Gen4_8|GP_Gen4_9|GP_Gen4_10|GP_Gen4_16|GP_Gen4_24
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|3072|3072|3072|3072|4096|4096|
|Tamanho máximo de log (GB)|922|922|922|922|1229|1229|
|Tamanho máximo de dados de TempDB (GB)|224|256|288|320|512|768|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)
|IOPS de dados máximo *|2240|2560|2880|3200|5120|7680|
|Taxa máxima de logs (MBps)|26,3|30|30|30|30|30|
|Máximo de trabalhos simultâneos (solicitações)|1.400|1600|1800|2000|3200|4800|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\*O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---gen5"></a>Computação provisionada de uso geral-Gen5

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Tamanho da computação (objetivo do serviço)|GP_Gen5_2|GP_Gen5_4|GP_Gen5_6|GP_Gen5_8|GP_Gen5_10|GP_Gen5_12|GP_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|3072|
|Tamanho máximo de log (GB)|307|307|461|461|461|922|922|
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|320|384|384|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|640|1280|1920|2560|3200|3840|4480|
|Taxa máxima de logs (MBps)|7,5|15|22,5|30|30|30|30|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|1.400|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\*O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho da computação (objetivo do serviço)|GP_Gen5_16|GP_Gen5_18|GP_Gen5_20|GP_Gen5_24|GP_Gen5_32|GP_Gen5_40|GP_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo de log (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho máximo de dados de TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|5120|5760|6400|7680|10240|12800|12800|
|Taxa máxima de logs (MBps)|30|30|30|30|30|30|30|
|Máximo de trabalhos simultâneos (solicitações)|1600|1800|2000|2400|3200|4000|8000|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|1|1|1|1|1|1|1|
|Multi-AZ|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Escala de leitura|N/D|N/D|N/D|N/D|N/D|N/D|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\*O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

## <a name="general-purpose---provisioned-compute---fsv2-series"></a>Computação provisionada de uso geral-série Fsv2

### <a name="fsv2-series-compute-generation-preview"></a>Geração de computação da série Fsv2 (visualização)

|Tamanho da computação (objetivo do serviço)|GP_Fsv2_72|
|:--- | --: |
|Geração de computação|Série Fsv2|
|vCores|72|
|Memória (GB)|136,2|
|Suporte de columnstore|Sim|
|Armazenamento OLTP na memória (GB)|N/D|
|Tamanho máximo de dados (GB)|4096|
|Tamanho máximo de log (GB)|1024|
|Tamanho máximo de dados de TempDB (GB)|333|
|Tipo de armazenamento|SSD remoto|
|Latência de E/S (aproximada)|5-7 ms (gravação)<br>5-10 ms (leitura)|
|IOPS de dados máximo *|12.800|
|Taxa máxima de logs (MBps)|30|
|Máximo de trabalhos simultâneos (solicitações)|3600|
|Máximo de logons simultâneos|3600|
|Máximo de sessões simultâneas|30,000|
|Número de réplicas|1|
|Multi-AZ|N/D|
|Escala de leitura|N/D|
|Armazenamento de backup incluído|Tamanho de BD 1X|

\*O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen4"></a>Comercialmente crítico-computação provisionada-Gen4

> [!IMPORTANT]
> Novos bancos de dados Gen4 não têm mais suporte nas regiões leste da Austrália ou sul do Brasil.

### <a name="gen4-compute-generation-part-1"></a>Geração de computação Gen4 (parte 1)

|Tamanho da computação (objetivo do serviço)|BC_Gen4_1|BC_Gen4_2|BC_Gen4_3|BC_Gen4_4|BC_Gen4_5|BC_Gen4_6|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|1|2|3|4|5|6|
|Memória (GB)|7|14|21|28|35|42|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|1|2|3|4|5|6|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo de log (GB)|307|307|307|307|307|307|
|Tamanho máximo de dados de TempDB (GB)|32|64|96|128|160|192|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo *|4.000|8,000|12.000|16.000|20.000|24.000|
|Taxa máxima de logs (MBps)|8|16|24|32|40|48|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|
|Máximo de logons simultâneos|200|400|600|800|1000|1200|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\*O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

### <a name="gen4-compute-generation-part-2"></a>Geração de computação Gen4 (parte 2)

|Tamanho da computação (objetivo do serviço)|BC_Gen4_7|BC_Gen4_8|BC_Gen4_9|BC_Gen4_10|BC_Gen4_16|BC_Gen4_24|
|:--- | --: |--: |--: |--: |--: |--: |
|Geração de computação|Gen4|Gen4|Gen4|Gen4|Gen4|Gen4|
|vCores|7|8|9|10|16|24|
|Memória (GB)|49|56|63|70|112|159,5|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|7|8|9,5|11|20|36|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Tamanho máximo de dados (GB)|1024|1024|1024|1024|1024|1024|
|Tamanho máximo de log (GB)|307|307|307|307|307|307|
|Tamanho máximo de dados de TempDB (GB)|224|256|288|320|512|768|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo |28.000|32.000|36.000|40.000|64.000|76.800|
|Taxa máxima de logs (MBps)|56|64|64|64|64|64|
|Máximo de trabalhos simultâneos (solicitações)|1.400|1600|1800|2000|3200|4800|
|Máximo de logons simultâneos (solicitações)|1.400|1600|1800|2000|3200|4800|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\*O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---gen5"></a>Comercialmente crítico-computação provisionada-Gen5

### <a name="gen5-compute-generation-part-1"></a>Geração de computação Gen5 (parte 1)

|Tamanho da computação (objetivo do serviço)|BC_Gen5_2|BC_Gen5_4|BC_Gen5_6|BC_Gen5_8|BC_Gen5_10|BC_Gen5_12|BC_Gen5_14|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|2|4|6|8|10|12|14|
|Memória (GB)|10,4|20,8|31,1|41,5|51,9|62,3|72,7|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|1,57|3.14|4,71|6,28|8,65|11, 2|13,39|
|Tamanho máximo de dados (GB)|1024|1024|1536|1536|1536|3072|3072|
|Tamanho máximo de log (GB)|307|307|461|461|461|922|922|
|Tamanho máximo de dados de TempDB (GB)|64|128|192|256|320|384|448|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo *|8000|16.000|24.000|32.000|40.000|48.000|56.000|
|Taxa máxima de logs (MBps)|24|48|72|96|96|96|96|
|Máximo de trabalhos simultâneos (solicitações)|200|400|600|800|1000|1200|1.400|
|Máximo de logons simultâneos|200|400|600|800|1000|1200|1.400|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\*O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

### <a name="gen5-compute-generation-part-2"></a>Geração de computação Gen5 (parte 2)

|Tamanho da computação (objetivo do serviço)|BC_Gen5_16|BC_Gen5_18|BC_Gen5_20|BC_Gen5_24|BC_Gen5_32|BC_Gen5_40|BC_Gen5_80|
|:--- | --: |--: |--: |--: |---: | --: |--: |
|Geração de computação|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|Gen5|
|vCores|16|18|20|24|32|40|80|
|Memória (GB)|83|93,4|103,8|124,6|166,1|207,6|415,2|
|Suporte de columnstore|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento OLTP na memória (GB)|15,77|18,14|20,51|25,25|37,94|52,23|131.64|
|Tamanho máximo de dados (GB)|3072|3072|3072|4096|4096|4096|4096|
|Tamanho máximo de log (GB)|922|922|922|1229|1229|1229|1229|
|Tamanho máximo de dados de TempDB (GB)|512|576|640|768|1024|1280|2560|
|Tipo de armazenamento|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo *|64.000|72.000|80.000|96.000|128.000|160.000|204.800|
|Taxa máxima de logs (MBps)|96|96|96|96|96|96|96|
|Máximo de trabalhos simultâneos (solicitações)|1600|1800|2000|2400|3200|4000|8000|
|Máximo de logons simultâneos|1600|1800|2000|2400|3200|4000|8000|
|Máximo de sessões simultâneas|30,000|30,000|30,000|30,000|30,000|30,000|30,000|
|Número de réplicas|4|4|4|4|4|4|4|
|Multi-AZ|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Escala de leitura|Sim|Sim|Sim|Sim|Sim|Sim|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|Tamanho de BD 1X|

\*O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

## <a name="business-critical---provisioned-compute---m-series"></a>Computação comercialmente crítica-série M

### <a name="m-series-compute-generation-preview"></a>Geração de computação da série M (versão prévia)

|Tamanho da computação (objetivo do serviço)|BC_M_128|
|:--- | --: |
|Geração de computação|Série M|
|vCores|128|
|Memória (GB)|3767,1|
|Suporte de columnstore|Sim|
|Armazenamento OLTP na memória (GB)|1768|
|Tamanho máximo de dados (GB)|4096|
|Tamanho máximo de log (GB)|2.048|
|Tamanho máximo de dados de TempDB (GB)|4096|
|Tipo de armazenamento|SSD Local|
|Latência de E/S (aproximada)|1-2 ms (gravação)<br>1-2 ms (leitura)|
|IOPS de dados máximo *|160.000|
|Taxa máxima de logs (MBps)|264|
|Máximo de trabalhos simultâneos (solicitações)|12.800|
|Máximo de logons simultâneos|12.800|
|Máximo de sessões simultâneas|30000|
|Número de réplicas|4|
|Multi-AZ|Sim|
|Escala de leitura|Sim|
|Armazenamento de backup incluído|Tamanho de BD 1X|

\*O valor máximo para tamanhos de e/s variando entre 8 KB e 64 KB. IOPS reais são dependentes da carga de trabalho. Para obter detalhes, consulte [governança de e/s de dados](resource-limits-logical-server.md#resource-governance).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [gerenciar o espaço de arquivo no banco de dados SQL do Azure](file-space-manage.md).

## <a name="next-steps"></a>Próximas etapas

- Para os limites de recursos de DTU para um único banco de dados, consulte [limites de recursos para bancos de dados individuais usando o modelo de compra de DTU](resource-limits-dtu-single-databases.md)
- Para limites de recursos vCore para pools elásticos, consulte [limites de recursos para pools elásticos usando o modelo de compra vCore](resource-limits-vcore-elastic-pools.md)
- Para os limites de recursos de DTU para pools elásticos, consulte [limites de recursos para pools elásticos usando o modelo de compra de DTU](resource-limits-dtu-elastic-pools.md)
- Para os limites de recursos para o SQL Instância Gerenciada, consulte [limites de recursos do sql instância gerenciada](../managed-instance/resource-limits.md).
- Para saber mais sobre limites gerais do Azure, confira [Assinatura do Azure e limites de serviço, cotas e restrições](../../azure-resource-manager/management/azure-subscription-service-limits.md).
- Para obter informações sobre limites de recursos em um servidor, consulte [visão geral dos limites de recursos em um servidor](resource-limits-logical-server.md) para obter informações sobre os limites nos níveis de servidor e assinatura.
