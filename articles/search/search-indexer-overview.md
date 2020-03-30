---
title: Indexadores para dados de rastreamento durante a importação
titleSuffix: Azure Cognitive Search
description: O banco de dados Crawl Azure SQL, o Azure Cosmos DB ou o Azure armazenamento para extrair dados pesquisáveis e preencher um índice de Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2719bba0e88ba3125bd5ba163804e31885b286a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282985"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexadores na Pesquisa Cognitiva do Azure

Um *indexador* no Azure Cognitive Search é um rastreador que extrai dados e metadados pesquisáveis de uma fonte de dados externa do Azure e preenche um índice com base em mapeamentos de campo para campo entre o índice e sua fonte de dados. Essa abordagem às vezes é referida como um "modelo de tração" porque o serviço puxa dados sem que você tenha que escrever qualquer código que adicione dados a um índice.

Os indexadores são baseados em tipos ou plataformas de origem de dados, com indexadores individuais para SQL Server no Azure, Cosmos DB, Azure Table Storage e Blob Storage. Os indexadores de armazenamento Blob têm propriedades adicionais específicas para tipos de conteúdo blob.

Você pode usar um indexador como o único meio para ingestão de dados ou usar uma combinação de técnicas que incluam o uso de um indexador para carregar apenas alguns dos campos no índice.

Você pode executar indexadores demanda ou em um cronograma de atualização de dados recorrente que é executado com tanta freqüência quanto a cada cinco minutos. Atualizações mais freqüentes requerem um modelo de push que atualiza simultaneamente dados tanto na Pesquisa Cognitiva do Azure quanto na sua fonte de dados externos.

## <a name="approaches-for-creating-and-managing-indexers"></a>Abordagens para criar e gerenciar indexadores

Você pode criar e gerenciar indexadores usando estas abordagens:

* [Assistente de dados de importação de > do Portal](search-import-data-portal.md)
* [API REST do Serviço](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Inicialmente, um novo indexador é anunciado como uma versão prévia do recurso. As versões prévias do recurso são introduzidas em APIs (REST e .NET) e então integradas ao portal após a graduação para a disponibilidade geral. Se você estiver avaliando um novo indexador, deverá planejar escrever código.

## <a name="permissions"></a>Permissões

Todas as operações relacionadas aos indexadores, incluindo solicitações GET para status ou definições, requerem [um api-key de admin](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Fontes de dados com suporte

Armazenamentos de dados de rastreamento de indexadores no Azure.

* [Armazenamento azure Blob](search-howto-indexing-azure-blob-storage.md)
* [Azure Data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (em pré-visualização)
* [Armazenamento de mesa azure](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Banco de dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [SQL Server nas Máquinas Virtuais do Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [Instâncias gerenciadas do SQL no Azure](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>Etapas da configuração básica
Os indexadores podem oferecer recursos que são exclusivos da fonte de dados. Nesse sentido, alguns aspectos de configuração da fonte de dados ou do indexador variam de acordo com o tipo de indexador. No entanto, todos os indexadores compartilham a mesma composição básica e os mesmos requisitos. As etapas que são comuns a todos os indexadores são abordadas a seguir.

### <a name="step-1-create-a-data-source"></a>Etapa 1: Criar uma fonte de dados
Um indexador obtém conexão de origem de dados a partir de um objeto *de origem de dados.* A definição de fonte de dados fornece uma seqüência de conexão e possivelmente credenciais. Chame o [criar fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) API REST ou [classe DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) para criar o recurso.

As fontes de dados são configuradas e gerenciadas independentemente dos indexadores que as utilizam, o que significa que uma fonte de dados pode ser usada por vários indexadores para carregar mais de um índice por vez.

### <a name="step-2-create-an-index"></a>Etapa 2: Criar um índice
Um indexador automatizará algumas tarefas relacionadas à ingestão de dados, mas a criação de um índice não é uma delas. Como pré-requisito, você deve ter um índice predefinido com campos iguais aos da sua fonte de dados externa. Os campos precisam corresponder por nome e tipo de dados. Para obter mais informações sobre a estruturação de um índice, consulte [Criar um Índice (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) ou [classe Index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Para obter ajuda com associações de campo, consulte [mapeamentos de campo em indexadores de pesquisa cognitiva do Azure](search-indexer-field-mappings.md).

> [!Tip]
> Embora indexadores não consigam gerar um índice para você, o assistente de **Importar dados** no portal pode ajudar. Na maioria dos casos, o assistente pode inferir um esquema de índice a partir de metadados existentes na fonte, apresentando um esquema de índice preliminar que você pode editar em linha enquanto o assistente estiver ativo. Assim que o índice é criado no serviço, outras edições no portal são, na sua maior parte, limitadas a adicionar novos campos. Leve o assistente em consideração para criar, mas não para revisar um índice. Para o aprendizado prático, percorra o [passo a passo portal](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Etapa 3: Criar e agendar o indexador
A definição do indexador é um construto que reúne todos os elementos relacionados à ingestão de dados. Os elementos necessários incluem uma fonte de dados e índice. Os elementos opcionais incluem um cronograma e mapeamentos de campo. O mapeamento de campo só é opcional se os campos de origem e os campos de índice corresponderem claramente. Para obter mais informações sobre a estruturação de um indexador, consulte [Criar Indexador (API de pesquisa cognitiva do Azure)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Executar indexadores demanda

Embora seja comum agendar indexação, um indexador também pode ser invocado demanda usando o [comando Executar](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Quando a API de Execução for retornada com êxito, isso indica que a invocação do indexador foi agendada, mas o processamento real ocorre de forma assíncrona. 

Você pode monitorar o status do indexador no portal ou através da API Get Indexer Status. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Obter status de indexador

Você pode recuperar o histórico de status e execução de um indexador através do [comando Obter status do indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):


    GET https://[service name].search.windows.net/indexers/[indexer name]/status?api-version=2019-05-06
    api-key: [Search service admin key]

A resposta contém o status geral do indexador, a última invocação (ou em andamento) do indexador e o histórico de invocações recentes do indexador.

    {
        "status":"running",
        "lastResult": {
            "status":"success",
            "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
         },
        "executionHistory":[ {
            "status":"success",
             "errorMessage":null,
            "startTime":"2018-11-26T03:37:18.853Z",
            "endTime":"2018-11-26T03:37:19.012Z",
            "errors":[],
            "itemsProcessed":11,
            "itemsFailed":0,
            "initialTrackingState":null,
            "finalTrackingState":null
        }]
    }

O histórico de execução contém até as 50 execuções mais recentes, que são classificadas em ordem cronológica inversa (de modo que a execução mais recente fique em primeiro lugar).

## <a name="next-steps"></a>Próximas etapas
Agora que você tem as noções básicas, a próxima etapa é examinar os requisitos e as tarefas específicas para cada tipo de fonte de dados.

* [Banco de Dados SQL do Azure ou SQL Server em uma máquina virtual do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Armazenamento azure Blob](search-howto-indexing-azure-blob-storage.md)
* [Armazenamento de mesa azure](search-howto-indexing-azure-tables.md)
* [Indexação de bolhas CSV usando o indexador Azure Cognitive Search Blob](search-howto-index-csv-blobs.md)
* [Indexação de bolhas JSON com indexador Azure Cognitive Search Blob](search-howto-index-json-blobs.md)
