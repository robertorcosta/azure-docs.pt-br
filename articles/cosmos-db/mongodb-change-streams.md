---
title: Alterar fluxos na API do Azure Cosmos DB para MongoDB
description: Saiba como usar fluxos de alteração na API do Azure Cosmos DB para MongoDB para obter as alterações feitas nos seus dados.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: 7a6060448175530ada5ba95ceda470056a7be002
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872146"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Alterar fluxos na API do Azure Cosmos DB para MongoDB

O suporte do [feed de alterações](change-feed.md) na api do Azure Cosmos DB para MongoDB está disponível usando a API de fluxos de alteração. Usando a API de fluxos de alteração, seus aplicativos podem obter as alterações feitas na coleção ou nos itens em um único fragmento. Posteriormente, você pode executar ações adicionais com base nos resultados. As alterações nos itens na coleção são capturadas na ordem de seu tempo de modificação e a ordem de classificação é garantida por chave de fragmentação.

> [!NOTE]
> Para usar os fluxos de alteração, crie a conta com a versão 3,6 da API do Azure Cosmos DB para MongoDB ou uma versão posterior. Se você executar os exemplos de fluxo de alterações em uma versão anterior, poderá ver `Unrecognized pipeline stage name: $changeStream` o erro.

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações são aplicáveis ao usar fluxos de alteração:

* As `operationType` propriedades `updateDescription` e ainda não têm suporte no documento de saída.
* Atualmente `insert`, `update`há suporte `replace` para os tipos de operações, e. 
* Ainda não há suporte para a operação de exclusão ou outros eventos.

Devido a essas limitações, as opções estágio de $match, estágio de $project e fullDocument são necessárias, conforme mostrado nos exemplos anteriores.

Ao contrário do feed de alterações na API do SQL Azure Cosmos DB, não há uma [biblioteca](change-feed-processor.md) separada do processador do feed de alterações para consumir fluxos de alteração ou uma necessidade de um contêiner de concessões. Atualmente, não há suporte para [gatilhos Azure Functions](change-feed-functions.md) para processar fluxos de alteração.

## <a name="error-handling"></a>Tratamento de erros

Há suporte para os seguintes códigos de erro e mensagens ao usar fluxos de alteração:

* **Código de erro HTTP 16500** -quando o fluxo de alteração é limitado, ele retorna uma página vazia.

* **NamespaceNotFound (OperationType Invalidate)** – se você executar o fluxo de alterações na coleção que não existe ou se a coleção for descartada, `NamespaceNotFound` um erro será retornado. Como a `operationType` propriedade não pode ser retornada no documento de saída, em vez `operationType Invalidate` do erro, `NamespaceNotFound` o erro é retornado.

## <a name="examples"></a>Exemplos

O exemplo a seguir mostra como obter fluxos de alteração em todos os itens na coleção. Este exemplo cria um cursor para observar itens quando eles são inseridos, atualizados ou substituídos. O `$match` estágio, `$project` estágio e `fullDocument` opção são necessários para obter os fluxos de alteração. Não há suporte para a observação de operações de exclusão usando fluxos de alteração no momento. Como alternativa, você pode adicionar um marcador flexível nos itens que estão sendo excluídos. Por exemplo, você pode adicionar um atributo no item chamado "excluído". Quando você quiser excluir o item, poderá definir "Deleted" como `true` e definir um TTL no item. Como atualizar "Deleted" para `true` é uma atualização, essa alteração será visível no fluxo de alteração.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
var cursor = db.coll.watch(
    [
        { $match: { "operationType": { $in: ["insert", "update", "replace"] } } },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1 } }
    ],
    { fullDocument: "updateLookup" });

while (!cursor.isExhausted()) {
    if (cursor.hasNext()) {
        printjson(cursor.next());
    }
}
```
# <a name="c"></a>[C#](#tab/csharp)

```csharp
var pipeline = new EmptyPipelineDefinition<ChangeStreamDocument<BsonDocument>>()
    .Match(change => change.OperationType == ChangeStreamOperationType.Insert || change.OperationType == ChangeStreamOperationType.Update || change.OperationType == ChangeStreamOperationType.Replace)
    .AppendStage<ChangeStreamDocument<BsonDocument>, ChangeStreamDocument<BsonDocument>, BsonDocument>(
    "{ $project: { '_id': 1, 'fullDocument': 1, 'ns': 1, 'documentKey': 1 }}");

var options = new ChangeStreamOptions{
        FullDocument = ChangeStreamFullDocumentOption.UpdateLookup
    };

var enumerator = coll.Watch(pipeline, options).ToEnumerable().GetEnumerator();

while (enumerator.MoveNext()){
        Console.WriteLine(enumerator.Current);
    }

enumerator.Dispose();
```

## <a name="changes-within-a-single-shard"></a>Alterações em um único fragmento

O exemplo a seguir mostra como obter alterações nos itens em um único fragmento. Este exemplo obtém as alterações de itens que têm a chave de fragmentação igual a "a" e o valor de chave de fragmento igual a "1". É possível ter diferentes clientes lendo alterações de fragmentos diferentes em paralelo.

```javascript
var cursor = db.coll.watch(
    [
        {
            $match: {
                $and: [
                    { "fullDocument.a": 1 }, 
                    { "operationType": { $in: ["insert", "update", "replace"] } }
                ]
            }
        },
        { $project: { "_id": 1, "fullDocument": 1, "ns": 1, "documentKey": 1} }
    ],
    { fullDocument: "updateLookup" });

```

## <a name="next-steps"></a>Próximas etapas

* [Use a vida útil para expirar os dados automaticamente na API do Azure Cosmos DB para MongoDB](mongodb-time-to-live.md)
* [Indexação na API do Azure Cosmos DB para MongoDB](mongodb-indexing.md)
