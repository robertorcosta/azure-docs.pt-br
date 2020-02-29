---
title: 'Tutorial: indexar dados semiestruturados em BLOBs JSON'
titleSuffix: Azure Cognitive Search
description: Saiba como indexar e pesquisar blobs JSON do Azure semiestruturados usando APIs REST da Pesquisa Cognitiva do Azure e o Postman.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: f025b3357943014a6d9c6e331c47f019fe94c5bf
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196936"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>Tutorial: indexar BLOBs JSON do armazenamento do Azure usando REST

A Pesquisa Cognitiva do Azure pode indexar matrizes e documentos JSON no armazenamento de blobs do Azure usando um [indexador](search-indexer-overview.md) que faz leitura de dados semiestruturados. Dados semi-estruturados contêm marcas ou marcações que separam o conteúdo dentro dos dados. Eles dividem a diferença entre dados não estruturados, que devem ser totalmente indexados, e dados estruturados formalmente que aderem a um modelo de dados, como um esquema de banco de dados relacional, que pode ser indexado por campo.

Este tutorial usa o postmaster e as [APIs REST de pesquisa](https://docs.microsoft.com/rest/api/searchservice/) para executar as seguintes tarefas:

> [!div class="checklist"]
> * Configurar uma fonte de dados da Pesquisa Cognitiva do Azure para um contêiner de blobs do Azure
> * Criar um índice da Pesquisa Cognitiva do Azure para conter conteúdo pesquisável
> * Configurar e executar um indexador para ler o contêiner e extrair conteúdo pesquisável do armazenamento de blobs do Azure
> * Pesquisar no índice que você acabou de criar

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Prerequisites

+ [Armazenamento do Azure](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account)
+ [Aplicativo Postman para a área de trabalho](https://www.getpostman.com/)
+ [Criar](search-create-service-portal.md) ou [localizar um serviço de pesquisa existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) 

> [!Note]
> Você pode usar o serviço gratuito para este tutorial. Um serviço de pesquisa gratuito limita você a três índices, três indexadores e três fontes de dados. Este tutorial cria um de cada. Antes de começar, verifique se você tem espaço em seu serviço para aceitar os novos recursos.

## <a name="download-files"></a>Baixar arquivos

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) contém os dados usados neste tutorial. Baixe e descompacte este arquivo em sua própria pasta. Para este tutorial, os dados são provenientes do [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results) convertidos para JSON.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

As chamadas REST exigem a URL do serviço e uma chave de acesso em cada solicitação. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou a Pesquisa Cognitiva do Azure à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

1. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-get-started-postman/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="prepare-sample-data"></a>Preparar os dados de exemplo

1. [Entre no portal do Azure](https://portal.azure.com), navegue até sua conta de Armazenamento do Azure, clique em **Blobs** e, em seguida, clique em **+ Contêiner**.

1. [Crie um contêiner de Blob](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) para conter dados de exemplo. Você pode definir o Nível de Acesso Público para qualquer um de seus valores válidos.

1. Depois que o contêiner for criado, abra-o e selecione **Carregar** na barra de comandos.

   ![Carregar na barra de comandos](media/search-semi-structured-data/upload-command-bar.png "Carregar na barra de comandos")

1. Navegue até a pasta que contém os arquivos de exemplo. Selecione todos eles e, em seguida, clique em **Carregar**.

   ![Carregar arquivos](media/search-semi-structured-data/clinicalupload.png "Carregar arquivos")

Após o upload ser concluído, os arquivos devem aparecer em sua própria subpasta dentro do contêiner de dados.

## <a name="set-up-postman"></a>Configurar o Postman

Inicie o Postman e configure uma solicitação HTTP. Se não estiver familiarizado com essa ferramenta, consulte [Explorar APIs REST da Pesquisa Cognitiva do Azure usando Postman](search-get-started-postman.md).

O método de solicitação para cada chamada neste tutorial é **POST**. As chaves de cabeçalho são "Content-type" e "api-key". Os valores das chaves de cabeçalho são "application/json" e sua "chave de administrador" (a chave de administrador é um espaço reservado para sua chave primária de pesquisa) respectivamente. O corpo é onde você coloca o conteúdo real de sua chamada. Dependendo do cliente que você está usando, talvez haja algumas variações em como você cria sua consulta, mas essas são as noções básicas.

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/postmanoverview.png)

Estamos usando o Postman fazer três chamadas à API para o serviço de pesquisa para criar uma fonte de dados, um índice e um indexador. A fonte de dados inclui um ponteiro para sua conta de armazenamento e seus dados JSON. O serviço de pesquisa faz a conexão ao carregar os dados.

Cadeias de caracteres de consulta devem especificar uma versão de API, e cada chamada de consulta deve retornar **201 criado**. A versão de API disponível em geral para o uso de matrizes JSON é `2019-05-06`.

Execute as seguintes três chamadas à API do cliente REST.

## <a name="create-a-data-source"></a>Criar uma fonte de dados

A [API criar fonte de dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source) cria um objeto de pesquisa cognitiva do Azure que especifica quais dados indexar.

O ponto de extremidade desta chamada é `https://[service name].search.windows.net/datasources?api-version=2019-05-06`. Substitua `[service name]` pelo nome do serviço de pesquisa. 

Para essa chamada, o corpo da solicitação deve incluir o nome da sua conta de armazenamento, a chave de conta de armazenamento e o nome do contêiner de blob. A chave de conta de armazenamento pode ser encontrada no Portal do Azure dentro das **Chaves de Acesso** da sua conta de armazenamento. A localização é mostrada na imagem a seguir:

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/storagekeys.png)

Substitua `[storage account name]`, `[storage account key]` e `[blob container name]` no corpo da chamada antes de executar a chamada.

```json
{
    "name" : "clinical-trials-json",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
    "container" : { "name" : "[blob container name]"}
}
```

A resposta deve ser semelhante a:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
    "@odata.etag": "\"0x8D505FBC3856C9E\"",
    "name": "clinical-trials-json",
    "description": null,
    "type": "azureblob",
    "subtype": null,
    "credentials": {
        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
    },
    "container": {
        "name": "[mycontainernamehere]",
        "query": null
    },
    "dataChangeDetectionPolicy": null,
    "dataDeletionDetectionPolicy": null
}
```

## <a name="create-an-index"></a>Crie um índice
    
A segunda chamada é à [API de Criação de Índice](https://docs.microsoft.com/rest/api/searchservice/create-index), criando um índice da Pesquisa Cognitiva do Azure que armazena todos os dados pesquisáveis. Um índice especifica todos os parâmetros e seus atributos.

A URL para esta chamada é `https://[service name].search.windows.net/indexes?api-version=2019-05-06`. Substitua `[service name]` pelo nome do serviço de pesquisa.

Primeiro, substitua a URL. Em seguida, copie e cole o código a seguir no corpo e execute a consulta.

```json
{
  "name": "clinical-trials-json-index",  
  "fields": [
  {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
  {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
  {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
  {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
  {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
  {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
  {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
  {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
  {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
  {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
  ],
  "suggesters": [
  {
    "name": "sg",
    "searchMode": "analyzingInfixMatching",
    "sourceFields": ["Title"]
  }
  ]
}
```

A resposta deve ser semelhante a:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
    "@odata.etag": "\"0x8D505FC00EDD5FA\"",
    "name": "clinical-trials-json-index",
    "fields": [
        {
            "name": "FileName",
            "type": "Edm.String",
            "searchable": false,
            "filterable": false,
            "retrievable": true,
            "sortable": true,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        {
            "name": "Description",
            "type": "Edm.String",
            "searchable": true,
            "filterable": false,
            "retrievable": false,
            "sortable": false,
            "facetable": false,
            "key": false,
            "indexAnalyzer": null,
            "searchAnalyzer": null,
            "analyzer": null,
            "synonymMaps": []
        },
        ...
          "scoringProfiles": [],
    "defaultScoringProfile": null,
    "corsOptions": null,
    "suggesters": [],
    "analyzers": [],
    "tokenizers": [],
    "tokenFilters": [],
    "charFilters": []
}
```

## <a name="create-and-run-an-indexer"></a>Criar e executar um indexador

Um indexador conecta a fonte de dados, importa dados para o índice de pesquisa de destino e, opcionalmente, fornece um planejamento para automatizar a atualização de dados. A API REST é [Criar Indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

A URL para esta chamada é `https://[service name].search.windows.net/indexers?api-version=2019-05-06`. Substitua `[service name]` pelo nome do serviço de pesquisa.

Primeiro, substitua a URL. Em seguida, copie e cole o código a seguir no corpo e envie a solicitação. A solicitação é processada imediatamente. Quando a resposta voltar, você terá um índice que é pesquisável em texto completo.

```json
{
  "name" : "clinical-trials-json-indexer",
  "dataSourceName" : "clinical-trials-json",
  "targetIndexName" : "clinical-trials-json-index",
  "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
}
```

A resposta deve ser semelhante a:

```json
{
    "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
    "@odata.etag": "\"0x8D505FDE143D164\"",
    "name": "clinical-trials-json-indexer",
    "description": null,
    "dataSourceName": "clinical-trials-json",
    "targetIndexName": "clinical-trials-json-index",
    "schedule": null,
    "parameters": {
        "batchSize": null,
        "maxFailedItems": null,
        "maxFailedItemsPerBatch": null,
        "base64EncodeKeys": null,
        "configuration": {
            "parsingMode": "jsonArray"
        }
    },
    "fieldMappings": [],
    "enrichers": [],
    "disabled": null
}
```

## <a name="search-your-json-files"></a>Pesquisar os arquivos JSON

Você poderá iniciar a pesquisa assim que o primeiro documento for carregado. Para essa tarefa, use o [**Gerenciador de pesquisa**](search-explorer.md) no portal.

No portal do Azure, abra a página **Visão Geral** do o serviço de pesquisa e localize o índice criado na lista **Índices**.

Escolha o índice que você acabou de criar. 

  ![Pesquisa não estruturada](media/search-semi-structured-data/indexespane.png)

### <a name="user-defined-metadata-search"></a>Pesquisa de metadados definida pelo usuário

Como antes, os dados podem ser consultados de várias maneiras: pesquisa de texto completo, propriedades do sistema ou metadados definidos pelo usuário. As propriedades do sistema e os metadados definidos pelo usuário podem ser pesquisados com o parâmetro `$select` apenas se forem marcados como **recuperáveis** durante a criação do índice de destino. Os parâmetros no índice não podem ser alterados depois que são criados. No entanto, parâmetros adicionais podem ser adicionados.

Um exemplo de uma consulta básica é `$select=Gender,metadata_storage_size`, que limita o retorno para esses dois parâmetros.

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/lastquery.png)

Um exemplo de consulta mais complexa seria `$filter=MinimumAge ge 30 and MaximumAge lt 75`, que retorna apenas resultados em que o parâmetro MinimumAge é maior que ou igual a 30 e o MaximumAge é menor que 75.

  ![Pesquisa semi-estruturada](media/search-semi-structured-data/metadatashort.png)

Se você quiser experimentar e tentar algumas consultas adicionais por conta própria, fique à vontade para fazer isso. Saiba que você pode usar operadores lógicos (and, or, not) e operadores de comparação (eq, ne, gt, lt, ge, le). Comparações de cadeia de caracteres diferenciam maiúsculas de minúsculas.

O parâmetro `$filter` funciona somente com metadados que foram marcadas como filtráveis na criação do índice.

## <a name="reset-and-rerun"></a>Redefinir e execute novamente

Nos estágios experimentais antecipados do desenvolvimento, a abordagem mais prática para a iteração de design é excluir os objetos do Azure Pesquisa Cognitiva e permitir que seu código os reconstrua. Nomes de recurso são exclusivos. Excluir um objeto permite que você recriá-la usando o mesmo nome.

Você pode usar o portal para excluir índices, indexadores e fontes de dados. Ou use **delete** e forneça URLs para cada objeto. O comando a seguir exclui um indexador.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2019-05-06
```

Código de status 204 é retornado na exclusão com êxito.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando você está trabalhando em sua própria assinatura, no final de um projeto, é uma boa ideia remover os recursos que já não são necessários. Recursos deixados em execução podem custar dinheiro. Você pode excluir os recursos individualmente ou excluir o grupo de recursos para excluir todo o conjunto de recursos.

Você pode encontrar e gerenciar recursos no portal, usando o link todos os recursos ou grupos de recursos no painel de navegação esquerdo.

## <a name="next-steps"></a>Próximas etapas

Agora que você está familiarizado com os conceitos básicos da indexação de BLOBs do Azure, vamos examinar mais de perto a configuração do indexador.

> [!div class="nextstepaction"]
> [Configurar um indexador de armazenamento de BLOBs do Azure](search-howto-indexing-azure-blob-storage.md)