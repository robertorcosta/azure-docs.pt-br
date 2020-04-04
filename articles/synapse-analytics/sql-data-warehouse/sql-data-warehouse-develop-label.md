---
title: Usando rótulos para consultas de instrumentos
description: Dicas para usar etiquetas para fazer consultas de instrumentos no pool Synapse SQL para o desenvolvimento de soluções.
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
ms.openlocfilehash: 5e2cd03ae878e80139a7f7a8ba67cef15b24d571
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633503"
---
# <a name="using-labels-to-instrument-queries-in-synapse-sql-pool"></a>Usando rótulos para fazer consultas de instrumentos no pool Synapse SQL

Incluem-se neste artigo dicas para desenvolver soluções usando etiquetas para consultas de instrumentos no pool SQL.

Dicas para usar rótulos para consultas de instrumento no SQL Data Warehouse do Azure para desenvolvimento de soluções.

## <a name="what-are-labels"></a>O que são rótulos?

O pool SQL suporta um conceito chamado rótulos de consulta. Antes de entrar em qualquer profundidade, vamos examinar um exemplo:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

A última linha marca a cadeia de caracteres ‘Meu Rótulo de Consulta’ à consulta. Essa tag é útil porque o rótulo pode ser consultar através dos DMVs.

Consultar rótulos fornece um mecanismo para localizar as consultas de problema e ajudar a identificar o andamento por meio de uma execução de ELT.

Uma boa convenção de nomenclatura ajuda muito. Por exemplo, iniciar o rótulo com PROJECT, PROCEDURE, STATEMENT ou COMMENT identifica exclusivamente a consulta entre todos os códigos no controle de origem.

A consulta a seguir usa uma exibição de gerenciamento dinâmico para pesquisar por rótulo:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> É essencial colocar colchetes ou aspas duplas em torno da palavra do rótulo ao consultar. Rótulo é uma palavra reservada e causa um erro quando ele não é delimitado.

## <a name="next-steps"></a>Próximas etapas

Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](sql-data-warehouse-overview-develop.md).
