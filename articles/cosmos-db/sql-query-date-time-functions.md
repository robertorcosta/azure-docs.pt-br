---
title: Funções de data e hora no idioma de consulta do Azure Cosmos DB
description: Saiba mais sobre as funções do sistema SQL de data e hora no Azure Cosmos DB para executar operações de DataHora e carimbo de hora.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 93b3e90deb5e3764cb5402938c5d0e14cadb7883
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873379"
---
# <a name="date-and-time-functions-azure-cosmos-db"></a>Funções de data e hora (Azure Cosmos DB)

As funções de data e hora permitem que você execute as operações DateTime e timestamp no Azure Cosmos DB.

## <a name="functions"></a>Funções

As seguintes funções escalares permitem obter a data e a hora atuals do UTC em duas formas; um carimbo de tempo numérico cujo valor é a época unix em milissegundos ou como uma string que está em conformidade com o formato ISO 8601:

|||
|-|-|
|[GetCurrentDateTime](sql-query-getcurrentdatetime.md)|[GetCurrentTimestamp](sql-query-getcurrenttimestamp.md)||


## <a name="next-steps"></a>Próximas etapas

- [Funcionamento do sistema Azure Cosmos DB](sql-query-system-functions.md)
- [Introdução ao Azure Cosmos DB](introduction.md)
- [Funções definidas pelo usuário](sql-query-udfs.md)
- [Agregações](sql-query-aggregates.md)
