---
title: Funções de data e hora na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre as funções do sistema SQL de data e hora no Azure Cosmos DB para executar as operações DateTime e timestamp.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/09/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 460abbc0b2a2f277aaeed57c5b938de530696776
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/14/2020
ms.locfileid: "88224944"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Funções de data e hora (Azure Cosmos DB)

As funções de data e hora permitem que você execute as operações DateTime e timestamp em Azure Cosmos DB.

## <a name="functions-to-obtain-the-date-and-time"></a>Funções para obter a data e a hora

As seguintes funções escalares permitem obter a data e a hora UTC atuais em duas formas: uma cadeia de caracteres que está em conformidade com o formato ISO 8601 ou um carimbo de data/hora numérico cujo valor é a época do UNIX em milissegundos:

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)
* [GetCurrentTicks](sql-query-getcurrentticks.md)

## <a name="functions-to-work-with-datetime-values"></a>Funções para trabalhar com valores DateTime

As funções a seguir permitem que você manipule facilmente os valores DateTime:

* [DateTimeAdd](sql-query-datetimeadd.md)
* [DateTimeDiff](sql-query-datetimediff.md)
* [DateTimeFromParts](sql-query-datetimefromparts.md)
* [DateTimePart](sql-query-datetimepart.md)
* [DateTimeToTicks](sql-query-datetimetoticks.md)

## <a name="next-steps"></a>Próximas etapas

- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções definidas pelo usuário](sql-query-udfs.md)
- [Agregações](sql-query-aggregates.md)
