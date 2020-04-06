---
title: Trabalhando com datas no Azure Cosmos DB
description: Saiba como armazenar, indexar e consultar objetos DataTime no Azure Cosmos DB
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 04/03/2020
ms.openlocfilehash: 174279e4bd241ee9b336fc1ce7e0af389d2297a3
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/05/2020
ms.locfileid: "80666998"
---
# <a name="working-with-dates-in-azure-cosmos-db"></a>Trabalhando com datas no Azure Cosmos DB

O Azure Cosmos DB fornece flexibilidade de esquema e indexação avançada por meio de um modelo de dados [JSON](https://www.json.org) nativo. Todos os recursos do Azure Cosmos DB, incluindo bancos de dados, contêineres, documentos e procedimentos armazenados são modelados e armazenados como documentos JSON. Como um requisito para ser portátil, o JSON (e o Azure Cosmos DB) dá suporte apenas a um conjunto pequeno de tipos básicos: Cadeia de caracteres, Número, Booliano, Matriz, Objeto e Nulo. No entanto, o JSON é flexível e permite aos desenvolvedores e estruturas representar tipos mais complexos usando esses primitivos e compondo-os como objetos ou matrizes.

Além dos tipos básicos, muitos aplicativos precisam do tipo DateTime para representar datas e carimbos de data/hora. Este artigo descreve como os desenvolvedores podem armazenar, recuperar e consultar datas no Azure Cosmos DB usando o SDK do .NET.

## <a name="storing-datetimes"></a>Armazenando DateTimes

O Azure Cosmos DB suporta tipos JSON como - string, number, booleano, null, array, object. Ele não suporta diretamente um tipo DateTime. Atualmente, o Azure Cosmos DB não suporta a localização de datas. Então, você precisa armazenar DateTimes como strings. O formato recomendado para strings DateTime no Azure Cosmos DB é `YYYY-MM-DDThh:mm:ss.fffffffZ` o que segue o padrão ISO 8601 UTC. Recomenda-se armazenar todas as datas no Azure Cosmos DB como UTC. A conversão das seqüências de datas para este formato permitirá classificar as datas lexicograficamente. Se as datas não UTC forem armazenadas, a lógica deve ser tratada no lado do cliente. Para converter um DateTime local em UTC, o deslocamento deve ser conhecido/armazenado como uma propriedade no JSON e o cliente pode usar a compensação para calcular o valor UTC DateTime.

As consultas de intervalo com as seqüências DateTime como filtros só são suportadas se as seqüências DateTime estiverem todas em UTC e no mesmo comprimento. No Azure Cosmos DB, a função [getcurrentdatetime](sql-query-getcurrentdatetime.md) do sistema retornará a data e a `YYYY-MM-DDThh:mm:ss.fffffffZ`hora atual do valor da seqüência ISO 8601 no formato: .

A maioria dos aplicativos pode usar a representação de cadeia de caracteres padrão para DateTime pelos seguintes motivos:

* As cadeias de caracteres podem ser comparadas e a ordenação relativa dos valores de DateTime é preservada quando eles são transformados em cadeias de caracteres.
* Essa abordagem não exige qualquer código personalizado ou atributos para conversão de JSON.
* As datas, conforme armazenadas no JSON, são legíveis para humanos.
* Essa abordagem pode aproveitar o índice do Azure Cosmos DB para um desempenho rápido de consulta.

Por exemplo, o snippet a seguir armazena um objeto `Order` que contém duas propriedades DateTime — `ShipDate` e `OrderDate` como um documento usando o SDK do .NET:

```csharp
    public class Order
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
        public DateTime OrderDate { get; set; }
        public DateTime ShipDate { get; set; }
        public double Total { get; set; }
    }

    await container.CreateItemAsync(
        new Order
        {
            Id = "09152014101",
            OrderDate = DateTime.UtcNow.AddDays(-30),
            ShipDate = DateTime.UtcNow.AddDays(-14),
            Total = 113.39
        });
```

Esse documento é armazenado no Azure Cosmos DB da seguinte maneira:

```json
    {
        "id": "09152014101",
        "OrderDate": "2014-09-15T23:14:25.7251173Z",
        "ShipDate": "2014-09-30T23:14:25.7251173Z",
        "Total": 113.39
    }
```  

Como alternativa, você pode armazenar DateTimes como carimbos de data/hora do Unix, ou seja, como um número que representa o número de segundos decorridos desde 1º de janeiro de 1970. A propriedade Timestamp (`_ts`) interna do Cosmos DB segue essa abordagem. Você pode usar a classe [UnixDateTimeConverter](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.unixdatetimeconverter.aspx) para serializar DateTimes como números.

## <a name="querying-datetimes-in-linq"></a>Consultando DateTimes no LINQ

O SDK do .NET para SQL dá suporte automaticamente à consulta de dados armazenados no Azure Cosmos DB via LINQ. Por exemplo, o seguinte trecho mostra uma consulta LINQ que filtra pedidos enviados nos últimos três dias:

```csharp
    IQueryable<Order> orders = container.GetItemLinqQueryable<Order>(allowSynchronousQueryExecution: true).Where(o => o.ShipDate >= DateTime.UtcNow.AddDays(-3));
```

Traduzido para a seguinte declaração SQL e executado no Azure Cosmos DB:

```sql
    SELECT * FROM root WHERE (root["ShipDate"] >= "2014-09-30T23:14:25.7251173Z")
```

Você pode aprender mais sobre a linguagem de consulta SQL do Azure Cosmos DB e o provedor LINQ no [Querying Cosmos DB em LINQ](sql-query-linq-to-sql.md).

## <a name="indexing-datetimes-for-range-queries"></a>Indexando DateTimes para consultas de intervalo

Consultas são comuns com valores de DateTime. Para executar essas consultas de forma eficiente, você deve ter um índice definido em quaisquer propriedades no filtro da consulta.

Saiba mais sobre como configurar as políticas de indexação em [Políticas de indexação do Azure Cosmos DB](index-policy.md). 

## <a name="next-steps"></a>Próximas etapas

* Baixe e execute os [Exemplos de código no GitHub](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples)
* Saiba mais sobre [consultas SQL](sql-query-getting-started.md)
* Saiba mais sobre as [Políticas de indexação do Azure Cosmos DB](index-policy.md)
