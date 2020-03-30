---
title: Pesquisar sobre o conteúdo de armazenamento do Azure Blob
titleSuffix: Azure Cognitive Search
description: Aprenda a indexar o Azure Blob Storage e extrair texto de documentos com o Azure Cognitive Search.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 5df1198e6681431738f886eb7c3ad549936eab1a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067655"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>Como indexar documentos no Azure Blob Storage com a Pesquisa Cognitiva do Azure

Este artigo mostra como usar o Azure Cognitive Search para indexar documentos (como PDFs, documentos do Microsoft Office e vários outros formatos comuns) armazenados no armazenamento Azure Blob. Primeiro, ele explica as noções básicas de configuração de um indexador de blob. Em seguida, ele explora mais profundamente os comportamentos e cenários que você pode encontrar.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Formatos de documento com suporte
O indexador de blob pode extrair o texto dos seguintes formatos de documento:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Configuração da indexação de blob
Você pode configurar um indexador do Armazenamento de Blobs do Azure usando:

* [Portal Azure](https://ms.portal.azure.com)
* [API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) de azure Cognitive Search REST
* Azure Cognitive Search [.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> Alguns recursos (por exemplo, mapeamentos de campo) ainda não estão disponíveis no portal e precisam ser usados por meio de programação.
>

Aqui, demonstraremos o fluxo usando a API REST.

### <a name="step-1-create-a-data-source"></a>Etapa 1: Criar uma fonte de dados
Uma fonte de dados especifica quais dados indexar, as credenciais necessárias para acessar os dados e as políticas que identificam com eficiência as alterações nos dados (linhas novas, modificadas ou excluídas). Uma fonte de dados pode ser usada por vários indexadores no mesmo serviço de pesquisa.

Para a indexação de blobs, a fonte de dados deve ter as seguintes propriedades requeridas:

* **name** é o nome exclusivo da fonte de dados dentro de seu serviço de pesquisa.
* **type** deve ser `azureblob`.
* **credentials** fornece a cadeia de conexão da conta de armazenamento como o parâmetro `credentials.connectionString`. Consulte [Como especificar credenciais](#Credentials) abaixo para obter detalhes.
* **container** especifica um contêiner em sua conta de armazenamento. Por padrão, todos os blobs no contêiner são recuperáveis. Se você quiser apenas blobs de índice em um diretório virtual específico, especifique o diretório usando o parâmetro opcional **query**.

Para criar uma fonte de dados:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

Para obter mais informações sobre Criar a API da Fonte de Dados, consulte [Criar Fonte de Dados](https://docs.microsoft.com/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>Como especificar credenciais ####

Você pode fornecer as credenciais para o contêiner de blobs de uma das seguintes maneiras:

- **Cadeia de conexão** `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` de conta de armazenamento de acesso completo : Você pode obter a seqüência de conexão do portal Azure navegando até o blade da conta de armazenamento > Configurações > Keys (para contas de armazenamento clássicas) ou configurações > chaves de acesso (para contas de armazenamento do Azure Resource Manager).
- Cadeia de conexão da SAS **(assinatura de acesso compartilhado) da conta de armazenamento**: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` o SAS deve ter a lista e permissões de leitura para os contêineres e objetos (blobs, neste caso).
-  **Assinatura de acesso** `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` compartilhado do contêiner : O SAS deve ter a lista e as permissões de leitura no contêiner.

Para saber mais sobre assinaturas de acesso compartilhado de armazenamento, veja [Uso de Assinaturas de Acesso Compartilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Se você usar credenciais SAS, você precisará atualizar as credenciais de fonte de dados periodicamente com assinaturas renovadas para impedir sua expiração. Se as credenciais SAS expirarem, o indexador irá falhar com uma mensagem de erro semelhante à `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Etapa 2: Criar um índice
O índice especifica os campos em um documento, atributos e outras construções que modelam a experiência de pesquisa.

Veja como criar um índice com um campo `content` pesquisável para armazenar o texto extraído dos blobs:   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Para obter mais informações sobre a criação de índices, consulte [Criar índice](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Etapa 3: Criar um indexador
Um indexador conecta uma fonte de dados a um índice de pesquisa de destino e fornece um agendamento para automatizar a atualização de dados.

Uma vez que o índice e a fonte de dados forem criados, será possível criar o indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

Esse indexador será executado a cada duas horas (o intervalo de agendamento é definido como "PT2H"). Para executar um indexador a cada 30 minutos, defina o intervalo para "PT30M". O intervalo mais curto com suporte é de 5 minutos. O agendamento é opcional – se ele for omitido, um indexador será executado apenas uma vez quando for criado. No entanto, você pode executar um indexador sob demanda a qualquer momento.   

Para saber mais sobre a API Criar Indexador, veja [Criar indexador](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Para obter mais informações sobre como definir horários de indexadores, [consulte Como agendar indexadores para a Pesquisa Cognitiva do Azure](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Como o Azure Cognitive Search indexa blobs

Dependendo da [configuração do indexador](#PartsOfBlobToIndex), o indexador de blobs pode indexar somente metadados de armazenamento (algo útil quando você está preocupado apenas com os metadados e não precisa indexar o conteúdo dos blobs), metadados de armazenamento e conteúdo ou conteúdo textual e de metadados. Por padrão, o indexador extrai os metadados e o conteúdo.

> [!NOTE]
> Por padrão, os blobs com conteúdo estruturado, como JSON, CSV e indexados como uma única parte de texto. Se você quiser indexar as bolhas JSON e CSV de forma estruturada, consulte [Indexando blobs JSON](search-howto-index-json-blobs.md) e [Indexando blobs CSV](search-howto-index-csv-blobs.md) para obter mais informações.
>
> Um documento composto ou incorporado (como um arquivo ZIP ou um documento do Word com email do Outlook incorporado contendo anexos) também é indexado como um único documento.

* O conteúdo textual do documento é extraído para um campo de cadeia de caracteres chamado `content`.

> [!NOTE]
> A Pesquisa Cognitiva do Azure limita a quantidade de texto que extrai dependendo do nível de preços: 32.000 caracteres para nível Livre, 64.000 para o Básico, 4 milhões para o Standard, 8 milhões para o Standard S2 e 16 milhões para o Standard S3. Um aviso é incluído na resposta de status do indexador para documentos truncados.  

* As propriedades de metadados especificadas pelo usuário e presentes no blob, se houver alguma, são extraídas literalmente. Observe que isso requer que um campo seja definido no índice com o mesmo nome da tecla de metadados da bolha. Por exemplo, se a sua bolha `Sensitivity` tiver `High`uma chave de metadados de valor, você deve definir um campo nomeado `Sensitivity` no seu índice de pesquisa e ele será preenchido com o valor `High`.
* As propriedades de metadados de blob padrão são extraídas para os seguintes campos:

  * **metadata\_storage\_name** (Edm.String): o nome do arquivo do blob. Por exemplo, se você tiver um blob /my-container/my-folder/subfolder/resume.pdf, o valor desse campo será `resume.pdf`.
  * **metadata\_storage\_path** (Edm.String): o URI completo do blob, incluindo a conta de armazenamento. Por exemplo, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadata\_storage\_content\_type** (Edm.String): o tipo de conteúdo, conforme especificado pelo código usado para carregar o blob. Por exemplo, `application/octet-stream`.
  * **metadata\_storage\_last\_modified** (Edm.DateTimeOffset): último carimbo de data/hora modificado para o blob. O Azure Cognitive Search usa este carimbo de tempo para identificar bolhas alteradas, para evitar reindexar tudo após a indexação inicial.
  * **metadata\_storage\_size** (Edm.Int64): tamanho do blob em bytes.
  * **metadata\_storage\_content\_md5** (Edm.String): hash MD5 do conteúdo do blob, se estiver disponível.
  * **metadata\_\_storage\_sas token** (Edm.String) - Um token SAS temporário que pode ser usado por [habilidades personalizadas](cognitive-search-custom-skill-interface.md) para ter acesso à bolha. Este token não deve ser armazenado para uso posterior, pois pode expirar.

* As propriedades de metadados específicas a cada formato de documento são extraídas nos campos listados [aqui](#ContentSpecificMetadata).

Não é necessário definir os campos para todas as propriedades acima em seu índice de pesquisa, basta capturar as propriedades necessárias para seu aplicativo.

> [!NOTE]
> Geralmente, os nomes de campo no índice existente serão diferentes dos nomes de campo gerados durante a extração do documento. Você pode usar **mapeamentos de campo** para mapear os nomes de propriedades fornecidos pelo Azure Cognitive Search para os nomes de campo em seu índice de pesquisa. Você verá um exemplo de uso de mapeamento de campo abaixo.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definir chaves de documento e mapeamentos de campo
Na Pesquisa Cognitiva do Azure, a chave do documento identifica exclusivamente um documento. Cada índice de pesquisa deve ter exatamente um campo de chave do tipo Edm.String. O campo de chave é necessário para cada documento adicionado ao índice (é, na verdade, o único campo obrigatório).  

Você deve considerar cuidadosamente qual campo extraído deve ser mapeado para o campo de chave de seu índice. Os candidatos são:

* **metadata\_storage\_name**: este pode ser um candidato conveniente, mas observe que 1) talvez os nomes não sejam exclusivos, pois você pode ter blobs com o mesmo nome em pastas diferentes, e 2) o nome pode conter caracteres inválidos em chaves de documento, por exemplo, traços. Você pode lidar com caracteres inválidos usando a `base64Encode` [função de mapeamento de campo](search-indexer-field-mappings.md#base64EncodeFunction). Se fizer isso, lembre-se de codificar as chaves de documento ao transmiti-las em chamadas à API, como Pesquisa. (Por exemplo, em .NET você pode usar o método [UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) para essa finalidade).
* **metadata\_storage\_path**: o uso do caminho completo garante a exclusividade, mas o caminho contém definitivamente caracteres `/` que são [inválidos em uma chave de documento](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Como foi mencionado acima, você tem a opção de codificar as chaves usando a  [função](search-indexer-field-mappings.md#base64EncodeFunction)`base64Encode`.
* Se nenhuma das opções acima funcionar para você, adicione uma propriedade de metadados personalizada aos blobs. No entanto, essa opção exige que seu processo de carregamento de blob adicione essa propriedade de metadados a todos os blobs. Como a chave é uma propriedade obrigatória, todos os blobs que não tiverem essa propriedade apresentarão falha na indexação.

> [!IMPORTANT]
> Se não houver um mapeamento explícito para o campo-chave do `metadata_storage_path` índice, o Azure Cognitive Search usará automaticamente como chave e a chave-64 codifica valores-chave (a segunda opção acima).
>
>

Para este exemplo, vamos escolher o campo `metadata_storage_name` como a chave do documento. Vamos supor também que o índice tenha um campo de chave chamado `key` e um campo `fileSize` para armazenamento do tamanho do documento. Para conectar as coisas conforme o desejado, especifique os seguintes mapeamentos de campo ao criar ou atualizar o indexador:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Para unir tudo isso, veja como você pode adicionar mapeamentos de campo e habilitar a codificação das chaves em base-64 para um indexador existente:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }

> [!NOTE]
> Para saber mais sobre mapeamentos de campo, consulte [este artigo](search-indexer-field-mappings.md).
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>Controlando quais blobs serão indexados
É possível controlar quais blobs são indexados e quais são ignorados.

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>Indexe apenas os blobs com extensões de arquivo específicas
Você pode indexar apenas os blobs com as extensões do nome de arquivo especificadas usando o parâmetro de configuração do indexador `indexedFileNameExtensions`. O valor é uma cadeia de caracteres que contém uma lista separada por vírgulas das extensões de arquivo (com um ponto à esquerda). Por exemplo, para indexar apenas blobs de .PDF e .DOCX, faça o seguinte:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Excluir blobs com extensões de arquivo específicas
Você pode excluir os blobs com extensões do nome de arquivo específicas da indexação usando o parâmetro de configuração `excludedFileNameExtensions`. O valor é uma cadeia de caracteres que contém uma lista separada por vírgulas das extensões de arquivo (com um ponto à esquerda). Por exemplo, para indexar todos os blobs, exceto aqueles com as extensões .PNG e .JPEG, faça o seguinte:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Se `indexedFileNameExtensions` ambos `excludedFileNameExtensions` e parâmetros estiverem presentes, a `indexedFileNameExtensions`Busca `excludedFileNameExtensions`Cognitiva Do Azure primeiro olha para , em seguida, para . Isso significa que, se a mesma extensão de arquivo estiver presente nas duas listas, ela será excluída da indexação.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Controlando quais partes do blob são indexadas

Você pode controlar quais partes dos blobs são indexadas usando o parâmetro de configuração `dataToExtract`. Ele pode usar os seguintes valores:

* `storageMetadata` – especifica que somente [as propriedades de blob padrão e os metadados especificados pelo usuário](../storage/blobs/storage-properties-metadata.md) são indexados.
* `allMetadata` – especifica que os metadados de armazenamento e os [metadados específicos do tipo de conteúdo](#ContentSpecificMetadata) extraídos do conteúdo do blob são indexados.
* `contentAndMetadata` – especifica que todos os metadados e conteúdo textual extraídos do blob são indexados. Esse é o valor padrão.

Por exemplo, para indexar apenas os metadados de armazenamento, use:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>Usando metadados de blob para controlar como os blobs são indexados

Os parâmetros de configuração descritos acima se aplicam a todos os blobs. Às vezes, você pode desejar controlar como *blobs individuais* são indexados. Faça isso adicionando as seguintes propriedades de metadados de blob e valores:

| Nome da propriedade | Valor da propriedade | Explicação |
| --- | --- | --- |
| AzureSearch_Skip |"true" |Instrui o indexador de blobs a ignorar o blob por completo. Não há nenhuma tentativa de extração de metadados nem de conteúdo. Isso é útil quando um blob específico falha repetidamente e interrompe o processo de indexação. |
| AzureSearch_SkipContent |"true" |Isso é equivalente à configuração `"dataToExtract" : "allMetadata"` descrita [acima](#PartsOfBlobToIndex), no escopo de um blob específico. |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>Lidando com problemas

Por padrão, o indexador de blobs é interrompido assim que encontra um blob com um tipo de conteúdo sem suporte (por exemplo, uma imagem). Evidentemente, você pode usar o parâmetro `excludedFileNameExtensions` para ignorar alguns tipos de conteúdo. No entanto, talvez você precise indexar blobs sem conhecer todos os possíveis tipos de conteúdo com antecedência. Para continuar a indexação quando um tipo de conteúdo sem suporte é encontrado, defina o parâmetro de configuração `failOnUnsupportedContentType` como `false`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Para alguns blobs, o Azure Cognitive Search não é capaz de determinar o tipo de conteúdo ou não pode processar um documento de outro tipo de conteúdo suportado. Para ignorar este modo de falha, defina o parâmetro de configuração `failOnUnprocessableDocument` como false:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

A Pesquisa Cognitiva Do Azul limita o tamanho das bolhas indexadas. Esses limites estão documentados em [Limites de Serviço na Pesquisa Cognitiva do Azure](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Por padrão, os blobs superdimensionados são tratados como erros. No entanto, você ainda pode indexar os metadados de armazenamento de blobs superdimensionados se você definir o parâmetro de configuração `indexStorageMetadataOnlyForOversizedDocuments` como true: 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

Você também poderá continuar a indexação se ocorrem erros a qualquer momento do processamento, ao analisar blobs ou ao adicionar documentos a um índice. Para ignorar um número específico de erros, defina os parâmetros de configuração `maxFailedItems` e `maxFailedItemsPerBatch` como os valores desejados. Por exemplo: 

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>Indexação incremental e detecção de exclusão 

Quando você configura um indexador de blob para execução em um cronograma, ele reindexa apenas os blobs alterados, conforme determinado pelo carimbo de data/hora `LastModified` do blob.

> [!NOTE]
> Não é necessário especificar uma política de detecção de alteração, a indexação incremental é habilitada automaticamente para você.

Para dar suporte à exclusão de documentos, use uma abordagem de "exclusão reversível". Se você excluir os blobs imediatamente, documentos correspondentes não serão removidos do índice de pesquisa.

Existem duas maneiras de implementar a abordagem de exclusão suave. Ambos estão descritos abaixo.

### <a name="native-blob-soft-delete-preview"></a>Blob nativo soft delete (visualização)

> [!IMPORTANT]
> O suporte para blob nativo soft delete está na pré-visualização. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Suplementares para Visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2019-05-06-versão prévia](https://docs.microsoft.com/azure/search/search-api-preview) fornece esse recurso. Atualmente, não há suporte a portal ou .NET SDK.

> [!NOTE]
> Ao usar a política de exclusão suave de bolha nativa, as chaves de documento para os documentos em seu índice devem ser uma propriedade blob ou metadados blob.

Neste método, você usará o recurso [nativo blob soft delete](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) oferecido pelo armazenamento Azure Blob. Se a exclusão suave do blob nativo estiver habilitada em sua conta de armazenamento, sua fonte de dados terá um conjunto de diretiva de exclusão suave nativa e o indexador encontra uma bolha que foi transitória para um estado excluído suavemente, o indexador removerá esse documento do índice. A política nativa blob soft delete não é suportada ao indexar blobs do Azure Data Lake Storage Gen2.

Use as seguintes etapas:
1. Habilite [a exclusão macia nativa para armazenamento Azure Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete). Recomendamos definir a política de retenção para um valor muito maior do que o seu cronograma de intervalo de indexador. Desta forma, se houver um problema executando o indexador ou se você tiver um grande número de documentos para indexar, há muito tempo para o indexador eventualmente processar as bolhas suprimidas suaves. Os indexadores de pesquisa cognitiva do Azure só excluirão um documento do índice se ele processar a bolha enquanto estiver em um estado deletado suave.
1. Configure uma política nativa de detecção de exclusão suave de bolha na fonte de dados. Um exemplo é mostrado abaixo. Uma vez que este recurso está em pré-visualização, você deve usar a API REST de visualização.
1. Execute o indexador ou defina o indexador para funcionar em um cronograma. Quando o indexador for executado e processa a bolha, o documento será removido do índice.

    ```
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

#### <a name="reindexing-undeleted-blobs"></a>Reindexando blobs não excluídos

Se você excluir uma bolha do armazenamento Azure Blob com exclusão macia nativa ativada em sua conta de armazenamento, a bolha passará para um estado excluído suave, dando-lhe a opção de desexcluir essa bolha dentro do período de retenção. Quando uma fonte de dados do Azure Cognitive Search tem uma política nativa de exclusão suave e o indexador processa uma bolha suave excluída, ela removerá esse documento do índice. Se essa bolha for posteriormente deletada, o indexador nem sempre reindexará essa bolha. Isso porque o indexador determina quais bolhas indexam com `LastModified` base no carimbo de tempo da bolha. Quando uma bolha excluída suave não `LastModified` é excluída, seu carimbo de tempo não é atualizado, portanto, se o indexador já processou blobs com `LastModified` carimbos de tempo mais recentes do que a bolha não excluída, não reindexará a bolha não excluída. Para garantir que uma bolha não excluída seja reindexada, você precisará `LastModified` atualizar o carimbo de tempo da bolha. Uma maneira de fazer isso é resalvando os metadados dessa bolha. Você não precisa alterar os metadados, mas resalvar os metadados `LastModified` atualizará o carimbo de tempo da bolha para que o indexador saiba que precisa reindexar essa bolha.

### <a name="soft-delete-using-custom-metadata"></a>Exclusão suave usando metadados personalizados

Neste método, você usará os metadados de uma bolha para indicar quando um documento deve ser removido do índice de pesquisa.

Use as seguintes etapas:

1. Adicione um par personalizado de valor de chave de metadados à bolha para indicar ao Azure Cognitive Search que ele é excluído logicamente.
1. Configure uma política de detecção de coluna de exclusão suave na fonte de dados. Um exemplo é mostrado abaixo.
1. Uma vez que o indexador tenha processado a bolha e excluído o documento do índice, você pode excluir a bolha para armazenamento Azure Blob.

Por exemplo, a política a seguir considerará que um blob foi excluído se tiver uma propriedade de metadados `IsDeleted` com o valor `true`:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }

#### <a name="reindexing-undeleted-blobs"></a>Reindexando blobs não excluídos

Se você definir uma política de detecção de coluna de exclusão suave na sua fonte de dados, adicione os metadados personalizados a uma bolha com o valor do marcador e execute o indexador, o indexador removerá esse documento do índice. Se você quiser reindexar esse documento, basta alterar o valor de metadados de exclusão suave para essa bolha e executar o indexador.

## <a name="indexing-large-datasets"></a>Indexando grandes conjuntos de dados

A indexação de blobs pode ser um processo demorado. Nos casos em que você tem milhões de blobs para indexar, é possível acelerar a indexação particionando seus dados e usando vários indexadores para processar os dados em paralelo. Veja como você pode configurar isso:

- Particione seus dados em vários contêineres de blob ou pastas virtuais
- Configure várias fontes de dados do Azure Cognitive Search, uma por contêiner ou pasta. Para apontar para uma pasta de blobs, use o parâmetro `query`:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Crie um indexador correspondente para cada fonte de dados. Todos os indexadores podem apontar para o mesmo índice de pesquisa de destino.  

- Uma unidade de pesquisa em seu serviço pode executar um indexador a qualquer momento. Criar vários indexadores conforme descrito acima será útil somente se eles realmente forem executados em paralelo. Para executar vários indexadores em paralelo, escale horizontalmente seu serviço de pesquisa criando um número apropriado de partições e réplicas. Por exemplo, se o serviço de pesquisa tiver seis unidades de pesquisa (por exemplo, 2 partições x 3 réplicas), seis indexadores poderão ser executados simultaneamente, resultando em um aumento de seis vezes a taxa de transferência da indexação. Para saber mais sobre escala e planejamento de capacidade, consulte [Dimensionar os níveis de recursos para consultar e indexar cargas de trabalho na Pesquisa Cognitiva do Azure](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indexação de documentos junto com os dados relacionados

Talvez você queira "montar" documentos de várias fontes em seu índice. Por exemplo, convém mesclar texto de blobs com outros metadados armazenados no Cosmos DB. Você pode até usar a API de indexação por push junto a vários indexadores para criar documentos de pesquisa de várias partes. 

Para que isso funcione, todos os indexadores e outros componentes precisam concordar com a chave de documento. Para obter mais detalhes sobre este tópico, consulte [Index múltiplas fontes de dados do Azure](https://docs.microsoft.com/azure/search/tutorial-multiple-data-sources). Para um passo-a-passo detalhado, consulte este artigo externo: [Combine documentos com outros dados na Pesquisa Cognitiva do Azure](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indexação de texto sem formatação 

Se todos os seus blobs contiverem texto sem formatação na mesma codificação, você poderá melhorar significativamente o desempenho de indexação usando o **modo de análise de texto**. Para usar o modo de análise de texto, defina a propriedade de configuração `parsingMode` como `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

Por padrão, a codificação `UTF-8` será assumida. Para especificar uma codificação diferente, use a propriedade de configuração `encoding`: 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>Propriedades de metadados específicas ao tipo de conteúdo
A tabela a seguir resume o processamento feito para cada formato de documento e descreve as propriedades de metadados extraídas pelo Azure Cognitive Search.

| Formato de documento/tipo de conteúdo | Propriedades de metadados específicas do tipo de conteúdo | Detalhes do processamento |
| --- | --- | --- |
| HTML (texto/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Remoção da marcação HTML e extração do texto |
| PDF (aplicação/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extração do texto, incluindo documentos incorporados (excluindo imagens) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extração de texto, incluindo documentos incorporados |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extração de texto, incluindo documentos incorporados |
| DOCM (aplicativo/vnd.ms-word.document.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extração de texto, incluindo documentos incorporados |
| WORD XML (aplicação/vnd.ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Remoção da marcação XML e extração do texto |
| WORD 2003 XML (aplicação/vnd.ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Remoção da marcação XML e extração do texto |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extração de texto, incluindo documentos incorporados |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extração de texto, incluindo documentos incorporados |
| XLSM (aplicativo/vnd.ms-excel.sheet.macroativado.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extração de texto, incluindo documentos incorporados |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extração de texto, incluindo documentos incorporados |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extração de texto, incluindo documentos incorporados |
| PPTM (aplicativo/vnd.ms-powerpoint.presentation.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extração de texto, incluindo documentos incorporados |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extrair texto, incluindo anexos. `metadata_message_to_email`, `metadata_message_cc_email` `metadata_message_bcc_email` e são coleções de cordas, o resto dos campos são cordas.|
| ODT (application/vnd.oasis.opendocument.text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extração de texto, incluindo documentos incorporados |
| ODS (application/vnd.oasis.opendocument.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extração de texto, incluindo documentos incorporados |
| ODP (application/vnd.oasis.opendocument.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Extração de texto, incluindo documentos incorporados |
| ZIP (application/zip) |`metadata_content_type` |Extração do texto de todos os documentos no arquivo |
| GZ (aplicação/gzip) |`metadata_content_type` |Extração do texto de todos os documentos no arquivo |
| EPUB (aplicativo/epub+zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Extração do texto de todos os documentos no arquivo |
| XML (application/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Remoção da marcação XML e extração do texto |
| JSON (application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |Extrair texto<br/>OBSERVAÇÃO: se você precisar extrair vários campos de documento de um blob JSON, consulte [Como indexar blobs JSON](search-howto-index-json-blobs.md) para obter detalhes |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extração do texto, incluindo anexos |
| RTF (aplicativo/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Extrair texto|
| Texto sem formatação (text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Extrair texto|


## <a name="help-us-make-azure-cognitive-search-better"></a>Ajude-nos a tornar a Busca Cognitiva do Azure melhor
Caso você tenha solicitações de recursos ou ideias para melhorias, entre em contato conosco pelo [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
