---
title: Unidades de data warehouse (DWUs, cDWUs) no Azure Synapse Analytics (antigo SQL DW)
description: Recomendações sobre como escolher o número ideal de unidades do data warehouse (DWUs, cDWUs) para otimizar o preço e o desempenho, e como alterar o número unidades.
services: sql-data-warehouse
author: mlee3gsd
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/04/2019
ms.author: martinle
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: caa23d3e86fba86aa45e677f7ab85859cda6ddce
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133167"
---
# <a name="data-warehouse-units-dwus-and-compute-data-warehouse-units-cdwus"></a>DWUs (Unidades do Data Warehouse) cDWUs (Unidades do Data Warehouse de computação)

Recomendações sobre como escolher o número ideal de unidades do data warehouse (DWUs, cDWUs) para otimizar o preço e o desempenho, e como alterar o número unidades.

## <a name="what-are-data-warehouse-units"></a>O que são unidades de data warehouse

O pool do SQL representa uma coleção de recursos analíticos que estão sendo provisionados ao usar a [análise do SQL](sql-data-warehouse-overview-what-is.md#sql-analytics-and-sql-pool-in-azure-synapse). Os recursos analíticos são definidos como uma combinação de CPU, memória e e/s. Esses três recursos são agrupados em unidades de escala de computação chamadas de unidades de data warehouse (DWUs). Uma DWU representa uma medida abstrata normalizada de recursos de computação e desempenho. Uma alteração no seu nível de serviço altera o número de DWUs que estão disponíveis para o sistema, o que, por sua vez, ajusta o desempenho e o custo do seu sistema.

Para um melhor desempenho, você pode aumentar o número de unidades de data warehouse. Para menos desempenho, reduza data warehouse unidades. Os custos de armazenamento e computação são cobrados separadamente, para que as mudanças de unidades de data warehouse não afetem os custos de armazenamento.

O desempenho de unidades de data warehouse baseia-se nessas métricas de carga de trabalho:

- A velocidade com que uma consulta de data warehouse padrão pode verificar um grande número de linhas e, em seguida, executar uma agregação complexa. Essa operação tem uso intensivo de E/S e CPU.
- Quão rápido a data warehouse pode ingerir dados de blobs de armazenamento do Azure ou Azure Data Lake. Essa operação tem uso intensivo de rede e CPU.
- Quão rápido o [`CREATE TABLE AS SELECT`](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) comando t-SQL pode copiar uma tabela. Essa operação envolve a leitura de dados do armazenamento, distribuindo-os em todos os nós do dispositivo e gravando novamente no armazenamento. Essa operação é de uso intensivo de CPU, rede e E/S.

Aumento de DWUs:

- Altera de maneira linear o desempenho do sistema para verificações, agregações e instruções de CTAS
- Aumenta o número de leitores e gravadores para operações de carregamento do PolyBase
- Aumenta o número máximo de consultas simultâneas e slots de simultaneidade.

## <a name="service-level-objective"></a>Objetivo de nível de serviço

O Objetivo de nível de serviço (SLO) é a configuração de escalabilidade que determina o nível de custo e desempenho do data warehouse. Os níveis de serviço para o pool SQL Gen2 são medidos em cDWU (unidades de data warehouse de computação), por exemplo, DW2000c. Os níveis de serviço do pool SQL Gen1 são medidos em DWUs, por exemplo, DW2000.
  > [!NOTE]
  > O pool SQL Gen 2 adicionou recentemente recursos de escala adicionais para dar suporte a camadas de computação tão baixas que 100 cDWU. Os pools existentes do SQL atualmente em Gen1 que exigem as camadas de computação inferiores agora podem ser atualizados para Gen2 nas regiões que estão atualmente disponíveis sem custo adicional.  Se ainda não houver suporte para a sua região, você poderá atualizar para uma região com suporte. Para saber mais, confira [Atualizar para Gen2](upgrade-to-latest-generation.md).

No T-SQL, a configuração de SERVICE_OBJECTIVE determina o nível de serviço e a camada de desempenho para o pool do SQL.

```sql
--Gen1
CREATE DATABASE myElasticSQLDW
WITH
(    SERVICE_OBJECTIVE = 'DW1000'
)
;

--Gen2
CREATE DATABASE myComputeSQLDW
(Edition = 'Datawarehouse'
 ,SERVICE_OBJECTIVE = 'DW1000c'
)
;
```

## <a name="performance-tiers-and-data-warehouse-units"></a>Níveis de desempenho e unidades de data warehouse

Cada nível de desempenho usa uma unidade de medida ligeiramente diferente para suas unidades de data warehouse. Essa diferença é refletida na fatura à medida que a unidade de escala é convertida diretamente para cobrança.

- Os pools de SQL Gen1 são medidos em unidades de data warehouse (DWUs).
- Os pools de SQL Gen2 são medidos em cDWUs (unidades de data warehouse de computação).

O DWUs e o cDWUs dão suporte ao dimensionamento da computação para cima ou para baixo e pausar a computação quando você não precisa usar o pool do SQL. Todas essas operações são sob demanda. O Gen2 usa um cache local baseado em disco em nós de computação para melhorar o desempenho. Quando você dimensiona ou pausa o sistema, o cache é invalidado e, portanto, é necessário um período de aquecimento de cache antes que o desempenho ideal seja obtido.  

À medida que você aumenta as unidades de data warehouse, estará aumentando linearmente os recursos de computação. O Gen2 fornece o melhor desempenho de consulta e dimensionamento mais alto. Os sistemas Gen2 também fazem o máximo de uso do cache.

### <a name="capacity-limits"></a>Limites de capacidade

Cada servidor SQL (por exemplo, myserver.database.windows.net) tem uma cota de [Unidade de Transação de Banco de Dados (DTU)](../sql-database/sql-database-what-is-a-dtu.md) que permite um número específico de unidades de depósito de dados. Para mais informações, consulte o [limites de capacidade de gerenciamento de carga de trabalho](sql-data-warehouse-service-capacity-limits.md#workload-management).

## <a name="how-many-data-warehouse-units-do-i-need"></a>Quantas unidades de data warehouse são necessárias

O número ideal de unidades de data warehouse depende muito de sua carga de trabalho e da quantidade de dados carregados no sistema.

Etapas para encontrar a melhor DWU para sua carga de trabalho:

1. Comece selecionando um DWU menor.
2. Monitore o desempenho do seu aplicativo à medida que testa as cargas de dados no sistema, observando o número de DWUs selecionadas comparado ao desempenho que você observar.
3. Identifique quaisquer requisitos adicionais para períodos de atividade de pico temporários. As cargas de trabalho que mostram picos significativos e ciclos na atividade podem precisar ser dimensionadas com frequência.

A análise de SQL é um sistema de expansão que pode provisionar enormes quantidades de dados de computação e de considerável de consulta. Para ver seus verdadeiros recursos para dimensionamento, especialmente em DWUs maiores, é recomendável dimensionar o conjunto de dados ao dimensionar para garantir que você tenha dados suficientes para alimentar as CPUs. Para testar o dimensionamento, é recomendável usar pelo menos 1 TB.

> [!NOTE]
>
> O desempenho de consulta só aumentará com mais paralelização se o trabalho puder ser dividido entre nós de computação. Se você achar que o dimensionamento não está alterando o desempenho, será necessário ajustar o design da tabela e/ou de suas consultas. Para obter diretrizes de ajuste de consulta, consulte [Gerenciar consultas de usuário](sql-data-warehouse-overview-manage-user-queries.md).

## <a name="permissions"></a>permissões

Alterar as unidades de data warehouse exige as permissões descritas em [ALTERAR BANCO DE DADOS](/sql/t-sql/statements/alter-database-transact-sql).

Funções internas para recursos do Azure, como Colaborador do BD SQL e Colaborador do SQL Server, podem alterar as configurações da DWU.

## <a name="view-current-dwu-settings"></a>Exibir configurações atuais de DWU

Para exibir a configuração atual de DWU:

1. Abra o Pesquisador de Objetos do SQL Server no Visual Studio.
2. Conecte-se ao banco de dados mestre associado ao servidor lógico do Banco de Dados SQL.
3. Selecione do modo de exibição de gerenciamento dinâmico sys.database_service_objectives. Aqui está um exemplo:

```sql
SELECT  db.name [Database]
,       ds.edition [Edition]
,       ds.service_objective [Service Objective]
FROM    sys.database_service_objectives   AS ds
JOIN    sys.databases                     AS db ON ds.database_id = db.database_id
;
```

## <a name="change-data-warehouse-units"></a>Alterar unidades do data warehouse

### <a name="azure-portal"></a>Portal do Azure

Para alterar DWUs ou cDWUs:

1. Abra o [portal do Azure](https://portal.azure.com), abra o banco de dados e clique em **Escala**.

2. Em **Escala**, mova o controle deslizante para a esquerda ou direita para alterar a configuração de DWU.

3. Clique em **Salvar**. Será exibida uma mensagem de confirmação. Clique em **sim** para confirmar ou em **não** para cancelar.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Para alterar o DWUs ou o cDWUs, use o cmdlet [set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) do PowerShell. O exemplo a seguir define o objetivo de nível de serviço como DW1000 para o banco de dados MySQLDW, que está hospedado no servidor MyServer.

```Powershell
Set-AzSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

Para obter mais informações, consulte [cmdlets do PowerShell para SQL Data Warehouse](sql-data-warehouse-reference-powershell-cmdlets.md)

### <a name="t-sql"></a>T-SQL

Com o T-SQL, é possível exibir as configurações atuais de DWU ou cDWU, alterar as configurações e verificar o progresso.

Para alterar as DWUs ou as cDWUs:

1. Conecte-se ao banco de dados mestre associado ao seu servidor lógico do Banco de Dados SQL.
2. use a declaração TSQL [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql) . O exemplo a seguir define o objetivo de nível de serviço como DW1000 para o banco de dados MySQLDW.

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

### <a name="rest-apis"></a>APIs REST

Para alterar as DWUs, use a API REST [Criar ou Atualizar Banco de Dados](/rest/api/sql/databases/createorupdate) . O exemplo a seguir define o objetivo de nível de serviço como DW1000 para o banco de dados MySQLDW, que é hospedado no servidor meuservidor. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

```
PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/databases/{database-name}?api-version=2014-04-01-preview HTTP/1.1
Content-Type: application/json; charset=UTF-8

{
    "properties": {
        "requestedServiceObjectiveName": DW1000
    }
}
```

Para obter mais exemplos de API REST, consulte [APIs REST para o SQL Data Warehouse](sql-data-warehouse-manage-compute-rest-api.md).

## <a name="check-status-of-dwu-changes"></a>Verificar o status das alterações de DWU

As alterações de DWU podem levar vários minutos para concluir. Se você estiver dimensionando automaticamente, considere implementar a lógica para assegurar que determinadas operações tenham sido concluídas antes de prosseguir com outra ação.

Verificar o estado do banco de dados por meio de vários pontos de extremidade permitirá que você implemente corretamente a automação. O portal fornece uma notificação após a conclusão de uma operação e o estado atual do bancos de dados, mas não permite a verificação de estado programática.

Não é possível verificar o estado do banco de dados para operações de dimensionamento horizontal com o portal do Azure.

Para verificar o status de alterações de DWU:

1. Conecte-se ao banco de dados mestre associado ao seu servidor lógico do Banco de Dados SQL.

1. Envie a seguinte consulta para verificar o estado do banco de dados.

    ```sql
    SELECT    *
    FROM      sys.databases
    ;
    ```
    
1. Envie a seguinte consulta para verificar o estado da operação

    ```sql
    SELECT    *
    FROM      sys.dm_operation_status
    WHERE     resource_type_desc = 'Database'
    AND       major_resource_id = 'MySQLDW'
    ;
    ```
    
Essa DMV retorna informações sobre várias operações de gerenciamento em seu pool do SQL, como a operação e o estado da operação, que é IN_PROGRESS ou concluída.

## <a name="the-scaling-workflow"></a>O fluxo de trabalho do dimensionamento

Quando você inicia uma operação de dimensionamento, o sistema primeiro interrompe todas as sessões abertas, revertendo as transações abertas para garantir um estado consistente. Para operações de dimensionamento, o dimensionamento ocorrerá somente após a conclusão dessa reversão de transação.  

- Para uma operação de expansão, o sistema desanexa todos os nós de computação, provisiona os nós de computação adicionais e, em seguida, anexa novamente à camada de armazenamento.
- Para uma operação de redução, o sistema desanexa todos os nós de computação e, em seguida, anexa apenas os nós necessários à camada de armazenamento.

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre como gerenciamento do desempenho, consulte [Classes de recursos para gerenciamento de carga de trabalho](resource-classes-for-workload-management.md) e [Limites de simultaneidade e memória](memory-concurrency-limits.md).
