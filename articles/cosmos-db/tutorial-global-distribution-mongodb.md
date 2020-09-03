---
title: Tutorial para configurar a distribuição global com a API do Azure Cosmos DB para MongoDB
description: Saiba como configurar a distribuição global usando a API do Azure Cosmos DB para MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.reviewer: sngun
ms.custom: devx-track-csharp
ms.openlocfilehash: e167ccf1717c539dc676d85a19eb6eacc3ec0bda
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021112"
---
# <a name="set-up-global-distributed-database-using-azure-cosmos-dbs-api-for-mongodb"></a>Configurar o banco de dados distribuído globalmente usando a API do Azure Cosmos DB para MongoDB

Neste artigo, mostramos como usar o portal do Azure para configurar um banco de dados distribuído globalmente e conectar-se a ele usando a API do Azure Cosmos DB para MongoDB.

Este artigo aborda as seguintes tarefas: 

> [!div class="checklist"]
> * Configurar a distribuição global usando o Portal do Azure
> * Configurar a distribuição global usando a [API do Azure Cosmos DB para MongoDB](mongodb-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]

## <a name="verifying-your-regional-setup"></a>Verificando a configuração regional 
Uma maneira simples de verificar a configuração global com a API do Cosmos DB para MongoDB é executar o comando *isMaster()* usando o shell do Mongo.

No Shell do Mongo:

   ```
      db.isMaster()
   ```
   
Resultados do exemplo:

   ```JSON
      {
         "_t": "IsMasterResponse",
         "ok": 1,
         "ismaster": true,
         "maxMessageSizeBytes": 4194304,
         "maxWriteBatchSize": 1000,
         "minWireVersion": 0,
         "maxWireVersion": 2,
         "tags": {
            "region": "South India"
         },
         "hosts": [
            "vishi-api-for-mongodb-southcentralus.documents.azure.com:10255",
            "vishi-api-for-mongodb-westeurope.documents.azure.com:10255",
            "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
         ],
         "setName": "globaldb",
         "setVersion": 1,
         "primary": "vishi-api-for-mongodb-southindia.documents.azure.com:10255",
         "me": "vishi-api-for-mongodb-southindia.documents.azure.com:10255"
      }
   ```

## <a name="connecting-to-a-preferred-region"></a>Conectando a uma região preferencial 

A API do Azure Cosmos DB para MongoDB permite que você especifique a preferência de leitura da coleção para um banco de dados distribuído globalmente. Para leituras de baixa latência e alta disponibilidade global, recomendamos a definição de sua preferência de leitura da coleção como *mais próximo*. Uma preferência de leitura de *mais próximo* está configurada para ler a região mais próxima.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Nearest));
```

Para aplicativos com uma região de leitura/gravação primária e uma região secundária para cenários de DR (recuperação de desastres), recomendamos a definição da preferência de leitura da coleção como *secundário preferido*. Uma preferência de leitura de *secundário preferido* é configurada para ler a região secundária quando a região primária não está disponível.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.SecondaryPreferred));
```

Por fim, se você quiser especificar manualmente suas regiões de leitura. Defina a marca region dentro de sua preferência de leitura.

```csharp
var collection = database.GetCollection<BsonDocument>(collectionName);
var tag = new Tag("region", "Southeast Asia");
collection = collection.WithReadPreference(new ReadPreference(ReadPreferenceMode.Secondary, new[] { new TagSet(new[] { tag }) }));
```

Assim, concluímos este tutorial. Aprenda a gerenciar a consistência de sua conta globalmente replicada lendo [Níveis de consistência no Azure Cosmos DB](consistency-levels.md). E para saber mais sobre como a replicação de banco de dados global funciona no Azure Cosmos DB, veja [Distribuir dados globalmente com o Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você fez o seguinte:

> [!div class="checklist"]
> * Configurar a distribuição global usando o Portal do Azure
> * Configurar a distribuição global usando a API do Cosmos DB para MongoDB

Agora você pode prosseguir para o próximo tutorial e aprender a desenvolver localmente usando o emulador local do Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Desenvolver localmente com o emulador do Azure Cosmos DB](local-emulator.md)
