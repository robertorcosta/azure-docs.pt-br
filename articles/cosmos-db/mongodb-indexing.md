---
title: Gerencie a indexação na API do Azure Cosmos DB para MongoDB
description: Este artigo apresenta uma visão geral dos recursos de indexação do Azure Cosmos DB usando a API Do MongoDB.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: conceptual
ms.date: 04/03/2020
author: timsander1
ms.author: tisande
ms.openlocfilehash: fd602f88acf26e821e57e0a844f543aac08dad0d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732699"
---
# <a name="manage-indexing-in-azure-cosmos-dbs-api-for-mongodb"></a>Gerencie a indexação na API do Azure Cosmos DB para MongoDB

A API do Azure Cosmos DB para O MongoDB aproveita os recursos principais de gerenciamento de índices do Azure Cosmos DB. Este artigo se concentra em como adicionar índices usando a API do Azure Cosmos DB para o MongoDB. Você também pode ler uma [visão geral da indexação no Azure Cosmos DB](index-overview.md) que é relevante em todas as APIs.

## <a name="indexing-for-mongodb-server-version-36"></a>Indexação para servidor MongoDB versão 3.6

A API do Azure Cosmos DB para servidor MongoDB `_id` versão 3.6 indexa automaticamente o campo, que não pode ser descartado. Ele automaticamente impõe a singularidade `_id` do campo por chave de fragmento.

Para indexar campos adicionais, você aplica os comandos de gerenciamento de índice singoDB. Como no MongoDB, a API do Azure Cosmos DB para `_id` MongoDB indexa automaticamente o campo apenas. Esta política de indexação padrão difere da API Azure Cosmos DB SQL, que indexa todos os campos por padrão.

Para aplicar um tipo a uma consulta, você deve criar um índice nos campos usados na operação de classificação.

## <a name="index-types"></a>Tipos de índice

### <a name="single-field"></a>Campo único

Você pode criar índices em qualquer campo. A ordem do tipo do índice de campo único não importa. O comando a seguir cria `name`um índice no campo:

`db.coll.createIndex({name:1})`

Uma consulta usa vários índices de campo únicos quando disponíveis. Você pode criar até 500 índices de campo único por contêiner.

### <a name="compound-indexes-mongodb-server-version-36"></a>Índices compostos (versão 3.6 do servidor MongoDB)

A API do Azure Cosmos DB para MongoDB suporta índices compostos para contas que usam o protocolo de fio versão 3.6. Você pode incluir até oito campos em um índice composto. Ao contrário do MongoDB, você deve criar um índice composto somente se sua consulta precisar classificar eficientemente em vários campos ao mesmo tempo. Para consultas com vários filtros que não precisam ser classificados, crie vários índices de campo único em vez de um único índice composto.

O comando a seguir cria `name` um `age`índice composto nos campos e:

`db.coll.createIndex({name:1,age:1})`

Você pode usar índices compostos para classificar eficientemente em vários campos ao mesmo tempo, como mostrado no exemplo a seguir:

`db.coll.find().sort({name:1,age:1})`

Você também pode usar o índice composto anterior para classificar eficientemente uma consulta com a ordem de classificação oposta em todos os campos. Aqui está um exemplo:

`db.coll.find().sort({name:-1,age:-1})`

No entanto, a seqüência dos caminhos no índice composto deve corresponder exatamente à consulta. Aqui está um exemplo de uma consulta que exigiria um índice composto adicional:

`db.coll.find().sort({age:1,name:1})`

### <a name="multikey-indexes"></a>Índices multichave

O Azure Cosmos DB cria índices multi-chave para indexar o conteúdo armazenado em arrays. Se você indexar um campo com um valor de matriz, o Azure Cosmos DB indexa automaticamente cada elemento da matriz.

### <a name="geospatial-indexes"></a>Índices geoespaciais

Muitos operadores geoespaciais se beneficiarão de índices geoespaciais. Atualmente, a API do Azure Cosmos DB `2dsphere` para MongoDB suporta índices. A API ainda `2d` não suporta índices.

Aqui está um exemplo de criação de `location` um índice geoespacial no campo:

`db.coll.createIndex({ location : "2dsphere" })`

### <a name="text-indexes"></a>Índices de texto

A API do Azure Cosmos DB para MongoDB não suporta atualmente índices de texto. Para consultas de pesquisa de texto em strings, você deve usar a integração [azure Cognitive Search](https://docs.microsoft.com/azure/search/search-howto-index-cosmosdb) com o Azure Cosmos DB.

## <a name="index-properties"></a>Propriedades de índice

As operações a seguir são comuns para contas que servem o protocolo de fio versão 3.6 e contas que atendem versões anteriores. Você pode aprender mais sobre [índices suportados e propriedades indexadas.](mongodb-feature-support-36.md#indexes-and-index-properties)

### <a name="unique-indexes"></a>Índices exclusivos

[Índices únicos](unique-keys.md) são úteis para reforçar que dois ou mais documentos não contêm o mesmo valor para campos indexados.

> [!IMPORTANT]
> Índices únicos só podem ser criados quando a coleta estiver vazia (não contém documentos).

O comando a seguir cria `student_id`um índice único no campo :

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

Para coleções com fragmentos, você deve fornecer a chave de fragmento (partição) para criar um índice único. Em outras palavras, todos os índices exclusivos em uma coleção fragmentada são índices compostos em que um dos campos é a chave de partição.

Os seguintes comandos criam ```coll``` uma coleção de ```university```fragmentos (a chave `student_id` de `university`fragmento é ) com um índice único nos campos e :

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

Para habilitar a expiração do documento em uma determinada coleção, você precisa criar um [índice de tempo de vida (TTL).](../cosmos-db/time-to-live.md) Um índice TTL é `_ts` um índice `expireAfterSeconds` no campo com um valor.

Exemplo:

```JavaScript
globaldb:PRIMARY> db.coll.createIndex({"_ts":1}, {expireAfterSeconds: 10})
```

O comando anterior exclui quaisquer ```db.coll``` documentos da coleção que não tenham sido modificados nos últimos 10 segundos.

> [!NOTE]
> O campo **_ts** é específico do Azure Cosmos DB e não é acessível a partir de clientes Do MongoDB. É uma propriedade reservada (sistema) que contém o carimbo de data da última modificação do documento.

## <a name="track-index-progress"></a>Acompanhar o progresso do índice

A versão 3.6 da API do Azure Cosmos DB `currentOp()` para MongoDB suporta o comando para acompanhar o progresso do índice em uma instância de banco de dados. Este comando retorna um documento que contém informações sobre operações em andamento em uma instância de banco de dados. Você usa `currentOp` o comando para rastrear todas as operações em andamento no MongoDB nativo. Na API do Azure Cosmos DB para MongoDB, este comando só suporta o rastreamento da operação de índice.

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

### <a name="examples-of-index-progress-output"></a>Exemplos de saída de progresso do índice

Os detalhes do progresso do índice mostram a porcentagem de progresso para a operação atual do índice. Aqui está um exemplo que mostra o formato do documento de saída para diferentes estágios de progresso do índice:

- Uma operação de índice em um banco de dados de coleta "foo" e "barra" que esteja 60% concluída terá o seguinte documento de saída. O `Inprog[0].progress.total` campo mostra 100 como a porcentagem de conclusão da meta.

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

- Se uma operação de índice tiver acabado de começar em um banco de dados de coleta "foo" e "barra", o documento de saída pode mostrar progresso de 0% até atingir um nível mensurável.

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

- Quando a operação do índice em andamento termina, o documento de saída mostra operações vazias. `inprog`

   ```json
   {
      "inprog" : [],
      "ok" : 1
   }
   ```

### <a name="background-index-updates"></a>Atualizações do índice de antecedentes

Independentemente do valor especificado para a propriedade de índice **de fundo,** as atualizações de índice são sempre feitas em segundo plano. Como as atualizações de índice consomem Unidades de Solicitação (RUs) com uma prioridade menor do que outras operações de banco de dados, as alterações de índice não resultarão em qualquer tempo de inatividade para gravações, atualizações ou exclusões.

Quando você adiciona um novo índice, as consultas usarão imediatamente o índice. Isso significa que as consultas podem não retornar todos os resultados correspondentes e o farão sem retornar quaisquer erros. Quando a transformação do índice for concluída, os resultados da consulta serão consistentes. Você pode [acompanhar o progresso do índice](#track-index-progress).

## <a name="migrate-collections-with-indexes"></a>Migrar coleções com índices

Atualmente, você só pode criar índices únicos quando a coleção não contém documentos. As ferramentas de migração populares do MongoDB tentam criar os índices únicos após a importação dos dados. Para contornar esse problema, você pode criar manualmente as coleções correspondentes e índices únicos em vez de permitir que a ferramenta de migração tente. (Você pode alcançar ```mongorestore``` esse comportamento `--noIndexRestore` usando a bandeira na linha de comando.)

## <a name="indexing-for-mongodb-version-32"></a>Indexação para MongoDB versão 3.2

Os recursos de indexação e os padrões disponíveis são diferentes para as contas do Azure Cosmos compatíveis com a versão 3.2 do protocolo de fio MongoDB. Você pode [verificar a versão da sua conta.](mongodb-feature-support-36.md#protocol-support) Você pode atualizar para a versão 3.6 arquivando uma [solicitação de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

Se você estiver usando a versão 3.2, esta seção descreve as principais diferenças com a versão 3.6.

### <a name="dropping-default-indexes-version-32"></a>Queda dos índices de inadimplência (versão 3.2)

Ao contrário da versão 3.6 da API do Azure Cosmos DB para MongoDB, a versão 3.2 indexa cada propriedade por padrão. Você pode usar o seguinte comando para baixar```coll```esses índices padrão para uma coleção ( ):

```JavaScript
> db.coll.dropIndexes()
{ "_t" : "DropIndexesResponse", "ok" : 1, "nIndexesWas" : 3 }
```

Depois de baixar os índices de padrão, você pode adicionar mais índices como faria na versão 3.6.

### <a name="compound-indexes-version-32"></a>Índices compostos (versão 3.2)

Os índices compostos possuem referências a vários campos de um documento. Se você quiser criar um índice composto, atualize para a versão 3.6, arquivando uma [solicitação de suporte](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="next-steps"></a>Próximas etapas

* [Indexação no Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expirar os dados no Azure Cosmos DB automaticamente com a vida útil](../cosmos-db/time-to-live.md)
