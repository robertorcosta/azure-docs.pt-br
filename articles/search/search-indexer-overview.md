---
title: Indexadores para rastreamento de dados durante a importação
titleSuffix: Azure Cognitive Search
description: Rastreie o banco de dados SQL do Azure, o Azure Cosmos DB ou o armazenamento do Azure para extrair e popular um índice de Pesquisa Cognitiva do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2719bba0e88ba3125bd5ba163804e31885b286a2
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/26/2019
ms.locfileid: "74534521"
---
# <a name="indexers-in-azure-cognitive-search"></a>Indexadores no Azure Pesquisa Cognitiva

Um *indexador* no Azure pesquisa cognitiva é um rastreador que extrai dados pesquisáveis e metadados de uma fonte de dados externa do Azure e popula um índice com base em mapeamentos de campo para campo entre o índice e sua fonte de dados. Essa abordagem, às vezes, é chamada de "modelo de pull" porque o serviço recebe dados sem a necessidade de escrever nenhum código que adicione dados a um índice.

Os indexadores são baseados em tipos de fontes de dados ou plataformas, com indexadores individuais para SQL Server no Azure, Cosmos DB, armazenamento de tabelas do Azure e armazenamento de BLOBs. Indexadores de armazenamento de BLOBs têm propriedades adicionais específicas para tipos de conteúdo de BLOB.

Você pode usar um indexador como o único meio para ingestão de dados ou usar uma combinação de técnicas que incluam o uso de um indexador para carregar apenas alguns dos campos no índice.

Você pode executar indexadores sob demanda ou em uma agenda de atualização de dados recorrente que é executada sempre que a cada cinco minutos. Atualizações mais frequentes exigem um modelo de push que atualiza dados simultaneamente no Azure Pesquisa Cognitiva e na fonte de dados externa.

## <a name="approaches-for-creating-and-managing-indexers"></a>Abordagens para criar e gerenciar indexadores

Você pode criar e gerenciar indexadores usando estas abordagens:

* [Assistente de > importar dados do portal](search-import-data-portal.md)
* [API REST do Serviço](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* [SDK .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.iindexersoperations)

Inicialmente, um novo indexador é anunciado como uma versão prévia do recurso. As versões prévias do recurso são introduzidas em APIs (REST e .NET) e então integradas ao portal após a graduação para a disponibilidade geral. Se você estiver avaliando um novo indexador, deverá planejar escrever código.

## <a name="permissions"></a>permissões

Todas as operações relacionadas a indexadores, incluindo solicitações GET para status ou definições, exigem uma [chave de API de administração](search-security-api-keys.md). 

<a name="supported-data-sources"></a>

## <a name="supported-data-sources"></a>Fontes de dados com suporte

Armazenamentos de dados de rastreamento de indexadores no Azure.

* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Azure data Lake Storage Gen2](search-howto-index-azure-data-lake-storage.md) (em versão prévia)
* [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
* [Azure Cosmos DB](search-howto-index-cosmosdb.md)
* [Banco de Dados SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [SQL Server nas Máquinas Virtuais do Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
* [Instâncias gerenciadas do SQL no Azure](search-howto-connecting-azure-sql-mi-to-azure-search-using-indexers.md)

## <a name="basic-configuration-steps"></a>Etapas da configuração básica
Os indexadores podem oferecer recursos que são exclusivos da fonte de dados. Nesse sentido, alguns aspectos de configuração da fonte de dados ou do indexador variam de acordo com o tipo de indexador. No entanto, todos os indexadores compartilham a mesma composição básica e os mesmos requisitos. As etapas que são comuns a todos os indexadores são abordadas a seguir.

### <a name="step-1-create-a-data-source"></a>Etapa 1: Criar uma fonte de dados
Um indexador Obtém a conexão da fonte de dados de um objeto de *fonte de dados* . A definição da fonte de dados fornece uma cadeia de conexão e possivelmente credenciais. Chame o [criar fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) API REST ou [classe DataSource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource) para criar o recurso.

As fontes de dados são configuradas e gerenciadas independentemente dos indexadores que as utilizam, o que significa que uma fonte de dados pode ser usada por vários indexadores para carregar mais de um índice por vez.

### <a name="step-2-create-an-index"></a>Etapa 2: Criar um índice
Um indexador automatizará algumas tarefas relacionadas à ingestão de dados, mas a criação de um índice não é uma delas. Como pré-requisito, você deve ter um índice predefinido com campos iguais aos da sua fonte de dados externa. Os campos precisam corresponder por nome e tipo de dados. Para obter mais informações sobre como estruturar um índice, consulte [criar um índice (API REST do Azure pesquisa cognitiva)](https://docs.microsoft.com/rest/api/searchservice/Create-Index) ou [classe de índice](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index). Para obter ajuda com associações de campo, consulte [mapeamentos de campo no Azure pesquisa cognitiva indexadores](search-indexer-field-mappings.md).

> [!Tip]
> Embora indexadores não consigam gerar um índice para você, o assistente de **Importar dados** no portal pode ajudar. Na maioria dos casos, o assistente pode inferir um esquema de índice a partir de metadados existentes na fonte, apresentando um esquema de índice preliminar que você pode editar em linha enquanto o assistente estiver ativo. Assim que o índice é criado no serviço, outras edições no portal são, na sua maior parte, limitadas a adicionar novos campos. Leve o assistente em consideração para criar, mas não para revisar um índice. Para o aprendizado prático, percorra o [passo a passo portal](search-get-started-portal.md).

### <a name="step-3-create-and-schedule-the-indexer"></a>Etapa 3: Criar e agendar o indexador
A definição do indexador é um constructo que reúne todos os elementos relacionados à ingestão de dados. Os elementos necessários incluem uma fonte de dados e um índice. Os elementos opcionais incluem um agendamento e mapeamentos de campo. O mapeamento de campos só será opcional se os campos de origem e de índice corresponderem claramente. Para obter mais informações sobre como estruturar um indexador, consulte [criar indexador (API REST do Azure pesquisa cognitiva)](https://docs.microsoft.com/rest/api/searchservice/Create-Indexer).

<a id="RunIndexer"></a>

## <a name="run-indexers-on-demand"></a>Executar indexadores sob demanda

Embora seja comum agendar a indexação, um indexador também pode ser invocado sob demanda usando o [comando executar](https://docs.microsoft.com/rest/api/searchservice/run-indexer):

    POST https://[service name].search.windows.net/indexers/[indexer name]/run?api-version=2019-05-06
    api-key: [Search service admin key]

> [!NOTE]
> Quando a API de Execução for retornada com êxito, isso indica que a invocação do indexador foi agendada, mas o processamento real ocorre de forma assíncrona. 

Você pode monitorar o status do indexador no portal ou por meio da API obter status do indexador. 

<a name="GetIndexerStatus"></a>

## <a name="get-indexer-status"></a>Obter o status do indexador

Você pode recuperar o status e o histórico de execução de um indexador por meio do [comando obter status do indexador](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status):


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
* [Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
* [Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
* [Indexando BLOBs CSV usando o indexador de blob Pesquisa Cognitiva do Azure](search-howto-index-csv-blobs.md)
* [Indexando BLOBs JSON com o indexador de blob Pesquisa Cognitiva do Azure](search-howto-index-json-blobs.md)
