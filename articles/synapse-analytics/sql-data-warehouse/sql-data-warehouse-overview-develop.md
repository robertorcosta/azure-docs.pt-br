---
title: Recursos para o desenvolvimento de um pool Synapse SQL no Azure Synapse Analytics
description: Conceitos de desenvolvimento, decisões de design, recomendações e técnicas de codificação para o SQL Data Warehouse.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 933a175213afd81c81eb237a2b2dd4c3e24e3315
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633147"
---
# <a name="design-decisions-and-coding-techniques-for-a-synapse-sql-pool-in-azure-synapse-analytics"></a>Decisões de design e técnicas de codificação para um pool Synapse SQL no Azure Synapse Analytics 
 Neste artigo, você encontrará recursos adicionais para ajudá-lo a entender melhor as principais decisões de design, recomendações e técnicas de codificação para um pool SQL no Azure Synapse.

## <a name="key-design-decisions"></a>Principais decisões de design
Os seguintes artigos destacam conceitos e decisões de design para o desenvolvimento de um data warehouse distribuído usando o recurso de pool SQL no Azure Synapse:

* [Conexões](sql-data-warehouse-connect-overview.md)
* [Simultaneidade](resource-classes-for-workload-management.md)
* [Transações](sql-data-warehouse-develop-transactions.md)
* [esquemas definidos pelo usuário](sql-data-warehouse-develop-user-defined-schemas.md)
* [distribuição da tabela](sql-data-warehouse-tables-distribute.md)
* [índices da tabela](sql-data-warehouse-tables-index.md)
* [partições da tabela](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [Estatísticas](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Recomendações para o desenvolvimento e técnicas de codificação
Os seguintes artigos apresentam técnicas de codificação específicas, dicas e recomendações para o desenvolvimento de um pool SQL:

* [procedimentos armazenados](sql-data-warehouse-develop-stored-procedures.md)
* [Rótulos](sql-data-warehouse-develop-label.md)
* [Modos de exibição](sql-data-warehouse-develop-views.md)
* [tabelas temporárias](sql-data-warehouse-tables-temporary.md)
* [SQL dinâmico](sql-data-warehouse-develop-dynamic-sql.md)
* [looping](sql-data-warehouse-develop-loops.md)
* [grupo por opções](sql-data-warehouse-develop-group-by-options.md)
* [atribuição variável](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações de referência, consulte [as instruções T-SQL](sql-data-warehouse-reference-tsql-statements.md).
