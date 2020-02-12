---
title: Recursos para o desenvolvimento de um data warehouse no Azure Synapse Analytics
description: Conceitos de desenvolvimento, decisões de design, recomendações e técnicas de codificação para o SQL Data Warehouse.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 08/29/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 0f8967eab1be8351c50035d8afa784aa7afd79e1
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153308"
---
# <a name="design-decisions-and-coding-techniques-for-a-data-warehouse-in-azure-synapse-analytics"></a>Decisões de design e técnicas de codificação para uma data warehouse no Azure Synapse Analytics 
 Neste artigo, você encontrará recursos adicionais para ajudá-lo a entender melhor as principais decisões de design, recomendações e técnicas de codificação para uma data warehouse no Azure Synapse.

## <a name="key-design-decisions"></a>Principais decisões de design
Os artigos a seguir destacam os conceitos e as decisões de design para o desenvolvimento de um data warehouse distribuído usando o recurso de análise de SQL no Azure Synapse:

* [localNetworkGateways](sql-data-warehouse-connect-overview.md)
* [concurrency](resource-classes-for-workload-management.md)
* [transações](sql-data-warehouse-develop-transactions.md)
* [esquemas definidos pelo usuário](sql-data-warehouse-develop-user-defined-schemas.md)
* [distribuição da tabela](sql-data-warehouse-tables-distribute.md)
* [índices da tabela](sql-data-warehouse-tables-index.md)
* [partições de tabela](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [estatísticas](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Recomendações para o desenvolvimento e técnicas de codificação
Os artigos a seguir apresentam técnicas de codificação específicas, dicas e recomendações para o desenvolvimento de um data warehouse com a análise de SQL:

* [procedimentos armazenados](sql-data-warehouse-develop-stored-procedures.md)
* [rótulos](sql-data-warehouse-develop-label.md)
* [modos de exibição](sql-data-warehouse-develop-views.md)
* [tabelas temporárias](sql-data-warehouse-tables-temporary.md)
* [SQL dinâmico](sql-data-warehouse-develop-dynamic-sql.md)
* [looping](sql-data-warehouse-develop-loops.md)
* [agrupar por opções](sql-data-warehouse-develop-group-by-options.md)
* [atribuição de variável](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações de referência, consulte [instruções T-SQL](sql-data-warehouse-reference-tsql-statements.md).
