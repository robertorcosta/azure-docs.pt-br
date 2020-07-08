---
title: Usando exibições do T-SQL
description: Dicas para usar exibições T-SQL e desenvolver soluções no pool SQL do Synapse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 26eb3a495fd1c896416265687d92da66dfc3599b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85212285"
---
# <a name="views-in-synapse-sql-pool"></a>Exibições no pool SQL Synapse

As exibições podem ser usadas em diversas maneiras diferentes de melhorar a qualidade de sua solução.

O pool de SQL é compatível com exibições padrão e materializadas. Ambas são tabelas virtuais criadas com expressões SELECT e apresentadas às consultas como tabelas lógicas.

As exibições encapsulam a complexidade da computação de dados comum e adicionam uma camada de abstração às alterações de computação para que não seja necessário regravar consultas.

## <a name="standard-view"></a>Exibição padrão

Uma exibição padrão calcula os dados sempre que a exibição é usada. Não há dados armazenados no disco. Normalmente, as pessoas usam exibições padrão como uma ferramenta que ajuda a organizar os objetos lógicos e as consultas em um banco de dados.

Para usar a exibição padrão, uma consulta precisa fazer referência direta a ela. Para saber mais, consulte a documentação [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

Os modos de exibição no pool SQL são armazenados somente como metadados. Consequentemente, as seguintes opções não estão disponíveis:

* Não há opção de associação de esquema
* As tabelas base não podem ser atualizadas por meio da exibição
* As exibições não podem ser criadas em tabelas temporárias
* Não há suporte para as dicas EXPAND / NOEXPAND
* Não há exibições indexadas no pool do SQL

As exibições padrão podem ser utilizadas para impor junções otimizadas de desempenho entre tabelas. Por exemplo, uma exibição pode incorporar uma chave de distribuição redundante como parte dos critérios de junção para minimizar a movimentação dos dados.

Outro benefício de uma exibição pode ser forçar uma dica de consulta ou junção específica. Usar exibições dessa maneira garante que as junções sempre sejam executadas de forma ideal, evitando a necessidade dos usuários lembrarem a construção correta de suas junções.

## <a name="materialized-view"></a>Exibição materializada

Uma exibição materializada pré-calcula, armazena e mantém seus dados no pool de SQL como uma tabela. Não é necessário recálculo sempre que uma exibição materializada é usada.

À medida que os dados são carregados em tabelas base, o pool do SQL atualiza de forma síncrona as exibições materializadas.  O otimizador de consulta usa automaticamente exibições materializadas implantadas para melhorar o desempenho da consulta, mesmo que as exibições não sejam referenciadas na consulta.  

As consultas que mais se beneficiam com exibições materializadas são consultas complexas (normalmente consultas com junções e agregações) em tabelas grandes que produzem pequenos conjuntos de resultados.  

Para obter detalhes sobre a sintaxe de exibição materializada e outros requisitos, consulte [CREATE MATERIALIZED VIEW AS SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  

Para obter diretrizes de ajuste de consulta, verifique o [ajuste de desempenho com exibições materializadas](performance-tuning-materialized-views.md).

## <a name="example"></a>Exemplo

Um padrão de aplicativo comum é recriar tabelas usando CREATE TABLE como SELECT (CTAS) seguido por um padrão de renomeação de objeto ao carregar dados.  

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
FROM   dbo.DimDate_stg AS stg;

RENAME OBJECT DimDate TO DimDate_Old;
RENAME OBJECT DimDate_New TO DimDate;

```

No entanto, essa abordagem pode resultar em tabelas aparecendo e desaparecendo da exibição de um usuário, juntamente com a emissão de mensagens de erro "a tabela não existe".

As exibições podem ser usadas para fornecer aos usuários uma camada de apresentação consistente enquanto os objetos subjacentes são renomeados. Ao fornecer acesso aos dados por meio de exibições, os usuários não precisam de visibilidade para as tabelas subjacentes.

Essa camada fornece uma experiência de usuário consistente enquanto garante que os designers do data warehouse podem desenvolver o modelo de dados. Capacidade para desenvolver as tabelas subjacentes significa que os designers podem usar CTAS para maximizar o desempenho durante o processo de carregamento de dados.

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, consulte [visão geral do desenvolvimento de pools do SQL](sql-data-warehouse-overview-develop.md).
