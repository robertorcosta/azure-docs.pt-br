---
title: Funções de data e hora na linguagem de consulta Azure Cosmos DB
description: Saiba mais sobre as funções do sistema SQL de data e hora no Azure Cosmos DB para executar as operações DateTime e timestamp.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 055fd5182920ebe15cb2f3ed4b7a8ff69bb94c16
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85549508"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Funções de data e hora (Azure Cosmos DB)

As funções de data e hora permitem que você execute as operações DateTime e timestamp em Azure Cosmos DB.

## <a name="functions"></a>Funções

As seguintes funções escalares permitem obter a data e a hora UTC atuais em duas formas; um carimbo de data/hora numérico cujo valor é a época do UNIX em milissegundos ou como uma cadeia de caracteres que está de acordo com o formato ISO 8601:

* [GetCurrentDateTime](sql-query-getcurrentdatetime.md)
* [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)

## <a name="next-steps"></a>Próximas etapas

- [Funções de sistema do Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções definidas pelo usuário](sql-query-udfs.md)
- [Agregações](sql-query-aggregates.md)
