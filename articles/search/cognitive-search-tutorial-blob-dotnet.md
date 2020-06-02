---
title: Tutorial de C# sobre o uso de IA nos Blobs do Azure
titleSuffix: Azure Cognitive Search
description: Veja um exemplo de extração de texto e processamento em idioma natural do conteúdo no Armazenamento de Blobs usando o C# e o SDK do .NET do Azure Cognitive Search.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 05/05/2020
ms.openlocfilehash: 57cb68726adf8818f9ef0c8804be9c388ea39ff5
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872315"
---
# <a name="tutorial-ai-generated-searchable-content-from-azure-blobs-using-the-net-sdk"></a>Tutorial: Conteúdo pesquisável gerado por IA de Blobs do Azure usando o SDK do .NET

Se você tiver um texto não estruturado ou imagens no Armazenamento de Blobs do Azure, um [pipeline de enriquecimento de IA](cognitive-search-concept-intro.md) poderá extrair informações e criar um conteúdo que seja útil para cenários de pesquisa de texto completo ou mineração de conhecimento. Neste tutorial do C#, aplique o OCR (reconhecimento óptico de caracteres) em imagens e execute o processamento em idioma natural para criar campos que possam ser aproveitados em consultas, facetas e filtros.

Este tutorial usa o C# e o [SDK do .NET](https://aka.ms/search-sdk) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Comece com arquivos de aplicativo e imagens no Armazenamento de Blobs do Azure.
> * Defina um pipeline para adicionar OCR, extração de texto, detecção de idioma, reconhecimento de frases-chave e entidade.
> * Defina um índice para armazenar a saída (conteúdo bruto, além de pares nome-valor gerados pelo pipeline).
> * Execute o pipeline para começar com as transformações e a análise e para criar e carregar o índice.
> * Explore os resultados usando a pesquisa de texto completo e uma sintaxe de consulta avançada.

Caso não tenha uma assinatura do Azure, abra uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

+ [Armazenamento do Azure](https://azure.microsoft.com/services/storage/)
+ [Visual Studio](https://visualstudio.microsoft.com/downloads/)
+ [Criar](search-create-service-portal.md) ou [encontrar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Use o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita você a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Antes de começar, reserve um espaço no seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Baixar arquivos

1. Abra esta [pasta do OneDrive](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) e, no canto superior esquerdo, clique em **Baixar** para copiar os arquivos para o computador. 

1. Clique com o botão direito do mouse no arquivo zip e selecione **Extrair Tudo**. Há 14 arquivos de vários tipos. Você usará sete para este exercício.

Você também pode baixar o código-fonte deste tutorial. O código-fonte está na pasta tutorial-ia-enrichment no repositório [azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples).

## <a name="1---create-services"></a>1 – Criar serviços

Este tutorial usa o Azure Cognitive Search para indexação e consultas, os Serviços Cognitivos no back-end para enriquecimento de IA e o Armazenamento de Blobs do Azure para fornecer os dados. Este tutorial permanece sob a alocação gratuita de 20 transações por indexador por dia nos Serviços Cognitivos, de modo que os únicos serviços que você precisará criar são pesquisa e armazenamento.

Se possível, crie os dois na mesma região e no mesmo grupo de recursos para facilitar a proximidade e a capacidade de gerenciamento. Na prática, sua conta de armazenamento do Azure pode estar em qualquer região.

### <a name="start-with-azure-storage"></a>Começar com o Armazenamento do Azure

1. [Entre no portal do Azure](https://portal.azure.com/) e clique em **+ Criar Recurso**.

1. Pesquise *conta de armazenamento* e selecione a oferta Conta de Armazenamento da Microsoft.

   ![Criar conta de armazenamento](media/cognitive-search-tutorial-blob/storage-account.png "Criar Conta de Armazenamento")

1. Na guia Informações Básicas, os itens a seguir são obrigatórios. Aceite os padrões para todo o restante.

   + **Grupo de recursos**. Selecione um grupo existente ou crie um, mas use o mesmo grupo para todos os serviços, de modo que você possa gerenciá-los em conjunto.

   + **Nome da conta de armazenamento**. Se acreditar que possa ter vários recursos do mesmo tipo, use o nome para desfazer a ambiguidade por tipo e região, por exemplo, *blobstoragewestus*. 

   + **Local**. Se possível, escolha a mesma localização usada para a Pesquisa Cognitiva do Azure e os Serviços Cognitivos. Uma única localização anula os encargos de largura de banda.

   + **Tipo de Conta**. Escolha o padrão, *StorageV2 (Uso Geral v2)* .

1. Clique em **Examinar + Criar** para criar o serviço.

1. Após a criação, clique em **Ir para o recurso** para abrir a página Visão Geral.

1. Clique em serviço **Blobs**.

1. Clique em **+ Contêiner** para criar um contêiner e nomeie-o *cog-search-demo*.

1. Selecione *cog-search-demo* e, em seguida, clique em **Upload** para abrir a pasta em que você salvou os arquivos de download. Selecione todos os quatorze arquivos e clique em **OK** para carregá-los.

   ![Carregar arquivos de exemplo](media/cognitive-search-quickstart-blob/sample-data.png "Carregar arquivos de exemplo")

1. Antes de sair do Armazenamento do Azure, obtenha uma cadeia de conexão, de modo que você possa formular uma conexão na Pesquisa Cognitiva do Azure. 

   1. Navegue novamente até a página Visão Geral de sua conta de armazenamento (usamos *blobstoragewestus* como exemplo). 
   
   1. No painel de navegação à esquerda, selecione **Chaves de acesso** e copie uma das cadeias de conexão. 

   A cadeia de conexão é uma URL semelhante ao seguinte exemplo:

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Salve a cadeia de conexão no Bloco de notas. Você precisará dela mais tarde ao configurar a conexão de fonte de dados.

### <a name="cognitive-services"></a>Serviços Cognitivos

O enriquecimento de IA é apoiado pelos Serviços Cognitivos, incluindo Análise de Texto e Pesquisa Visual Computacional para processamento de imagem e idioma natural. Caso seu objetivo seja concluir um protótipo ou um projeto real, neste ponto, você poderá provisionar os Serviços Cognitivos (na mesma região da Pesquisa Cognitiva do Azure), de modo que você possa anexá-lo às operações de indexação.

Para este exercício, no entanto, ignore o provisionamento de recursos, porque Pesquisa Cognitiva do Azure pode se conectar aos Serviços Cognitivos nos bastidores e fornecer a você 20 transações gratuitas por execução de indexador. Como este tutorial usa 14 transações, a alocação gratuita é suficiente. Para projetos maiores, planeje o provisionamento dos Serviços Cognitivos no nível S0 pago conforme o uso. Para obter mais informações, confira [Anexar Serviços Cognitivos](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Pesquisa Cognitiva do Azure

O terceiro componente é a Pesquisa Cognitiva do Azure, que pode ser [criada no portal](search-create-service-portal.md). Use a Camada gratuita para concluir este passo a passo. 

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Obter uma URL e uma chave de API de administração para a Pesquisa Cognitiva do Azure

Para interagir com o serviço da Pesquisa Cognitiva do Azure, será necessária a URL de serviço e uma chave de acesso. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou a Pesquisa Cognitiva do Azure à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

   Obtenha a chave de consulta também. É uma melhor prática para emitir solicitações de consulta com acesso somente leitura.

   ![Obter o nome do serviço e as chaves de consulta e de administrador](media/search-get-started-nodejs/service-name-and-keys.png)

Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="2---set-up-your-environment"></a>2 – Configurar o ambiente

Comece abrindo o Visual Studio e criando um novo projeto de aplicativo de console que possa ser executado no .NET Core.

### <a name="install-nuget-packages"></a>Instalar os pacotes NuGet

O [SDK do .NET da Pesquisa Cognitiva do Azure](https://aka.ms/search-sdk) consiste em algumas bibliotecas de clientes que permitem que você gerencie seus índices, fontes de dados, indexadores e conjuntos de qualificações, carregue e gerencie documentos e execute consultas, sem a necessidade de lidar com os detalhes de HTTP e JSON. Essas bibliotecas de cliente são distribuídas como pacotes NuGet.

Para este projeto, instale a versão 9 ou posterior do pacote NuGet `Microsoft.Azure.Search`.

1. Em um navegador, acesse [página do pacote NuGet Microsoft.Azure.Search](https://www.nuget.org/packages/Microsoft.Azure.Search).

1. Selecione a última versão (9 ou posterior).

1. Copie o comando do Gerenciador de Pacotes.

1. Abra o Console do Gerenciador de Pacotes. Selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Console do Gerenciador de Pacotes**. 

1. Cole e execute o comando que você copiou na etapa anterior.

Em seguida, instale o último pacote NuGet `Microsoft.Extensions.Configuration.Json`.

1. Selecione **Ferramentas** > **Gerenciador de Pacotes NuGet** > **Gerenciar Pacotes NuGet para a Solução...** . 

1. Selecione **Procurar** e procure o pacote NuGet `Microsoft.Extensions.Configuration.Json`. 

1. Selecione o pacote, selecione o projeto, confirme se a versão é a última versão estável e, em seguida, clique em **Instalar**.

### <a name="add-service-connection-information"></a>Adicionar informações de conexão de serviço

1. Clique com o botão direito do mouse no projeto no Gerenciador de Soluções e selecione **Adicionar** > **Novo Item...** . 

1. Nomeie o arquivo `appsettings.json` e selecione **Adicionar**. 

1. Inclua esse arquivo no diretório de saída.
    1. Clique com o botão direito do mouse em `appsettings.json` e selecione **Propriedades**. 
    1. Altere o valor de **Copiar para Diretório de Saída** para **Copiar se for o mais recente**.

1. Copie o JSON abaixo em seu novo arquivo JSON.

    ```json
    {
      "SearchServiceName": "Put your search service name here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```
    
Adicione as informações do seu serviço de pesquisa e da conta de armazenamento de blobs. Lembre-se de que você pode obter essas informações nas etapas de provisionamento do serviço indicadas na seção anterior.

Para **SearchServiceName**, insira o nome do serviço curto, não a URL completa.

### <a name="add-namespaces"></a>Adicionar namespaces

Em `Program.cs`, adicione os namespaces a seguir.

```csharp
using System;
using System.Collections.Generic;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Extensions.Configuration;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Criar um cliente

Crie uma instância da classe `SearchServiceClient` em `Main`.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);
```

`CreateSearchServiceClient` cria um novo `SearchServiceClient` usando valores que estão armazenados no arquivo de configuração do aplicativo (appsettings.json).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string adminApiKey = configuration["SearchServiceAdminApiKey"];

   SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
   return serviceClient;
}
```

> [!NOTE]
> A classe `SearchServiceClient` gerencia conexões para o seu serviço de pesquisa. Para evitar abrir um número excessivo de conexões, você deve tentar compartilhar uma única instância de `SearchServiceClient` em seu aplicativo, se possível. Esses métodos são thread-safe para habilitar esse compartilhamento.
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>Adicionar uma função para sair do programa durante uma falha

Este tutorial pretende ajudar você a entender cada etapa do pipeline de indexação. Se houver um problema crítico que impeça o programa de criar a fonte de dados, o conjunto de habilidades, o índice ou o indexador, o programa produzirá a mensagem de erro e será fechado, de modo que o problema possa ser compreendido e resolvido.

Adicione `ExitProgram` a `Main` para processar cenários que exigem o fechamento do programa.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 – Criar o pipeline

Na Pesquisa Cognitiva do Azure, o processamento de IA ocorre durante a indexação (ou a ingestão de dados). Esta parte do passo a passo cria quatro objetos: fonte de dados, definição de índice, conjunto de habilidades e indexador. 

### <a name="step-1-create-a-data-source"></a>Etapa 1: Criar uma fonte de dados

`SearchServiceClient` tem uma propriedade `DataSources`. Essa propriedade fornece todos os métodos necessários para criar, listar, atualizar ou excluir fontes de dados da Pesquisa Cognitiva do Azure.

Crie uma nova instância `DataSource` chamando `serviceClient.DataSources.CreateOrUpdate(dataSource)`. `DataSource.AzureBlobStorage` requer que você especifique o nome da fonte de dados, a cadeia de caracteres de conexão e o nome do contêiner de blob.

```csharp
private static DataSource CreateOrUpdateDataSource(SearchServiceClient serviceClient, IConfigurationRoot configuration)
{
    DataSource dataSource = DataSource.AzureBlobStorage(
        name: "demodata",
        storageConnectionString: configuration["AzureBlobConnectionString"],
        containerName: "cog-search-demo",
        description: "Demo files to demonstrate cognitive search capabilities.");

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.DataSources.CreateOrUpdate(dataSource);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

Para uma solicitação bem-sucedida, o método retornará a fonte de dados que foi criada. Se houver um problema com a solicitação, como um parâmetro inválido, o método gerará uma exceção.

Agora, adicione uma linha à `Main` para chamar a função `CreateOrUpdateDataSource` recém-adicionada.

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();
    SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

    // Create or Update the data source
    Console.WriteLine("Creating or updating the data source...");
    DataSource dataSource = CreateOrUpdateDataSource(serviceClient, configuration);
```

Compile e execute a solução. Como esta é sua primeira solicitação, verifique o portal do Azure para confirmar a fonte de dados foi criado na Pesquisa Cognitiva do Azure. Na página de painel do serviço de pesquisa, verifique se o bloco de fontes de dados tem um novo item. Talvez seja necessário aguardar alguns minutos para que a página do portal atualizar.

  ![Bloco de fontes de dados no portal](./media/cognitive-search-tutorial-blob/data-source-tile.png "Bloco de fontes de dados no portal")

### <a name="step-2-create-a-skillset"></a>Etapa 2: Criar um conjunto de habilidades

Nesta seção, você deve definir um conjunto de etapas de enriquecimento que você deseja aplicar aos seus dados. Cada etapa de enriquecimento é chamada de uma *qualificação* e o conjunto de etapas de enriquecimento um *conjunto de qualificações*. Este tutorial usa [habilidades cognitivas internas](cognitive-search-predefined-skills.md) para o conjunto de habilidades:

+ A qualificação [OCR (Reconhecimento Óptico de Caracteres)](cognitive-search-skill-ocr.md) reconhece textos impressos e manuscritos em arquivos de imagem.

+ A qualificação [Fusão de Texto](cognitive-search-skill-textmerger.md) consolida o texto de uma coleção de campos em um único campo.

+ [A detecção de idioma](cognitive-search-skill-language-detection.md) para identificar o idioma do conteúdo.

+ A [Divisão de Texto](cognitive-search-skill-textsplit.md) divide o conteúdo grande em partes menores antes de chamar a habilidade de extração de frase-chave e a habilidade de reconhecimento de entidade. A extração de frase-chave e o reconhecimento de entidade aceitam entradas de 50 mil caracteres ou menos. Alguns dos arquivos de exemplo precisam dividir para se ajustar dentro desse limite.

+ [Reconhecimento de entidade](cognitive-search-skill-entity-recognition.md) para extrair os nomes das organizações de conteúdo no contêiner de blobs.

+ [Extração de frase chave](cognitive-search-skill-keyphrases.md) para destacar as principais frases-chave.

Durante o processamento inicial, a Pesquisa Cognitiva do Azure abre cada documento para ler conteúdo de diferentes formatos de arquivo. Encontrado um texto de origem no arquivo de origem é colocado em um campo gerado ```content```, uma para cada documento. Assim, defina a entrada como ```"/document/content"``` para usar esse texto. 

Saídas podem ser mapeadas para um índice usado como entrada para uma habilidade de downstream, ou ambos, como é o caso com o código de idioma. No índice, um código de idioma é útil para filtragem. Como uma entrada, o código de idioma é usado por habilidades de análise de texto para informar as regras linguísticas em torno de quebra de palavras.

Para obter mais informações sobre conceitos básicos do conjunto de qualificações, consulte [como definir um conjunto de qualificações](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>Habilidade OCR

A habilidade **OCR** extrai o texto das imagens. Essa habilidade presume que um campo de imagens normalizado existe. Para gerar este campo, mais adiante no tutorial, definiremos a configuração ```"imageAction"``` na definição do indexador para ```"generateNormalizedImages"```.

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "image",
        source: "/document/normalized_images/*"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "text",
        targetName: "text"));

    OcrSkill ocrSkill = new OcrSkill(
        description: "Extract text (plain and structured) from image",
        context: "/document/normalized_images/*",
        inputs: inputMappings,
        outputs: outputMappings,
        defaultLanguageCode: OcrSkillLanguage.En,
        shouldDetectOrientation: true);

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Habilidade de mesclar

Nesta seção, você criará uma habilidade **Mesclar** que mescla o campo de conteúdo do documento com o texto produzido pela habilidade de OCR.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/content"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "itemsToInsert",
        source: "/document/normalized_images/*/text"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "offsets",
        source: "/document/normalized_images/*/contentOffset"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "mergedText",
        targetName: "merged_text"));

    MergeSkill mergeSkill = new MergeSkill(
        description: "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        insertPreTag: " ",
        insertPostTag: " ");

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Habilidade de detecção de idioma

A habilidade **Detecção de Idioma** detecta o idioma de texto de entrada e os relatórios de um código de idioma único para cada documento enviado na solicitação. Usaremos a saída da habilidade **Detecção de Idioma** como parte da entrada para a habilidade **Divisão de Texto**.

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "languageCode",
        targetName: "languageCode"));

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(
        description: "Detect the language used in the document",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings);

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Habilidade de divisão de texto

A habilidade **Dividir** abaixo dividirá o texto por páginas e limitará o tamanho da página a quatro mil caracteres, conforme medido por `String.Length`. O algoritmo tentará dividir o texto em blocos com no máximo `maximumPageLength` de tamanho. Nesse caso, o algoritmo fará o melhor para quebrar a frase em um limite de orações, de modo que o tamanho da parte possa ser um pouco menor que `maximumPageLength`.

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();

    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/merged_text"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "textItems",
        targetName: "pages"));

    SplitSkill splitSkill = new SplitSkill(
        description: "Split content into pages",
        context: "/document",
        inputs: inputMappings,
        outputs: outputMappings,
        textSplitMode: TextSplitMode.Pages,
        maximumPageLength: 4000);

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Habilidade de reconhecimento de entidade

Esta instância `EntityRecognitionSkill` está configurada para reconhecer o tipo de categoria `organization`. A habilidade de **Reconhecimento de Entidade** também pode reconhecer os tipos de categoria `person` e `location`.

Observe que o campo "context" está definido como ```"/document/pages/*"``` com um asterisco, o que significa que a etapa de enriquecimento é chamada para cada página em ```"/document/pages"```.

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "organizations",
        targetName: "organizations"));

    List<EntityCategory> entityCategory = new List<EntityCategory>();
    entityCategory.Add(EntityCategory.Organization);

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(
        description: "Recognize organizations",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings,
        categories: entityCategory,
        defaultLanguageCode: EntityRecognitionSkillLanguage.En);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Habilidade de extração de frase-chave

Como a instância `EntityRecognitionSkill` que acabou de ser criada, a habilidade **Extração de Frases-chave** é chamada para cada página do documento.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry(
        name: "text",
        source: "/document/pages/*"));
    inputMappings.Add(new InputFieldMappingEntry(
        name: "languageCode",
        source: "/document/languageCode"));

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry(
        name: "keyPhrases",
        targetName: "keyPhrases"));

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(
        description: "Extract the key phrases",
        context: "/document/pages/*",
        inputs: inputMappings,
        outputs: outputMappings);

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>Construir e criar o conjunto de habilidades

Construa o `Skillset` usando as habilidades que você criou.

```csharp
private static Skillset CreateOrUpdateDemoSkillSet(SearchServiceClient serviceClient, IList<Skill> skills)
{
    Skillset skillset = new Skillset(
        name: "demoskillset",
        description: "Demo skillset",
        skills: skills);

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        serviceClient.Skillsets.CreateOrUpdate(skillset);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Adicione as linhas a seguir a `Main`.

```csharp
    // Create the skills
    Console.WriteLine("Creating the skills...");
    OcrSkill ocrSkill = CreateOcrSkill();
    MergeSkill mergeSkill = CreateMergeSkill();
    EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
    LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
    SplitSkill splitSkill = CreateSplitSkill();
    KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

    // Create the skillset
    Console.WriteLine("Creating or updating the skillset...");
    List<Skill> skills = new List<Skill>();
    skills.Add(ocrSkill);
    skills.Add(mergeSkill);
    skills.Add(languageDetectionSkill);
    skills.Add(splitSkill);
    skills.Add(entityRecognitionSkill);
    skills.Add(keyPhraseExtractionSkill);

    Skillset skillset = CreateOrUpdateDemoSkillSet(serviceClient, skills);
```

### <a name="step-3-create-an-index"></a>Etapa 3: Crie um índice

Nesta seção, você define o esquema de índice especificando quais campos serão incluídos no índice de pesquisa e os atributos de pesquisa para cada campo. Campos têm um tipo e podem levar a atributos que determinam como o campo é usado (pesquisável, classificável e assim por diante). Nomes de campo em um índice não são necessários para identicamente correspondem aos nomes de campo na fonte. Em uma etapa posterior, você deve adicionar mapeamentos de campo em um indexador para conectar-se campos de origem-destino. Nesta etapa, defina o índice usando as convenções de nomenclatura de campo relevantes para o aplicativo de pesquisa.

Este exercício usa os seguintes campos e tipos de campo:

| nomes de campo: | `id`       | content   | languageCode | keyPhrases         | organizações     |
|--------------|----------|-------|----------|--------------------|-------------------|
| nomes de campo: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |


#### <a name="create-demoindex-class"></a>Criar classe DemoIndex

Os campos para este índice são definidos usando uma classe de modelo. Cada propriedade da classe de modelo tem atributos que determinam os comportamentos relacionados à pesquisa do campo de índice correspondente. 

Vamos adicionar a classe de modelo a um novo arquivo C#. Clique com o botão direito do mouse em seu projeto e selecione **Adicionar** > **Novo Item...** , selecione "Classe" e nomeie o arquivo `DemoIndex.cs`, depois selecione **Adicionar**.

Certifique-se de indicar que você deseja usar os tipos dos namespaces `Microsoft.Azure.Search` e `Microsoft.Azure.Search.Models`.

Adicione a definição de classe de modelo abaixo a `DemoIndex.cs` e inclua-a no mesmo namespace onde você criará o índice.

```csharp
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
    // It ensures that Pascal-case property names in the model class are mapped to camel-case
    // field names in the index.
    [SerializePropertyNamesAsCamelCase]
    public class DemoIndex
    {
        [System.ComponentModel.DataAnnotations.Key]
        [IsSearchable, IsSortable]
        public string Id { get; set; }

        [IsSearchable]
        public string Content { get; set; }

        [IsSearchable]
        public string LanguageCode { get; set; }

        [IsSearchable]
        public string[] KeyPhrases { get; set; }

        [IsSearchable]
        public string[] Organizations { get; set; }
    }
}
```

Agora que você definiu uma classe de modelo, em `Program.cs` você pode criar uma definição de índice com bastante facilidade. O nome desse índice será `demoindex`. Se já existir um índice com esse nome, ele será excluído.

```csharp
private static Index CreateDemoIndex(SearchServiceClient serviceClient)
{
    var index = new Index()
    {
        Name = "demoindex",
        Fields = FieldBuilder.BuildForType<DemoIndex>()
    };

    try
    {
        bool exists = serviceClient.Indexes.Exists(index.Name);

        if (exists)
        {
            serviceClient.Indexes.Delete(index.Name);
        }

        serviceClient.Indexes.Create(index);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

Durante o teste, você pode constatar que está tentando criar o índice mais de uma vez. Por isso, verifique se o índice que você está prestes a criar já existe antes de tentar criá-lo.

Adicione as linhas a seguir a `Main`.

```csharp
    // Create the index
    Console.WriteLine("Creating the index...");
    Microsoft.Azure.Search.Models.Index demoIndex = CreateDemoIndex(serviceClient);
```

Adicione a instrução using a seguir para resolver a referência de desambiguação.

```csharp
using Index = Microsoft.Azure.Search.Models.Index;
```

Para saber mais sobre como definir um índice, consulte [Criar Índice (API REST da Pesquisa Cognitiva do Azure)](https://docs.microsoft.com/rest/api/searchservice/create-index).

### <a name="step-4-create-and-run-an-indexer"></a>Etapa 4: Criar e executar um indexador

Até agora, você criou uma fonte de dados, um conjunto de qualificações e um índice. Esses três componentes se tornam parte de um [indexador](search-indexer-overview.md) que reúne cada item em uma única operação de várias fases. Para unir esses em um indexador, você deve definir mapeamentos de campo.

+ Os fieldMappings são processados antes do conjunto de habilidades, mapeando campos de origem da fonte de dados para campos de destino em um índice. Se os tipos e os nomes do campo forem os mesmos em ambas as extremidades, nenhum mapeamento será necessário.

+ Os outputFieldMappings são processados depois do conjunto de habilidades, fazendo referência a sourceFieldNames que não existem até a decodificação de documentos ou até que o enriquecimento os crie. O targetFieldName é um campo em um índice.

Além de conectar entradas a saídas, você também pode usar mapeamentos de campo para mesclar estruturas de dados. Para obter mais informações, consulte [Como mapear campos enriquecidos para um índice pesquisável](cognitive-search-output-field-mapping.md).

```csharp
private static Indexer CreateDemoIndexer(SearchServiceClient serviceClient, DataSource dataSource, Skillset skillSet, Index index)
{
    IDictionary<string, object> config = new Dictionary<string, object>();
    config.Add(
        key: "dataToExtract",
        value: "contentAndMetadata");
    config.Add(
        key: "imageAction",
        value: "generateNormalizedImages");

    List<FieldMapping> fieldMappings = new List<FieldMapping>();
    fieldMappings.Add(new FieldMapping(
        sourceFieldName: "metadata_storage_path",
        targetFieldName: "id",
        mappingFunction: new FieldMappingFunction(
            name: "base64Encode")));
    fieldMappings.Add(new FieldMapping(
        sourceFieldName: "content",
        targetFieldName: "content"));

    List<FieldMapping> outputMappings = new List<FieldMapping>();
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/pages/*/organizations/*",
        targetFieldName: "organizations"));
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/pages/*/keyPhrases/*",
        targetFieldName: "keyPhrases"));
    outputMappings.Add(new FieldMapping(
        sourceFieldName: "/document/languageCode",
        targetFieldName: "languageCode"));

    Indexer indexer = new Indexer(
        name: "demoindexer",
        dataSourceName: dataSource.Name,
        targetIndexName: index.Name,
        description: "Demo Indexer",
        skillsetName: skillSet.Name,
        parameters: new IndexingParameters(
            maxFailedItems: -1,
            maxFailedItemsPerBatch: -1,
            configuration: config),
        fieldMappings: fieldMappings,
        outputFieldMappings: outputMappings);

    try
    {
        bool exists = serviceClient.Indexers.Exists(indexer.Name);

        if (exists)
        {
            serviceClient.Indexers.Delete(indexer.Name);
        }

        serviceClient.Indexers.Create(indexer);
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", e.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```
Adicione as linhas a seguir a `Main`.

```csharp
    // Create the indexer, map fields, and execute transformations
    Console.WriteLine("Creating the indexer and executing the pipeline...");
    Indexer demoIndexer = CreateDemoIndexer(serviceClient, dataSource, skillset, demoIndex);
```

Espera-se que a criação do indexador leve algum tempo para ser concluída. Embora o conjunto de dados é pequeno, capacidades analíticas são intensivas na computação. Algumas técnicas, como a análise de imagem, são demoradas.

> [!TIP]
> Criar um indexador invoca o pipeline. Se houver problemas ao acessar os dados, as entradas de mapeamento e saídas ou ordem de operações, eles aparecem neste estágio.

### <a name="explore-creating-the-indexer"></a>Explorar a criação do indexador

O código define ```"maxFailedItems"``` como -1, que instrui o mecanismo de indexação para ignorar erros durante a importação de dados. Isso é útil porque há portanto alguns documentos na fonte de dados de demonstração. Para uma fonte de dados maior, você definirá o valor maior que 0.

Observe também que ```"dataToExtract"``` está definido como ```"contentAndMetadata"```. Essa instrução informa o indexador para extrair automaticamente o conteúdo de diferentes formatos de arquivo, como também os metadados relacionados a cada arquivo.

Quando o conteúdo é extraído, você pode definir `imageAction` para extrair o texto da imagem foi encontrada na fonte de dados. A configuração ```"imageAction"``` definida como ```"generateNormalizedImages"```, combinada com a habilidade de OCR e a habilidade de Mesclagem de Texto, instrui o indexador a extrair o texto das imagens (por exemplo, a palavra "pare" de uma placa de trânsito "Pare") e inseri-la como parte do campo de conteúdo. Esse comportamento se aplica a ambas as imagens inseridas nos documentos (imagine uma imagem dentro de um PDF), bem como imagens encontradas na fonte de dados, por exemplo um arquivo JPG.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 – Monitorar a indexação

Depois que o indexador for definido, ele é executado automaticamente quando você enviar a solicitação. Dependendo de quais habilidades cognitivas definida, a indexação pode demorar mais do que o esperado. Para descobrir se o indexador ainda está em execução, use o método `GetStatus`.

```csharp
private static void CheckIndexerOverallStatus(SearchServiceClient serviceClient, Indexer indexer)
{
    try
    {
        IndexerExecutionInfo demoIndexerExecutionInfo = serviceClient.Indexers.GetStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (Exception e)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", e.Message);
    }
}
```

`IndexerExecutionInfo` representa o status atual e o histórico de execução de um indexador.

Os avisos são comuns com algumas combinações de arquivo e a habilidade de origem e sempre não indicam um problema. Neste tutorial, os avisos são benignos (por exemplo, nenhuma entrada de texto dos arquivos JPEG).

Adicione as linhas a seguir a `Main`.

```csharp
    // Check indexer overall status
    Console.WriteLine("Check the indexer overall status...");
    CheckIndexerOverallStatus(serviceClient, demoIndexer);
```
 
## <a name="5---search"></a>5 – Pesquisar

Depois de terminar de indexação, você pode executar consultas que retornam o conteúdo dos campos individuais. Por padrão, a Pesquisa Cognitiva do Azure retorna os 50 melhores resultados. Os dados de exemplo serão pequenos para que o padrão funciona bem. No entanto, ao trabalhar com grandes conjuntos de dados, você precisará incluir parâmetros na cadeia de caracteres de consulta para retornar mais resultados. Para obter instruções, consulte [Como paginar resultados da Pesquisa Cognitiva do Azure](search-pagination-page-layout.md).

Como uma etapa de verificação, consulte o índice para todos os campos.

Adicione as linhas a seguir a `Main`.

```csharp
DocumentSearchResult<DemoIndex> results;

ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
}
catch (Exception e)
{
    // Handle exception
}
```

`CreateSearchIndexClient` cria um novo `SearchIndexClient` usando valores que estão armazenados no arquivo de configuração do aplicativo (appsettings.json). Observe que a chave de API de consulta do serviço de pesquisa é usada e não a chave do administrador.

```csharp
private static SearchIndexClient CreateSearchIndexClient(IConfigurationRoot configuration)
{
   string searchServiceName = configuration["SearchServiceName"];
   string queryApiKey = configuration["SearchServiceQueryApiKey"];

   SearchIndexClient indexClient = new SearchIndexClient(searchServiceName, "demoindex", new SearchCredentials(queryApiKey));
   return indexClient;
}
```

Adicione o código a seguir a `Main`. O primeiro try-catch retorna a definição de índice, com o nome, o tipo e os atributos de cada campo. O segundo é uma consulta parametrizada, em que `Select` especifica quais campos serão incluídos nos resultados, por exemplo, `organizations`. Uma cadeia de caracteres de pesquisa de `"*"` retorna todo o conteúdo de um só campo.

```csharp
//Verify content is returned after indexing is finished
ISearchIndexClient indexClientForQueries = CreateSearchIndexClient(configuration);

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*");
    Console.WriteLine("First query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("First query failed\n Exception message: {0}\n", e.Message);
}

SearchParameters parameters =
    new SearchParameters
    {
        Select = new[] { "organizations" }
    };

try
{
    results = indexClientForQueries.Documents.Search<DemoIndex>("*", parameters);
    Console.WriteLine("Second query succeeded with a result count of {0}", results.Results.Count);
}
catch (Exception e)
{
    Console.WriteLine("Second query failed\n Exception message: {0}\n", e.Message);
}
```

Repita para campos adicionais: conteúdo, idioma, frases-chave e organizações neste exercício. Você pode retornar vários campos por meio da propriedade [Select](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.searchparameters.select?view=azure-dotnet) usando uma lista delimitada por vírgula.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Redefinir e execute novamente

Nos primeiros estágios experimentais de desenvolvimento, a abordagem mais prática para a iteração de design é excluir os objetos do Azure Cognitive Search e permitir que o código os recompile. Nomes de recurso são exclusivos. Excluir um objeto permite que você recriá-la usando o mesmo nome.

O código de exemplo deste tutorial verifica se há objetos existentes e os exclui, de modo que você possa executar novamente o código.

Use também o portal para excluir índices, indexadores, fontes de dados e conjuntos de habilidades.

## <a name="takeaways"></a>Observações

Este tutorial demonstrou as etapas básicas para a criação de um pipeline de indexação enriquecido durante a criação de partes dos componentes: uma fonte de dados, o conjunto de qualificações, o índice e o indexador.

[Habilidades internas](cognitive-search-predefined-skills.md) foram introduzidas, junto com a definição de conjunto de qualificações e a mecânica de encadear habilidades por meio de entradas e saídas. Você também aprendeu que `outputFieldMappings` na definição do indexador é necessário para encaminhar valores enriquecidos do pipeline em um índice pesquisável em um serviço da Pesquisa Cognitiva do Azure.

Por fim, você aprendeu como resultados de teste e reinicie o sistema para obter mais iterações. Você aprendeu que emitir consultas em relação ao índice retorna a saída criada pelo pipeline de indexação enriquecido. Você também aprendeu como verificar o status do indexador e quais objetos a serem excluídos antes de executar novamente um pipeline.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, no final de um projeto, é uma boa ideia remover os recursos que já não são necessários. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Encontre e gerencie recursos no portal usando o link Todos os recursos ou Grupos de recursos no painel de navegação à esquerda.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com todos os objetos de um pipeline de enriquecimento de IA, vamos examinar mais de perto as definições de conjunto de habilidades e as habilidades individuais.

> [!div class="nextstepaction"]
> [Como criar um conjunto de habilidades](cognitive-search-defining-skillset.md)
