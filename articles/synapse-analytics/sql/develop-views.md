---
title: Exibições T-SQL usando Synapse SQL
description: Dicas para usar exibições T-SQL e desenvolver soluções com Synapse SQL.
services: synapse-analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: v-stazar
ms.reviewer: jrasnick
ms.openlocfilehash: 3a073c9539f2fb996ae59ef513525c217170f2e7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81428648"
---
# <a name="t-sql-views-using-synapse-sql"></a>Exibições T-SQL usando Synapse SQL
Neste artigo, você encontrará dicas para usar exibições T-SQL e desenvolver soluções com o Synapse SQL. 

## <a name="why-use-views"></a>Por que usar exibições?

As exibições podem ser usadas em diversas maneiras diferentes de melhorar a qualidade de sua solução.  Este artigo destaca alguns exemplos de como enriquecer sua solução com exibições e inclui as limitações que precisam ser consideradas.

### <a name="sql-pool---create-view"></a>Pool do SQL – criar exibição

> [!NOTE]
> **Pool do SQL**: a sintaxe para Create View não é discutida neste artigo. Para saber mais, consulte a documentação [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

### <a name="sql-on-demand-preview---create-view"></a>SQL sob demanda (visualização)-criar exibição

> [!NOTE]
> **SQL sob demanda**: a sintaxe para Create View não é discutida neste artigo. Para saber mais, consulte a documentação [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest).

## <a name="architectural-abstraction"></a>Abstração de arquitetura

Um padrão de aplicativo comum é recriar tabelas usando [CREATE TABLE como SELECT](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest) (CTAS), que é seguido por um padrão de renomeação de objeto ao carregar dados.

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

Lembre-se de que essa abordagem pode resultar em tabelas aparecendo e desaparecendo da exibição de um usuário e solicita mensagens de erro "a tabela não existe". As exibições podem ser usadas para fornecer aos usuários uma camada de apresentação consistente enquanto os objetos subjacentes são renomeados.

Ao fornecer acesso aos dados por meio de exibições, os usuários não precisam de visibilidade para as tabelas subjacentes. Além de uma experiência de usuário consistente, essa camada garante que os designers de análise possam desenvolver o modelo de dados. A capacidade de desenvolver tabelas subjacentes significa que os designers podem usar o CTAS para maximizar o desempenho durante o processo de carregamento de dados.

## <a name="performance-optimization"></a>Otimização do desempenho

As exibições também podem ser usadas para impor junções otimizadas de desempenho entre tabelas. Por exemplo, uma exibição pode incorporar uma chave de distribuição redundante como parte dos critérios de junção para minimizar a movimentação dos dados.

Forçar uma consulta específica ou uma dica de junção é outro benefício de usar exibições T-SQL. Dessa forma, a capacidade das exibições garante que as junções sejam sempre executadas de maneira ideal. Você evitará a necessidade de os usuários se lembrarem da construção correta para suas junções.

## <a name="limitations"></a>Limitações

Exibições em Synapse SQL são armazenadas somente como metadados. Consequentemente, as seguintes opções não estão disponíveis:

* Não há uma opção de associação de esquema
* As tabelas base não podem ser atualizadas por meio da exibição
* As exibições não podem ser criadas em tabelas temporárias
* Não há suporte para as dicas expandir/desexpandir
* Não há exibições indexadas no Synapse SQL

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, consulte [visão geral do desenvolvimento do SQL Synapse](develop-overview.md).



