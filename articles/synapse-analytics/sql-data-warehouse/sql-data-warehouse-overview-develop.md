---
title: Recursos para o desenvolvimento de um pool SQL Synapse no Azure Synapse Analytics
description: Conceitos de desenvolvimento, decisões de design, recomendações e técnicas de codificação para SQL Data Warehouse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: a3c0d7924fb550761d050c9c404b1065c7d3cf72
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85211486"
---
# <a name="design-decisions-and-coding-techniques-for-a-synapse-sql-pool-in-azure-synapse-analytics"></a>Decisões de design e técnicas de codificação para um pool SQL Synapse no Azure Synapse Analytics 
 Neste artigo, você encontrará recursos adicionais para ajudá-lo a entender melhor as principais decisões de design, recomendações e técnicas de codificação para um pool do SQL no Azure Synapse.

## <a name="key-design-decisions"></a>Principais decisões de design
Os artigos a seguir destacam os conceitos e as decisões de design para o desenvolvimento de um data warehouse distribuído usando o recurso de pool do SQL no Azure Synapse:

* [conexões](../sql/connect-overview.md)
* [corrente](resource-classes-for-workload-management.md)
* [transações](sql-data-warehouse-develop-transactions.md)
* [esquemas definidos pelo usuário](sql-data-warehouse-develop-user-defined-schemas.md)
* [distribuição de tabela](sql-data-warehouse-tables-distribute.md)
* [índices de tabela](sql-data-warehouse-tables-index.md)
* [partições de tabela](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [estatística](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Recomendações para o desenvolvimento e técnicas de codificação
Os artigos a seguir apresentam técnicas de codificação específicas, dicas e recomendações para o desenvolvimento de um pool SQL:

* [procedimentos armazenados](sql-data-warehouse-develop-stored-procedures.md)
* [las](sql-data-warehouse-develop-label.md)
* [modos de exibição](sql-data-warehouse-develop-views.md)
* [tabelas temporárias](sql-data-warehouse-tables-temporary.md)
* [SQL dinâmico](sql-data-warehouse-develop-dynamic-sql.md)
* [loop](sql-data-warehouse-develop-loops.md)
* [Agrupar por opções](sql-data-warehouse-develop-group-by-options.md)
* [atribuição de variável](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações de referência, consulte [instruções T-SQL](sql-data-warehouse-reference-tsql-statements.md).
