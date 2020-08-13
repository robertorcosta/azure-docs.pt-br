---
title: Exemplos de Python da API do SQL para o Azure Cosmos DB
description: Encontre exemplos do Python no GitHub para tarefas comuns no Azure Cosmos DB, incluindo operações CRUD.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 05/20/2020
ms.author: sngun
ms.custom: devx-track-python
ms.openlocfilehash: 6602d757e210837aadadd431b27bb7cf76f06715
ms.sourcegitcommit: dea88d5e28bd4bbd55f5303d7d58785fad5a341d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2020
ms.locfileid: "87873340"
---
# <a name="azure-cosmos-db-python-examples"></a>Exemplos do Python no Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Exemplos de SDK do .NET V2](sql-api-dotnet-samples.md)
> * [Exemplos de SDK do .NET V3](sql-api-dotnet-v3sdk-samples.md)
> * [Exemplos do SDK do Java V4](sql-api-java-sdk-samples.md)
> * [Exemplos do Node.js](sql-api-nodejs-samples.md)
> * [Exemplos do Python](sql-api-python-samples.md)
> * [Galeria de Exemplos de Código do Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

Soluções de exemplo que realizam operações CRUD e outras operações comuns em recursos do Azure Cosmos DB estão incluídas no repositório GitHub [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python). Esse artigo fornece:

* Links para as tarefas em cada um dos arquivos do projeto de exemplo do Node.js.
* Links para o conteúdo de referência da API relacionada.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- Você pode [ativar benefícios de assinante do Visual Studio](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Todos os meses, sua Assinatura do Visual Studio lhe oferece créditos que podem ser usados para serviços pagos do Azure.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

Também é necessário ter o [SDK do Python](sql-api-sdk-python.md).

   > [!NOTE]
   > Cada exemplo é autossuficiente, eles se configuram e fazem sua limpeza sozinhos. Os exemplos emitem várias chamadas para `CosmosClient.CreateContainer`. Cada vez que isso for feito, sua assinatura é cobrada por uma hora de uso. Para obter mais informações sobre a cobrança do Azure Cosmos DB, veja [Preços do Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

## <a name="database-examples"></a>Exemplos de banco de dados

O exemplo [database_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) do Python mostra como realizar as seguintes tarefas. Para saber mais sobre os bancos de dados Azure Cosmos antes de executar os exemplos a seguir, veja o artigo conceitual [Trabalhando com bancos de dados, contêineres e itens](databases-containers-items.md).

| Tarefa | Referência de API |
| --- | --- |
| [Criar um banco de dados](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient.create_database|
| [Ler um banco de dados por ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient.get_database_client|
| [Consultar os bancos de dados](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient.query_databases|
| [Listar bancos de dados para uma conta](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient.list_databases|
| [Excluir um banco de dados](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient.delete_database|

## <a name="container-examples"></a>Exemplos de contêiner

O exemplo [container_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) do Python mostra como realizar as seguintes tarefas. Para saber mais sobre as coleções do Azure Cosmos antes de executar os exemplos a seguir, veja o artigo conceitual [Trabalhando com bancos de dados, contêineres e itens](databases-containers-items.md).

| Tarefa | Referência de API |
| --- | --- |
| [Consulta de um contêiner](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |database.query_containers |
| [Criar um contêiner](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |database.create_container |
| [Listar todos os contêineres em um banco de dados](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |database.list_containers |
| [Obter um contêiner pela respectiva ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |database.get_container_client |
| [Gerenciar a taxa de transferência provisionada do contêiner](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |container.read_offer, container.replace_throughput|
| [Excluir um contêiner](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |database.delete_container |

## <a name="item-examples"></a>Exemplos de item

O exemplo [item_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) do Python mostra como realizar as seguintes tarefas. Para saber mais sobre os documentos do Azure Cosmos antes de executar os exemplos a seguir, veja o artigo conceitual [Trabalhando com bancos de dados, contêineres e itens](databases-containers-items.md).

| Tarefa | Referência de API |
| --- | --- |
| [Criar itens em um contêiner](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |container.create_item |
| [Ler um item pela respectiva ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |container.read_item |
| [Ler todos os itens em um contêiner](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |container.read_all_items |
| [Consultar um item por sua ID](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |container.query_items |
| [Substituir um item](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |container.replace_items |
| [Fazer upsert de um item](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |container.upsert_item |
| [Excluir um item](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |container.delete_item |
| [Obter o feed de alterações dos itens em um contêiner](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |container.query_items_change_feed |

## <a name="indexing-examples"></a>Exemplos de indexação

O exemplo [index_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) do Python mostra como realizar as seguintes tarefas. Para saber mais sobre a indexação no Azure Cosmos DB antes de executar os exemplos a seguir, consulte os artigos conceituais [políticas de indexação](index-policy.md), [tipos de indexação](index-types.md) e [caminhos de indexação](index-paths.md).

| Tarefa | Referência de API |
| --- | --- |
| [Excluir um item específico da indexação](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [Usar indexação manual com itens específicos indexados](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | documents.IndexingDirective.Include |
| [Excluir caminhos da indexação](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |Definir caminhos a excluir na propriedade `IndexingPolicy` |
| [Usar índices de intervalo em cadeias de caracteres](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | Defina a política de indexação com índices de intervalo em tipo de dados de cadeia de caracteres. `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [Executar uma transformação de índice](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |database.replace_container (usar a política de indexação atualizada)|
| [Usar exames quando houver apenas índice de hash no caminho](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | definir o `enable_scan_in_query=True` e `enable_cross_partition_query=True` ao consultar os itens |
