---
title: Usar rótulos de consulta no SQL Synapse
description: Estão incluídos neste artigo dicas essenciais para usar rótulos de consulta no SQL Synapse.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "81430026"
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

A última linha marca a cadeia de caracteres ‘Meu Rótulo de Consulta’ à consulta. Essa marca é particularmente útil, pois o rótulo é capaz de executar consultas por meio de DMVs. Consultar rótulos fornece um mecanismo para localizar consultas de problemas e ajuda a identificar o progresso por meio de uma execução de ELT.

Boa Convenção de nomenclatura é mais útil. Por exemplo, iniciar o rótulo com projeto, procedimento, instrução ou comentário identifica exclusivamente a consulta entre todo o código no controle do código-fonte.

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


