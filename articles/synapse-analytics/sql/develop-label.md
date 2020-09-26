---
title: Usar rótulos de consulta no SQL Synapse
description: Estão incluídos neste artigo dicas essenciais para usar rótulos de consulta no SQL Synapse.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.custom: ''
ms.openlocfilehash: e8f78d6031e57da42e1d69587aedca0763c9fec2
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/25/2020
ms.locfileid: "91289065"
---
# <a name="use-query-labels-in-synapse-sql"></a>Usar rótulos de consulta no SQL Synapse
Estão incluídos neste artigo dicas essenciais para usar rótulos de consulta no SQL Synapse.

> [!NOTE]
> O SQL sob demanda (versão prévia) não dá suporte a consultas de rotulagem.

## <a name="what-are-query-labels"></a>O que são rótulos de consulta
O pool SQL dá suporte a um conceito chamado rótulos de consulta. Antes de entrar em qualquer profundidade, vamos examinar um exemplo:

```sql
SELECT *
FROM sys.tables
OPTION (LABEL = 'My Query Label')
;
```

A última linha marca a cadeia de caracteres ‘Meu Rótulo de Consulta’ à consulta. Essa marca é útil porque o rótulo é habilitado para consulta por meio de DMVs. Consultar rótulos fornece um mecanismo para localizar consultas de problemas e ajuda a identificar o progresso por meio de uma execução de ELT.

Boas convenções de nomenclatura são mais úteis. Por exemplo, iniciar o rótulo com projeto, procedimento, instrução ou comentário identifica exclusivamente a consulta entre todo o código no controle do código-fonte.

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


