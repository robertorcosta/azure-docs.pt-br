---
title: Alterar fluxos na API do Azure Cosmos DB para MongoDB
description: Saiba como usar os fluxos de alteração na API do Azure Cosmos DB para o MongoDB para obter as alterações feitas em seus dados.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: tisande
ms.openlocfilehash: ecfa98241f74aac43a827b645a6ed877624d643d
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437816"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Alterar fluxos na API do Azure Cosmos DB para MongoDB

O suporte de feed de [alterações](change-feed.md) na API do Azure Cosmos DB para MongoDB está disponível usando a API de fluxos de alteração. Usando a API de fluxos de alteração, seus aplicativos podem obter as alterações feitas na coleção ou nos itens em um único fragmento. Mais tarde você pode tomar outras ações com base nos resultados. As alterações nos itens da coleção são capturadas na ordem do tempo de modificação e a ordem de classificação é garantida por chave de fragmento.

> [!NOTE]
> Para usar fluxos de alteração, crie a conta com a versão 3.6 da API do Azure Cosmos DB para MongoDB, ou uma versão posterior. Se você executar os exemplos de fluxo de alteração `Unrecognized pipeline stage name: $changeStream` contra uma versão anterior, você pode ver o erro.

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações são aplicáveis ao usar fluxos de alteração:

* As `operationType` `updateDescription` propriedades e propriedades ainda não são suportadas no documento de saída.
* Os `insert` `update`tipos `replace` de operações e operações são suportados atualmente. A operação de exclusão ou outros eventos ainda não são suportados.

Devido a essas limitações, as opções $match, $project e fullDocument são necessárias, como mostrado nos exemplos anteriores.

Ao contrário do feed de alterações na API SQL do Azure Cosmos DB, não há uma Biblioteca de [Processador de Ração](change-feed-processor.md) de Ração separada para consumir fluxos de alteração ou a necessidade de um contêiner de arrendamentos. No momento, não há suporte para [acionadores de funções do Azure](change-feed-functions.md) para processar fluxos de alteração.

## <a name="error-handling"></a>Tratamento de erros

Os seguintes códigos de erro e mensagens são suportados ao usar fluxos de alteração:

* **Código de erro HTTP 16500** - Quando o fluxo de alteração é estrangulado, ele retorna uma página vazia.

* **NamespaceNotFound (OperationType Invalid)** - Se você executar o fluxo de alteração na coleção `NamespaceNotFound` que não existe ou se a coleção for descartada, então um erro será devolvido. Como `operationType` a propriedade não pode ser devolvida no documento `operationType Invalidate` de `NamespaceNotFound` saída, em vez do erro, o erro é devolvido.

## <a name="examples"></a>Exemplos

O exemplo a seguir mostra como obter fluxos de alteração em todos os itens da coleção. Este exemplo cria um cursor para observar itens quando eles são inseridos, atualizados ou substituídos. O `$match` palco, `$project` o `fullDocument` palco e a opção são necessários para obter os fluxos de mudança. Atualmente, não é suportado o uso de operações de exclusão usando fluxos de alteração. Como solução de solução, você pode adicionar um marcador macio nos itens que estão sendo excluídos. Por exemplo, você pode adicionar um atributo no item chamado "excluído". Quando você quiser excluir o item, você pode definir `true` "excluído" e definir um TTL no item. Uma vez que a `true` atualização "excluída" é uma atualização, essa alteração será visível no fluxo de alterações.

### <a name="javascript"></a>JavaScript:

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

### <a name="c"></a>C#:

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

## <a name="changes-within-a-single-shard"></a>Mudanças dentro de um único fragmento

O exemplo a seguir mostra como obter alterações nos itens dentro de um único fragmento. Este exemplo obtém as alterações de itens que têm chave de fragmento igual a "a" e o valor da chave de fragmento igual a "1". É possível ter diferentes clientes lendo mudanças de diferentes fragmentos em paralelo.

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

* [Use o tempo para viver para expirar dados automaticamente na API do Azure Cosmos DB para MongoDB](mongodb-time-to-live.md)
* [Indexação na API do Azure Cosmos DB para MongoDB](mongodb-indexing.md)
