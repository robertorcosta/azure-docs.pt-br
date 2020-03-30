---
title: Recursos para o desenvolvimento de um data warehouse no Azure Synapse Analytics
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
ms.openlocfilehash: aa0f5fd631dfa3e4deca4853c27a667fcf312fec
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350289"
---
# <a name="design-decisions-and-coding-techniques-for-a-data-warehouse-in-azure-synapse-analytics"></a>Decisões de design e técnicas de codificação para um data warehouse no Azure Synapse Analytics 
 Neste artigo, você encontrará recursos adicionais para ajudá-lo a entender melhor as principais decisões de design, recomendações e técnicas de codificação para um data warehouse no Azure Synapse.

## <a name="key-design-decisions"></a>Principais decisões de design
Os artigos a seguir destacam conceitos e decisões de design para o desenvolvimento de um data warehouse distribuído usando o recurso SQL Analytics no Azure Synapse:

* [Conexões](sql-data-warehouse-connect-overview.md)
* [Simultaneidade](resource-classes-for-workload-management.md)
* [Transações](sql-data-warehouse-develop-transactions.md)
* [esquemas definidos pelo usuário](sql-data-warehouse-develop-user-defined-schemas.md)
* [distribuição da tabela](sql-data-warehouse-tables-distribute.md)
* [índices da tabela](sql-data-warehouse-tables-index.md)
* [partições da tabela](sql-data-warehouse-tables-partition.md)
* [CTAS](sql-data-warehouse-develop-ctas.md)
* [estatísticas](sql-data-warehouse-tables-statistics.md)

## <a name="development-recommendations-and-coding-techniques"></a>Recomendações para o desenvolvimento e técnicas de codificação
Os seguintes artigos apresentam técnicas de codificação específicas, dicas e recomendações para o desenvolvimento de um data warehouse com o SQL Analytics:

* [procedimentos armazenados](sql-data-warehouse-develop-stored-procedures.md)
* [rótulos](sql-data-warehouse-develop-label.md)
* [modos de exibição](sql-data-warehouse-develop-views.md)
* [tabelas temporárias](sql-data-warehouse-tables-temporary.md)
* [SQL dinâmico](sql-data-warehouse-develop-dynamic-sql.md)
* [looping](sql-data-warehouse-develop-loops.md)
* [agrupar por opções](sql-data-warehouse-develop-group-by-options.md)
* [atribuição variável](sql-data-warehouse-develop-variable-assignment.md)

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações de referência, consulte [as instruções T-SQL](sql-data-warehouse-reference-tsql-statements.md).
