---
title: Pesquisar sobre bolhas JSON
titleSuffix: Azure Cognitive Search
description: As bolhas Do Crawl Azure JSON para conteúdo de texto usando o indexador Azure Cognitive Search Blob. Os indexadores automatizam a ingestão de dados para fontes de dados selecionadas, como o armazenamento de blobs do Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 37fc78971124240077a59d4ad99aa06cc408dbae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533963"
---
# <a name="how-to-index-json-blobs-using-a-blob-indexer-in-azure-cognitive-search"></a>Como indexar bolhas JSON usando um indexador Blob na Pesquisa Cognitiva Do Azure

Este artigo mostra como configurar um [indexador](search-indexer-overview.md) de blob de pesquisa cognitiva do Azure para extrair conteúdo estruturado de documentos JSON no armazenamento Azure Blob e torná-lo pesquisável na Pesquisa Cognitiva do Azure. Este fluxo de trabalho cria um índice de pesquisa cognitiva do Azure e o carrega com texto existente extraído de blobs JSON. 

Você pode usar o [portal](#json-indexer-portal), [APIs REST](#json-indexer-rest), ou [SDK do .NET](#json-indexer-dotnet) para indexar o conteúdo JSON. Comum a todas as abordagens é que os documentos JSON estão localizados em um contêiner blob em uma conta do Azure Storage. Para obter orientação sobre como pressionar documentos JSON de outras plataformas não-Azure, consulte [Importação de dados na Pesquisa Cognitiva do Azure](search-what-is-data-import.md).

Os blobs JSON no armazenamento Azure Blob são tipicamente um único `json`documento JSON (modo de análise é ) ou uma coleção de entidades JSON. Para coleções, a bolha pode ter uma **matriz** de elementos JSON `jsonArray`bem formados (modo de análise é ). Blobs também poderiam ser compostos de múltiplas entidades JSON individuais `jsonLines`separadas por uma newline (modo de análise é ). O parâmetro **parsingMode** na solicitação determina as estruturas de saída.

> [!NOTE]
> Para obter mais informações sobre como indexar vários documentos de pesquisa de uma única bolha, consulte [Indexação de um a muitos](search-howto-index-one-to-many-blobs.md).

<a name="json-indexer-portal"></a>

## <a name="use-the-portal"></a>Usar o portal

O método mais fácil para indexação de documentos JSON é usar um assistente no [portal do Microsoft Azure](https://portal.azure.com/). Ao analisar metadados no contêiner de blob do Azure, o assistente [**Importar dados**](search-import-data-portal.md) pode criar um índice padrão, mapear os campos de origem para campos de índice de destino e carregar o índice em uma única operação. Dependendo do tamanho e complexidade da fonte de dados, é possível ter um índice de pesquisa de texto completo operando em minutos.

Recomendamos o uso da mesma região ou local tanto para o Azure Cognitive Search quanto para o Azure Storage para obter menor latência e evitar cargas de largura de banda.

### <a name="1---prepare-source-data"></a>1- Preparar dados de origem

[Faça login no portal Azure](https://portal.azure.com/) e [crie um contêiner Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter seus dados. O Nível de Acesso Público pode ser definido como qualquer um de seus valores válidos.

Você precisará do nome da conta de armazenamento, nome do contêiner e uma chave de acesso para recuperar seus dados no assistente **de dados Importar.**

### <a name="2---start-import-data-wizard"></a>2 - Iniciar o Assistente para Importação de Dados 

Na página Visão geral do seu serviço de pesquisa, você pode [iniciar o assistente](search-import-data-portal.md) a partir da barra de comando.

   ![Importar comando de dados no portal](./media/search-import-data-portal/import-data-cmd2.png "Iniciar o assistente Importar dados")

### <a name="3---set-the-data-source"></a>3 - Configurar a fonte de dados

Na página **fonte de dados**, a fonte deve ser **Armazenamento de blobs do Azure** com as seguintes especificações:

+ Os **Dados para extração** devem ser *Conteúdo e Metadados*. Esta opção permite que ao assistente inferir um esquema de índice e mapear os campos para importação.
   
+ **O modo de análise** deve ser definido para *linhas JSON,* *JSON* ou *JSON.* 

  O *JSON* articula cada blob como um documento de pesquisa única, aparecendo como um item independente nos resultados da pesquisa. 

  *O array JSON* é para blobs que contêm dados JSON bem formados - o JSON bem formado corresponde a uma matriz de objetos, ou tem uma propriedade que é uma matriz de objetos e você quer que cada elemento seja articulado como um documento de pesquisa independente e independente. Se os blobs são complexos e você não escolher *matriz JSON*, o blob inteiro será ingerido como um único documento.

  *As linhas JSON* são para blobs compostas por várias entidades JSON separadas por uma nova linha, onde você quer que cada entidade seja articulada como um documento de pesquisa independente independente. Se as bolhas são complexas e você não escolhe o modo de análise de *linhas JSON,* então toda a bolha é ingerida como um único documento.
   
+ O **Contêiner de armazenamento** deve especificar sua conta de armazenamento e contêiner ou uma cadeia de caracteres de conexão que apontam para o contêiner. Obtenhas as cadeias de caracteres de conexão na página de portal de serviço Blob.

   ![Definição de fonte de dados de Blob](media/search-howto-index-json/import-wizard-json-data-source.png)

### <a name="4---skip-the-enrich-content-page-in-the-wizard"></a>4 - Pule a página "Enriquecer conteúdo" no assistente

Adicionar habilidades cognitivas (ou enriquecimento) não é um requisito de importação. A menos que você tenha uma necessidade específica de [adicionar o enriquecimento de IA](cognitive-search-concept-intro.md) ao seu pipeline de indexação, você deve pular esta etapa.

Para pular a etapa, clique nos botões azuis na parte inferior da página para "Next" e "Skip".

### <a name="5---set-index-attributes"></a>5- Definir atributos de índice

Na página **índice**, deve haver uma lista de campos com um tipo de dados e uma série de caixas de seleção para definir os atributos de índice. O assistente pode gerar uma lista de campos com base em metadados e amostrando os dados de origem. 

Você pode selecionar atributos em massa clicando na caixa de seleção na parte superior de uma coluna de atributos. Escolha **Recuperável** e **Pesquisável** para cada campo que deve ser devolvido a um aplicativo cliente e sujeito ao processamento completo de pesquisa de texto. Você notará que os inteiros não são texto completo ou pesquisáveis em delineados (os números são avaliados verbatim e muitas vezes são úteis em filtros).

Revise a descrição dos [atributos](https://docs.microsoft.com/rest/api/searchservice/create-index#bkmk_indexAttrib) de índice e [dos analisadores de idiomas](https://docs.microsoft.com/rest/api/searchservice/language-support) para obter mais informações. 

Reserve um tempo para revisar suas seleções. Depois de executar o assistente, estruturas de dados físicos são criadas e você não poderá editar esses campos sem descartar e recriar todos os objetos.

   ![Definição do índice de blob](media/search-howto-index-json/import-wizard-json-index.png)

### <a name="6---create-indexer"></a>6 - Criar indexador

Totalmente especificado, o assistente cria três objetos diferentes em seu serviço de pesquisa. Um objeto de origem de dados e objeto de índice são salvos como recursos nomeados no serviço de pesquisa cognitiva do Azure. A última etapa cria um objeto do indexador. Dar um nome ao indexador permite que ele exista como um recurso autônomo, que pode ser agendado e gerenciado independentemente do objeto de fonte de dados e índice criado na mesma sequência do assistente.

Se você não estiver familiarizado com indexadores, um *indexador* é um recurso no Azure Cognitive Search que rastreia uma fonte de dados externa para conteúdo pesquisável. A saída do assistente de **dados Import** é um indexador que rastreia sua fonte de dados JSON, extrai conteúdo pesquisável e importa-o para um índice na Pesquisa Cognitiva do Azure.

   ![Definição de indexador de blob](media/search-howto-index-json/import-wizard-json-indexer.png)

Clique em **Ok** para executar o assistente e criar todos os objetos. A indexação começa imediatamente.

Pode-se monitorar a importação de dados nas páginas do portal. As notificações de progresso indicam o status da indexação e quantos documentos são carregados. 

Quando a indexação estiver concluída, você pode usar o [Search explorer](search-explorer.md) para consultar seu índice.

> [!NOTE]
> Se você não ver os dados que você espera, talvez seja necessário definir mais atributos em mais campos. Exclua o índice e o indexador que você acabou de criar e passe pelo assistente novamente, modificando suas seleções para atributos de índice na etapa 5. 

<a name="json-indexer-rest"></a>

## <a name="use-rest-apis"></a>Usar APIs REST

Você pode usar a API REST para indexar blobs JSON, seguindo um fluxo de trabalho de três partes comum a todos os indexadores no Azure Cognitive Search: crie uma fonte de dados, crie um índice, crie um indexador. A extração de dados do armazenamento blob ocorre quando você envia a solicitação Criar indexador. Depois que essa solicitação for concluída, você terá um índice que pode ser consultado. 

Você pode rever o [código de exemplo REST](#rest-example) no final desta seção que mostra como criar os três objetos. Esta seção também contém detalhes sobre [os modos de análise JSON,](#parsing-modes) [blobs únicos,](#parsing-single-blobs) [matrizes JSON](#parsing-arrays)e [matrizes aninhadas](#nested-json-arrays).

Para indexação JSON baseada em código, use [o Carteiro](search-get-started-postman.md) e a API REST para criar esses objetos:

+ [index](https://docs.microsoft.com/rest/api/searchservice/create-index)
+ [fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source)
+ [indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer)

A ordem das operações requer que você crie e chame objetos nesta ordem. Em contraste com o fluxo de trabalho do portal, uma abordagem de código requer um índice disponível para aceitar os documentos JSON enviados através da solicitação **De Criar Indexador.**

Os blobs JSON no armazenamento Azure Blob são tipicamente um único documento JSON ou uma "matriz" JSON. O indexador blob no Azure Cognitive Search pode analisar qualquer construção, dependendo de como você definir o parâmetro **parsingMode** na solicitação.

| Documento JSON | parsingMode | Descrição | Disponibilidade |
|--------------|-------------|--------------|--------------|
| Um por blob | `json` | Analisa blobs JSON como um único bloco de texto. Cada bolha JSON torna-se um único documento de pesquisa cognitiva do Azure. | Geralmente disponível tanto na API [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) quanto [no .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Múltiplos por blob | `jsonArray` | Analisa uma matriz JSON na bolha, onde cada elemento da matriz se torna um documento separado de Pesquisa Cognitiva Azure.  | Geralmente disponível tanto na API [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) quanto [no .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |
| Múltiplos por blob | `jsonLines` | Analisa uma bolha que contém várias entidades JSON (uma "matriz") separadas por uma nova linha, onde cada entidade se torna um documento separado do Azure Cognitive Search. | Geralmente disponível tanto na API [REST](https://docs.microsoft.com/rest/api/searchservice/indexer-operations) quanto [no .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer) SDK. |

### <a name="1---assemble-inputs-for-the-request"></a>1 - Montar entradas para a solicitação

Para cada solicitação, você deve fornecer o nome do serviço e a chave de administração para a Pesquisa Cognitiva do Azure (no cabeçalho POST) e o nome da conta de armazenamento e a chave para armazenamento blob. Você pode usar [o Carteiro](search-get-started-postman.md) para enviar solicitações HTTP para a Pesquisa Cognitiva do Azure.

Copie os quatro valores a seguir no Bloco de Notas para que você possa colá-los em uma solicitação:

+ Nome do serviço de busca cognitiva do Azure
+ Chave de administrador de pesquisa cognitiva do Azure
+ Nome da conta de armazenamento do Azure
+ Chave da conta de armazenamento do Azure

Você pode encontrar esses valores no portal:

1. Nas páginas do portal para a Pesquisa Cognitiva do Azure, copie a URL do serviço de pesquisa da página Visão Geral.

2. No painel de navegação à esquerda, clique **em Teclas** e copie a tecla primária ou secundária (elas são equivalentes).

3. Mude para as páginas do portal para sua conta de armazenamento. No painel de navegação à esquerda, em **Configurações,** clique em **Teclas de acesso**. Esta página fornece o nome da conta e a chave. Copie o nome da conta de armazenamento e uma das chaves do bloco de notas.

### <a name="2---create-a-data-source"></a>2 - Criar uma fonte de dados

Esta etapa fornece informações de conexão de fonte de dados usadas pelo indexador. A fonte de dados é um objeto nomeado no Azure Cognitive Search que persiste as informações de conexão. O tipo `azureblob`de origem dos dados determina quais comportamentos de extração de dados são invocados pelo indexador. 

Substitua os valores válidos para nome do serviço, chave de administração, conta de armazenamento e espaços reservados para a chave da conta.

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }   

### <a name="3---create-a-target-search-index"></a>3 - Criar um índice de pesquisa de destino 

Indexadores são emparelhados com um esquema de índice. Se você estiver usando a API (em vez do portal), prepare um índice com antecedência para que você pode especificá-lo na operação do indexador.

O índice armazena conteúdo pesquisável no Azure Cognitive Search. Para criar um índice, forneça um esquema que especifique os campos em um documento, atributos e outras construções que modelam a experiência de pesquisa. Se você criar um índice que tem os mesmos nomes de campo e tipos de dados que a origem, o indexador corresponderá os campos de origem e destino, economizando o trabalho de precisar mapear explicitamente os campos.

O exemplo a seguir mostra uma solicitação de [Criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index). O índice possuirá um campo `content` pesquisável para armazenar o texto extraído dos blobs:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="4---configure-and-run-the-indexer"></a>4 - Configurar e executar o indexador

Como acontece com um índice e uma fonte de dados, e o indexador também é um objeto nomeado que você cria e reutiliza em um serviço de Pesquisa Cognitiva Do Azure. Uma solicitação totalmente especificada para criar um indexador pode parecer a seguinte:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } }
    }

A configuração do indexador está no corpo da solicitação. Ele requer uma fonte de dados e um índice de destino vazio que já existe na Pesquisa Cognitiva do Azure. 

O horário e os parâmetros são opcionais. Se você omiti-los, o indexador é executado imediatamente, usando `json` como modo de análise.

Este indexador em particular não inclui mapeamentos de campo. Dentro da definição do indexador, você pode deixar de fora **mapeamentos** de campo se as propriedades do documento JSON de origem coincidirem com os campos do seu índice de pesquisa de destino. 


### <a name="rest-example"></a>Exemplo DE RESTO

Esta seção é uma recapitulação de todas as solicitações usadas para a criação de objetos. Para uma discussão sobre partes componentes, consulte as seções anteriores neste artigo.

### <a name="data-source-request"></a>Solicitação de origem de dados

Todos os indexadores requerem um objeto de origem de dados que forneça informações de conexão aos dados existentes. 

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
        "name" : "my-blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "optional, my-folder" }
    }  


### <a name="index-request"></a>Solicitação de índice

Todos os indexadores requerem um índice de destino que receba os dados. O corpo da solicitação define o esquema de índice, composto por campos, atribuído sustam os comportamentos desejados em um índice pesquisável. Este índice deve estar vazio quando você executar o indexador. 

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }


### <a name="indexer-request"></a>Solicitação de indexador

Esta solicitação mostra um indexador totalmente especificado. Inclui mapeamentos de campo, que foram omitidos em exemplos anteriores. Lembre-se de que "agendar", "parâmetros" e "fieldMappings" são opcionais desde que haja um padrão disponível. Omitir "agendar" faz com que o indexador seja executado imediatamente. Omitir "parsingMode" faz com que o índice use o padrão "json".

A criação do indexador no Azure Cognitive Search desencadeia a importação de dados. Ele é executado imediatamente, e depois em um cronograma, se você forneceu um.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key for Azure Cognitive Search]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "json" } },
      "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
        ]
    }


<a name="json-indexer-dotnet"></a>

## <a name="use-net-sdk"></a>Usar o SDK do .NET

O .NET SDK tem total paridade com a API REST. É recomendável examinar a seção anterior da API REST para aprender os conceitos, fluxo de trabalho e requisitos. Consulte a seguinte documentação de referência de API do .NET para implementar um indexador JSON em código gerenciado.

+ [microsoft.azure.search.models.datasource](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet)
+ [microsoft.azure.search.models.datasourcetype](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasourcetype?view=azure-dotnet) 
+ [microsoft.azure.search.models.index](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) 
+ [microsoft.azure.search.models.indexer](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.indexer?view=azure-dotnet)

<a name="parsing-modes"></a>

## <a name="parsing-modes"></a>Modos de análise

As bolhas JSON podem assumir múltiplas formas. O parâmetro **parsingMode** no indexador JSON determina como o conteúdo do blob JSON é analisado e estruturado em um índice de Pesquisa Cognitiva Azure:

| parsingMode | Descrição |
|-------------|-------------|
| `json`  | Indexe cada bolha como um único documento. Esse é o padrão. |
| `jsonArray` | Escolha este modo se suas bolhas consistem em matrizes JSON, e você precisa que cada elemento da matriz se torne um documento separado na Pesquisa Cognitiva do Azure. |
|`jsonLines` | Escolha este modo se suas bolhas consistem em várias entidades JSON, que são separadas por uma nova linha, e você precisa que cada entidade se torne um documento separado na Pesquisa Cognitiva do Azure. |

Você pode pensar em um documento como um único item nos resultados da pesquisa. Se você quiser que cada elemento na matriz apareça nos resultados `jsonArray` de `jsonLines` pesquisa como um item independente, use a opção ou conforme apropriado.

Dentro da definição de indexador, use opcionalmente [mapeamentos de campo](search-indexer-field-mappings.md) para selecionar as propriedades do documento JSON de origem usado para preencher o índice de pesquisa de destino. Para `jsonArray` o modo de análise, se a matriz existir como uma propriedade de nível inferior, você pode definir uma raiz de documento indicando onde a matriz é colocada dentro da bolha.

> [!IMPORTANT]
> Quando você `json` `jsonArray` usa `jsonLines` , ou modo de análise, o Azure Cognitive Search assume que todas as bolhas na sua fonte de dados contêm JSON. Se você precisar dar suporte a uma combinação de blobs JSON e não JSON na mesma fonte de dados, informe-nos em nosso [site UserVoice](https://feedback.azure.com/forums/263029-azure-search).


<a name="parsing-single-blobs"></a>

## <a name="parse-single-json-blobs"></a>Parse únicas bolhas JSON

Por padrão, [o indexador de bolhas azure Cognitive Search](search-howto-indexing-azure-blob-storage.md) analisa as bolhas JSON como um único pedaço de texto. Muitas vezes, você deseja preservar a estrutura dos seus documentos JSON. Por exemplo, suponha que você tem o seguinte documento JSON no armazenamento de BLOBs do Azure:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13",
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

O indexador blob analisa o documento JSON em um único documento de Pesquisa Cognitiva Azure. O indexador carrega um índice por correspondência de "texto", "data de publicação" e "marcas" da origem em relação a campos de destino de forma idêntica nomeados e digitados.

Conforme observado, os mapeamentos de campo não são necessários. Dado um índice com os campos "texto", "data de publicação e "marcas", o indexador blob pode inferir o mapeamento correto sem um campo de mapeamento presente na solicitação.

<a name="parsing-arrays"></a>

## <a name="parse-json-arrays"></a>Parse matrizes JSON

Alternativamente, você pode usar a opção de matriz JSON. Essa opção é útil quando as bolhas contêm uma *matriz de objetos JSON bem formados,* e você quer que cada elemento se torne um documento separado do Azure Cognitive Search. Por exemplo, dada a seguinte bolha JSON, você pode preencher seu índice de pesquisa cognitiva do Azure com três documentos separados, cada um com campos "id" e "text".  

    [
        { "id" : "1", "text" : "example 1" },
        { "id" : "2", "text" : "example 2" },
        { "id" : "3", "text" : "example 3" }
    ]

Para uma matriz JSON, a definição do indexador deve ser semelhante ao exemplo a seguir. Observe que o parâmetro parsingMode especifica o `jsonArray` analisador. Especificar o analisador certo e ter a entrada de dados correta são os dois únicos requisitos específicos do array para indexar blobs JSON.

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }

Novamente, os mapeamentos de campo não são necessários. Dado um índice com nomes idênticos nos campos "texto", "data de publicação e "marcas", o indexador blob pode inferir o mapeamento correto sem uma lista campo de mapeamento explícita.

<a name="nested-json-arrays"></a>

## <a name="parse-nested-arrays"></a>Parse matrizes aninhadas
Para matrizes JSON com elementos aninhados, você pode especificar um `documentRoot` para indicar uma estrutura de vários níveis. Por exemplo, se o seu blob tiver esta aparência:

    {
        "level1" : {
            "level2" : [
                { "id" : "1", "text" : "Use the documentRoot property" },
                { "id" : "2", "text" : "to pluck the array you want to index" },
                { "id" : "3", "text" : "even if it's nested inside the document" }  
            ]
        }
    }

Use esta configuração para indexar a matriz contida na propriedade `level2`:

    {
        "name" : "my-json-array-indexer",
        ... other indexer properties
        "parameters" : { "configuration" : { "parsingMode" : "jsonArray", "documentRoot" : "/level1/level2" } }
    }

## <a name="parse-blobs-separated-by-newlines"></a>Bolhas de análise separadas por newlines

Se o seu blob contiver várias entidades JSON separadas por uma nova linha e você quiser que cada elemento se torne um documento separado do Azure Cognitive Search, você pode optar pela opção linhas JSON. Por exemplo, dada a seguinte bolha (onde há três entidades JSON diferentes), você pode preencher seu índice de Pesquisa Cognitiva Do Azure com três documentos separados, cada um com campos "id" e "text".

    { "id" : "1", "text" : "example 1" }
    { "id" : "2", "text" : "example 2" }
    { "id" : "3", "text" : "example 3" }

Para linhas JSON, a definição do indexador deve ser semelhante ao exemplo a seguir. Observe que o parâmetro parsingMode especifica o `jsonLines` analisador. 

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "my-json-indexer",
      "dataSourceName" : "my-blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "parameters" : { "configuration" : { "parsingMode" : "jsonLines" } }
    }

Novamente, observe que os mapeamentos de campo `jsonArray` podem ser omitidos, semelhante ao modo de análise.

## <a name="add-field-mappings"></a>Adicionar mapeamentos de campo

Quando os campos de origem e destino não estiverem devidamente alinhados, você pode definir uma seção de mapeamento de campo no corpo da solicitação de associações de campo para campo explícitas.

Atualmente, o Azure Cognitive Search não pode indexar documentos JSON arbitrários diretamente porque suporta apenas tipos de dados primitivos, arrays de strings e pontos GeoJSON. No entanto, você pode usar **mapeamentos de campo** para separar partes do documento JSON e "elevá-los" para campos de nível superior do documento de pesquisa. Para saber mais sobre o básico do mapeamento de campo, consulte [mapeamentos de campo em indexadores de pesquisa cognitiva do Azure](search-indexer-field-mappings.md).

Voltando ao nosso documento JSON de exemplo:

    {
        "article" : {
            "text" : "A hopefully useful article explaining how to parse JSON blobs",
            "datePublished" : "2016-04-13"
            "tags" : [ "search", "storage", "howto" ]    
        }
    }

Digamos que você tenha um índice de pesquisa com os seguintes campos: `text` do tipo `Edm.String`, `date` do tipo `Edm.DateTimeOffset` e `tags` do tipo `Collection(Edm.String)`. Observe a discrepância entre "data de publicação" na fonte e campo `date` no índice. Para mapear seu JSON para a forma desejada, use os seguintes mapeamentos de campo:

    "fieldMappings" : [
        { "sourceFieldName" : "/article/text", "targetFieldName" : "text" },
        { "sourceFieldName" : "/article/datePublished", "targetFieldName" : "date" },
        { "sourceFieldName" : "/article/tags", "targetFieldName" : "tags" }
      ]

Os nomes dos campos de origem nos mapeamentos são especificados usando a notação [JSON Pointer](https://tools.ietf.org/html/rfc6901) . Comece com uma barra invertida para referir-se à raiz do documento JSON, então selecione a propriedade desejada (em nível arbitrário de aninhamento) usando um caminho separado por barra invertida.

Você também pode se referir a elementos individuais da matriz usando um índice com base em zero. Por exemplo, para selecionar o primeiro elemento da matriz "tags" do exemplo anterior, use um mapeamento de campo como este:

    { "sourceFieldName" : "/article/tags/0", "targetFieldName" : "firstTag" }

> [!NOTE]
> Se o nome de um campo de origem em um caminho de mapeamento de campo se referir a uma propriedade que não existe em JSON, esse mapeamento será ignorado sem erro. Isso é feito para que possamos dar suporte a documentos com um esquema diferente (o que é um caso de uso comum). Como não há nenhuma validação, você precisa tomar cuidado para evitar erros de digitação na especificação do mapeamento do campo.
>
>

## <a name="see-also"></a>Confira também

+ [Indexadores na Pesquisa Cognitiva do Azure](search-indexer-overview.md)
+ [Indexação do armazenamento azure Blob com pesquisa cognitiva do Azure](search-howto-index-json-blobs.md)
+ [Indexação de bolhas CSV com indexador de bolhas de pesquisa cognitiva azure](search-howto-index-csv-blobs.md)
+ [Tutorial: Pesquise dados semi-estruturados do armazenamento Azure Blob](search-semi-structured-data.md)
