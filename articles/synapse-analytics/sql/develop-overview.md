---
title: Recursos para o desenvolvimento de recursos Synapse SQL
description: Conceitos de desenvolvimento, decisões de design, recomendações e técnicas de codificação para Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: ba6ceec3064c5c876ca899ab58881e23913b9701
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429012"
---
# <a name="design-decisions-and-coding-techniques-for-synapse-sql-features-in-azure-synapse-analytics"></a>Decisões de design e técnicas de codificação para recursos Synapse SQL no Azure Synapse Analytics
Neste artigo, você encontrará uma lista de recursos para funções SQL pool e SQL on-demand (preview) do Synapse SQL. Os artigos recomendados são divididos em duas seções: Decisões principais de design e técnicas de desenvolvimento e codificação.

O objetivo desses artigos é ajudá-lo a desenvolver a abordagem técnica ideal para os componentes Synapse SQL dentro do Synapse Analytics.

## <a name="key-design-decisions"></a>Principais decisões de design
Os artigos abaixo destacam conceitos e decisões de design para o desenvolvimento do Synapse SQL:

|                                                          |   Piscina SQL   | SQL sob demanda |
| -----------------------------------------------------    | ---- | ---- |
| [Conexões](connect-overview.md)                    | Sim | Sim |
| [Classes de recursos e concurrency](../sql-data-warehouse/resource-classes-for-workload-management.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json) | Sim    | Não |
| [Transactions](develop-transactions.md)              | Sim | Não |
| [Esquemas definidos pelo usuário](develop-user-defined-schemas.md) | Sim | Sim |
| [Distribuição de tabelas](../sql-data-warehouse/sql-data-warehouse-tables-distribute.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                 | Sim | Não |
| [Índices de tabela](../sql-data-warehouse/sql-data-warehouse-tables-index.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                           | Sim | Não |
| [Partições de tabela](../sql-data-warehouse/sql-data-warehouse-tables-partition.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                     | Sim | Não |
| [Estatísticas](develop-tables-statistics.md)            | Sim | Sim |
| [CTAS](../sql-data-warehouse/sql-data-warehouse-develop-ctas.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)                                             | Sim | Não |
| [Tabelas externas](develop-tables-external-tables.md) | Sim | Sim |
| [CETAS](develop-tables-cetas.md)                     | Sim | Sim |


## <a name="recommendations"></a>Recomendações

Abaixo você encontrará artigos essenciais que enfatizam técnicas de codificação específicas, dicas e recomendações para o desenvolvimento:

|                                            | Piscina SQL | SQL sob demanda |
| ------------------------------------------ | ------------------ | ----------------------- |
| [Procedimentos armazenados](develop-stored-procedures.md)  | Sim                | Não                      |
| [Rótulos](develop-label.md)                           | Sim                | Não                      |
| [Exibições](develop-views.md)                             | Sim                | Sim                     |
| [Tabelas temporárias](develop-tables-temporary.md)       | Sim                | Sim                     |
| [SQL dinâmico](develop-dynamic-sql.md)                 | Sim                | Sim                     |
| [Looping](develop-loops.md)                         | Sim                | Sim                     |
| [Agrupar por opções](develop-group-by-options.md)       | Sim                | Não                      |
| [Atribuição de variável](develop-variable-assignment.md) | Sim                | Sim                     |

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações de referência, consulte [as instruções T-SQL do pool SQL](../sql-data-warehouse/sql-data-warehouse-reference-tsql-statements.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json).

