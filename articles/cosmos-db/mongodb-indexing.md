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
ms.openlocfilehash: e51e96c0c553bcf37284878cab11f3ec592ddd05
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/22/2019
ms.locfileid: "72753387"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indexação usando a API do Azure Cosmos DB para MongoDB

A API do Azure Cosmos DB para MongoDB aproveita os recursos de gerenciamento automático de índices do Cosmos DB. Como resultado, os usuários têm acesso às políticas de indexação padrão do Cosmos DB. Portanto, se nenhum índice for definido pelo usuário, ou se nenhum índice for removido, todos os campos serão automaticamente indexados por padrão quando inseridos em uma coleção. Na maioria dos cenários, recomendamos usar a política de indexação padrão definida na conta.

## <a name="indexing-for-version-36"></a>Indexação para a versão 3,6

As contas que atendem ao protocolo de transmissão versão 3,6 fornecem uma política de indexação padrão diferente da política fornecida por versões anteriores. Por padrão, somente o campo _id é indexado. Para indexar campos adicionais, o usuário deve aplicar os comandos de gerenciamento de índice do MongoDB. Para aplicar uma classificação a uma consulta, no momento um índice deve ser criado nos campos usados na operação de classificação.

### <a name="dropping-the-default-indexes-36"></a>Descartando os índices padrão (3,6)

Para as contas que atendem a versão 3,6 do protocolo de transmissão, o único índice padrão é _id, que não pode ser Descartado.

### <a name="creating-a-compound-index-36"></a>Criando um índice composto (3,6)

Os índices compostos verdadeiros têm suporte para contas que usam o protocolo 3,6 Wire. O comando a seguir criará um índice composto nos campos ' a ' e ' b ': `db.coll.createIndex({a:1,b:1})`

Os índices compostos podem ser usados para classificar com eficiência em vários campos de uma só vez, como: `db.coll.find().sort({a:1,b:1})`

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

## <a name="next-steps"></a>Próximos passos

* [Indexação no Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expirar os dados no Azure Cosmos DB automaticamente com a vida útil](../cosmos-db/time-to-live.md)
