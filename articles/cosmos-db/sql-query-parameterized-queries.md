---
title: Consultas parametrizadas no Azure Cosmos DB
description: Saiba como as consultas parametrizadas do SQL fornecem um manuseio robusto e escapa da entrada do usuário e evitam a exposição acidental de dados através da injeção SQL.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870812"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Consultas parametrizadas no Azure Cosmos DB

Cosmos DB suporta consultas com parâmetros expressos pela notação @familiar. O SQL parametrizado proporciona um manuseio robusto e escapa da entrada do usuário, e evita a exposição acidental de dados através da injeção SQL.

## <a name="examples"></a>Exemplos

Por exemplo, você pode escrever `lastName` uma `address.state` consulta que toma e como `lastName` `address.state` parâmetros, e executá-la por vários valores e com base na entrada do usuário.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

Em seguida, você pode enviar esta solicitação para o Cosmos DB como uma consulta JSON parametrizada como a seguinte:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

O exemplo a seguir define o argumento TOP com uma consulta parametrizada: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Os valores dos parâmetros podem ser qualquer JSON válido: strings, números, Booleans, nulos, até mesmo matrizes ou JSON aninhado. Como o Cosmos DB não tem esquema, os parâmetros não são validados contra nenhum tipo.


## <a name="next-steps"></a>Próximas etapas

- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Dados de documento modelo](modeling-data.md)
