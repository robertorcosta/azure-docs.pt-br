---
title: Gerenciar a indexação na API do Azure Cosmos DB para MongoDB
description: Este artigo apresenta uma visão geral de Azure Cosmos DB recursos de indexação usando a API do MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "81732699"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Gerenciar a indexação na API do Azure Cosmos DB para MongoDB

A API do Azure Cosmos DB para MongoDB aproveita os principais recursos de gerenciamento de índice do Azure Cosmos DB. Este artigo se concentra em como adicionar índices usando a API do Azure Cosmos DB para MongoDB. Você também pode ler uma [visão geral da indexação em Azure Cosmos DB](index-overview.md) relevante em todas as APIs.

## <a name="indexing-for-mongodb-server-version-36"></a>Indexação para o servidor MongoDB versão 3,6

A API do Azure Cosmos DB para o servidor MongoDB versão 3,6 indexa `_id` automaticamente o campo, que não pode ser removido. Ele impõe automaticamente a exclusividade do `_id` campo por chave de fragmentação.

Para indexar campos adicionais, aplique os comandos de gerenciamento de índice do MongoDB. Como no MongoDB, a API do Azure Cosmos DB para MongoDB indexa automaticamente `_id` o campo somente. Essa política de indexação padrão difere da API do SQL Azure Cosmos DB, que indexa todos os campos por padrão.

Para aplicar uma classificação a uma consulta, você deve criar um índice nos campos usados na operação de classificação.

## <a name="index-types"></a>Tipos de índice

### <a name="single-field"></a>Campo único

Você pode criar índices em qualquer campo único. A ordem de classificação do índice de campo único não importa. O comando a seguir cria um índice no campo `name`:

`db.coll.createIndex({name:1})`

Uma consulta usa vários índices de campo único, quando disponíveis. Você pode criar até 500 índices de campo único por contêiner.

### <a name="compound-indexes-mongodb-server-version-36"></a>Índices compostos (servidor MongoDB versão 3,6)

A API do Azure Cosmos DB para MongoDB dá suporte a índices compostos para contas que usam o protocolo de transmissão da versão 3,6. Você pode incluir até oito campos em um índice composto. Ao contrário do MongoDB, você deve criar um índice composto somente se sua consulta precisar classificar com eficiência em vários campos ao mesmo tempo. Para consultas com vários filtros que não precisam ser classificados, crie vários índices de campo único em vez de um único índice composto.

O comando a seguir cria um índice composto nos campos `name` e `age`:

`db.coll.createIndex({name:1,age:1})`

Você pode usar índices compostos para classificar com eficiência em vários campos ao mesmo tempo, conforme mostrado no exemplo a seguir:

`db.coll.find().sort({name:1,age:1})`

Você também pode usar o índice composto anterior para classificar com eficiência uma consulta com a ordem de classificação oposta em todos os campos. Aqui está um exemplo:

`db.coll.find().sort({name:-1,age:-1})`

No entanto, a sequência dos caminhos no índice composto deve corresponder exatamente à consulta. Veja um exemplo de uma consulta que exigiria um índice composto adicional:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Índices de multikey

Azure Cosmos DB cria índices multikey para indexar o conteúdo armazenado em matrizes. Se você indexar um campo com um valor de matriz, Azure Cosmos DB indexa automaticamente todos os elementos na matriz.

### <a name="geospatial-indexes"></a>Índices geoespaciais

Muitos operadores geoespaciais se beneficiarão dos índices geoespaciais. Atualmente, a API do Azure Cosmos DB para MongoDB `2dsphere` dá suporte a índices. A API ainda não oferece suporte `2d` a índices.

Aqui está um exemplo de criação de um índice geoespacial `location` no campo:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Índices de texto

Atualmente, a API do Azure Cosmos DB para MongoDB não dá suporte a índices de texto. Para consultas de pesquisa de texto em cadeias de caracteres, você deve usar a integração de [pesquisa cognitiva do Azure](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) com Azure Cosmos DB.

## <a name="index-properties"></a>Propriedades de índice

As operações a seguir são comuns para as contas que servem o protocolo de conexão versão 3,6 e as contas que atendem a versões anteriores. Você pode saber mais sobre [índices com suporte e propriedades indexadas](mongodb-feature-support-36.md#indexes-and-index-properties).

### <a name="unique-indexes"></a>Índices exclusivos

[Índices exclusivos](unique-keys.md) são úteis para impor que dois ou mais documentos não contenham o mesmo valor para campos indexados.

> [!IMPORTANT]
> Índices exclusivos podem ser criados somente quando a coleção está vazia (não contém nenhum documento).

O comando a seguir cria um índice exclusivo no campo `student_id`:

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

Para coleções fragmentadas, você deve fornecer a chave de fragmento (partição) para criar um índice exclusivo. Em outras palavras, todos os índices exclusivos em uma coleção fragmentada são índices compostos em que um dos campos é a chave de partição.

Os comandos a seguir criam uma ```coll``` coleção fragmentada (a chave de ```university```fragmento é) com um índice exclusivo nos `student_id` campos `university`e:

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

No exemplo anterior, omitir a ```"university":1``` cláusula retorna um erro com a seguinte mensagem:

```"cannot create unique index over {student_id : 1.0} with shard key pattern { university : 1.0 }"```

### <a name="ttl-indexes"></a>Índices TTL

Para habilitar a expiração do documento em uma determinada coleção, você precisa criar um [índice TTL (vida útil)](../cosmos-db/time-to-live.md). Um índice de TTL é um índice no `_ts` campo com um `expireAfterSeconds` valor.

Exemplo:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

O comando anterior exclui todos os documentos na ```db.coll``` coleção que não foram modificados nos últimos 10 segundos.

> [!NOTE]
> O campo **_ts** é específico para Azure Cosmos DB e não pode ser acessado de clientes do MongoDB. É uma propriedade reservada (sistema) que contém o carimbo de data/hora da última modificação do documento.

## <a name="track-index-progress"></a>Acompanhar o progresso do índice

A versão 3,6 da API do Azure Cosmos DB para MongoDB dá `currentOp()` suporte ao comando para acompanhar o progresso do índice em uma instância de banco de dados. Esse comando retorna um documento que contém informações sobre operações em andamento em uma instância de banco de dados. Você usa o `currentOp` comando para controlar todas as operações em andamento no MongoDB nativo. Na API do Azure Cosmos DB para MongoDB, esse comando só dá suporte ao acompanhamento da operação de índice.

Aqui estão alguns exemplos que mostram como usar o comando `currentOp` para acompanhar o progresso do índice:

* Obter o progresso do índice para uma coleção:

   ```shell
   db.currentOp({"command.createIndexes": <collectionName>, "command.$db": <databaseName>})
   ```

* Obter o progresso do índice para todas as coleções em um banco de dados:

  ```shell
  db.currentOp({"command.$db": <databaseName>})
  ```

* Obtenha o progresso do índice para todos os bancos de dados e coleções em uma conta do Azure Cosmos:

  ```shell
  db.currentOp({"command.createIndexes": { $exists : true } })
  ```

### <a name="examples-of-index-progress-output"></a>Exemplos de saída de progresso de índice

Os detalhes do progresso do índice mostram a porcentagem de progresso para a operação de índice atual. Aqui está um exemplo que mostra o formato do documento de saída para diferentes estágios do progresso do índice:

- Uma operação de índice em uma coleção "foo" e no banco de dados de "barra" que é 60 por cento concluído terá o seguinte documento de saída. O `Inprog[0].progress.total` campo mostra 100 como a porcentagem de conclusão de destino.

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

- Se uma operação de índice acabou de ser iniciada em uma coleção "foo" e no banco de dados "bar", o documento de saída poderá mostrar 0% de progresso até atingir um nível mensurável.

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

- Quando a operação de índice em andamento for concluída, o documento de saída `inprog` mostrará operações vazias.

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Atualizações de índice em segundo plano

Independentemente do valor especificado para a propriedade de índice de **plano de fundo** , as atualizações de índice sempre são feitas em segundo plano. Como as atualizações de índice consomem as unidades de solicitação (RUs) em uma prioridade mais baixa que outras operações de banco de dados, as alterações de índice não resultarão em nenhum tempo de inatividade para gravações, atualizações ou exclusões.

Quando você adicionar um novo índice, as consultas usarão imediatamente o índice. Isso significa que as consultas podem não retornar todos os resultados correspondentes e farão isso sem retornar nenhum erro. Quando a transformação do índice for concluída, os resultados da consulta serão consistentes. Você pode [acompanhar o progresso do índice](#track-index-progress).

## <a name="migrate-collections-with-indexes"></a>Migrar coleções com índices

No momento, você só pode criar índices exclusivos quando a coleção não contém nenhum documento. Ferramentas populares de migração do MongoDB tente criar os índices exclusivos depois de importar os dados. Para evitar esse problema, você pode criar manualmente as coleções correspondentes e os índices exclusivos em vez de permitir que a ferramenta de migração tente. (Você pode obter esse comportamento para ```mongorestore``` o usando o `--noIndexRestore` sinalizador na linha de comando.)

## <a name="indexing-for-mongodb-version-32"></a>Indexação para o MongoDB versão 3,2

Os recursos de indexação disponíveis e os padrões são diferentes para contas do Azure cosmos que são compatíveis com a versão 3,2 do protocolo de transmissão do MongoDB. Você pode [verificar a versão da sua conta](mongodb-feature-support-36.md#protocol-support). Você pode atualizar para a versão 3,6 ao arquivar uma [solicitação de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Se você estiver usando a versão 3,2, esta seção descreve as principais diferenças com a versão 3,6.

### <a name="dropping-default-indexes-version-32"></a>Descartando índices padrão (versão 3,2)

Ao contrário da versão 3,6 da API do Azure Cosmos DB para MongoDB, a versão 3,2 indexa cada propriedade por padrão. Você pode usar o seguinte comando para descartar esses índices padrão para uma```coll```coleção ():

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Depois de descartar os índices padrão, você pode adicionar mais índices como faria na versão 3,6.

### <a name="compound-indexes-version-32"></a>Índices compostos (versão 3,2)

Os índices compostos possuem referências a vários campos de um documento. Se você quiser criar um índice composto, atualize para a versão 3,6, preenchendo uma [solicitação de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Próximas etapas

* [Indexação no Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expirar os dados no Azure Cosmos DB automaticamente com a vida útil](../cosmos-db/time-to-live.md)
