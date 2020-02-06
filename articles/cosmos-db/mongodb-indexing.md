---
title: Indexação na API do Azure Cosmos DB para MongoDB
description: Apresenta uma visão geral dos recursos de indexação com a API do Azure Cosmos DB para MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: c8879884cf3d882e6a6b441244ed139072bedeeb
ms.sourcegitcommit: f0f73c51441aeb04a5c21a6e3205b7f520f8b0e1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029462"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indexação usando a API do Azure Cosmos DB para MongoDB

A API do Azure Cosmos DB para MongoDB aproveita os recursos de gerenciamento automático de índices do Cosmos DB. Como resultado, os usuários têm acesso às políticas de indexação padrão do Cosmos DB. Portanto, se nenhum índice for definido pelo usuário, ou se nenhum índice for removido, todos os campos serão automaticamente indexados por padrão quando inseridos em uma coleção. Na maioria dos cenários, recomendamos usar a política de indexação padrão definida na conta.

## <a name="indexing-for-version-36"></a>Indexação para a versão 3,6

As contas que atendem ao protocolo de transmissão versão 3,6 fornecem uma política de indexação padrão diferente da política fornecida por versões anteriores. Por padrão, somente o campo _id é indexado. Para indexar campos adicionais, o usuário deve aplicar os comandos de gerenciamento de índice do MongoDB. Para aplicar uma classificação a uma consulta, no momento um índice deve ser criado nos campos usados na operação de classificação.

### <a name="dropping-the-default-indexes-36"></a>Descartando os índices padrão (3,6)

Para as contas que atendem a versão 3,6 do protocolo de transmissão, o único índice padrão é _id, que não pode ser descartada.

### <a name="creating-a-compound-index-36"></a>Criando um índice composto (3,6)

Os índices compostos verdadeiros têm suporte para contas que usam o protocolo 3,6 Wire. O comando a seguir criará um índice composto nos campos ' a ' e ' b ': `db.coll.createIndex({a:1,b:1})`

Os índices compostos podem ser usados para classificar com eficiência em vários campos de uma só vez, como: `db.coll.find().sort({a:1,b:1})`

### <a name="track-the-index-progress"></a>Acompanhar o progresso do índice

A versão 3,6 da API para as contas do MongoDB do Azure Cosmos DB dá suporte ao comando `currentOp()` para acompanhar o progresso do índice em uma instância do banco de dados. Esse comando retorna um documento que contém informações sobre as operações em andamento em uma instância de banco de dados. O comando `currentOp` é usado para rastrear todas as operações em andamento no MongoDB nativo, enquanto na API do Azure Cosmos DB para MongoDB, esse comando só dá suporte ao acompanhamento da operação de índice.

Aqui estão alguns exemplos que mostram como usar o comando `currentOp` para acompanhar o progresso do índice:

• Obter o progresso do índice para uma coleção:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

• Obtenha o progresso do índice para todas as coleções em um banco de dados:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

• Obtenha o progresso do índice para todos os bancos de dados e coleções em uma conta do Azure Cosmos:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

Os detalhes de progresso do índice contêm a porcentagem de progresso para a operação de índice atual. O exemplo a seguir mostra o formato do documento de saída para diferentes estágios do progresso do índice:

1. Se a operação de índice em uma coleção ' foo ' e um banco de dados de ' barra ' que tenha 60% de indexação concluídas, terá o seguinte documento de saída. `Inprog[0].progress.total` mostra 100 como a conclusão de destino.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 60 %",
                "progress" : {
                        "done" : 60,
                        "total" : 100
                },
                …………..…..
        }
        ],
        "ok" : 1
   }
   ```

2. Para uma operação de índice que acabou de ser iniciada em uma coleção ' foo ' e um banco de dados ' bar ', o documento de saída pode mostrar 0% de progresso até atingir um nível mensurável.

   ```json
   {
        "inprog" : [
        {
                ………………...
                "command" : {
                        "createIndexes" : foo
                        "indexes" :[ ],
                        "$db" : bar
                },
                "msg" : "Index Build (background) Index Build (background): 0 %",
                "progress" : {
                        "done" : 0,
                        "total" : 100
                },
                …………..…..
        }
        ],
       "ok" : 1
   }
   ```

3. Quando a operação de índice em andamento for concluída, o documento de saída mostrará as operações de inprog vazia.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

## <a name="indexing-for-version-32"></a>Indexação para a versão 3,2

### <a name="dropping-the-default-indexes-32"></a>Descartando os índices padrão (3,2)

Os seguintes comandos podem ser usados para descartar os índices padrão de uma coleção ```coll```:

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

### <a name="creating-a-compound-index-32"></a>Criando um índice composto (3,2)

Os índices compostos possuem referências a vários campos de um documento. Logicamente, eles são equivalentes à criação de vários índices individuais por campo. Para aproveitar as otimizações fornecidas pelas técnicas de indexação do Azure Cosmos DB, recomendamos criar vários índices individuais em vez de um único índice composto (não exclusivo).

## <a name="common-indexing-operations"></a>Operações comuns de indexação

As operações a seguir são comuns para ambas as contas que atendem o protocolo de conexão versão 3,6 e as contas que atendem às versões anteriores do protocolo de conexão 

## <a name="creating-unique-indexes"></a>Criando índices exclusivos

[Índices exclusivos](unique-keys.md) são úteis para impor que nenhum documento contenha o mesmo valor para o campo indexado.

>[!Important]
> Atualmente, índices exclusivos podem ser criados apenas quando a coleção está vazia (sem documentos).

O comando abaixo cria um índice exclusivo no campo "student_id":

```shell
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1 }, {unique:true} )
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 1,
        "numIndexesAfter" : 4
}
```

Para coleções fragmentadas, de acordo com o comportamento do MongoDB, a criação de um índice exclusivo requer o fornecimento da chave do fragmento (partição). Em outras palavras, todos os índices exclusivos em uma coleção fragmentada são índices compostos em que um dos campos é a chave de partição.

Os seguintes comandos criam uma coleção fragmentada ```coll``` (a chave do fragmento é ```university```) com um índice exclusivo nos campos student_id e university:

```shell
globaldb:PRIMARY> db.runCommand({shardCollection: db.coll._fullName, key: { university: "hashed"}});
{
        "_t" : "ShardCollectionResponse",
        "ok" : 1,
        "collectionsharded" : "test.coll"
}
globaldb:PRIMARY> db.coll.createIndex( { "student_id" : 1, "university" : 1 }, {unique:true})
{
        "_t" : "CreateIndexesResponse",
        "ok" : 1,
        "createdCollectionAutomatically" : false,
        "numIndexesBefore" : 3,
        "numIndexesAfter" : 4
}
```

No exemplo acima, se a cláusula ```"university":1``` for omitida, um erro com a seguinte mensagem será retornado:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

## <a name="ttl-indexes"></a>Índices TTL

Para habilitar a validade de documento em determinada coleção, um ["Índice TTL" (índice de vida útil)](../cosmos-db/time-to-live.md) precisará ser criado. Um TTL é um índice no campo _ts com um valor de "expireAfterSeconds".

Exemplo:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

O comando anterior causará a exclusão de todos os documentos na coleção ```db.coll``` que não foram modificados nos últimos 10 segundos.

> [!NOTE]
> **_ts** é um campo específico do Azure Cosmos DB e não pode ser acessado por clientes do MongoDB. É uma propriedade reservada (sistema) que contém o carimbo de data/hora da última modificação do documento.

## <a name="migrating-collections-with-indexes"></a>Migrando coleções com índices

Atualmente, a criação de índices exclusivos é possível apenas quando a coleção não contém documentos. Ferramentas de migração do MongoDB populares tentam criar índices exclusivos depois da importação dos dados. Para evitar esse problema, é recomendável que os usuários criem manualmente as coleções e os índices exclusivos correspondentes, em vez de permitir a ferramenta de migração (por ```mongorestore``` esse comportamento é obtido usando o sinalizador `--noIndexRestore` na linha de comando).

## <a name="next-steps"></a>{1&gt;{2&gt;Próximas etapas&lt;2}&lt;1}

* [Indexação no Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expirar os dados no Azure Cosmos DB automaticamente com a vida útil](../cosmos-db/time-to-live.md)
