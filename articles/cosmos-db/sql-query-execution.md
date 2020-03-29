---
title: Execução de consulta SQL no Azure Cosmos DB
description: Aprenda a criar uma consulta SQL e execute-a no Azure Cosmos DB. Este artigo descreve como criar e executar uma consulta SQL usando a API REST, .Net SDK, JavaScript SDK e vários outros SDKs.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 70eb81b6d13c57a7ebc131244c7aa318cb2b2fd4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74871254"
---
# <a name="azure-cosmos-db-sql-query-execution"></a>Azure Cosmos DB SQL query execução

Qualquer idioma capaz de fazer solicitações HTTP/HTTPS pode chamar a API Cosmos DB REST. O Cosmos DB também oferece bibliotecas de programação para linguagens de programação .NET, Node.js, JavaScript e Python. A API REST e as bibliotecas suportam consultas através do SQL, e o .NET SDK também suporta [consulta linq](sql-query-linq-to-sql.md).

Os exemplos a seguir mostram como criar uma consulta e enviá-la contra uma conta de banco de dados Cosmos.

## <a name="rest-api"></a><a id="REST-API"></a>REST API

O Cosmos DB oferece um modelo de programação RESTful por HTTP. O modelo de recursos consiste em um conjunto de recursos em uma conta de banco de dados, que uma assinatura do Azure prospassa. A conta do banco de dados consiste em um conjunto de *bancos*de dados , cada um dos quais pode conter vários *contêineres*, que por sua vez contêm *itens,* UDFs e outros tipos de recursos. Cada recurso do Cosmos DB é endereçado usando um URI lógico e estável. Um conjunto de recursos é chamado de *feed*. 

O modelo básico de interação com esses `GET`recursos `PUT` `POST`é `DELETE`através dos verbos HTTP, e , com suas interpretações padrão. Use `POST` para criar um novo recurso, executar um procedimento armazenado ou emitir uma consulta do Cosmos DB. As consultas sempre são operações somente leitura, sem efeitos colaterais.

Os exemplos a `POST` seguir mostram a para uma consulta de API SQL contra os itens de amostra. A consulta tem um filtro simples `name` na propriedade JSON. O `x-ms-documentdb-isquery` e Tipo `application/query+json` de Conteúdo: os cabeçalhos denotam que a operação é uma consulta. Substitua pelo `mysqlapicosmosdb.documents.azure.com:443` URI para sua conta Cosmos DB.

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

A próxima consulta mais complexa retorna vários resultados de uma adesão:

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

Se os resultados de uma consulta não puderem caber em uma única página, `x-ms-continuation-token` a API REST retorna um token de continuação através do cabeçalho de resposta. Os clientes podem paginar os resultados incluindo o cabeçalho nos resultados subsequentes. Você também pode controlar o número `x-ms-max-item-count` de resultados por página através do cabeçalho numérdia.

Se uma consulta tiver uma função de agregação como COUNT, a página de consulta poderá retornar um valor parcialmente agregado em apenas uma página de resultados. Os clientes devem realizar uma agregação de segundo nível sobre esses resultados para produzir os resultados finais. Por exemplo, soma sobre as contagens retornadas nas páginas individuais para retornar a contagem total.

Para gerenciar a política de consistência de `x-ms-consistency-level` dados para consultas, use o cabeçalho como em todas as solicitações de API REST. A consistência da sessão também `x-ms-session-token` requer ecoar o cabeçalho de cookies mais recente na solicitação de consulta. A política de indexação do contêiner consultado também pode influenciar a consistência dos resultados da consulta. Com as configurações de política de indexação padrão para contêineres, o índice está sempre atualizado com o conteúdo do item, e os resultados da consulta correspondem à consistência escolhida para os dados. Para obter mais informações, consulte [níveis de consistência do Azure Cosmos DB][níveis de consistência].

Se a política de indexação configurada no contêiner não puder suportar a consulta especificada, o servidor Azure Cosmos DB reah3 400 "Má solicitação". Esta mensagem de erro retorna para consultas com caminhos explicitamente excluídos da indexação. Você pode `x-ms-documentdb-query-enable-scan` especificar o cabeçalho para permitir que a consulta execute uma varredura quando um índice não estiver disponível.

Você pode obter métricas detalhadas na `x-ms-documentdb-populatequerymetrics` execução da consulta definindo o cabeçalho para `true`. Para obter mais informações, consulte [Métricas de consulta SQL para o Azure Cosmos DB](sql-api-query-metrics.md).

## <a name="c-net-sdk"></a>C# (.NET SDK)

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

O exemplo a seguir compara duas propriedades para igualdade dentro de cada item e usa projeções anônimas.

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

O próximo exemplo mostra junções, expressas através do LINQ `SelectMany`.

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

O cliente .NET itera automaticamente através de todas as `foreach` páginas de consulta os resultados dos blocos, como mostrado no exemplo anterior. As opções de consulta introduzidas na seção [Rest API](#REST-API) também `FeedOptions` estão `FeedResponse` disponíveis `CreateDocumentQuery` no .NET SDK, usando as classes e classes no método. Você pode controlar o número `MaxItemCount` de páginas usando a configuração.

Você também pode controlar explicitamente `IDocumentQueryable` a `IQueryable` paginação criando `ResponseContinuationToken` usando o objeto, `RequestContinuationToken` `FeedOptions`em seguida, lendo os valores e passando-os de volta como em . Você pode `EnableScanInQuery` configurar para ativar varreduras quando a consulta não for suportada pela diretiva de indexação configurada. Para contêineres particionados, você pode usar `PartitionKey` para executar a consulta contra uma única partição, embora o Azure Cosmos DB possa extrair isso automaticamente do texto de consulta. Você pode `EnableCrossPartitionQuery` usar para executar consultas contra várias partições.

Para obter mais amostras .NET com consultas, consulte as [amostras Azure Cosmos DB .NET](https://github.com/Azure/azure-cosmos-dotnet-v3) no GitHub.

## <a name="javascript-server-side-api"></a><a id="JavaScript-server-side-API"></a>API do lado servidor do JavaScript

O Azure Cosmos DB fornece um modelo de programação para executar a lógica [de aplicativos baseada em JavaScript](stored-procedures-triggers-udfs.md) diretamente nos contêineres, usando procedimentos e gatilhos armazenados. A lógica JavaScript registrada no nível do contêiner pode então emitir operações de banco de dados nos itens do determinado contêiner, envoltos em transações ACID ambiente.

O exemplo a seguir `queryDocuments` mostra como usar na API do servidor JavaScript para fazer consultas de dentro de procedimentos e gatilhos armazenados:

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
