---
title: 'C#Tutorial: indexar várias fontes de dados'
titleSuffix: Azure Cognitive Search
description: Saiba como importar dados de várias fontes de dados para um único índice Pesquisa Cognitiva do Azure usando indexadores. Este tutorial e código de exemplo estão C#em.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 6408689deec7de365ede86665a0eaeb0bd0de64b
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196562"
---
# <a name="tutorial-index-data-from-multiple-data-sources-in-c"></a>Tutorial: indexar dados de várias fontes de dados noC#

A Pesquisa Cognitiva do Azure pode importar, analisar e indexar dados de várias fontes de dados em um único índice de pesquisa combinado. Isso dá suporte a situações em que os dados estruturados são agregados com os dados menos estruturados ou, até mesmo, de texto sem formatação de outras fontes, como texto, HTML ou documentos JSON.

Este tutorial descreve como indexar dados de hotéis de uma fonte de dados do Azure Cosmos DB e mesclá-los com os detalhes de quartos de hotel extraídos de documentos do Armazenamento de Blobs do Azure. O resultado será um índice combinado de pesquisa de hotéis contendo tipos de dados complexos.

Este tutorial usa C# o e o [SDK do .net](https://aka.ms/search-sdk) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Fazer upload de dados de exemplo e criar fontes de dados
> * Identificar a chave de documento
> * Definir e criar o índice
> * Indexar dados de hotéis do Azure Cosmos DB
> * Mesclar dados de quartos de hotel do Armazenamento de Blobs

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Prerequisites

+ [Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal)
+ [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Visual Studio 2019](https://visualstudio.microsoft.com/)
+ [Criar](search-create-service-portal.md) ou [localizar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Você pode usar o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita você a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Antes de começar, verifique se você tem espaço em seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Baixar arquivos

1. Localize o repositório de exemplo no GitHub: [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).
1. Selecione **Clonar ou baixar** e faça sua cópia local particular do repositório.
1. Abra o Visual Studio 2019 e instale o Microsoft Azure Pesquisa Cognitiva pacote NuGet, se ainda não estiver instalado. No menu **ferramentas** , selecione **Gerenciador de pacotes NuGet** e, em seguida, **gerenciar pacotes NuGet para a solução...** . Na guia **procurar** , localize e instale **o Microsoft. Azure. Search** (versão 9.0.1 ou posterior). Você precisará clicar nas caixas de diálogo adicionais para concluir a instalação.

    ![Como usar o NuGet para adicionar bibliotecas do Azure](./media/tutorial-csharp-create-first-app/azure-search-nuget-azure.png)

1. Usando o Visual Studio, navegue até o repositório local e abra o arquivo de solução **AzureSearchMultipleDataSources.sln**.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

Para interagir com o serviço da Pesquisa Cognitiva do Azure, será necessária a URL de serviço e uma chave de acesso. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou a Pesquisa Cognitiva do Azure à sua assinatura, siga estas etapas para obter as informações necessárias:

1. Entre no [portal do Azure](https://portal.azure.com/)e, em sua página de **visão geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que a manipula.

## <a name="prepare-sample-azure-cosmos-db-data"></a>Preparar dados de exemplo do Azure Cosmos DB

Esta amostra usa dois conjuntos de dados pequenos que descrevem sete hotéis fictícios. Um conjunto descreve os hotéis em si e será carregado em um banco de dados do Azure Cosmos DB. O outro conjunto contém detalhes de quartos de hotel e é fornecido como sete arquivos JSON separados a serem carregados no Armazenamento de Blobs do Azure.

1. Entre no [portal do Azure](https://portal.azure.com)e, em seguida, navegue na página Visão geral da conta do Azure Cosmos DB.

1. Selecione **Data Explorer** e, em seguida, selecione **novo banco de dados**.

   ![Criar um novo banco de dados](media/tutorial-multiple-data-sources/cosmos-newdb.png "Criar um novo banco de dados")

1. Insira o nome **Hotel-Rooms-DB**. Aceite os valores padrão para as configurações restantes.

   ![Configurar banco de dados](media/tutorial-multiple-data-sources/cosmos-dbname.png "Configurar banco de dados")

1. Criar um novo contêiner. Use o banco de dados existente que você acabou de criar. Insira **Hotéis** para o nome do contêiner e use **/HotelId** para a chave de partição.

   ![Adicionar contêiner](media/tutorial-multiple-data-sources/cosmos-add-container.png "Adicionar contêiner")

1. Selecione **os itens** em **Hotéis**e clique em **carregar item** na barra de comandos. Navegue até e selecione o arquivo **cosmosdb/HotelsDataSubset_CosmosDb. JSON** na pasta do projeto.

   ![Carregar na coleção do Azure Cosmos DB](media/tutorial-multiple-data-sources/cosmos-upload.png "Carregar na coleção do Cosmos DB")

1. Use o botão Atualizar para atualizar a exibição dos itens na coleção de hotéis. Você deverá ver sete novos documentos de banco de dados listados.

## <a name="prepare-sample-blob-data"></a>Preparar dados de blob de exemplo

1. Entre no [portal do Azure](https://portal.azure.com), navegue até sua conta de armazenamento do Azure, clique em **BLOBs**e, em seguida, clique em **+ contêiner**.

1. [Crie um contêiner de blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) chamado **hotel-rooms** para armazenar os arquivos JSON de quartos de hotel de exemplo. Você pode definir o Nível de Acesso Público para qualquer um de seus valores válidos.

   ![Crie um contêiner de blob](media/tutorial-multiple-data-sources/blob-add-container.png "Criar um contêiner de blob")

1. Depois que o contêiner for criado, abra-o e selecione **Carregar** na barra de comandos. Navegue até a pasta que contém os arquivos de exemplo. Selecione todos eles e, em seguida, clique em **Carregar**.

   ![Carregar arquivos](media/tutorial-multiple-data-sources/blob-upload.png "Carregar arquivos")

Após a conclusão do upload, os arquivos deverão ser exibidos na lista do contêiner de dados.

## <a name="set-up-connections"></a>Configurar as conexões

As informações de conexão para o serviço de pesquisa e as fontes de dados são especificadas no arquivo **appsettings.json** na solução. 

1. No Visual Studio, abra o arquivo **AzureSearchMultipleDataSources.sln**.

1. No Gerenciador de Soluções, edite o arquivo **appsettings.json**.  

```json
{
  "SearchServiceName": "Put your search service name here",
  "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
  "BlobStorageAccountName": "Put your Azure Storage account name here",
  "BlobStorageConnectionString": "Put your Azure Blob Storage connection string here",
  "CosmosDBConnectionString": "Put your Cosmos DB connection string here",
  "CosmosDBDatabaseName": "hotel-rooms-db"
}
```

As duas primeiras entradas usam a URL e as chaves de administração do serviço da Pesquisa Cognitiva do Azure. Considerando o ponto de extremidade `https://mydemo.search.windows.net`, o nome do serviço a ser fornecido é `mydemo`.

As próximas entradas especificam os nomes de conta e as informações de cadeia de conexão do Armazenamento de Blobs do Azure, bem como as fontes de dados do Azure Cosmos DB.

### <a name="identify-the-document-key"></a>Identificar a chave de documento

Na Pesquisa Cognitiva do Azure, o campo de chave identifica exclusivamente cada documento no índice. Cada índice de pesquisa deve ter exatamente um campo de chave do tipo `Edm.String`. Esse campo de chave precisa estar presente para cada documento em uma fonte de dados que é adicionada ao índice. (Na verdade, ele é o único campo obrigatório.)

Ao indexar dados de várias fontes de dados, use uma chave de documento comum para mesclar dados de dois documentos de origem fisicamente distintos em um novo documento de pesquisa no índice combinado. Geralmente, é necessário um planejamento antecipado para identificar uma chave de documento significativa para o índice e verificar se ele existe em ambas as fontes de dados. Nesta demonstração, a chave do Hotelid para cada hotel em Cosmos DB também está presente nos BLOBs JSON de salas no armazenamento de BLOBs.

Os indexadores da Pesquisa Cognitiva do Azure podem usar mapeamentos de campo para renomear e, até mesmo, reformatar os campos de dados durante o processo de indexação, de modo que os dados de origem possam ser direcionados para o campo de índice correto.

Por exemplo, em nossos dados de Azure Cosmos DB de exemplo, o identificador do Hotel é chamado de **`HotelId`** . Mas nos arquivos de blob JSON para as salas de Hotel, o identificador do Hotel é denominado **`Id`** . O programa manipula isso mapeando o campo de **`Id`** dos BLOBs para o campo de chave de **`HotelId`** no índice.

> [!NOTE]
> Na maioria dos casos, as chaves de documento geradas automaticamente, como aquelas criadas por padrão por alguns indexadores, não servem como chaves de documento úteis para índices combinados. Em geral, o recomendável é usar um valor de chave significativo e exclusivo que já exista nas fontes de dados ou que possa ser adicionado a elas com facilidade.

## <a name="understand-the-code"></a>Compreender o código

Depois que os dados e as definições configuração estiverem em vigor, o programa de exemplo em **AzureSearchMultipleDataSources.sln** deverá estar pronto para ser compilado e executado.

Este aplicativo de console C#/.NET simples realiza as seguintes tarefas:
* Cria um novo índice com base na estrutura de dados da C# classe Hotel (que também faz referência às classes address e Room).
* Cria uma nova fonte de dados e um indexador que mapeia Azure Cosmos DB dados para campos de índice. Esses são os dois objetos no Azure Pesquisa Cognitiva.
* Executa o indexador para carregar dados de Hotel de Cosmos DB.
* Cria uma segunda fonte de dados e um indexador que mapeia dados de blob JSON para campos de índice.
* Executa o segundo indexador para carregar dados de salas do armazenamento de BLOBs.

 Antes de executar o programa, reserve um minuto para estudar o código e as definições de índice e indexador desta amostra. O código relevante encontra-se em dois arquivos:

  + **Hotel.cs** contém o esquema que define o índice
  + **Program.cs** contém funções que criam o índice, as fontes de dados e os indexadores da Pesquisa Cognitiva do Azure e carrega os resultados combinados no índice.

### <a name="define-the-index"></a>Definir o índice

Este programa de exemplo usa o SDK do .NET para definir e criar um índice da Pesquisa Cognitiva do Azure. Ele aproveita a classe [FieldBuilder](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.fieldbuilder) para gerar uma estrutura de índice de uma classe de modelo de dados C#.

O modelo de dados é definido pela classe Hotel, que também contém referências às classes Address e Room. O FieldBuilder faz uma busca detalhada em várias definições de classe para gerar uma estrutura de dados complexa para o índice. As marcas de metadados são usadas para definir os atributos de cada campo, como se ele é pesquisável ou classificável.

Os snippets a seguir do arquivo **Hotel.cs** mostram como um único campo e uma referência a outra classe de modelo de dados podem ser especificados.

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
public Room[] Rooms { get; set; }
. . .
```

No arquivo **Program.cs**, o índice é definido com um nome e uma coleção de campos gerada pelo método `FieldBuilder.BuildForType<Hotel>()` e, em seguida, é criado da seguinte maneira:

```csharp
private static async Task CreateIndex(string indexName, SearchServiceClient searchService)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address and Room classes are referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    var definition = new Index()
    {
        Name = indexName,
        Fields = FieldBuilder.BuildForType<Hotel>()
    };
    await searchService.Indexes.CreateAsync(definition);
}
```

### <a name="create-azure-cosmos-db-data-source-and-indexer"></a>Criar uma fonte de dados e um indexador do Azure Cosmos DB

Em seguida, o programa principal inclui a lógica para criar a fonte de dados do Azure Cosmos DB para os dados de hotéis.

Primeiro, ele concatena o nome do banco de dados do Azure Cosmos DB com a cadeia de conexão. Em seguida, ele define o objeto de fonte de dados, incluindo configurações específicas de fontes do Azure Cosmos DB, como a propriedade [useChangeDetection].

  ```csharp
private static async Task CreateAndRunCosmosDbIndexer(string indexName, SearchServiceClient searchService)
{
    // Append the database name to the connection string
    string cosmosConnectString = 
        configuration["CosmosDBConnectionString"]
        + ";Database=" 
        + configuration["CosmosDBDatabaseName"];

    DataSource cosmosDbDataSource = DataSource.CosmosDb(
        name: configuration["CosmosDBDatabaseName"], 
        cosmosDbConnectionString: cosmosConnectString,
        collectionName: "hotels",
        useChangeDetection: true);

    // The Azure Cosmos DB data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(cosmosDbDataSource);
  ```

Depois que a fonte de dados é criada, o programa configura um indexador do Azure Cosmos DB chamado **hotel-rooms-cosmos-indexer**.

```csharp
    Indexer cosmosDbIndexer = new Indexer(
        name: "hotel-rooms-cosmos-indexer",
        dataSourceName: cosmosDbDataSource.Name,
        targetIndexName: indexName,
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
    
    // Indexers keep metadata about how much they have already indexed.
    // If we already ran this sample, the indexer will remember that it already
    // indexed the sample data and not run again.
    // To avoid this, reset the indexer if it exists.
    bool exists = await searchService.Indexers.ExistsAsync(cosmosDbIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(cosmosDbIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(cosmosDbIndexer);
```
O programa excluirá os indexadores existentes com o mesmo nome antes de criar outro, caso você deseje executar este exemplo mais de uma vez.

Este exemplo define um agendamento para o indexador, de modo que ele seja executado uma vez por dia. Você poderá remover a propriedade de agendamento desta chamada se não desejar que o indexador seja executado de forma automática novamente no futuro.

### <a name="index-azure-cosmos-db-data"></a>Indexar dados do Azure Cosmos DB

Depois que a fonte de dados e o indexador forem criados, o código que executa o indexador será breve:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Este exemplo inclui um bloco simples try-catch para relatar os erros que podem ocorrer durante a execução.

Depois que o indexador do Azure Cosmos DB for executado, o índice de pesquisa conterá um conjunto completo de documentos de hotéis de exemplo. No entanto, o campo de quartos de cada hotel será uma matriz vazia, pois a fonte de dados do Azure Cosmos DB não continha nenhum detalhe de quarto. Em seguida, o programa efetuará pull do Armazenamento de Blobs para carregar e mesclar os dados de quartos.

### <a name="create-blob-storage-data-source-and-indexer"></a>Criar uma fonte de dados e um indexador do Armazenamento de Blobs

Para obter os detalhes de quartos, o programa primeiro configura uma fonte de dados do Armazenamento de Blobs para referenciar um conjunto de arquivos de blob JSON individuais.

```csharp
private static async Task CreateAndRunBlobIndexer(string indexName, SearchServiceClient searchService)
{
    DataSource blobDataSource = DataSource.AzureBlobStorage(
        name: configuration["BlobStorageAccountName"],
        storageConnectionString: configuration["BlobStorageConnectionString"],
        containerName: "hotel-rooms");

    // The blob data source does not need to be deleted if it already exists,
    // but the connection string might need to be updated if it has changed.
    await searchService.DataSources.CreateOrUpdateAsync(blobDataSource);
```

Depois que a fonte de dados é criada, o programa configura um indexador de blob chamado **hotel-rooms-blob-indexer**.

```csharp
    // Add a field mapping to match the Id field in the documents to 
    // the HotelId key field in the index
    List<FieldMapping> map = new List<FieldMapping> {
        new FieldMapping("Id", "HotelId")
    };

    Indexer blobIndexer = new Indexer(
        name: "hotel-rooms-blob-indexer",
        dataSourceName: blobDataSource.Name,
        targetIndexName: indexName,
        fieldMappings: map,
        parameters: new IndexingParameters().ParseJson(),
        schedule: new IndexingSchedule(TimeSpan.FromDays(1)));

    // Reset the indexer if it already exists
    bool exists = await searchService.Indexers.ExistsAsync(blobIndexer.Name);
    if (exists)
    {
        await searchService.Indexers.ResetAsync(blobIndexer.Name);
    }
    await searchService.Indexers.CreateOrUpdateAsync(blobIndexer);
```

Os BLOBs JSON contêm um campo de chave chamado **`Id`** em vez de **`HotelId`** . O código usa a classe `FieldMapping` para instruir o indexador a direcionar o valor do campo **`Id`** para a chave do documento **`HotelId`** no índice.

Os indexadores do Armazenamento de Blobs podem usar parâmetros que identificam o modo de análise a ser usado. O modo de análise varia para blobs que representam um único documento ou vários documentos no mesmo blob. Neste exemplo, cada blob representa um único documento de índice e, portanto, o código usa o parâmetro `IndexingParameters.ParseJson()`.

Para obter mais informações sobre parâmetros de análise de indexador para blobs JSON, confira [Indexar blobs JSON](search-howto-index-json-blobs.md). Para obter mais informações sobre como especificar esses parâmetros usando o SDK do .NET, confira a classe [IndexerParametersExtension](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexingparametersextensions).

O programa excluirá os indexadores existentes com o mesmo nome antes de criar outro, caso você deseje executar este exemplo mais de uma vez.

Este exemplo define um agendamento para o indexador, de modo que ele seja executado uma vez por dia. Você poderá remover a propriedade de agendamento desta chamada se não desejar que o indexador seja executado de forma automática novamente no futuro.

### <a name="index-blob-data"></a>Indexar dados de blob

Depois que a fonte de dados e o indexador do Armazenamento de Blobs forem criados, o código que executa o indexador será simples:

```csharp
    try
    {
        await searchService.Indexers.RunAsync(cosmosDbIndexer.Name);
    }
    catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
    {
        Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
    }
```

Como o índice já foi populado com os dados de hotéis do banco de dados do Azure Cosmos DB, o indexador de blob atualiza os documentos existentes no índice e adiciona os detalhes de quartos.

> [!NOTE]
> Se você tiver os mesmos campos não chave em ambas as fontes de dados e os dados contidos nesses campos não forem correspondentes, o índice conterá os valores de qualquer indexador executado mais recentemente. Em nosso exemplo, ambas as fontes de dados contêm um campo **HotelName**. Se, por alguma razão, os dados desse campo forem diferentes, para documentos com o mesmo valor de chave, os dados de **HotelName** da fonte de dados que foi indexada mais recentemente serão o valor armazenado no índice.

## <a name="search-your-json-files"></a>Pesquisar os arquivos JSON

Explore o índice de pesquisa populado após a execução do programa usando o [**Search Explorer**](search-explorer.md) no portal.

No portal do Azure, abra a página **Visão Geral** do serviço de pesquisa e localize o índice **hotel-rooms-sample** na lista **Índices**.

  ![Lista de índices da Pesquisa Cognitiva do Azure](media/tutorial-multiple-data-sources/index-list.png "Lista de índices da Pesquisa Cognitiva do Azure")

Clique no índice hotel-rooms-sample na lista. Você verá uma interface do Search Explorer para o índice. Insira uma consulta para um termo como "Luxo". Você deverá ver, pelo menos, um documento nos resultados, o qual deverá mostrar uma lista de objetos de quarto em sua matriz de quartos.

## <a name="reset-and-rerun"></a>Redefinir e execute novamente

Nos estágios experimentais antecipados do desenvolvimento, a abordagem mais prática para a iteração de design é excluir os objetos do Azure Pesquisa Cognitiva e permitir que seu código os reconstrua. Nomes de recurso são exclusivos. Excluir um objeto permite que você recriá-la usando o mesmo nome.

O código de exemplo para este tutorial verifica os objetos existentes e os exclui para que você possa executar novamente o código.

Você também pode usar o portal para excluir índices, indexadores e fontes de dados.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, no final de um projeto, é uma boa ideia remover os recursos que já não são necessários. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode encontrar e gerenciar recursos no portal, usando o link todos os recursos ou grupos de recursos no painel de navegação esquerdo.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com o conceito de ingestão de dados de várias fontes, vamos examinar mais de perto a configuração do indexador, começando com Cosmos DB.

> [!div class="nextstepaction"]
> [Configurar um indexador Azure Cosmos DB](search-howto-index-cosmosdb.md)