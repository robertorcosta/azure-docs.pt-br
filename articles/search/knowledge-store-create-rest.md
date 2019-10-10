---
title: Criar um repositório de conhecimento usando a REST – Azure Search
description: Crie um repositório de conhecimento do Azure Search para persistir enriquecimentos do pipeline de pesquisa cognitiva usando a API REST e o Postman.
author: lobrien
services: search
ms.service: search
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: laobri
ms.openlocfilehash: 26dc66474eecffd7f5a34bcfcaf93fd49f59606c
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/03/2019
ms.locfileid: "71936513"
---
# <a name="create-an-azure-search-knowledge-store-using-rest"></a>Criar um repositório de conhecimento do Azure Search usando a REST

O repositório de conhecimento é um recurso do Azure Search que persiste a saída de um pipeline de enriquecimento de IA para análise posterior ou outro processamento downstream. Um pipeline aprimorado por IA aceita arquivos de imagem ou arquivos de texto não estruturados, indexa-os usando o Azure Search, aplica os aprimoramentos de IA dos Serviços Cognitivos (tais como análise de imagem e processamento de linguagem natural) e, em seguida, salva os resultados em um repositório de conhecimento no Armazenamento do Azure. É possível usar ferramentas como o Power BI ou o Gerenciador de Armazenamento para explorar o repositório de conhecimento.

Neste artigo, você usará a interface da API REST para ingerir, indexar e aplicar enriquecimentos de IA a um conjunto de avaliações de hotéis. As avaliações de hotéis são importadas para o Armazenamento de Blobs do Azure. Os resultados são salvos como um repositório de conhecimento no Armazenamento de Tabelas do Azure.

Depois de criar o repositório de conhecimento, você poderá aprender a acessá-lo usando o [Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md) ou o [Power BI](knowledge-store-connect-power-bi.md).

## <a name="1---create-services"></a>1 – Criar serviços

+ [Crie um serviço Azure Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. Você pode usar um serviço gratuito para este tutorial.

+ [Crie uma conta de Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar dados de exemplo e o repositório de conhecimento. Sua conta de armazenamento precisa usar a mesma localização (como Oeste dos EUA) para o serviço Azure Search. O *Tipo de conta* precisa ser *StorageV2 (uso geral V2)* (padrão) ou *Armazenamento (uso geral V1)* .

+ Recomendável: [Aplicativo da área de trabalho do Postman](https://www.getpostman.com/) para enviar solicitações para o Azure Search. Você pode usar a API REST com qualquer ferramenta que possa trabalhar com solicitações e respostas HTTP. O Postman é uma boa opção para explorar as APIs REST e será usado neste artigo. Além disso, o [código-fonte](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) para este artigo inclui uma coleção do Postman de solicitações. 

## <a name="2---store-the-data"></a>2 – Armazenar os dados

Carregue o arquivo CSV de resenhas de hotéis no Armazenamento de Blobs do Azure para que ele possa ser acessado por um indexador do Azure Search e alimentado por meio do pipeline de aprimoramento de IA.

### <a name="create-an-azure-blob-container-with-the-data"></a>Criar um contêiner de Blobs do Azure com os dados

1. [Baixe os dados de resenhas de hotel salvos em um arquivo CSV (HotelReviews_Free.csv)](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Esses dados são originados do Kaggle.com e contêm comentários do cliente sobre hotéis.
1. [Entre no portal do Azure](https://portal.azure.com) e navegue até sua conta de armazenamento do Azure.
1. [Crie um contêiner de blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Para criar o contêiner, na barra de navegação à esquerda de sua conta de armazenamento, clique em **Blobs** e, em seguida, clique em **+ Contêiner** na barra de comandos.
1. Para o novo contêiner **Nome**, insira `hotel-reviews`.
1. Selecione qualquer **Nível de Acesso Público**. Nós usamos o padrão.
1. Clique em **OK** para criar o contêiner de Blobs do Azure.
1. Abra o novo contêiner `hotels-review`, clique em **Upload** e selecione o arquivo **HotelReviews-Free.csv** que você baixou na primeira etapa.

    ![Fazer upload dos dados](media/knowledge-store-create-portal/upload-command-bar.png "Carregar as resenhas de hotéis")

1. Clique em **Upload** para importar o arquivo CSV no Armazenamento de Blobs do Azure. O novo contêiner será exibido.

    ![Criar o contêiner de Blobs do Azure](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "o contêiner de Blobs do Azure")

## <a name="3---configure-postman"></a>3 – Configurar o Postman

Baixe o [código-fonte da coleção do Postman](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) e importe-o para o Postman usando **Arquivo, Importar...** . Alterne para a guia **Coleções**, clique no botão **...** e selecione **Editar**. 

![Aplicativo do Postman mostrando a navegação](media/knowledge-store-create-rest/postman-edit-menu.png "Navegar no menu Editar do Postman")

Na caixa de diálogo Editar resultante, navegue até a guia **Variáveis**. 

A guia **Variáveis** permite adicionar valores que o Postman trocará sempre que encontrá-los entre chaves duplas. Por exemplo, o Postman substituirá o símbolo `{{admin-key}}` pelo "valor atual" da `admin-key`. O Postman fará essa substituição nas URLs, nos cabeçalhos, no corpo da solicitação e assim por diante. 

Você encontrará o valor de `admin-key` na guia **Chaves** do Serviço de Pesquisa. Você precisará alterar `search-service-name` e `storage-account-name` para os valores escolhidos na [Etapa 1](#1---create-services). Defina `storage-connection-string` com base no valor na guia **Chaves de Acesso** da conta de armazenamento. Os outros valores podem ser mantidos inalterados.

![Guia de variáveis do aplicativo do Postman](media/knowledge-store-create-rest/postman-variables-window.png "Janela de variáveis do Postman")


| Variável    | Onde obter |
|-------------|-----------------|
| `admin-key` | Serviço de Pesquisa, guia **Chaves**              |
| `api-version` | Mantenha como "2019-05-06-Preview" |
| `datasource-name` | Mantenha como "hotel-reviews-ds" | 
| `indexer-name` | Mantenha como "hotel-reviews-ixr" | 
| `index-name` | Mantenha como "hotel-reviews-ix" | 
| `search-service-name` | Serviço de Pesquisa, nome principal. A URL é `https://{{search-service-name}}.search.windows.net` | 
| `skillset-name` | Mantenha como "hotel-reviews-ss" | 
| `storage-account-name` | Conta de armazenamento, nome principal | 
| `storage-connection-string` | Conta de armazenamento, guia **Chaves de Acesso**, **Cadeia de conexão** de **chave1** | 
| `storage-container-name` | Mantenha como "hotel-reviews" | 

### <a name="review-the-request-collection-in-postman"></a>Examinar a coleção de solicitações no Postman

A criação de um repositório de conhecimento exige a emissão de quatro solicitações HTTP: 

1. Uma solicitação PUT para criar o índice. Esse índice armazena os dados usados e retornados pelo Azure Search.
1. Uma solicitação POST para criar a fonte de dados. Essa fonte de dados conecta o comportamento do Azure Search à conta de armazenamento dos dados e do repositório de conhecimento. 
1. Uma solicitação PUT para criar o conjunto de habilidades. O conjunto de habilidades especifica os enriquecimentos aplicados aos dados e à estrutura do repositório de conhecimento.
1. Uma solicitação PUT para criar o indexador. A execução do indexador lê os dados, aplica o conjunto de habilidades e armazena os resultados. É necessário executar essa solicitação por último.

O [código-fonte](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) contém uma coleção do Postman com essas quatro solicitações. Para emitir as solicitações, alterne para a guia da solicitação no Postman e adicione os cabeçalhos de solicitação `api-key` e `Content-Type`. Defina o valor `api-key` como `{{admin-key}}`. Defina o valor `Content-type` como `application/json`. 

> [!div class="mx-imgBorder"]
> ![Captura de tela mostrando a interface do Postman para cabeçalhos](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Você precisará definir os cabeçalhos `api-key` e `Content-type` em todas as solicitações. Se uma variável for reconhecida pelo Postman, ela será renderizada em um texto laranja, assim como mostrado em `{{admin-key}}` na captura de tela. Se a variável for digitada incorretamente, ela será renderizada em um texto vermelho.
>

## <a name="4---create-an-azure-search-index"></a>4 – Criar um índice do Azure Search

Você precisa criar um índice do Azure Search para representar os dados nos quais você está interessado em pesquisar, filtrar e fazer melhorias. Crie o índice emitindo uma solicitação PUT para `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. O Postman substituirá os símbolos entre as chaves duplas, como `{{search-service-name}}`, `{{index-name}}` e `{{api-version}}`, pelos valores especificados na [Etapa 3](#3---configure-postman). Se você estiver usando outra ferramenta para emitir os comandos REST, precisará substituir essas variáveis por conta própria.

Especifique a estrutura do índice do Azure Search no corpo da solicitação. No Postman, depois de definir os cabeçalhos `api-key` e `Content-type`, alterne para o painel **Corpo** da solicitação. Você deverá ver o seguinte JSON, mas se não estiver, escolha **Bruto** e **JSON (application/json)** e cole o seguinte código como o corpo:

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

Você verá que essa definição de índice é uma combinação de dados que você deseja apresentar ao usuário (nome do hotel, conteúdo da avaliação, data e assim por diante), metadados de pesquisa e dados de melhoria de IA (sentimento, frases-chave e idioma).

Pressione o botão **Enviar** para emitir a solicitação PUT. Você deverá receber a mensagem de status `201 - Created`. Se você receber outro status, o painel **Corpo** mostrará uma resposta JSON com uma mensagem de erro. 

## <a name="5---create-the-datasource"></a>5 – Criar a fonte de dados

Agora, você precisa conectar o Azure Search aos dados de hotéis armazenados na [Etapa 2](#2---store-the-data). A criação da fonte de dados é feita com um POST para `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. Novamente, você precisará definir os cabeçalhos `api-key` e `Content-Type`, conforme especificado anteriormente. 

No Postman, abra a solicitação "Criar fonte de dados". Alterne para o painel **Corpo**, que deverá ter o seguinte código:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Pressione o botão **Enviar** para emitir a solicitação POST. 

## <a name="6---create-the-skillset"></a>6 – Criar o conjunto de habilidades 

A próxima etapa é especificar o conjunto de habilidades, que especifica as melhorias a serem aplicadas e o repositório de conhecimento no qual os resultados serão armazenados. No Postman, abra a guia "Criar o Conjunto de Habilidades". Essa solicitação envia um PUT para `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`.
Defina os cabeçalhos `api-key` e `Content-type` como você fez anteriormente. 

Há dois objetos grandes de nível superior: `"skills"` e `"knowledgeStore"`. Cada objeto dentro do objeto `"skills"` é um serviço de enriquecimento. Cada serviço de enriquecimento tem `"inputs"` e `"outputs"`. Observe como a `LanguageDetectionSkill` tem uma saída `targetName` de `"Language"`. O valor desse nó é usado pela maioria das outras habilidades como entrada, com a origem como `document/Language`. Essa funcionalidade de usar a saída de um nó como a entrada para outro fica ainda mais evidente na `ShaperSkill`, que especifica como os dados fluirão para as tabelas do repositório de conhecimento.

O objeto `"knowledge_store"` se conecta à conta de armazenamento por meio da variável `{{storage-connection-string}}` do Postman. Em seguida, ele contém um conjunto de mapeamentos entre o documento aprimorado e as tabelas e as colunas que ficarão disponíveis no próprio repositório de conhecimento. 

Para gerar o conjunto de habilidades, execute uma operação PUT para a solicitação pressionando o botão **Enviar** no Postman.

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="7---create-the-indexer"></a>7 – Criar o indexador

A etapa final é criar o indexador, que, na verdade, lê os dados e ativa o conjunto de habilidades. No Postman, alterne para a solicitação "Criar indexador" e examine o corpo. Como você pode ver, a definição do indexador refere-se a vários outros recursos que você já criou – a fonte de dados, o índice e o conjunto de habilidades. 

O objeto `"parameters/configuration"` controla como o indexador ingere os dados. Nesse caso, os dados de entrada estão em um único documento com uma linha de cabeçalho e valores separados por vírgula. A chave do documento é um identificador exclusivo para o documento, que antes da codificação é a URL do documento de origem. Por fim, os valores de saída do conjunto de habilidades, como código de idioma, sentimento e frases-chave, são mapeados para os locais apropriados no documento. Observe que, embora haja um único valor para `Language`, `Sentiment` é aplicado a cada elemento na matriz de `pages`. `Keyphrases` é, em si, uma matriz e também é aplicada a cada elemento na matriz `pages`.

Depois de definir os cabeçalhos `api-key` e `Content-type` e confirmar que o corpo da solicitação é semelhante ao código-fonte a seguir, pressione **Enviar** no Postman. O Postman executará a operação PUT para a solicitação em `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`. O Azure Search criará e executará o indexador. 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="8---run-the-indexer"></a>8 – Executar o indexador 

Na portal do Azure, navegue até a **Visão geral** do Serviço de Pesquisa e selecione a guia **Indexadores**. Clique em **hotels-reviews-ixr** criado na etapa anterior. Se o indexador ainda não tiver sido executado, pressione o botão **Executar**. A tarefa de indexação poderá gerar alguns avisos relacionados ao reconhecimento de idioma, pois os dados incluem algumas avaliações escritas em idiomas ainda sem suporte nas habilidades cognitivas. 

## <a name="next-steps"></a>Próximas etapas

Agora que você enriqueceu seus dados usando os Serviços Cognitivos e projetou os resultados em um repositório de conhecimento, use o Gerenciador de Armazenamento ou o Power BI para explorar o conjunto de dados enriquecido.

Para saber como explorar esse repositório de conhecimento usando o Gerenciador de Armazenamento, confira o seguinte passo a passo.

> [!div class="nextstepaction"]
> [Exibir com o Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md)

Para saber como conectar esse repositório de conhecimento ao Power BI, confira o seguinte passo a passo.

> [!div class="nextstepaction"]
> [Conectar com o Power BI](knowledge-store-connect-power-bi.md)

Se desejar repetir esse exercício ou experimentar um passo a passo de aprimoramento de IA diferente, exclua o indexador *hotel-reviews-idx*. A exclusão do indexador redefine o contador de transações diárias gratuito para zero.
