---
title: Converter classe de recurso em um grupo de carga de trabalho
description: Saiba como criar um grupo de carga de trabalho semelhante a uma classe de recurso no Azure SQL Data Warehouse.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5d73ba8f21fe7731fb751d42a8497ff8e1ebba7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81383637"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Converter classes de recursos em grupos de cargas de trabalho

Os grupos de carga de trabalho fornecem um mecanismo para isolar e conter recursos do sistema.  Além disso, os grupos de cargas de trabalho permitem definir regras de execução para as solicitações em execução nelas.  Uma regra de execução de tempo limite de consulta permite que consultas de fuga sejam canceladas sem intervenção do usuário.  Este artigo explica como usar uma classe de recurso existente e criar um grupo de carga de trabalho com uma configuração semelhante.  Além disso, uma regra de tempo limite de consulta opcional é adicionada.

> [!NOTE]
> Consulte a seção [misturar atribuições de classe de recurso com classificadores](sql-data-warehouse-workload-classification.md#mixing-resource-class-assignments-with-classifiers) no documento conceito de [classificação de carga de trabalho](sql-data-warehouse-workload-classification.md) para obter orientação sobre como usar grupos de carga de trabalho e classes de recursos ao mesmo tempo.

## <a name="understanding-the-existing-resource-class-configuration"></a>Compreendendo a configuração da classe de recurso existente

Os grupos de cargas de trabalho `REQUEST_MIN_RESOURCE_GRANT_PERCENT` exigem um parâmetro chamado que especifica a porcentagem de recursos gerais do sistema alocados por solicitação.  A alocação de recursos é feita para [classes de recursos](resource-classes-for-workload-management.md#what-are-resource-classes) alocando slots de simultaneidade.  Para determinar o valor a ser especificado `REQUEST_MIN_RESOURCE_GRANT_PERCENT`para, use o DMV sys <link tbd> . dm_workload_management_workload_groups_stats.  Por exemplo, a consulta de consulta abaixo retorna um valor que pode ser usado para `REQUEST_MIN_RESOURCE_GRANT_PERCENT` o parâmetro para criar um grupo de carga de trabalho semelhante a staticrc40.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Os grupos de cargas de trabalho operam com base na porcentagem de recursos gerais do sistema.  

Como os grupos de cargas de trabalho operam com base na porcentagem de recursos gerais do sistema, à medida que você escala e reduz verticalmente, a porcentagem de recursos alocados para classes de recursos estáticos em relação às alterações gerais dos recursos do sistema.  Por exemplo, staticrc40 em DW1000c aloca 9,6% dos recursos gerais do sistema.  Em DW2000c, 19,2% são alocados.  Esse modelo é semelhante se você deseja escalar verticalmente para simultaneidade em vez de alocar mais recursos por solicitação.

## <a name="create-workload-group"></a>Criar grupo de carga de trabalho

Com o conhecido `REQUEST_MIN_RESOURCE_GRANT_PERCENT`, você pode usar a sintaxe criar grupo <link> de carga de trabalho para criar o grupo de cargas de trabalho.  Opcionalmente, você pode especificar `MIN_PERCENTAGE_RESOURCE` um valor maior que zero para isolar recursos para o grupo de carga de trabalho.  Além disso, você pode especificar `CAP_PERCENTAGE_RESOURCE` , opcionalmente, menos de 100 para limitar a quantidade de recursos que o grupo de carga de trabalho pode consumir.  

O exemplo abaixo define o `MIN_PERCENTAGE_RESOURCE` para dedicar 9,6% dos recursos do sistema ao `wgDataLoads` e garante que uma consulta poderá ser executada o tempo todo.  Além disso `CAP_PERCENTAGE_RESOURCE` , é definido como 38,4% e limita esse grupo de cargas de trabalho a quatro solicitações simultâneas.  Ao definir o `QUERY_EXECUTION_TIMEOUT_SEC` parâmetro como 3600, qualquer consulta executada por mais de uma hora será cancelada automaticamente.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Criar o classificador

Anteriormente, o mapeamento de consultas para classes de recursos foi feito com [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Para obter a mesma funcionalidade e mapear solicitações para grupos de carga de trabalho, use a sintaxe [Create Workload classificador](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) .  O uso de sp_addrolemember só permitia que você mapeie recursos para uma solicitação com base em um logon.  Um classificador fornece opções adicionais além do logon, como:
    - label
    - sessão
    - Quando o exemplo abaixo atribui consultas `AdfLogin` do logon que também têm o rótulo de [opção](sql-data-warehouse-develop-label.md) definido como `factloads` para o grupo `wgDataLoads` de carga de trabalho criado acima.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>Testar com uma consulta de exemplo

Veja abaixo uma consulta de exemplo e uma consulta DMV para garantir que o grupo de carga de trabalho e o classificador estejam configurados corretamente.

```sql
SELECT SUSER_SNAME() --should be 'AdfLogin'

--change to a valid table AdfLogin has access to
SELECT TOP 10 *
  FROM nation
  OPTION (label='factloads')

SELECT request_id, [label], classifier_name, group_name, command
  FROM sys.dm_pdw_exec_requests
  WHERE [label] = 'factloads'
  ORDER BY submit_time DESC
```

## <a name="next-steps"></a>Próximas etapas

- [Isolamento da carga de trabalho](sql-data-warehouse-workload-isolation.md)
- [Como criar um grupo de carga de trabalho](quickstart-configure-workload-isolation-tsql.md)
- [CREATE WORKLOAD CLASSIFIER (Transact-SQL)](/sql/t-sql/statements/create-workload-classifier-transact-sql?&view=azure-sqldw-latest)
- [CREATE WORKLOAD GROUP (Transact-SQL)](/sql/t-sql/statements/create-workload-group-transact-sql?view=azure-sqldw-latest)
