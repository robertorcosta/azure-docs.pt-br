---
title: Acessar as propriedades do documento do sistema por meio do Grafo do Azure Cosmos DB
description: Saiba como ler e gravar as propriedades do documento do sistema do Cosmos DB por meio da API do Gremlin
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: how-to
ms.date: 09/10/2019
author: SnehaGunda
ms.author: sngun
ms.openlocfilehash: c03e4db30d590df21a8ceb3c483ece4b59e548d8
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397310"
---
# <a name="system-document-properties"></a>Propriedades do documento do sistema

Azure Cosmos DB tem [Propriedades do sistema](/rest/api/cosmos-db/databases) , como,,, ```_ts``` ```_self``` ```_attachments``` ```_rid``` e ```_etag``` em cada documento. Além disso, o mecanismo do Gremlin adiciona as propriedades ```inVPartition``` e ```outVPartition``` nas bordas. Por padrão, essas propriedades estão disponíveis para passagem. No entanto, é possível incluir propriedades específicas ou todas elas em passagem de Gremlin.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>Etag

Esta propriedade é usada para controle de simultaneidade otimista. Se o aplicativo precisar dividir a operação em passagens diferentes, poderá usar a propriedade eTag para evitar a perda de dados em gravações simultâneas.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>Vida útil (TTL)

Se a coleção tiver a expiração de documento habilitada e os documentos tiverem a propriedade ```ttl``` definida neles, então, essa propriedade estará disponível em passagem de Gremlin como uma propriedade de vértice ou borda regular. ```ProjectionStrategy``` não é necessário para habilitar a exposição da propriedade de vida útil.

O vértice criado com a passagem abaixo será excluído automaticamente em **123 segundos**.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Próximas etapas
* [Simultaneidade otimista do Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [Vida útil (TTL)](time-to-live.md) no Azure Cosmos DB
