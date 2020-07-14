---
title: Tutorial de C# sobre a indexação de dados SQL do Azure
titleSuffix: Azure Cognitive Search
description: Neste tutorial do C#, conecte o Banco de Dados SQL do Azure, extraia os dados pesquisáveis e carregue-os em um índice do Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 06/23/2020
ms.openlocfilehash: a3a7657aa83a675982adc304de01ba0fcc26d193
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/07/2020
ms.locfileid: "86045443"
---
# <a name="tutorial-index-azure-sql-data-using-the-net-sdk"></a>Tutorial: Indexar dados SQL do Azure usando o SDK do .NET

Configure um [indexador](search-indexer-overview.md) para extrair dados pesquisáveis do Banco de Dados SQL do Azure, enviando-os para um índice de pesquisa no Azure Cognitive Search. 

Este tutorial usa o C# e o [SDK do .NET](https://docs.microsoft.com/dotnet/api/overview/azure/search) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Criar uma fonte de dados que se conecta ao Banco de Dados SQL do Azure
> * Criar um indexador
> * Executar um indexador para carregar dados em um índice
> * Consultar um índice como uma etapa de verificação

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

+ [Banco de Dados SQL do Azure](https://azure.microsoft.com/services/sql-database/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Use o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita você a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Antes de começar, reserve um espaço no seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Baixar arquivos

O código-fonte deste tutorial se encontra na pasta [DotNetHowToIndexer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToIndexers) do repositório GitHub [Azure-Samples/search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started).

## <a name="1---create-services"></a>1 – Criar serviços

Este tutorial usa o Azure Cognitive Search para indexação e consultas e o Banco de Dados SQL do Azure como uma fonte de dados externa. Se possível, crie ambos os serviços na mesma região e no mesmo grupo de recursos para facilitar a proximidade e a capacidade de gerenciamento. Na prática, o Banco de Dados SQL do Azure pode estar em qualquer região.

### <a name="start-with-azure-sql-database"></a>Começar com o Banco de Dados SQL do Azure

Nesta etapa, crie uma fonte de dados externa no Banco de Dados SQL do Azure que possa ser rastreada por um indexador. Você pode usar o portal do Azure e o arquivo *hotels.sql* do download de exemplo para criar o conjunto de dados no Banco de Dados SQL do Azure. A Pesquisa Cognitiva do Azure consome conjuntos de linhas bidimensionais, como aqueles gerados em uma exibição ou consulta. O arquivo SQL na solução de exemplo cria e preenche uma única tabela.

Caso você tenha um recurso existente do Banco de Dados SQL do Azure, adicione a tabela de hotéis a ele, começando na etapa 4.

1. [Entre no Portal do Azure](https://portal.azure.com/).

1. Encontre ou crie um **Banco de Dados SQL**. Você pode usar os padrões e o tipo de preço com o valor mais baixo. Uma vantagem de criar um servidor é que você pode especificar um nome de usuário e senha do administrador, necessários para criar e carregar tabelas em uma etapa posterior.

   ![Página Novo banco de dados](./media/search-indexer-tutorial/indexer-new-sqldb.png "Nova página de banco de dados")

1. Clique em **Examinar + criar** para implantar o novo servidor e o banco de dados. Aguarde a implantação do servidor e do banco de dados.

1. No painel de navegação, clique em **Editor de consultas (versão prévia)** e insira o nome de usuário e a senha do administrador do servidor. 

   Se o acesso for negado, copie o endereço IP do cliente na mensagem de erro e, em seguida, clique no link **Definir firewall do servidor** para adicionar uma regra que permita o acesso do computador cliente, usando o IP do cliente para o intervalo. Podem ser necessários vários minutos para que a regra entre em vigor.

1. No Editor de consultas, clique em **Abrir consulta** e navegue até a localização do arquivo *hotels.sql* no computador local. 

1. Selecione o arquivo e clique em **Abrir**. O script deve ser semelhante à captura de tela a seguir:

   ![Script SQL](./media/search-indexer-tutorial/sql-script.png "Script SQL")

1. Clique em **Executar** para executar a consulta. No painel Resultados, você verá uma mensagem de consulta bem-sucedida, para 3 linhas.

1. Para retornar um conjunto de linhas desta tabela, você pode executar a consulta a seguir como uma etapa de verificação:

    ```sql
    SELECT * FROM Hotels
    ```

1. Copie a cadeia de conexão ADO.NET do banco de dados. Em **Configurações** > **Cadeias de Conexão**, copie a cadeia de conexão ADO.NET de maneira semelhante ao exemplo abaixo.

    ```sql
    Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;
    ```

Você precisará dessa cadeia de conexão no próximo exercício, configurando seu ambiente.

### <a name="azure-cognitive-search"></a>Pesquisa Cognitiva do Azure

O próximo componente é o Azure Cognitive Search, que pode ser [criado no portal](search-create-service-portal.md). Use a Camada gratuita para concluir este passo a passo. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obter uma URL e uma chave de API de administração para a Pesquisa Cognitiva do Azure

As chamadas à API exigem a URL do serviço e uma chave de acesso. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou a Pesquisa Cognitiva do Azure à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

   ![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

## <a name="2---set-up-your-environment"></a>2 – Configurar o ambiente

1. Inicie o Visual Studio e abra **DotNetHowToIndexers.sln**.

1. No Gerenciador de Soluções, abra **appsettings.json** para fornecer informações de conexão.

1. Para `searchServiceName`, se a URL completa for "https://my-demo-service.search.windows.net", o nome do serviço a ser fornecido será "my-demo-service".

1. Por `AzureSqlConnectionString`, o formato da cadeia de caracteres é semelhante a este: `"Server=tcp:{your_dbname}.database.windows.net,1433;Initial Catalog=hotels-db;Persist Security Info=False;User ID={your_username};Password={your_password};MultipleActiveResultSets=False;Encrypt=True;TrustServerCertificate=False;Connection Timeout=30;"`

    ```json
    {
      "SearchServiceName": "<placeholder-Azure-Search-service-name>",
      "SearchServiceAdminApiKey": "<placeholder-admin-key-for-Azure-Search>",
      "AzureSqlConnectionString": "<placeholder-ADO.NET-connection-string",
    }
    ```

1. Na cadeia de conexão, verifique se a cadeia de conexão contém uma senha válida. Embora os nomes de banco de dados e de usuário sejam copiados, a senha deverá ser inserida manualmente.

## <a name="3---create-the-pipeline"></a>3 – Criar o pipeline

Os indexadores exigem um objeto de fonte de dados e um índice. O código relevante encontra-se em dois arquivos:

  + **hotel.cs**, que contém um esquema que define o índice
  + **Program.cs**, que contém funções para criar e gerenciar estruturas no seu serviço

### <a name="in-hotelcs"></a>Em hotel.cs

O esquema de índice define a coleção de campos, incluindo atributos especificando as operações permitidas, por exemplo, se um campo é de texto completo que pode ser pesquisado, filtrado ou classificado, conforme mostrado na definição de campo a seguir para HotelName. 

```csharp
. . . 
[IsSearchable, IsFilterable, IsSortable]
public string HotelName { get; set; }
. . .
```

Um esquema também pode incluir outros elementos, incluindo perfis de pontuação para acelerar uma pontuação de pesquisa, analisadores personalizados e outros constructos. No entanto, neste caso, o esquema é definido de forma esparsa, consistindo somente em campos encontrados nos conjuntos de dados de exemplo.

### <a name="in-programcs"></a>Em Program.cs

O programa principal inclui lógica para a criação de um cliente, um índice, uma fonte de dados e um indexador. O código verifica e exclui os recursos existentes do mesmo nome, sob a suposição de que você pode executar este programa várias vezes.

O objeto de fonte de dados é definido com configurações específicas de recursos do Banco de Dados SQL do Azure, incluindo a [indexação parcial ou incremental](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#capture-new-changed-and-deleted-rows) para aproveitar os [recursos internos de detecção de alterações](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server) do SQL do Azure. O banco de dados de hotéis de demonstração no SQL Azure tem uma coluna de "exclusão reversível" chamada **IsDeleted**. Quando essa coluna está definida como true no banco de dados, o indexador remove o documento correspondente do índice da Pesquisa Cognitiva do Azure.

  ```csharp
  Console.WriteLine("Creating data source...");

  DataSource dataSource = DataSource.AzureSql(
      name: "azure-sql",
      sqlConnectionString: configuration["AzureSQLConnectionString"],
      tableOrViewName: "hotels",
      deletionDetectionPolicy: new SoftDeleteColumnDeletionDetectionPolicy(
          softDeleteColumnName: "IsDeleted",
          softDeleteMarkerValue: "true"));
  dataSource.DataChangeDetectionPolicy = new SqlIntegratedChangeTrackingPolicy();

  searchService.DataSources.CreateOrUpdateAsync(dataSource).Wait();
  ```

Um objeto de indexador é independente da plataforma, com invocação, a programação e a configuração sendo as mesmas independentemente da origem. Esse indexador de exemplo inclui uma agenda e uma opção de redefinir que limpa o histórico do indexador e chama um método para criar e executar o indexador imediatamente.

  ```csharp
  Console.WriteLine("Creating Azure SQL indexer...");
  Indexer indexer = new Indexer(
      name: "azure-sql-indexer",
      dataSourceName: dataSource.Name,
      targetIndexName: index.Name,
      schedule: new IndexingSchedule(TimeSpan.FromDays(1)));
  // Indexers contain metadata about how much they have already indexed
  // If we already ran the sample, the indexer will remember that it already
  // indexed the sample data and not run again
  // To avoid this, reset the indexer if it exists
  exists = await searchService.Indexers.ExistsAsync(indexer.Name);
  if (exists)
  {
      await searchService.Indexers.ResetAsync(indexer.Name);
  }

  await searchService.Indexers.CreateOrUpdateAsync(indexer);

  // We created the indexer with a schedule, but we also
  // want to run it immediately
  Console.WriteLine("Running Azure SQL indexer...");

  try
  {
      await searchService.Indexers.RunAsync(indexer.Name);
  }
  catch (CloudException e) when (e.Response.StatusCode == (HttpStatusCode)429)
  {
      Console.WriteLine("Failed to run indexer: {0}", e.Response.Content);
  }
  ```

## <a name="4---build-the-solution"></a>4 – Compilar a solução

Pressione F5 para compilar e executar sua solução. O programa é executado no modo de depuração. Uma janela de console relata o status de cada operação.

   ![Saída do console](./media/search-indexer-tutorial/console-output.png "Saída do console")

O código é executado localmente no Visual Studio, conectando-se ao serviço de pesquisa no Azure, que, por sua vez, conecta-se ao Banco de Dados SQL do Azure e recupera o conjunto de dados. Com essas muitas operações, há vários pontos potenciais de falha. Se você receber um erro, verifique as seguintes condições primeiro:

+ As informações de conexão do serviço de pesquisa que você fornece são limitadas ao nome do serviço neste tutorial. Se você digitou a URL completa, as operações são interrompidas durante a criação do índice, com um erro de falha de conexão.

+ Informações de conexão do banco de dados em **appsettings.json**. Elas devem ser limitadas à cadeia de conexão ADO.NET obtida no portal, modificadas para incluir um nome de usuário e senha válidos para o seu banco de dados. A conta de usuário deve ter permissão para recuperar dados. O endereço IP do cliente local precisa ter permissão de acesso.

+ Limites de recursos. Lembre-se de que a Camada gratuita tem um limite de três índices, indexadores e fontes de dados. Um serviço no limite máximo não pode criar novos objetos.

## <a name="5---search"></a>5 – Pesquisar

Use o portal do Azure para verificar a criação do objeto e, em seguida, use o **Gerenciador de pesquisa** para consultar o índice.

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão geral** do serviço de pesquisa, abra cada lista por vez para verificar se o objeto foi criado. Os **Índices**, os **Indexadores** e as **Fontes de dados** terão "hotels", "azure-sql-indexer" e "azure-sql", respectivamente.

   ![Blocos de indexador e de fonte de dados](./media/search-indexer-tutorial/tiles-portal.png)

1. Selecione o índice de hotéis. Na página de hotéis, o **Gerenciador de pesquisa** é a primeira guia. 

1. Clique em **Pesquisar** para emitir uma consulta vazia. 

   As três entradas no índice são retornadas como documentos JSON. O Gerenciador de pesquisa retorna documentos em JSON para que você possa exibir toda a estrutura.

   ![Consultar um índice](./media/search-indexer-tutorial/portal-search.png "Consultar um índice")
   
1. Em seguida, insira uma cadeia de caracteres de pesquisa: `search=river&$count=true`. 

   Essa consulta invoca a pesquisa de texto completo no termo `river`, e o resultado inclui uma contagem dos documentos correspondentes. Retornar a contagem de correspondência de documentos é útil em cenários de teste, quando você tiver um índice grande com milhares ou milhões de documentos. Nesse caso, apenas um documento corresponde à consulta.

1. Por fim, insira uma cadeia de caracteres de pesquisa que limita a saída JSON para campos de interesse: `search=river&$count=true&$select=hotelId, baseRate, description`. 

   A resposta da consulta é reduzida para os campos selecionados, resultando em uma saída mais concisa.

## <a name="reset-and-rerun"></a>Redefinir e execute novamente

Nos primeiros estágios experimentais de desenvolvimento, a abordagem mais prática para a iteração de design é excluir os objetos do Azure Cognitive Search e permitir que o código os recompile. Nomes de recurso são exclusivos. Excluir um objeto permite que você recriá-la usando o mesmo nome.

O código de exemplo deste tutorial verifica se há objetos existentes e os exclui, de modo que você possa executar novamente o código.

Use também o portal para excluir índices, indexadores e fontes de dados.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, no final de um projeto, é uma boa ideia remover os recursos que já não são necessários. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Encontre e gerencie recursos no portal usando o link Todos os recursos ou Grupos de recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com os conceitos básicos da indexação do Banco de Dados SQL, vamos examinar mais de perto a configuração do indexador.

> [!div class="nextstepaction"]
> [Configurar um indexador de Banco de Dados SQL](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)