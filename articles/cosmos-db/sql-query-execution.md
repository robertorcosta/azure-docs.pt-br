---
title: Execução de consulta SQL no Azure Cosmos DB
description: Saiba como criar uma consulta SQL e executá-la no Azure Cosmos DB. Este artigo descreve como criar e executar uma consulta SQL usando a API REST, o SDK do .net, o SDK do JavaScript e vários outros SDKs.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "74871254"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Execução de consulta do Azure Cosmos DB SQL

Qualquer linguagem capaz de fazer solicitações HTTP/HTTPS pode chamar a API REST do Cosmos DB. O Cosmos DB também oferece bibliotecas de programação para .NET, Node.js, JavaScript e linguagens de programação Python. A API REST e as bibliotecas oferecem suporte à consulta por meio do SQL, e o SDK do .NET também dá suporte à [consulta LINQ](sql-query-linq-to-sql.md).

Os exemplos a seguir mostram como criar uma consulta e enviá-la a uma conta de banco de dados Cosmos.

## <a name="rest-api"></a><a id="REST-API"></a>API REST

O Cosmos DB oferece um modelo de programação RESTful por HTTP. O modelo de recurso consiste em um conjunto de recursos em uma conta de banco de dados, que é provisionada por uma assinatura do Azure. A conta do banco de dados consiste em um conjunto de *bancos*de dados, cada um deles pode conter vários *contêineres*que, por sua vez, contêm *itens*, UDFs e outros tipos de recursos. Cada recurso de Cosmos DB é endereçável usando um URI lógico e estável. Um conjunto de recursos é chamado de *feed*. 

O modelo de interação básica com esses recursos é por meio dos verbos HTTP `GET` , `PUT` , `POST` e `DELETE` , com suas interpretações padrão. Use `POST` para criar um novo recurso, executar um procedimento armazenado ou emitir uma consulta Cosmos DB. As consultas sempre são operações somente leitura, sem efeitos colaterais.

Os exemplos a seguir mostram um `POST` para uma consulta de API do SQL em relação aos itens de exemplo. A consulta tem um filtro simples na propriedade JSON `name` . Os `x-ms-documentdb-isquery` cabeçalhos e Content-Type: `application/query+json` indicam que a operação é uma consulta. Substitua `mysqlapicosmosdb.documents.azure.com:443` pelo URI da sua conta de Cosmos DB.

```json
    POST https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",
        "parameters": [
            {"name": "@familyId", "value": "AndersenFamily"}
        ]
    }
```

Os resultados são:

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"Seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }
```

A próxima consulta mais complexa retorna vários resultados de uma junção:

```json
    POST https://https://mysqlapicosmosdb.documents.azure.com:443/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {
        "query": "SELECT
                     f.id AS familyName,
                     c.givenName AS childGivenName,
                     c.firstName AS childFirstName,
                     p.givenName AS petName
                  FROM Families f
                  JOIN c IN f.children
                  JOIN p in c.pets",
        "parameters": []
    }
```

Os resultados são: 

```json
    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }
```

Se os resultados de uma consulta não couberem em uma única página, a API REST retornará um token de continuação por meio do `x-ms-continuation-token` cabeçalho de resposta. Os clientes podem paginar os resultados incluindo o cabeçalho nos resultados subsequentes. Você também pode controlar o número de resultados por página por meio do `x-ms-max-item-count` cabeçalho de número.

Se uma consulta tiver uma função de agregação como contagem, a página consulta poderá retornar um valor parcialmente agregado em apenas uma página de resultados. Os clientes devem executar uma agregação de segundo nível sobre esses resultados para produzir os resultados finais. Por exemplo, somar as contagens retornadas nas páginas individuais para retornar a contagem total.

Para gerenciar a política de consistência de dados para consultas, use o `x-ms-consistency-level` cabeçalho como em todas as solicitações da API REST. A consistência da sessão também requer o eco do cabeçalho do cookie mais recente `x-ms-session-token` na solicitação de consulta. A política de indexação do contêiner consultado também pode influenciar a consistência dos resultados da consulta. Com as configurações de política de indexação padrão para contêineres, o índice é sempre atual com o conteúdo do item e os resultados da consulta correspondem à consistência escolhida para os dados. Para obter mais informações, consulte [Azure Cosmos DB níveis de consistência] [níveis de consistência].

Se a política de indexação configurada no contêiner não puder dar suporte à consulta especificada, o servidor de Azure Cosmos DB retornará 400 "solicitação incorreta". Essa mensagem de erro retorna para consultas com caminhos explicitamente excluídos da indexação. Você pode especificar o `x-ms-documentdb-query-enable-scan` cabeçalho para permitir que a consulta execute uma verificação quando um índice não estiver disponível.

Você pode obter métricas detalhadas na execução da consulta definindo o `x-ms-documentdb-populatequerymetrics` cabeçalho como `true` . Para obter mais informações, consulte [Métricas de consulta SQL para o Azure Cosmos DB](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (SDK DO .NET)

O SDK .NET suporta consultas LINQ e SQL. O exemplo a seguir mostra como executar a consulta de filtro anterior com .NET:

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(containerLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(containerLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(containerLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

O exemplo a seguir compara duas propriedades de igualdade dentro de cada item e usa projeções anônimas.

```csharp
    foreach (var family in client.CreateDocumentQuery(containerLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family
        FROM Families f
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(containerLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(containerLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }
```

O exemplo a seguir mostra junções, expressas por meio do LINQ `SelectMany` .

```csharp
    foreach (var pet in client.CreateDocumentQuery(containerLink,
          @"SELECT p
            FROM Families f
                 JOIN c IN f.children
                 JOIN p in c.pets
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions:
    foreach (var pet in
        client.CreateDocumentQuery<Family>(containerLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }
```

O cliente .NET itera automaticamente em todas as páginas de resultados da consulta nos `foreach` blocos, conforme mostrado no exemplo anterior. As opções de consulta introduzidas na seção da [API REST](#REST-API) também estão disponíveis no SDK do .net, usando as `FeedOptions` `FeedResponse` classes e no `CreateDocumentQuery` método. Você pode controlar o número de páginas usando a `MaxItemCount` configuração.

Você também pode controlar explicitamente a paginação criando `IDocumentQueryable` usando o `IQueryable` objeto, lendo os `ResponseContinuationToken` valores e passando-os de volta como `RequestContinuationToken` em `FeedOptions` . Você pode definir `EnableScanInQuery` para habilitar verificações quando a política de indexação configurada não oferecer suporte à consulta. Para contêineres particionados, você pode usar `PartitionKey` o para executar a consulta em uma única partição, embora Azure Cosmos DB possa extraí-la automaticamente do texto da consulta. Você pode usar o `EnableCrossPartitionQuery` para executar consultas em várias partições.

Para obter mais amostras do .NET com consultas, consulte os [exemplos do Azure Cosmos DB .net](https://github.com/Azure/azure-cosmos-dotnet-v3) no github.

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>API do lado servidor do JavaScript

O Azure Cosmos DB fornece um modelo de programação para executar a lógica de [aplicativo baseada em JavaScript](stored-procedures-triggers-udfs.md) diretamente em contêineres, usando procedimentos armazenados e gatilhos. A lógica JavaScript registrada no nível de contêiner pode emitir operações de banco de dados nos itens do contêiner fornecido, encapsulado em transações ACID ambiente.

O exemplo a seguir mostra como usar `queryDocuments` na API do servidor JavaScript para fazer consultas de dentro de procedimentos armazenados e gatilhos:

```javascript
    function findName(givenName, familyName) {
        var context = getContext();
        var containerManager = context.getCollection();
        var containerLink = containerManager.getSelfLink()

        // create a new item.
        containerManager.createDocument(containerLink,
            { givenName: givenName, familyName: familyName },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter items by familyName
                var filterQuery = "SELECT * from root r WHERE r.familyName = 'Wakefield'";
                containerManager.queryDocuments(containerLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the familyName for all items that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].familyName = "Robin Wakefield";
                            // we don't need to execute a callback because they are in parallel
                            containerManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }
```

## <a name="next-steps"></a>Próximas etapas

- [Introdução ao Azure Cosmos DB](introduction.md)
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Níveis de consistência do Azure Cosmos DB](consistency-levels.md)
