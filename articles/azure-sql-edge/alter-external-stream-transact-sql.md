---
title: ALTER EXTERNAL STREAM (Transact-SQL) - SQL do Azure no Edge (Versão Prévia)
description: Saiba mais sobre a instrução ALTER EXTERNAL STREAM no SQL do Azure no Edge (Versão Prévia)
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 2559c4b4b875403b7c70671e27cb6222a3f1103a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84235206"
---
# <a name="alter-external-stream-transact-sql"></a>ALTER EXTERNAL STREAM (Transact-SQL)

Modifica a definição de um Fluxo Externo. Não é permitido modificar um Fluxo Externo que é usado por um trabalho de fluxo no estado *Em execução*. 



## <a name="syntax"></a>Sintaxe

```sql
  ALTER EXTERNAL STREAM external_stream_name 
  SET 
    [DATA_SOURCE] = <data_source_name> 
    , LOCATION = <location_name> 
    , EXTERNAL_FILE_FORMAT = <external_file_format_name> 
    , INPUT_OPTIONS = <input_options> 
    , OUTPUT_OPTIONS = <output_options> 
```

## <a name="arguments"></a>Argumentos

Para obter detalhes sobre os argumentos do comando Alter External Stream, consulte [CREATE EXTERNAL STREAM (Transact-SQL)](create-external-stream-transact-sql.md).

## <a name="return-code-values"></a>Valores do código de retorno

ALTER EXTERNAL STREAM retorna 0 se é bem-sucedido. Um valor de retorno diferente de zero indica falha.


## <a name="see-also"></a>Confira também

- [CREATE EXTERNAL STREAM (Transact-SQL)](create-external-stream-transact-sql.md) 
- [DROP EXTERNAL STREAM (Transact-SQL)](drop-external-stream-transact-sql.md) 
