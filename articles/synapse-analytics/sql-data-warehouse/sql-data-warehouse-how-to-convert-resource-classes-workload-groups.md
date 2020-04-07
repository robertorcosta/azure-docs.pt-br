---
title: Converter classe de recursos em um grupo de carga de trabalho
description: Aprenda a criar um grupo de carga de trabalho semelhante a uma classe de recursos no Azure SQL Data Warehouse.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: ''
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: rortloff
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 8cee874106598c7d81b923d7dd32ba91902d9326
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2020
ms.locfileid: "80745177"
---
# <a name="convert-resource-classes-to-workload-groups"></a>Converter classes de recursos em grupos de carga de trabalho

Os grupos de carga de trabalho fornecem um mecanismo para isolar e conter os recursos do sistema.  Além disso, os grupos de carga de trabalho permitem definir regras de execução para as solicitações em execução neles.  Uma regra de execução de tempo de consulta permite que consultas desfugadas sejam canceladas sem intervenção do usuário.  Este artigo explica como fazer uma aula de recursos existente e criar um grupo de carga de trabalho com uma configuração semelhante.  Além disso, uma regra de tempo de tempo de consulta opcional é adicionada.

## <a name="understanding-the-existing-resource-class-configuration"></a>Entendendo a configuração da classe de recursos existente

Os grupos de carga `REQUEST_MIN_RESOURCE_GRANT_PERCENT` de trabalho exigem um parâmetro chamado que especifica a porcentagem dos recursos gerais do sistema alocados por solicitação.  A alocação de recursos é feita para [classes de recursos,](resource-classes-for-workload-management.md#what-are-resource-classes) alocando slots de concorrência.  Para determinar o valor `REQUEST_MIN_RESOURCE_GRANT_PERCENT`a especificar para <link tbd> , use o sys.dm_workload_management_workload_groups_stats Detran.  Por exemplo, a consulta de consulta abaixo retorna um `REQUEST_MIN_RESOURCE_GRANT_PERCENT` valor que pode ser usado para o parâmetro para criar um grupo de carga de trabalho semelhante ao staticrc40.

```sql
SELECT Request_min_resource_grant_percent = Effective_request_min_resource_grant_percent
  FROM sys.dm_workload_management_workload_groups_stats
  WHERE name = 'staticrc40'
```

> [!NOTE]
> Os grupos de carga de trabalho operam com base na porcentagem dos recursos globais do sistema.  

Como os grupos de carga de trabalho operam com base na porcentagem dos recursos globais do sistema, à medida que você escala para cima e para baixo, a porcentagem de recursos alocados para classes de recursos estáticos em relação às mudanças gerais de recursos do sistema.  Por exemplo, o staticrc40 no DW1000c aloca 9,6% dos recursos globais do sistema.  Na DW2000c, 19,2% são alocados.  Este modelo é semelhante se você deseja escalar para a concorrência versus alocar mais recursos por solicitação.

## <a name="create-workload-group"></a>Criar grupo de carga de trabalho

Com o `REQUEST_MIN_RESOURCE_GRANT_PERCENT`conhecido, você pode <link> usar a sintaxe CREATE WORKLOAD GROUP para criar o grupo de carga de trabalho.  Você pode especificar `MIN_PERCENTAGE_RESOURCE` opcionalmente um que seja maior que zero para isolar recursos para o grupo de carga de trabalho.  Além disso, você `CAP_PERCENTAGE_RESOURCE` pode especificar opcionalmente menos de 100 para limitar a quantidade de recursos que o grupo de carga de trabalho pode consumir.  

O exemplo abaixo `MIN_PERCENTAGE_RESOURCE` define o dever 9,6% dos recursos do sistema `wgDataLoads` e garante que uma consulta será capaz de executar todas as vezes.  Além disso, `CAP_PERCENTAGE_RESOURCE` é definido para 38,4% e limita este grupo de carga de trabalho a quatro solicitações simultâneas.  Ao definir `QUERY_EXECUTION_TIMEOUT_SEC` o parâmetro para 3600, qualquer consulta que seja executada por mais de 1 hora será automaticamente cancelada.

```sql
CREATE WORKLOAD GROUP wgDataLoads WITH  
( REQUEST_MIN_RESOURCE_GRANT_PERCENT = 9.6
 ,MIN_PERCENTAGE_RESOURCE = 9.6
 ,CAP_PERCENTAGE_RESOURCE = 38.4
 ,QUERY_EXECUTION_TIMEOUT_SEC = 3600)
```

## <a name="create-the-classifier"></a>Criar o Classificador

Anteriormente, o mapeamento de consultas às classes de recursos era feito com [sp_addrolemember](resource-classes-for-workload-management.md#change-a-users-resource-class).  Para obter a mesma funcionalidade e mapear solicitações para grupos de carga de trabalho, use a sintaxe [CREATE WORKLOAD CLASSIFIER.](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest)  O uso sp_addrolemember só permitia mapear recursos para uma solicitação com base em um login.  Um classificador oferece opções adicionais além do login, tais como:
    - label
    - sessão
    - tempo O exemplo abaixo atribui `AdfLogin` consultas do login que também `factloads` têm o `wgDataLoads` [RÓTULO OPÇÃO](sql-data-warehouse-develop-label.md) definido para o grupo de carga de trabalho criado acima.

```sql
CREATE WORKLOAD CLASSIFIER wcDataLoads WITH  
( WORKLOAD_GROUP = 'wgDataLoads'
 ,MEMBERNAME = 'AdfLogin'
 ,WLM_LABEL = 'factloads')
```

## <a name="test-with-a-sample-query"></a>Teste com uma consulta de amostra

Abaixo está uma consulta de exemplo e uma consulta do Detran para garantir que o grupo de carga de trabalho e o classificador estejam configurados corretamente.

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
- [Como criar um link de grupo de carga de trabalho](quickstart-configure-workload-isolation-tsql.md)
