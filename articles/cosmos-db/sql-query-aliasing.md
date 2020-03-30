---
title: Aliasing em Azure Cosmos DB
description: Aprenda a usar aliasing em consultas Azure Cosmos DB SQL para diferenciar duas propriedades com o mesmo nome
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873464"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Aliasing em Azure Cosmos DB

Você pode explicitamente alias valores em consultas. Se uma consulta tiver duas propriedades com o mesmo nome, use aliasing para renomear uma ou ambas as propriedades para que elas sejam desamparadas no resultado projetado.

## <a name="examples"></a>Exemplos

A palavra-chave AS usada para aliasing é opcional, como mostrado `NameInfo`no exemplo a seguir ao projetar o segundo valor como :

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Os resultados são:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>Próximas etapas

- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Cláusula SELECT](sql-query-select.md)
- [Cláusula de cláusula](sql-query-from.md)
