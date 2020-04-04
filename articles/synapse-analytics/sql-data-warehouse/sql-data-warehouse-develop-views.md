---
title: Usando visualizações T-SQL
description: Dicas para usar visualizações T-SQL e desenvolver soluções no pool Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 76442368fe4b3e498f622a8a3cd5b5b973f16bd6
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633385"
---
# <a name="views-in-synapse-sql-pool"></a>Vistas na piscina Synapse SQL

As exibições podem ser usadas em diversas maneiras diferentes de melhorar a qualidade de sua solução.

O pool SQL suporta vistas padrão e materializadas. Ambas são tabelas virtuais criadas com expressões SELECT e apresentadas a consultas como tabelas lógicas.

As visualizações encapsulam a complexidade da computação comum de dados e adicionam uma camada de abstração às alterações de computação para que não haja necessidade de reescrever consultas.

## <a name="standard-view"></a>Exibição padrão

Uma exibição padrão calcula seus dados cada vez que a exibição é usada. Não há dados armazenados no disco. As pessoas normalmente usam visualizações padrão como uma ferramenta que ajuda a organizar os objetos e consultas lógicas em um banco de dados.

Para usar uma exibição padrão, uma consulta precisa fazer referência direta a ela. Para saber mais, consulte a documentação [CREATE VIEW](/sql/t-sql/statements/create-view-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).

As exibições no pool SQL são armazenadas apenas como metadados. Consequentemente, as seguintes opções não estão disponíveis:

* Não há opção de associação de esquema
* As tabelas base não podem ser atualizadas através da exibição
* As visualizações não podem ser criadas sobre tabelas temporárias
* Não há suporte para as dicas EXPAND / NOEXPAND
* Não há visualizações indexadas no pool SQL

As visualizações padrão podem ser utilizadas para impor o desempenho otimizado entre as tabelas. Por exemplo, uma exibição pode incorporar uma chave de distribuição redundante como parte dos critérios de junção para minimizar a movimentação dos dados.

Outro benefício de uma exibição pode ser forçar uma dica de consulta ou junção específica. Usar exibições dessa maneira garante que as junções sempre sejam executadas de forma ideal, evitando a necessidade dos usuários lembrarem a construção correta de suas junções.

## <a name="materialized-view"></a>Exibição materializada

Uma visão materializada pré-computa, armazena e mantém seus dados no pool SQL como uma tabela. Não há necessidade de recomputação cada vez que uma visão materializada é usada.

À medida que os dados são carregados em tabelas base, o pool SQL atualiza sincronicamente as visualizações materializadas.  O otimizador de consulta usa automaticamente visualizações materializadas implantadas para melhorar o desempenho da consulta, mesmo que as visualizações não sejam referenciadas na consulta.  

As consultas que mais se beneficiam de visualizações materializadas são consultas complexas (normalmente consultas com agregações e agregações) em grandes tabelas que produzem pequenos conjuntos de resultados.  

Para obter detalhes sobre a sintaxe de exibição materializada e outros requisitos, consulte [CRIAR EXIBIÇÃO MATERIALIZADA COMO SELECT](/sql/t-sql/statements/create-materialized-view-as-select-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest).  

Para consultar a orientação de ajuste, verifique [a sintonia de desempenho com visualizações materializadas](performance-tuning-materialized-views.md).

## <a name="example"></a>Exemplo

Um padrão de aplicativo comum é recriar tabelas usando O CTAS (CREATE TABLE AS SELECT) seguido de um padrão de renomeação de objeto durante o carregamento de dados.  

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

No entanto, essa abordagem pode resultar em tabelas aparecendo e desaparecendo da visão de um usuário, juntamente com a emissão de mensagens de erro "tabela não existe".

As exibições podem ser usadas para fornecer aos usuários uma camada de apresentação consistente enquanto os objetos subjacentes são renomeados. Ao fornecer acesso aos dados através de visualizações, os usuários não precisam de visibilidade para as tabelas subjacentes.

Essa camada fornece uma experiência de usuário consistente enquanto garante que os designers do data warehouse podem desenvolver o modelo de dados. Capacidade para desenvolver as tabelas subjacentes significa que os designers podem usar CTAS para maximizar o desempenho durante o processo de carregamento de dados.

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, consulte [visão geral do desenvolvimento do pool SQL](sql-data-warehouse-overview-develop.md).
