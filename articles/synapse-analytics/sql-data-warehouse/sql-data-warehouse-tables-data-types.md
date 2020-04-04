---
title: Definindo tipos de dados
description: Recomendações para definir os tipos de dados de tabela no pool Synapse SQL.
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
ms.openlocfilehash: 5b3d24232c26bb4d483d360eb593bc361190ccfe
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631256"
---
# <a name="table-data-types-in-synapse-sql-pool"></a>Tipos de dados de tabela no pool Synapse SQL
Incluem-se neste artigo recomendações para definir os tipos de dados de tabela no pool SQL. 

## <a name="supported-data-types"></a>Tipos de dados com suporte

O pool SQL suporta os tipos de dados mais usados. Para obter uma lista dos tipos de dados com suporte, consulte [tipos de dados](/sql/t-sql/statements/create-table-azure-sql-data-warehouse#DataTypes) na instrução CREATE TABLE. 

## <a name="minimize-row-length"></a>Minimizar o tamanho da linha
Minimizar o tamanho dos tipos de dados reduz o tamanho da linha, o que leva a um desempenho de consulta melhor. Use o menor tipo de dados que funcione para seus dados. 

- Evite definir as colunas de caractere como um tamanho padrão grande. Por exemplo, se o maior valor for 25 caracteres, defina a coluna como VARCHAR(25). 
- Evite usar [NVARCHAR](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) quando precisar somente de VARCHAR.
- Quando possível, use NVARCHAR(4000) ou VARCHAR(8000) em vez de NVARCHAR(MAX) ou VARCHAR(MAX).

Se você estiver usando tabelas externas do PolyBase para carregar suas tabelas, o comprimento definido da linha de tabela não pode exceder 1 MB. Quando uma linha com dados de tamanho variável exceder 1 MB, você poderá carregar a linha com BCP, mas não com PolyBase.

## <a name="identify-unsupported-data-types"></a>Identificar tipos de dados sem suporte
Se você estiver migrando seu banco de dados de outro banco de dados SQL, você pode encontrar tipos de dados que não são suportados no pool SQL. Use a seguinte consulta para descobrir tipos de dados não suportados no esquema SQL existente:

```sql
SELECT  t.[name], c.[name], c.[system_type_id], c.[user_type_id], y.[is_user_defined], y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN ('geography','geometry','hierarchyid','image','text','ntext','sql_variant','xml')
 AND  y.[is_user_defined] = 1;
```


## <a name="workarounds-for-unsupported-data-types"></a><a name="unsupported-data-types"></a>Soluções alternativas para os tipos de dados sem suporte

A lista a seguir mostra os tipos de dados que o pool SQL não suporta e oferece alternativas úteis para tipos de dados não suportados.

| Tipos de dados sem suporte | Solução alternativa |
| --- | --- |
| [Geometria](/sql/t-sql/spatial-geometry/spatial-types-geometry-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Geografia](/sql/t-sql/spatial-geography/spatial-types-geography) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [Hierarchyid](/sql/t-sql/data-types/hierarchyid-data-type-method-reference) |[nvarchar](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql)(4000) |
| [image](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varbinary](/sql/t-sql/data-types/binary-and-varbinary-transact-sql) |
| [text](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [Ntext](/sql/t-sql/data-types/ntext-text-and-image-transact-sql) |[NVARCHAR](/sql/t-sql/data-types/nchar-and-nvarchar-transact-sql) |
| [Sql_variant](/sql/t-sql/data-types/sql-variant-transact-sql) |Divida a coluna em várias colunas fortemente tipadas. |
| [Tabela](/sql/t-sql/data-types/table-transact-sql) |Converta em tabelas temporárias. |
| [Timestamp](/sql/t-sql/data-types/date-and-time-types) |Refazer o código para usar [datetime2](/sql/t-sql/data-types/datetime2-transact-sql) e a função [CURRENT_TIMESTAMP](/sql/t-sql/functions/current-timestamp-transact-sql). Apenas as constantes são suportadas como padrão, então current_timestamp não pode ser definida como uma restrição padrão. Se você precisar migrar os valores da versão da linha a partir de uma coluna digitada por carimbo de ponto, use [BINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) ou [VARBINARY](/sql/t-sql/data-types/binary-and-varbinary-transact-sql)(8) para não anular valores de versão da linha. |
| [xml](/sql/t-sql/xml/xml-transact-sql) |[varchar](/sql/t-sql/data-types/char-and-varchar-transact-sql) |
| [Tipos definidos pelo usuário](/sql/relational-databases/native-client/features/using-user-defined-types) |Converta para o tipo de dados nativo quando possível. |
| valores padrão | Os valores padrão dão suporte somente a literais e constantes. |


## <a name="next-steps"></a>Próximas etapas

Para obter mais informações sobre o desenvolvimento de tabelas, consulte [Visão geral da tabela](sql-data-warehouse-tables-overview.md).
