---
title: Indexação na API do Azure Cosmos DB para MongoDB
description: Apresenta uma visão geral dos recursos de indexação com a API do Azure Cosmos DB para MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 03/27/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: 7c75f0d6f74fe8cf1417e0dc40a5ad01615d7057
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371067"
---
# <a name="indexing-using-azure-cosmos-dbs-api-for-mongodb"></a>Indexação usando a API do Azure Cosmos DB para MongoDB

A API do Azure Cosmos DB para O MongoDB aproveita os recursos principais de gerenciamento de índices do Azure Cosmos DB. Este documento se concentra em como adicionar índices usando a API do Azure Cosmos DB para O MongoDB. Você também pode ler uma [visão geral da indexação no Azure Cosmos DB](index-overview.md) que é relevante em todas as API's.

## <a name="indexing-for-version-36"></a>Indexação para versão 3.6

O `_id` campo é sempre indexado automaticamente e não pode ser descartado. A API do Azure Cosmos DB para MongoDB `_id` impõe automaticamente a exclusividade do campo por chave de fragmento.

Para indexar campos adicionais, você deve aplicar os comandos de gerenciamento de índice sustais Do MongoDB. Como no MongoDB, `_id` apenas o campo é indexado automaticamente. Essa política de indexação padrão é diferente da API Azure Cosmos DB SQL, que indexa todos os campos por padrão.

Para aplicar um tipo a uma consulta, um índice deve ser criado nos campos usados na operação de classificação.

## <a name="index-types"></a>Tipos de índice

### <a name="single-field"></a>Campo único

Você pode criar índices em qualquer campo. A ordem do tipo do índice de campo único não importa. O comando a seguir criará `name`um índice no campo :

`db.coll.createIndex({name:1})`

Uma consulta utilizará vários índices de campo único, quando disponíveis.

### <a name="compound-indexes-36"></a>Índices compostos (3,6)

Os índices compostos são suportados para contas usando o protocolo de fio 3.6. Você pode incluir até 8 campos em um índice composto. Ao contrário do MongoDB, você só deve criar um índice composto se sua consulta precisar classificar eficientemente em vários campos ao mesmo tempo. Para consultas com vários filtros que não precisam ser classificados, você deve criar vários índices de campo único em vez de um único índice composto.

O comando a seguir criará `name` um `age`índice composto nos campos e:

`db.coll.createIndex({name:1,age:1})`

Índices compostos podem ser usados para classificar eficientemente em vários campos ao mesmo tempo, tais como:

`db.coll.find().sort({name:1,age:1})`

O índice composto acima também pode ser usado para uma classificação eficiente em uma consulta com a ordem de classificação oposta em todos os campos. Aqui está um exemplo:

`db.coll.find().sort({name:-1,age:-1})`

No entanto, a seqüência dos caminhos no índice composto deve corresponder exatamente à consulta. Aqui está um exemplo de uma consulta que exigiria um índice composto adicional:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Índices multichave

O Azure Cosmos DB cria índices multi-chave para indexar o conteúdo armazenado em arrays. Se você indexar um campo com um valor de matriz, o Azure Cosmos DB indexa automaticamente cada elemento da matriz.

### <a name="geospatial-indexes"></a>Índices geoespaciais

Muitos operadores geoespaciais se beneficiarão de índices geoespaciais. Atualmente, a API do Azure Cosmos DB `2dsphere` para MongoDB suporta índices. `2d`os índices ainda não são suportados.

Aqui está um exemplo para criar um `location` índice geoespacial no campo:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Índices de texto

Os índices de texto não são suportados no momento. Para consultas de pesquisa de texto em strings, você deve usar a integração [do Azure Cognitive Search](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) com o Azure Cosmos DB.

## <a name="index-properties"></a>Propriedades de índice

As operações a seguir são comuns para ambas as contas que servem a versão 3.6 do protocolo de fio e contas que servem versões anteriores do protocolo de fio. Você também pode aprender mais sobre [índices suportados e propriedades indexadas.](mongodb-feature-support-36.md#indexes-and-index-properties)

### <a name="unique-indexes"></a>Índices exclusivos

[Índices exclusivos](unique-keys.md) são úteis para impor que nenhum documento contenha o mesmo valor para o campo indexado.

>[!Important]
> Índices únicos só podem ser criados quando a coleta estiver vazia (não contém documentos).

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

Para coleções com fragmentos, a criação de um índice único requer fornecer a chave de fragmento (partição). Em outras palavras, todos os índices exclusivos em uma coleção fragmentada são índices compostos em que um dos campos é a chave de partição.

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

### <a name="ttl-indexes"></a>Índices TTL

Para habilitar a validade de documento em determinada coleção, um ["Índice TTL" (índice de vida útil)](../cosmos-db/time-to-live.md) precisará ser criado. Um TTL é um índice no campo _ts com um valor de "expireAfterSeconds".

Exemplo:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

O comando anterior causará a exclusão de todos os documentos na coleção ```db.coll``` que não foram modificados nos últimos 10 segundos.

> [!NOTE]
> **_ts** é um campo específico do Azure Cosmos DB e não está acessível a partir de clientes do MongoDB. É uma propriedade reservada (sistema) que contém o carimbo de data/hora da última modificação do documento.

## <a name="track-the-index-progress"></a>Acompanhe o progresso do índice

A versão 3.6 da API do Azure Cosmos DB `currentOp()` para contas MongoDB suporta o comando para acompanhar o progresso do índice em uma instância de banco de dados. Este comando retorna um documento que contém informações sobre as operações em andamento em uma instância de banco de dados. O `currentOp` comando é usado para rastrear todas as operações em andamento no MongoDB nativo, enquanto na API do Azure Cosmos DB para MongoDB, este comando só suporta o rastreamento da operação de índice.

Aqui estão alguns exemplos que `currentOp` mostram como usar o comando para acompanhar o progresso do índice:

* Obtenha o progresso do índice para uma coleção:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Obtenha o progresso do índice para todas as coleções em um banco de dados:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Obtenha o progresso do índice para todos os bancos de dados e coleções em uma conta do Azure Cosmos:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

Os detalhes de progresso do índice contêm percentual de progresso para a operação atual do índice. O exemplo a seguir mostra o formato do documento de saída para diferentes estágios de progresso do índice:

1. Se a operação de índice em um banco de dados de coleta 'foo' e 'barra' que tenha 60 % de indexação completa terá o seguinte documento de saída. `Inprog[0].progress.total`mostra 100 como a conclusão do alvo.

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

2. Para uma operação de índice que acaba de começar em um banco de dados de coleta 'foo' e 'barra', o documento de saída pode mostrar progresso de 0% até chegar a um nível mensurável.

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

3. Quando a operação de índice em andamento é concluída, o documento de saída mostra operações inprog vazias.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Atualizações do índice de antecedentes

Independentemente do valor especificado para a propriedade de índice **de fundo,** as atualizações de índice são sempre feitas em segundo plano. As atualizações de índice consomem RU's com uma prioridade menor do que outras operações de banco de dados. Portanto, as alterações de índice não resultarão em qualquer tempo de inatividade para gravações, atualizações ou exclusões.

Ao adicionar um novo índice, as consultas o utilizarão imediatamente. Isso significa que as consultas podem não retornar todos os resultados correspondentes, e o farão sem retornar quaisquer erros. Uma vez concluída a transformação do índice, os resultados da consulta serão consistentes. Você pode [acompanhar o progresso do índice](#track-the-index-progress).

## <a name="migrating-collections-with-indexes"></a>Migrando coleções com índices

Atualmente, a criação de índices exclusivos é possível apenas quando a coleção não contém documentos. Ferramentas de migração do MongoDB populares tentam criar índices exclusivos depois da importação dos dados. Para contornar esse problema, sugere-se que os usuários criem manualmente as coleções correspondentes ```mongorestore``` e índices únicos, em vez de permitir que a ferramenta de migração (para esse comportamento é alcançada usando o `--noIndexRestore` sinalizador na linha de comando).

## <a name="indexing-for-version-32"></a>Indexação para versão 3.2

Para contas Azure Cosmos DB compatíveis com a versão 3.2 do protocolo de fio MongoDB, os recursos de indexação disponíveis e os padrões são diferentes. Você pode [verificar a versão da sua conta.](mongodb-feature-support-36.md#protocol-support) Você pode atualizar para a versão 3.6 arquivando uma [solicitação de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Se você estiver usando a versão 3.2, esta seção descreve as principais diferenças com a versão 3.6.

### <a name="dropping-the-default-indexes-32"></a>Queda dos índices de inadimplência (3,2)

Ao contrário da versão 3.6 da API do Azure Cosmos DB para MongoDB, 3.2 índices de versão de cada propriedade por padrão. O comando a seguir pode ser usado para ```coll```diminuir esses índices padrão para uma coleção :

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Depois de baixar os índices de inadimplência, você pode adicionar índices adicionais como feito na Versão 3.6.

### <a name="compound-indexes-32"></a>Índices compostos (3,2)

Os índices compostos possuem referências a vários campos de um documento. Se você quiser criar um índice composto, atualize para 3.6 versão, arquivando uma [solicitação de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Próximas etapas

* [Indexação no Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expirar os dados no Azure Cosmos DB automaticamente com a vida útil](../cosmos-db/time-to-live.md)
