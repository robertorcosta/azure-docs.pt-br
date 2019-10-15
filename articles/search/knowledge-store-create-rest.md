---
title: Criar um repositório de conhecimento usando REST – Azure Search
description: Use a API REST e o Postman para criar um repositório de conhecimento do Azure Search para persistir enriquecimentos do pipeline de pesquisa cognitiva.
author: lobrien
services: search
ms.service: search
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: laobri
ms.openlocfilehash: b67f0cf60d279c7bc52b4114d29c37847f5c57f1
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2019
ms.locfileid: "72244461"
---
# <a name="create-an-azure-search-knowledge-store-by-using-rest"></a>Criar um repositório de conhecimento do Azure Search usando REST

O recurso de repositório de conhecimento do Azure Search persiste a saída de um pipeline de enriquecimento de IA para análise posterior ou para outro processamento downstream. Um pipeline enriquecido com IA aceita arquivos de imagem ou arquivos de texto não estruturado, indexa-os usando o Azure Search, aplica os enriquecimentos de IA dos Serviços Cognitivos do Azure (tais como análise de imagem e processamento de linguagem natural) e, em seguida, salva os resultados em um repositório de conhecimento no Armazenamento do Azure. É possível usar ferramentas como o Power BI ou o Gerenciador de Armazenamento no portal do Azure para explorar o repositório de conhecimento.

Neste artigo, você usa a interface da API REST para ingerir, indexar e aplicar enriquecimentos de IA a um conjunto de avaliações de hotéis. As avaliações de hotéis são importadas para o Armazenamento de Blobs do Azure. Os resultados são salvos como um repositório de conhecimento no armazenamento de Tabelas do Azure.

Depois de criar o repositório de conhecimento, você aprenderá a acessá-lo usando o [Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md) ou o [Power BI](knowledge-store-connect-power-bi.md).

## <a name="create-services"></a>Criar serviços

Crie os seguintes recursos:

- Crie um [serviço do Azure Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. Você pode usar um serviço gratuito para este tutorial.

- Crie uma [conta de armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account) para armazenar os dados de exemplo e o repositório de conhecimento. Sua conta de armazenamento precisa usar a mesma localização (como Oeste dos EUA) para o serviço Azure Search. O valor de **Tipo de conta** precisa ser **StorageV2 (uso geral V2)** (padrão) ou **Armazenamento (uso geral V1)** .

- Recomendável: Obtenha o [aplicativo da área de trabalho do Postman](https://www.getpostman.com/) para enviar solicitações para o Azure Search. Você pode usar a API REST com qualquer ferramenta que possa trabalhar com solicitações e respostas HTTP. O Postman é uma boa opção para explorar APIs REST. Nós o usamos neste artigo. Além disso, o [código-fonte](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) deste artigo inclui uma coleção de solicitações do Postman. 

## <a name="store-the-data"></a>Armazenar os dados

Carregue o arquivo CSV de resenhas de hotéis no Armazenamento de Blobs do Azure para que ele possa ser acessado por um indexador do Azure Search e alimentado por meio do pipeline de aprimoramento de IA.

### <a name="create-a-blob-container-by-using-the-data"></a>Criar um contêiner de blob usando os dados

1. Baixe os [dados de avaliações de hotel](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) salvos em um arquivo CSV (HotelReviews_Free.csv). Esses dados são originados do Kaggle.com e contêm comentários do cliente sobre hotéis.
1. Entre no [portal do Azure](https://portal.azure.com) e vá até sua conta de armazenamento do Azure.
1. Crie um [contêiner de blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal). Para criar o contêiner, no menu à esquerda de sua conta de armazenamento, selecione **Blobs** e, em seguida, selecione **Contêiner**.
1. Para o **Nome** do novo contêiner, insira **hotel-reviews**.
1. Selecione qualquer valor para **Nível de Acesso Público**. Nós usamos o padrão.
1. Selecione **OK** para criar o contêiner de blob.
1. Abra o novo contêiner **hotels-review**, selecione **Upload** e, em seguida, selecione o arquivo HotelReviews-Free.csv que você baixou na primeira etapa.

    ![Fazer upload dos dados](media/knowledge-store-create-portal/upload-command-bar.png "Carregar as resenhas de hotéis")

1. Selecione **Upload** para importar o arquivo CSV no Armazenamento de Blobs do Azure. O novo contêiner é mostrado:

    ![Criar o contêiner de blobs](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Criar o contêiner de blobs")

## <a name="configure-postman"></a>Configurar Postman

Instale e configure o Postman.

### <a name="download-and-install-postman"></a>Baixar e instalar o Postman

1. Baixe o [código-fonte da coleção do Postman](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. Selecione **Arquivo** > **Importar** para importar o código-fonte para o Postman.
1. Selecione a guia **Coleções** e, em seguida, selecione o botão **...** (reticências).
1. Selecione **Editar**. 
   
   ![Aplicativo do Postman mostrando a navegação](media/knowledge-store-create-rest/postman-edit-menu.png "Vá para o menu Editar do Postman")
1. Na caixa de diálogo **Editar**, selecione a guia **Variáveis**. 

Na guia **Variáveis**, você pode adicionar valores que o Postman insere sempre que encontra uma variável específica dentro de chaves duplas. Por exemplo, o Postman substituirá o símbolo `{{admin-key}}` pelo valor atual que você definir para `admin-key`. O Postman faz essa substituição nas URLs, nos cabeçalhos, no corpo da solicitação e assim por diante. 

Para obter o valor de `admin-key`, vá para o serviço do Azure Search e selecione a guia **Chaves**. Altere `search-service-name` e `storage-account-name` para os valores escolhidos em [Criar serviços](#create-services). Defina `storage-connection-string` usando o valor na guia **Chaves de Acesso** da conta de armazenamento. Deixe os valores padrão para os outros valores.

![Guia de variáveis do aplicativo do Postman](media/knowledge-store-create-rest/postman-variables-window.png "Janela de variáveis do Postman")


| Variável    | Onde obter |
|-------------|-----------------|
| `admin-key` | Na guia **Chaves** do serviço do Azure Search.  |
| `api-version` | Deixe como **2019-05-06-Preview**. |
| `datasource-name` | Deixe como **hotel-reviews-ds**. | 
| `indexer-name` | Deixe como **hotel-reviews-ixr**. | 
| `index-name` | Deixe como **hotel-reviews-ix**. | 
| `search-service-name` | O nome principal do serviço do Azure Search. A URL é `https://{{search-service-name}}.search.windows.net`. | 
| `skillset-name` | Deixe como **hotel-reviews-ss**. | 
| `storage-account-name` | O nome principal da conta de armazenamento. | 
| `storage-connection-string` | Na conta de armazenamento, na guia **Chaves de Acesso**, selecione **key1** > **Cadeia de conexão**. | 
| `storage-container-name` | Deixe como **hotel-reviews**. | 

### <a name="review-the-request-collection-in-postman"></a>Examinar a coleção de solicitações no Postman

Quando cria um repositório de conhecimento, você precisa emitir quatro solicitações HTTP: 

- **Solicitação PUT para criar o índice**: este índice armazena os dados usados e retornados pelo Azure Search.
- **Solicitação POST para criar a fonte de dados**: Essa fonte de dados conecta o comportamento do Azure Search à conta de armazenamento dos dados e do repositório de conhecimento. 
- **Solicitação PUT para criar o conjunto de habilidades**: o conjunto de habilidades especifica os enriquecimentos aplicados aos dados e à estrutura do repositório de conhecimento.
- **Solicitação PUT para criar o indexador**: A execução do indexador lê os dados, aplica o conjunto de habilidades e armazena os resultados. É necessário executar essa solicitação por último.

O [código-fonte](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) contém uma coleção do Postman que tem as quatro solicitações. Para emitir as solicitações, no Postman, selecione a guia para a solicitação. Em seguida, adicione os cabeçalhos de solicitação `api-key` e `Content-Type`. Defina o valor `api-key` como `{{admin-key}}`. Defina o valor `Content-type` como `application/json`. 

![Captura de tela mostrando a interface do Postman para cabeçalhos](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> Você precisa definir os cabeçalhos `api-key` e `Content-type` em todas as suas solicitações. Se o Postman reconhecer uma variável, ela aparecerá em texto laranja, como ocorre com `{{admin-key}}` na captura de tela anterior. Se a variável for digitada incorretamente, ela aparecerá em texto vermelho.
>

## <a name="create-an-azure-search-index"></a>Criar um índice de Azure Search

Crie um índice do Azure Search para representar os dados que você está interessado em pesquisar, filtrar e aplicar melhorias. Crie o índice emitindo uma solicitação PUT para `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}`. O Postman substituirá símbolos colocados entre chaves duplas (como `{{search-service-name}}`, `{{index-name}}` e `{{api-version}}`) pelos valores que você definir em [Configurar o Postman](#configure-postman). Se usar outra ferramenta para emitir os comandos REST, você precisará substituir essas variáveis por conta própria.

Defina a estrutura do índice do Azure Search no corpo da solicitação. No Postman, depois de definir os cabeçalhos `api-key` e `Content-type`, vá para o painel **Corpo** da solicitação. Você deverá ver o seguinte JSON. Se não vir, selecione **Bruto** > **JSON (aplicativo/json)** e, em seguida, cole o seguinte código como o corpo:

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

Essa definição de índice é uma combinação de dados que você deseja apresentar ao usuário (o nome do hotel, o conteúdo da avaliação, a data), metadados de pesquisa e dados de melhoria de IA (sentimento, frases-chave e idioma).

Selecione **Enviar** para emitir a solicitação PUT. Você deverá ver o status `201 - Created`. Se você vir um status diferente, no painel **Corpo**, procure uma resposta JSON que contenha uma mensagem de erro. 

## <a name="create-the-datasource"></a>Criar a fonte de dados

Em seguida, conecte o Azure Search aos dados de hotéis armazenados em [Armazenar os dados](#store-the-data). Para criar a fonte de dados, envie uma solicitação POST para `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}`. Você precisa definir os cabeçalhos `api-key` e `Content-Type`, conforme discutido anteriormente. 

No Postman, vá para a solicitação **Criar Fonte de Dados** e, em seguida, para o painel **Corpo**. Você deverá ver o seguinte código:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Selecione **Enviar** para emitir a solicitação POST. 

## <a name="create-the-skillset"></a>Criar o conjunto de habilidades 

A próxima etapa é especificar o conjunto de habilidades, que especifica as melhorias a serem aplicadas e o repositório de conhecimento no qual os resultados serão armazenados. No Postman, selecione a guia **Criar o Conjunto de Habilidades**. Essa solicitação envia um PUT para `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}`. Defina os cabeçalhos `api-key` e `Content-type`, assim como você fez anteriormente. 

Há dois objetos grandes de nível superior: `skills` e `knowledgeStore`. Cada objeto dentro do objeto `skills` é um serviço de enriquecimento. Cada serviço de enriquecimento tem `inputs` e `outputs`. A `LanguageDetectionSkill` tem uma saída `targetName` de `Language`. O valor desse nó é usado pela maioria das outras habilidades como entrada. A fonte é `document/Language`. Essa funcionalidade de usar a saída de um nó como a entrada de outro fica ainda mais evidente na `ShaperSkill`, que especifica como os dados fluem para as tabelas do repositório de conhecimento.

O objeto `knowledge_store` se conecta à conta de armazenamento por meio da variável `{{storage-connection-string}}` do Postman. `knowledge_store` contém um conjunto de mapeamentos entre o documento aprimorado e as tabelas e colunas no repositório de conhecimento. 

Para gerar o conjunto de habilidades, selecione o botão **Enviar** no Postman para efetuar PUT da solicitação:

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

## <a name="create-the-indexer"></a>Criar o indexador

A última etapa é criar o indexador. O indexador lê os dados e ativa o conjunto de habilidades. No Postman, selecione a solicitação **Criar Indexador** e examine o corpo. A definição do indexador refere-se a vários outros recursos que você já criou: a fonte de dados, o índice e o conjunto de habilidades. 

O objeto `parameters/configuration` controla como o indexador ingere os dados. Nesse caso, os dados de entrada estão em um único documento com uma linha de cabeçalho e valores separados por vírgula. A chave do documento é um identificador exclusivo para o documento. Antes da codificação, a chave do documento é a URL do documento de origem. Por fim, os valores de saída do conjunto de habilidades, como código de idioma, sentimento e frases-chave, são mapeados para seus locais no documento. Embora haja um único valor para `Language`, `Sentiment` é aplicado a cada elemento na matriz de `pages`. `Keyphrases` é uma matriz que também é aplicada a cada elemento na matriz `pages`.

Depois de definir os cabeçalhos `api-key` e `Content-type` e confirmar que o corpo da solicitação é semelhante ao código-fonte a seguir, selecione **Enviar** no Postman. O Postman envia uma solicitação PUT para `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}`. O Azure Search cria e executa o indexador. 

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

## <a name="run-the-indexer"></a>Executar o indexador 

Na portal do Azure, vá para a página **Visão geral** do serviço do Azure Search. Selecione a guia **Indexadores** e, em seguida, selecione **hotels-reviews-ixr**. Se o indexador ainda não tiver sido executado, selecione **Executar**. A tarefa de indexação pode gerar alguns avisos relacionados ao reconhecimento de idioma. Os dados incluem algumas avaliações escritas em idiomas que ainda não têm suporte das habilidades cognitivas. 

## <a name="next-steps"></a>Próximas etapas

Agora que você enriqueceu seus dados usando os Serviços Cognitivos e projetou os resultados em um repositório de conhecimento, use o Gerenciador de Armazenamento ou o Power BI para explorar o conjunto de dados enriquecido.

Para saber como explorar esse repositório de conhecimento usando o Gerenciador de Armazenamento, confira este passo a passo:

> [!div class="nextstepaction"]
> [Exibir com o Gerenciador de Armazenamento](knowledge-store-view-storage-explorer.md)

Para saber como conectar esse repositório de conhecimento ao Power BI, confira este passo a passo:

> [!div class="nextstepaction"]
> [Conectar com o Power BI](knowledge-store-connect-power-bi.md)

Se desejar repetir esse exercício ou experimentar um passo a passo de aprimoramento de IA diferente, exclua o indexador **hotel-reviews-idx**. A exclusão do indexador redefine o contador de transações diárias gratuito para zero.
