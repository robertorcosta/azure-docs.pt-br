---
title: Comandos de extensão Do MongoDB para gerenciar dados na API do Azure Cosmos DB para MongoDB
description: Este artigo descreve como usar os comandos de extensão do MongoDB para gerenciar dados armazenados na API do Azure Cosmos DB para O MongoDB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sngun
ms.openlocfilehash: f57b274715eb1c8a4d517f5655c09c366574d412
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445214"
---
# <a name="use-mongodb-extension-commands-to-manage-data-stored-in-azure-cosmos-dbs-api-for-mongodb"></a>Use comandos de extensão Do MongoDB para gerenciar dados armazenados na API do Azure Cosmos DB para MongoDB 

O Azure Cosmos DB é o serviço de banco de dados multimodelo distribuído globalmente da Microsoft. Você pode se comunicar com a API do Azure Cosmos DB para MongoDB usando qualquer um dos [drivers clientes do MongoDB](https://docs.mongodb.org/ecosystem/drivers)de código aberto . A API do Azure Cosmos DB para MongoDB permite o uso de drivers clientes existentes aderindo ao [protocolo de fio MongoDB](https://docs.mongodb.org/manual/reference/mongodb-wire-protocol).

Ao usar a API do Azure Cosmos DB para MongoDB, você pode desfrutar dos benefícios do Cosmos DB, como distribuição global, fragmentação automática, alta disponibilidade, garantias de latência, automática, criptografia em repouso, backups e muito mais, preservando seus investimentos em seu aplicativo MongoDB.

## <a name="mongodb-protocol-support"></a>Suporte ao protocolo MongoDB

Por padrão, a API do Azure Cosmos DB para MongoDB é compatível com a versão 3.2 do servidor MongoDB, para obter mais detalhes, consulte [recursos suportados e sintaxe](mongodb-feature-support.md). Os recursos ou operadores de consulta adicionados na versão 3.4 do MongoDB estão atualmente disponíveis como uma visualização na API do Azure Cosmos DB para MongoDB. Os seguintes comandos de extensão suportam a funcionalidade específica do Azure Cosmos DB ao executar operações CRUD nos dados armazenados na API do Azure Cosmos DB para MongoDB:

* [Criar banco de dados](#create-database)
* [Banco de dados de atualização](#update-database)
* [Obter banco de dados](#get-database)
* [Criar coleção](#create-collection)
* [Coleção de atualizações](#update-collection)
* [Obter coleção](#get-collection)

## <a name="create-database"></a><a id="create-database"></a>Criar banco de dados

O comando criar extensão de banco de dados cria um novo banco de dados MongoDB. O nome do banco de dados é usado a partir do contexto de bancos de dados com os quais o comando é executado. O formato do comando CreateDatabase é o seguinte:

```
{
  customAction: "CreateDatabase",
  offerThroughput: <Throughput that you want to provision on the database>
}
```

A tabela a seguir descreve os parâmetros dentro do comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| customAction   |  string  |   Nome do comando personalizado, deve ser "CriarBanco de Dados".      |
| oferecerThroughput | INT  | Throughput provisionado que você definiu no banco de dados. Esse parâmetro é opcional. |

### <a name="output"></a>Saída

Retorna uma resposta de comando personalizada padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Criar um banco de dados**

Para criar um banco de dados chamado "teste", use o seguinte comando:

```shell
use test
db.runCommand({customAction: "CreateDatabase"});
```

**Criar um banco de dados com throughput**

Para criar um banco de dados chamado "teste" e throughput provisionado de 1000 RUs, use o seguinte comando:

```shell
use test
db.runCommand({customAction: "CreateDatabase", offerThroughput: 1000 });
```

## <a name="update-database"></a><a id="update-database"></a>Banco de dados de atualização

O comando de extensão do banco de dados de atualização atualiza as propriedades associadas ao banco de dados especificado. Atualmente, você só pode atualizar a propriedade "offerThroughput".

```
{
  customAction: "UpdateDatabase",
  offerThroughput: <New throughput that you want to provision on the database> 
}
```

A tabela a seguir descreve os parâmetros dentro do comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| customAction    |    string     |   Nome do comando personalizado. Deve ser "UpdateDatabase".      |
|  oferecerThroughput   |  INT       |     Novo throughput provisionado que você deseja definir no banco de dados.    |

### <a name="output"></a>Saída

Retorna uma resposta de comando personalizada padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Atualize o throughput provisionado associado a um banco de dados**

Para atualizar o throughput provisionado de um banco de dados com nome "teste" para 1200 RUs, use o seguinte comando:

```shell
use test
db.runCommand({customAction: "UpdateDatabase", offerThroughput: 1200 });
```

## <a name="get-database"></a><a id="get-database"></a>Obter banco de dados

O comando get database extension retorna o objeto do banco de dados. O nome do banco de dados é usado a partir do contexto do banco de dados com o qual o comando é executado.

```
{
  customAction: "GetDatabase"
}
```

A tabela a seguir descreve os parâmetros dentro do comando:


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  customAction   |   string      |   Nome do comando personalizado. Deve ser "GetDatabase"|
        
### <a name="output"></a>Saída

Se o comando for bem sucedido, a resposta contém um documento com os seguintes campos:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |   `int`     |   Estado de resposta. 1 == sucesso. 0 == falha.      |
| `database`    |    `string`        |   Nome do banco de dados.      |
|   `provisionedThroughput`  |    `int`      |    Throughput provisionado que está definido no banco de dados. Este é um parâmetro de resposta opcional.     |

Se o comando falhar, uma resposta de comando personalizado padrão será retornada. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Pegue o banco de dados**

Para obter o objeto de banco de dados de um banco de dados chamado "teste", use o seguinte comando:

```shell
use test
db.runCommand({customAction: "GetDatabase"});
```

## <a name="create-collection"></a><a id="create-collection"></a>Criar coleção

O comando create collection extension cria uma nova coleção MongoDB. O nome do banco de dados é usado a partir do contexto de bancos de dados com os quais o comando é executado. O formato do comando CreateCollection é o seguinte:

```
{
  customAction: "CreateCollection",
  collection: <Collection Name>,
  offerThroughput: <Throughput that you want to provision on the collection>,
  shardKey: <Shard key path>  
}
```

A tabela a seguir descreve os parâmetros dentro do comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| customAction    | string | Nome do comando personalizado. Deve ser "CreateCollection"     |
| collection      | string | Nome da coleção                                   |
| oferecerThroughput | INT    | Throughput provisionado para definir no banco de dados. É um parâmetro opcional |
| chave de fragmento        | string | Shard Key caminho para criar uma coleção de fragmentos. É um parâmetro opcional |

### <a name="output"></a>Saída

Retorna uma resposta de comando personalizada padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Crie uma coleção sem força**

Para criar uma coleção não-esfarizada com o nome "testCollection" e o throughput provisionado de 1000 RUs, use o seguinte comando: 

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000});
``` 

**Crie uma coleção de fragmentos**

Para criar uma coleção com o nome "testCollection" e o throughput provisionado de 1000 RUs, use o seguinte comando:

```shell
use test
db.runCommand({customAction: "CreateCollection", collection: "testCollection", offerThroughput: 1000, shardKey: "a.b" });
```

## <a name="update-collection"></a><a id="update-collection"></a>Coleção de atualizações

O comando de extensão de coleta de atualização atualiza as propriedades associadas à coleção especificada.

```
{
  customAction: "UpdateCollection",
  collection: <Name of the collection that you want to update>,
  offerThroughput: <New throughput that you want to provision on the collection> 
}
```

A tabela a seguir descreve os parâmetros dentro do comando:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  customAction   |   string      |   Nome do comando personalizado. Deve ser "UpdateCollection".      |
|  collection   |   string      |   Nome da coleção.       |
| oferecerThroughput   |INT|   Throughput provisionado para definir na coleção.|

## <a name="output"></a>Saída

Retorna uma resposta de comando personalizada padrão. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Atualize o throughput provisionado associado a uma coleção**

Para atualizar o throughput provisionado de uma coleção com o nome "testCollection" para 1200 RUs, use o seguinte comando:

```shell
use test
db.runCommand({customAction: "UpdateCollection", collection: "testCollection", offerThroughput: 1200 });
```

## <a name="get-collection"></a><a id="get-collection"></a>Obter coleção

O comando personalizado get collection retorna o objeto de coleta.

```
{
  customAction: "GetCollection",
  collection: <Name of the collection>
}
```

A tabela a seguir descreve os parâmetros dentro do comando:


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
| customAction    |   string      |   Nome do comando personalizado. Deve ser "GetCollection".      |
| collection    |    string     |    Nome da coleção.     |

### <a name="output"></a>Saída

Se o comando for bem sucedido, a resposta contém um documento com os seguintes campos


|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Estado de resposta. 1 == sucesso. 0 == falha.      |
| `database`    |    `string`     |   Nome do banco de dados.      |
| `collection`    |    `string`     |    Nome da coleção.     |
|  `shardKeyDefinition`   |   `document`      |  Documento de especificação de índice usado como chave de fragmento. Este é um parâmetro de resposta opcional.       |
|  `provisionedThroughput`   |   `int`      |    Throughput provisionado para definir na coleção. Este é um parâmetro de resposta opcional.     |

Se o comando falhar, uma resposta de comando personalizado padrão será retornada. Consulte a [saída padrão](#default-output) do comando personalizado para os parâmetros na saída.

### <a name="examples"></a>Exemplos

**Pegue a coleção**

Para obter o objeto de coleta de uma coleção chamada "testCollection", use o seguinte comando:

```shell
use test
db.runCommand({customAction: "GetCollection", collection: "testCollection"});
```

## <a name="default-output-of-a-custom-command"></a><a id="default-output"></a>Saída padrão de um comando personalizado

Se não for especificado, uma resposta personalizada contém um documento com os seguintes campos:

|**Campo**|**Tipo** |**Descrição** |
|---------|---------|---------|
|  `ok`   |    `int`     |   Estado de resposta. 1 == sucesso. 0 == falha.      |
| `code`    |   `int`      |   Só retornou quando o comando falhou (ou seja, ok == 0). Contém o código de erro MongoDB. Este é um parâmetro de resposta opcional.      |
|  `errMsg`   |  `string`      |    Só retornou quando o comando falhou (ou seja, ok == 0). Contém uma mensagem de erro fácil de usar. Este é um parâmetro de resposta opcional.      |

## <a name="next-steps"></a>Próximas etapas

Em seguida, você pode continuar a aprender os seguintes conceitos do Azure Cosmos DB: 

* [Indexação no Azure Cosmos DB](../cosmos-db/index-policy.md)
* [Expirar os dados no Azure Cosmos DB automaticamente com a vida útil](../cosmos-db/time-to-live.md)
