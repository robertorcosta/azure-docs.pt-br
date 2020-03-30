---
title: Dimensionar recursos do pool elástico
description: Esta página descreve os recursos de escala para pools elásticos no Banco de Dados SQL do Azure.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: carlrab
ms.date: 3/14/2019
ms.openlocfilehash: daca108cfc8bb2e5b2a068170a4a0244c72c9592
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462591"
---
# <a name="scale-elastic-pool-resources-in-azure-sql-database"></a>Dimensionar os recursos de pool elástico no banco de dados SQL do Azure

Este artigo descreve como dimensionar os recursos de computação e armazenamento disponíveis para pools elásticos e bancos de dados em pool no Banco de Dados SQL do Azure.

## <a name="change-compute-resources-vcores-or-dtus"></a>Alterar recursos de computação (vCores ou DTUs)

Depois de escolher inicialmente o número de vCores ou eDTUs, você pode escalar um pool elástico para cima ou para baixo dinamicamente com base na experiência real usando o [portal Azure,](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases) [PowerShell,](/powershell/module/az.sql/Get-AzSqlElasticPool)a [Cli Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)ou a [API REST](https://docs.microsoft.com/rest/api/sql/elasticpools/update).

### <a name="impact-of-changing-service-tier-or-rescaling-compute-size"></a>Impacto da mudança do nível de serviço ou redimensionamento do tamanho da computação

Alterar o nível de serviço ou o tamanho do cálculo de um pool elástico segue um padrão semelhante ao de bancos de dados únicos e envolve principalmente o serviço executando as seguintes etapas:

1. Crie uma nova instância de computação para o pool elástico  

    Uma nova instância de computação para o pool elástico é criada com o nível de serviço solicitado e o tamanho da computação. Para algumas combinações de alterações de nível de serviço e tamanho de computação, uma réplica de cada banco de dados deve ser criada na nova instância de computação que envolve copiar dados e pode influenciar fortemente a latência geral. Independentemente disso, os bancos de dados permanecem on-line durante esta etapa, e as conexões continuam sendo direcionadas para os bancos de dados na instância original da computação.

2. Alterne o roteamento de conexões para nova instância de computação

    As conexões existentes aos bancos de dados na instância original da computação são descartadas. Quaisquer novas conexões são estabelecidas nos bancos de dados na nova instância de computação. Para algumas combinações de alterações de nível de serviço e tamanho de computação, os arquivos do banco de dados são separados e reconectados durante o switch.  Independentemente disso, o switch pode resultar em uma breve interrupção do serviço quando os bancos de dados estão indisponíveis geralmente por menos de 30 segundos e muitas vezes por apenas alguns segundos. Se houver transações de longa duração em execução quando as conexões forem retiradas, a duração desta etapa pode levar mais tempo para recuperar transações abortadas. [A recuperação acelerada do banco](sql-database-accelerated-database-recovery.md) de dados pode reduzir o impacto de abortar transações de longo prazo.

> [!IMPORTANT]
> Nenhum dado é perdido durante qualquer etapa do fluxo de trabalho.

### <a name="latency-of-changing-service-tier-or-rescaling-compute-size"></a>Latência de alterar o nível de serviço ou redimensionar o tamanho do cálculo

A latência estimada para alterar o nível de serviço ou redimensionar o tamanho da computação de um único banco de dados ou pool elástico é parametrizada da seguinte forma:

|Camada de serviço|Banco de dados único básico,</br>Padrão (S0-S1)|Piscina elástica básica,</br>Padrão (S2-S12), </br>Hiperescala, </br>Banco de dados único de Uso Geral ou piscina elástica|Banco de dados único premium ou empresarial crítico ou pool elástico|
|:---|:---|:---|:---|
|**Banco de</br> dados único básico, Padrão (S0-S1)**|&bull;&nbsp;Latência temporal constante independente do espaço utilizado</br>&bull;&nbsp;Normalmente, menos de 5 minutos|&bull;&nbsp;Latência proporcional ao espaço de banco de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de banco de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|
|**Piscina elástica básica, </br>Standard </br>(S2-S12), Hyperscale, </br>General Purpose banco de dados único ou piscina elástica**|&bull;&nbsp;Latência proporcional ao espaço de banco de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência temporal constante independente do espaço utilizado</br>&bull;&nbsp;Normalmente, menos de 5 minutos|&bull;&nbsp;Latência proporcional ao espaço de banco de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|
|**Banco de dados único premium ou empresarial crítico ou pool elástico**|&bull;&nbsp;Latência proporcional ao espaço de banco de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de banco de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|&bull;&nbsp;Latência proporcional ao espaço de banco de dados utilizado devido à cópia de dados</br>&bull;&nbsp;Normalmente, menos de 1 minuto por GB de espaço usado|

> [!NOTE]
>
> - No caso de alterar o nível de serviço ou redimensionar o cálculo de um pool elástico, a soma do espaço utilizado em todos os bancos de dados do pool deve ser usada para calcular a estimativa.
> - No caso de mover um banco de dados para/a partir de um pool elástico, apenas o espaço utilizado pelo banco de dados impacta a latência, não o espaço utilizado pelo pool elástico.
>
> [!TIP]
> Para monitorar as operações em andamento, consulte: [Gerenciar operações usando a API SQL REST,](https://docs.microsoft.com/rest/api/sql/operations/list) [Gerenciar operações usando CLI,](/cli/azure/sql/db/op) [monitorar operações usando T-SQL](/sql/relational-databases/system-dynamic-management-views/sys-dm-operation-status-azure-sql-database) e estes dois comandos PowerShell: [Get-AzSqlDatabaseActivity](/powershell/module/az.sql/get-azsqldatabaseactivity) e [Stop-AzSqlDatabaseActivity](/powershell/module/az.sql/stop-azsqldatabaseactivity).

### <a name="additional-considerations-when-changing-service-tier-or-rescaling-compute-size"></a>Considerações adicionais ao alterar o nível de serviço ou redimensionar o tamanho do cálculo

- Ao reduzir os vCores ou eDTUs para um pool elástico, o espaço utilizado na piscina deve ser menor do que o tamanho máximo permitido do nível de serviço de destino e eDTUs de pool.
- Ao redimensionar eDTUs de um pool Elástico, um custo de armazenamento extra será aplicado se (1) o tamanho máximo de armazenamento do pool é compatível com o pool de destino, e (2) o tamanho máximo do armazenamento excede a quantidade de armazenamento incluída do pool de destino. Por exemplo, se um pool Standard de 100 eDTU com um tamanho máximo de 100 GB for reduzido para um pool Standard de 50 eDTU, um custo de armazenamento extra será aplicado, pois o pool de destino dá suporte a um tamanho máximo de 100 GB, e a quantidade de armazenamento incluído é somente de 50 GB. Assim, a quantidade de armazenamento extra será 100 GB – 50 GB = 50 GB. Para obter o preço do armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/). Se a quantidade real de espaço usado for menor do que a quantidade de armazenamento incluído, esse custo extra poderá ser evitado por meio da redução do tamanho máximo do banco de dados para a quantidade incluída.

### <a name="billing-during-rescaling"></a>Faturamento durante a redimensionamento

Você será cobrado pelas horas em que um banco de dados existir usando a camada de serviço mais alta mais o tamanho da computação aplicado durante essas horas, independentemente do uso ou se o banco de dados ficou ativo por menos de uma hora. Por exemplo, se você criar um banco de dados individual e o excluir depois de cinco minutos, sua fatura apresentará uma cobrança referente a uma hora de banco de dados.

## <a name="change-elastic-pool-storage-size"></a>Alterar o tamanho do armazenamento do pool elástico

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [Gerenciar espaço de arquivos no Banco de Dados SQL do Azure](sql-database-file-space-management.md).

### <a name="vcore-based-purchasing-model"></a>Modelo de compra baseado em vCore

- O armazenamento pode ser provisionado até o limite de tamanho máximo:

  - Para armazenamento nas camadas de serviço standard ou de uso geral, aumente ou diminua o tamanho em incrementos de 10 GB
  - Para armazenamento nas camadas de serviço críticas premium ou empresariais, aumente ou diminua o tamanho em incrementos de 250 GB
- O armazenamento de um pool elástico pode ser provisionado aumentando ou diminuindo seu tamanho máximo.
- O preço do armazenamento para um pool elástico é a quantidade de armazenamento multiplicada pelo preço unitário do armazenamento da camada de serviço. Para obter detalhes sobre o preço de armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [Gerenciar espaço de arquivos no Banco de Dados SQL do Azure](sql-database-file-space-management.md).

### <a name="dtu-based-purchasing-model"></a>Modelo de compra baseado em DTU

- O preço de eDTU para um pool elástico inclui uma determinada quantidade de armazenamento sem custo adicional. O armazenamento extra além da quantidade incluída pode ser provisionado mediante um custo adicional até o limite máximo de tamanho, em incrementos de 250 GB até 1 TB e, em seguida, em incrementos de 256 GB além de 1 TB. Para conhecer as quantidades de armazenamento incluídos e os limites de tamanho máximos, confira [Pool elástico: tamanhos de armazenamento e de computação](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes).
- É possível provisionar o armazenamento extra para um pool elástico aumentando seu tamanho máximo usando o [Portal do Azure](sql-database-elastic-pool-manage.md#azure-portal-manage-elastic-pools-and-pooled-databases), [PowerShell](/powershell/module/az.sql/Get-AzSqlElasticPool), a [CLI do Azure](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update) ou a [API REST](https://docs.microsoft.com/rest/api/sql/elasticpools/update).
- O preço do armazenamento extra para um pool elástico é a quantidade de armazenamento extra multiplicada pelo preço unitário do armazenamento extra da camada de serviço. Para obter detalhes sobre o preço de armazenamento extra, confira [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).

> [!IMPORTANT]
> Em algumas circunstâncias, talvez seja necessário reduzir um banco de dados para recuperar o espaço não utilizado. Para obter mais informações, consulte [Gerenciar espaço de arquivos no Banco de Dados SQL do Azure](sql-database-file-space-management.md).

## <a name="next-steps"></a>Próximas etapas

Para saber os limites de recurso global, confira [Limites de recurso baseado em vCore do banco de dados SQL – pools elásticos](sql-database-vcore-resource-limits-elastic-pools.md) e [Limites de DTU de banco de dados do SQL com base em recurso – pools elásticos](sql-database-dtu-resource-limits-elastic-pools.md).
