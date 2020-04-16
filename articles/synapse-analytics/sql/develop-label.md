---
title: Use etiquetas de consulta no Synapse SQL
description: Incluso neste artigo estão dicas essenciais para o uso de etiquetas de consulta no Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: 47b476cbc6997ca5ec63968bdc269e2273662100
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81430026"
---
# <a name="use-query-labels-in-synapse-sql"></a>Use etiquetas de consulta no Synapse SQL
Incluso neste artigo estão dicas essenciais para o uso de etiquetas de consulta no Synapse SQL.

> [!NOTE]
> SQL on-demand (preview) não suporta consultas de rotulagem.

## <a name="what-are-query-labels"></a>Quais são os rótulos de consulta
O pool SQL suporta um conceito chamado rótulos de consulta. Antes de entrar em qualquer profundidade, vamos examinar um exemplo:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

A última linha marca a cadeia de caracteres ‘Meu Rótulo de Consulta’ à consulta. Essa marca é particularmente útil, pois o rótulo é capaz de executar consultas por meio de DMVs. A consulta para rótulos fornece um mecanismo para localizar consultas de problemas e ajuda a identificar o progresso através de uma execução elt.

Uma boa convenção de nomes é muito útil. Por exemplo, iniciar o rótulo com PROJECT, PROCEDURE, STATEMENT ou COMMENT identifica exclusivamente a consulta entre todos os códigos no controle de origem.

A consulta a seguir usa uma exibição de gerenciamento dinâmico para pesquisar por rótulo:

```sql
SELECT  *
FROM    sys.dm_pdw_exec_requests r
WHERE   r.[label] = 'My Query Label'
;
```

> [!NOTE]
> É essencial colocar colchetes ou aspas duplas em torno da palavra do rótulo ao consultar. Rótulo é uma palavra reservada e causa um erro quando ele não é delimitado. 
> 
> 

## <a name="next-steps"></a>Próximas etapas
Para obter mais dicas de desenvolvimento, confira [visão geral de desenvolvimento](develop-overview.md).


