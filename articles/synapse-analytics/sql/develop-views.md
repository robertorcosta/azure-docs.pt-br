---
title: Visualizações T-SQL usando sinapse SqL
description: Dicas para usar visualizações T-SQL e desenvolver soluções com Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a073c9539f2fb996ae59ef513525c217170f2e7
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428648"
---
# <a name="t-sql-views-using-synapse-sql"></a>Visualizações T-SQL usando sinapse SqL
Neste artigo, você encontrará dicas para usar visualizações T-SQL e desenvolver soluções com Synapse SQL. 

## <a name="why-use-views"></a>Por que usar exibições?

As exibições podem ser usadas em diversas maneiras diferentes de melhorar a qualidade de sua solução.  Este artigo destaca alguns exemplos de como enriquecer sua solução com pontos de vista e inclui as limitações que precisam ser consideradas.

### <a name="sql-pool---create-view"></a>Pool SQL - criar visualização

> [!NOTE]
> **Pool SQL**: A sintaxe para CREATE VIEW não é discutida neste artigo. Para saber mais, consulte a documentação [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="sql-on-demand-preview---create-view"></a>SQL sob demanda (visualização) - criar visualização

> [!NOTE]
> **SQL sob demanda**: A sintaxe para CREATE VIEW não é discutida neste artigo. Para saber mais, consulte a documentação [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="architectural-abstraction"></a>Abstração de arquitetura

Um padrão de aplicativo comum é recriar tabelas usando [O CTAS (CREATE TABLE AS SELECT),](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) que é seguido por um padrão de renomeação de objeto durante o carregamento de dados.

O exemplo a seguir adiciona novos registros de data para uma dimensão de data. Observe como uma nova tabela, DimDate_New, é criada pela primeira vez e renomeada para substituir a versão original da tabela.

```sql
CREATE TABLE dbo.DimDate_New
WITH (DISTRIBUTION = ROUND_ROBIN
, CLUSTERED INDEX (DateKey ASC)
)
AS
SELECT *
FROM   dbo.DimDate  AS prod
UNION ALL
SELECT *
FROM   dbo.DimDate_stg AS stg
;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

Tenha em mente que essa abordagem pode resultar em tabelas aparecendo e desaparecendo da exibição de um usuário, e solicita mensagens de erro "tabela não existe". As exibições podem ser usadas para fornecer aos usuários uma camada de apresentação consistente enquanto os objetos subjacentes são renomeados.

Ao fornecer acesso aos dados através de visualizações, os usuários não precisam de visibilidade para as tabelas subjacentes. Além de uma experiência de usuário consistente, essa camada garante que os designers de análise possam evoluir o modelo de dados. A capacidade de evoluir as tabelas subjacentes significa que os designers podem usar o CTAS para maximizar o desempenho durante o processo de carregamento de dados.

## <a name="performance-optimization"></a>Otimização do desempenho

As visualizações também podem ser usadas para impor a performance otimizada entre as tabelas. Por exemplo, uma exibição pode incorporar uma chave de distribuição redundante como parte dos critérios de junção para minimizar a movimentação dos dados.

Forçar uma consulta específica ou juntar dicas é outro benefício do uso de visualizações T-SQL. Como tal, a capacidade de visualizações garante que as junções sejam sempre realizadas de forma ideal. Você evitará a necessidade de os usuários lembrarem da construção correta para suas adesões.

## <a name="limitations"></a>Limitações

As exibições no Synapse SQL são armazenadas apenas como metadados. Consequentemente, as seguintes opções não estão disponíveis:

* Não há uma opção de vinculação de esquemas
* As tabelas base não podem ser atualizadas através da exibição
* As visualizações não podem ser criadas sobre tabelas temporárias
* Não há suporte para as dicas EXPAND / NOEXPAND
* Não há visualizações indexadas no Synapse SQL

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, consulte visão geral do [desenvolvimento do Synapse SQL](develop-overview.md).



