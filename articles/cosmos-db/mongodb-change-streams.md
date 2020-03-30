---
title: Alterar fluxos na API do Azure Cosmos DB para MongoDB
description: Saiba como usar os fluxos de alteração n API do Azure Cosmos DB para o MongoDB para obter as alterações feitas em seus dados.
author: srchi
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 11/16/2019
ms.author: srchi
ms.openlocfilehash: ec1ec1a8a80953f8988355341ee7128bd29b982d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "77467770"
---
# <a name="change-streams-in-azure-cosmos-dbs-api-for-mongodb"></a>Alterar fluxos na API do Azure Cosmos DB para MongoDB

O suporte de feed de [alterações](change-feed.md) na API do Azure Cosmos DB para MongoDB está disponível usando a API de fluxos de alteração. Usando a API de fluxos de alteração, seus aplicativos podem obter as alterações feitas na coleção ou nos itens em um único fragmento. Mais tarde você pode tomar outras ações com base nos resultados. As alterações nos itens da coleção são capturadas na ordem do tempo de modificação e a ordem de classificação é garantida por chave de fragmento.

> [!NOTE]
> Para usar fluxos de alteração, crie a conta com a versão 3.6 da API do Azure Cosmos DB para MongoDB, ou uma versão posterior. Se você executar os exemplos de fluxo de alteração `Unrecognized pipeline stage name: $changeStream` contra uma versão anterior, você pode ver o erro. 

O exemplo a seguir mostra como obter fluxos de alteração em todos os itens da coleção. Este exemplo cria um cursor para observar itens quando eles são inseridos, atualizados ou substituídos. A $match etapa, $project etapa e a opção fullDocument são necessárias para obter os fluxos de mudança. Atualmente, não é suportado o uso de operações de exclusão usando fluxos de alteração. Como solução de solução, você pode adicionar um marcador macio nos itens que estão sendo excluídos. Por exemplo, você pode adicionar um atributo no item chamado "excluído" e defini-lo como "verdadeiro" e definir um TTL no item, para que você possa excluí-lo automaticamente, bem como rastreá-lo.

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

O exemplo a seguir mostra como obter alterações nos itens em um único fragmento. Este exemplo obtém as alterações de itens que têm chave de fragmento igual a "a" e o valor da chave de fragmento igual a "1".

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

## <a name="current-limitations"></a>Limitações atuais

As seguintes limitações são aplicáveis ao usar fluxos de alteração:

* As `operationType` `updateDescription` propriedades e propriedades ainda não são suportadas no documento de saída.
* Os `insert` `update`tipos `replace` de operações e operações são suportados atualmente. A operação de exclusão ou outros eventos ainda não são suportados.

Devido a essas limitações, as opções $match, $project e fullDocument são necessárias, como mostrado nos exemplos anteriores.

## <a name="error-handling"></a>Tratamento de erros

Os seguintes códigos de erro e mensagens são suportados ao usar fluxos de alteração:

* **Código de erro HTTP 429** - Quando o fluxo de alteração é estrangulado, ele retorna uma página vazia.

* **NamespaceNotFound (OperationType Invalid)** - Se você executar o fluxo de alteração na coleção `NamespaceNotFound` que não existe ou se a coleção for descartada, então um erro será devolvido. Como `operationType` a propriedade não pode ser devolvida no documento `operationType Invalidate` de `NamespaceNotFound` saída, em vez do erro, o erro é devolvido.

## <a name="next-steps"></a>Próximas etapas

* [Use o tempo para viver para expirar dados automaticamente na API do Azure Cosmos DB para MongoDB](mongodb-time-to-live.md)
* [Indexação na API do Azure Cosmos DB para MongoDB](mongodb-indexing.md)
