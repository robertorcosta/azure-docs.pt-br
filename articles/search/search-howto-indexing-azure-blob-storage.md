---
title: Pesquisar o conteúdo do armazenamento de BLOBs do Azure
titleSuffix: Azure Cognitive Search
description: Saiba como indexar o armazenamento de BLOBs do Azure e extrair texto de documentos com o Azure Pesquisa Cognitiva.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 6c7e1fcaebd415fcacfffcef62ca25cccde3e476
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85563168"
---
# <a name="how-to-index-documents-in-azure-blob-storage-with-azure-cognitive-search"></a>Como indexar documentos no armazenamento de BLOBs do Azure com o Azure Pesquisa Cognitiva

Este artigo mostra como usar o Pesquisa Cognitiva do Azure para indexar documentos (como, por exemplo, PDFs, Microsoft Office documentos e vários outros formatos comuns) armazenados no armazenamento de BLOBs do Azure. Primeiro, ele explica as noções básicas de configuração de um indexador de blob. Em seguida, ele explora mais profundamente os comportamentos e cenários que você pode encontrar.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Formatos de documento com suporte
O indexador de blob pode extrair o texto dos seguintes formatos de documento:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>Configuração da indexação de blob
Você pode configurar um indexador do Armazenamento de Blobs do Azure usando:

* [Azure portal](https://ms.portal.azure.com)
* [API REST](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations) do Azure pesquisa cognitiva
* SDK do [.net](https://docs.microsoft.com/dotnet/api/overview/azure/search) pesquisa cognitiva do Azure

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

    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
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

- **Cadeia de conexão da conta de armazenamento de acesso completo**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` você pode obter a cadeia de conexão do portal do Azure navegando até a folha da conta de armazenamento > configurações > chaves (para contas de armazenamento clássicas) ou configurações > chaves de acesso (para contas de armazenamento Azure Resource Manager).
- Cadeia de conexão da SAS **(assinatura de acesso compartilhado) da conta de armazenamento**: `BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl` o SAS deve ter a lista e permissões de leitura para os contêineres e objetos (blobs, neste caso).
-  **Assinatura de acesso compartilhado do contêiner**: `ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl` a SAS deve ter as permissões de lista e leitura no contêiner.

Para saber mais sobre assinaturas de acesso compartilhado de armazenamento, veja [Uso de Assinaturas de Acesso Compartilhado](../storage/common/storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Se você usar credenciais SAS, você precisará atualizar as credenciais de fonte de dados periodicamente com assinaturas renovadas para impedir sua expiração. Se as credenciais SAS expirarem, o indexador irá falhar com uma mensagem de erro semelhante à `Credentials provided in the connection string are invalid or have expired.`.  

### <a name="step-2-create-an-index"></a>Etapa 2: Criar um índice
O índice especifica os campos em um documento, atributos e outras construções que modelam a experiência de pesquisa.

Veja como criar um índice com um campo `content` pesquisável para armazenar o texto extraído dos blobs:   

    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

Para obter mais informações sobre a criação de índices, consulte [Criar Índice](https://docs.microsoft.com/rest/api/searchservice/create-index)

### <a name="step-3-create-an-indexer"></a>Etapa 3: Criar um indexador
Um indexador conecta uma fonte de dados a um índice de pesquisa de destino e fornece um agendamento para automatizar a atualização de dados.

Uma vez que o índice e a fonte de dados forem criados, será possível criar o indexador:

    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
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

Para obter mais informações sobre como definir as agendas do indexador, confira [Como agendar indexadores para o Azure Cognitive Search](search-howto-schedule-indexers.md).

<a name="how-azure-search-indexes-blobs"></a>

## <a name="how-azure-cognitive-search-indexes-blobs"></a>Como o Azure Pesquisa Cognitiva indexa BLOBs

Dependendo da [configuração do indexador](#PartsOfBlobToIndex), o indexador de blobs pode indexar somente metadados de armazenamento (algo útil quando você está preocupado apenas com os metadados e não precisa indexar o conteúdo dos blobs), metadados de armazenamento e conteúdo ou conteúdo textual e de metadados. Por padrão, o indexador extrai os metadados e o conteúdo.

> [!NOTE]
> Por padrão, os blobs com conteúdo estruturado, como JSON, CSV e indexados como uma única parte de texto. Se você quiser indexar BLOBs JSON e CSV de forma estruturada, consulte [indexando BLOBs JSON](search-howto-index-json-blobs.md) e [indexando BLOBs CSV](search-howto-index-csv-blobs.md) para obter mais informações.
>
> Um documento composto ou incorporado (como um arquivo ZIP, um documento do Word com emails inseridos do Outlook contendo anexos ou um. O arquivo MSG com anexos) também é indexado como um único documento. Por exemplo, todas as imagens extraídas dos anexos de um. O arquivo MSG será retornado no campo normalized_images.

* O conteúdo textual do documento é extraído para um campo de cadeia de caracteres chamado `content`.

> [!NOTE]
> O Azure Pesquisa Cognitiva limita a quantidade de texto que ele extrai, dependendo do tipo de preço: 32.000 caracteres para a camada gratuita, 64.000 para Basic, 4 milhões para Standard, 8 milhões para Standard S2 e 16 milhões para o Standard S3. Um aviso é incluído na resposta de status do indexador para documentos truncados.  

* As propriedades de metadados especificadas pelo usuário e presentes no blob, se houver alguma, são extraídas literalmente. Observe que isso requer que um campo seja definido no índice com o mesmo nome da chave de metadados do blob. Por exemplo, se o blob tiver uma chave de metadados de `Sensitivity` com valor `High` , você deverá definir um campo chamado `Sensitivity` no índice de pesquisa e ele será preenchido com o valor `High` .
* As propriedades de metadados de blob padrão são extraídas para os seguintes campos:

  * **metadata\_storage\_name** (Edm.String): o nome do arquivo do blob. Por exemplo, se você tiver um blob /my-container/my-folder/subfolder/resume.pdf, o valor desse campo será `resume.pdf`.
  * **metadata\_storage\_path** (Edm.String): o URI completo do blob, incluindo a conta de armazenamento. Por exemplo, `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadata\_storage\_content\_type** (Edm.String): o tipo de conteúdo, conforme especificado pelo código usado para carregar o blob. Por exemplo, `application/octet-stream`.
  * **metadata\_storage\_last\_modified** (Edm.DateTimeOffset): último carimbo de data/hora modificado para o blob. O Azure Pesquisa Cognitiva usa esse carimbo de data/hora para identificar BLOBs alterados, para evitar a reindexação de tudo após a indexação inicial.
  * **metadata\_storage\_size** (Edm.Int64): tamanho do blob em bytes.
  * **metadata\_storage\_content\_md5** (Edm.String): hash MD5 do conteúdo do blob, se estiver disponível.
  * ** \_ \_ \_ token SAS de armazenamento de metadados** (EDM. String)-um token SAS temporário que pode ser usado por [habilidades personalizadas](cognitive-search-custom-skill-interface.md) para obter acesso ao blob. Esse token não deve ser armazenado para uso posterior, pois ele pode expirar.

* As propriedades de metadados específicas a cada formato de documento são extraídas nos campos listados [aqui](#ContentSpecificMetadata).

Não é necessário definir os campos para todas as propriedades acima em seu índice de pesquisa, basta capturar as propriedades necessárias para seu aplicativo.

> [!NOTE]
> Geralmente, os nomes de campo no índice existente serão diferentes dos nomes de campo gerados durante a extração do documento. Você pode usar **mapeamentos de campo** para mapear os nomes de propriedade fornecidos pelo Azure pesquisa cognitiva para os nomes de campo no índice de pesquisa. Você verá um exemplo de uso de mapeamento de campo abaixo.
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>Definir chaves de documento e mapeamentos de campo
No Azure Pesquisa Cognitiva, a chave do documento identifica exclusivamente um documento. Cada índice de pesquisa deve ter exatamente um campo de chave do tipo Edm.String. O campo de chave é necessário para cada documento adicionado ao índice (é, na verdade, o único campo obrigatório).  

Você deve considerar cuidadosamente qual campo extraído deve ser mapeado para o campo de chave de seu índice. Os candidatos são:

* **metadata\_storage\_name**: este pode ser um candidato conveniente, mas observe que 1) talvez os nomes não sejam exclusivos, pois você pode ter blobs com o mesmo nome em pastas diferentes, e 2) o nome pode conter caracteres inválidos em chaves de documento, por exemplo, traços. Você pode lidar com caracteres inválidos usando a `base64Encode` [função de mapeamento de campo](search-indexer-field-mappings.md#base64EncodeFunction). Se fizer isso, lembre-se de codificar as chaves de documento ao transmiti-las em chamadas à API, como Pesquisa. (Por exemplo, em .NET você pode usar o método [UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) para essa finalidade).
* **metadata\_storage\_path**: o uso do caminho completo garante a exclusividade, mas o caminho contém definitivamente caracteres `/` que são [inválidos em uma chave de documento](https://docs.microsoft.com/rest/api/searchservice/naming-rules).  Como foi mencionado acima, você tem a opção de codificar as chaves usando a  [função](search-indexer-field-mappings.md#base64EncodeFunction)`base64Encode`.
* Se nenhuma das opções acima funcionar para você, adicione uma propriedade de metadados personalizada aos blobs. No entanto, essa opção exige que seu processo de carregamento de blob adicione essa propriedade de metadados a todos os blobs. Como a chave é uma propriedade obrigatória, todos os blobs que não tiverem essa propriedade apresentarão falha na indexação.

> [!IMPORTANT]
> Se não houver nenhum mapeamento explícito para o campo de chave no índice, o Azure Pesquisa Cognitiva usará automaticamente `metadata_storage_path` como os valores de chave key e base-64 codificações (a segunda opção acima).
>
>

Para este exemplo, vamos escolher o campo `metadata_storage_name` como a chave do documento. Vamos supor também que o índice tenha um campo de chave chamado `key` e um campo `fileSize` para armazenamento do tamanho do documento. Para conectar as coisas conforme o desejado, especifique os seguintes mapeamentos de campo ao criar ou atualizar o indexador:

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

Para unir tudo isso, veja como você pode adicionar mapeamentos de campo e habilitar a codificação das chaves em base-64 para um indexador existente:

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
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

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>Excluir blobs com extensões de arquivo específicas
Você pode excluir os blobs com extensões do nome de arquivo específicas da indexação usando o parâmetro de configuração `excludedFileNameExtensions`. O valor é uma cadeia de caracteres que contém uma lista separada por vírgulas das extensões de arquivo (com um ponto à esquerda). Por exemplo, para indexar todos os blobs, exceto aqueles com as extensões .PNG e .JPEG, faça o seguinte:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

Se ambos `indexedFileNameExtensions` os `excludedFileNameExtensions` parâmetros e estiverem presentes, o Azure pesquisa cognitiva primeiro examinará `indexedFileNameExtensions` , em seguida, em `excludedFileNameExtensions` . Isso significa que, se a mesma extensão de arquivo estiver presente nas duas listas, ela será excluída da indexação.

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>Controlando quais partes do blob são indexadas

Você pode controlar quais partes dos blobs são indexadas usando o parâmetro de configuração `dataToExtract`. Ele pode usar os seguintes valores:

* `storageMetadata` – especifica que somente [as propriedades de blob padrão e os metadados especificados pelo usuário](../storage/blobs/storage-properties-metadata.md) são indexados.
* `allMetadata` – especifica que os metadados de armazenamento e os [metadados específicos do tipo de conteúdo](#ContentSpecificMetadata) extraídos do conteúdo do blob são indexados.
* `contentAndMetadata` – especifica que todos os metadados e conteúdo textual extraídos do blob são indexados. Esse é o valor padrão.

Por exemplo, para indexar apenas os metadados de armazenamento, use:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
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

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

Para alguns BLOBs, o Azure Pesquisa Cognitiva não pode determinar o tipo de conteúdo ou não pode processar um documento de tipo de conteúdo com suporte de outra forma. Para ignorar este modo de falha, defina o parâmetro de configuração `failOnUnprocessableDocument` como false:

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

O Azure Pesquisa Cognitiva limita o tamanho dos BLOBs que são indexados. Esses limites são documentados em [limites de serviço no Azure pesquisa cognitiva](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity). Por padrão, os blobs superdimensionados são tratados como erros. No entanto, você ainda pode indexar os metadados de armazenamento de blobs superdimensionados se você definir o parâmetro de configuração `indexStorageMetadataOnlyForOversizedDocuments` como true: 

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

Há duas maneiras de implementar a abordagem de exclusão reversível. Ambos são descritos abaixo.

### <a name="native-blob-soft-delete-preview"></a>Exclusão reversível de blob nativo (versão prévia)

> [!IMPORTANT]
> O suporte para exclusão reversível de blob nativo está em versão prévia. A funcionalidade de versão prévia é fornecida sem um Contrato de Nível de Serviço e, portanto, não é recomendada para cargas de trabalho de produção. Para obter mais informações, consulte [Termos de Uso Complementares de Versões Prévias do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [API REST versão 2020-06-30-Preview](https://docs.microsoft.com/azure/search/search-api-preview) fornece esse recurso. No momento, não há suporte para Portal ou SDK do .NET.

> [!NOTE]
> Ao usar a política de exclusão reversível de blob nativo, as chaves de documento para os documentos em seu índice devem ser uma propriedade de BLOB ou metadados de BLOB.

Nesse método, você usará o recurso de [exclusão reversível de blob nativo](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete) oferecido pelo armazenamento de BLOBs do Azure. Se a exclusão reversível de blob nativo estiver habilitada em sua conta de armazenamento, sua fonte de dados terá um conjunto de políticas de exclusão reversível nativa e o indexador encontrará um blob que foi transferido para um estado de exclusão reversível, o indexador removerá esse documento do índice. Não há suporte para a política de exclusão reversível de blob nativo ao indexar blobs de Azure Data Lake Storage Gen2.

Use as etapas a seguir:
1. Habilite [a exclusão reversível nativa para o armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blob-soft-delete). É recomendável definir a política de retenção com um valor muito maior do que a agenda do intervalo do indexador. Dessa forma, se houver um problema ao executar o indexador ou se você tiver um grande número de documentos para indexar, haverá muito tempo para que o indexador eventualmente processe os BLOBs com exclusão reversível. Os indexadores do Azure Pesquisa Cognitiva excluirão apenas um documento do índice se ele processar o blob enquanto ele estiver em um estado de exclusão reversível.
1. Configure uma política de detecção de exclusão reversível de blob nativo na fonte de dados. Um exemplo é mostrado abaixo. Como esse recurso está em versão prévia, você deve usar a API REST de visualização.
1. Execute o indexador ou defina o indexador para ser executado em um agendamento. Quando o indexador executar e processar o blob, o documento será removido do índice.

    ```
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
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

#### <a name="reindexing-undeleted-blobs"></a>Reindexando BLOBs não excluídos

Se você excluir um blob do armazenamento de BLOBs do Azure com a exclusão reversível nativa habilitada na sua conta de armazenamento, o blob passará para um estado de exclusão reversível, dando a você a opção de restaurar esse blob dentro do período de retenção. Quando uma fonte de dados do Azure Pesquisa Cognitiva tiver uma política de exclusão reversível de blob nativo e o indexador processar um blob com exclusão reversível, ele removerá esse documento do índice. Se esse blob posteriormente não for excluído, o indexador nem sempre reindexa esse BLOB. Isso ocorre porque o indexador determina quais BLOBs serão indexados com base no `LastModified` carimbo de data/hora do blob. Quando um blob com exclusão reversível não é excluído `LastModified` , seu carimbo de data/hora não é atualizado, portanto, se o indexador já tiver processado BLOBs com carimbos de data/ `LastModified` hora mais recentes do que o BLOB não excluído, ele não reindexa o BLOB não excluído. Para garantir que um BLOB não excluído seja reindexado, será necessário atualizar o `LastModified` carimbo de data/hora do blob. Uma maneira de fazer isso é salvando novamente os metadados desse blob. Você não precisa alterar os metadados, mas resalvá-los atualizará o carimbo de `LastModified` data/hora do blob para que o indexador saiba que ele precisa reindexar esse BLOB.

### <a name="soft-delete-using-custom-metadata"></a>Exclusão reversível usando metadados personalizados

Nesse método, você usará os metadados de um blob para indicar quando um documento deve ser removido do índice de pesquisa.

Use as etapas a seguir:

1. Adicione um par chave-valor de metadados personalizados ao blob para indicar ao Azure Pesquisa Cognitiva que ele é excluído logicamente.
1. Configure uma política de detecção de coluna de exclusão reversível na fonte de dados. Um exemplo é mostrado abaixo.
1. Depois que o indexador tiver processado o blob e excluído o documento do índice, você poderá excluir o blob do armazenamento de BLOBs do Azure.

Por exemplo, a política a seguir considerará que um blob foi excluído se tiver uma propriedade de metadados `IsDeleted` com o valor `true`:

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
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

#### <a name="reindexing-undeleted-blobs"></a>Reindexando BLOBs não excluídos

Se você definir uma política de detecção de coluna de exclusão reversível na fonte de dados, adicione os metadados personalizados a um blob com o valor do marcador e, em seguida, execute o indexador, o indexador removerá esse documento do índice. Se você quiser reindexar esse documento, basta alterar o valor de metadados de exclusão reversível para esse BLOB e executar novamente o indexador.

## <a name="indexing-large-datasets"></a>Indexando grandes conjuntos de dados

A indexação de blobs pode ser um processo demorado. Nos casos em que você tem milhões de blobs para indexar, é possível acelerar a indexação particionando seus dados e usando vários indexadores para processar os dados em paralelo. Veja como você pode configurar isso:

- Particione seus dados em vários contêineres de blob ou pastas virtuais
- Configure várias fontes de dados do Azure Pesquisa Cognitiva, uma por contêiner ou pasta. Para apontar para uma pasta de blobs, use o parâmetro `query`:

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- Crie um indexador correspondente para cada fonte de dados. Todos os indexadores podem apontar para o mesmo índice de pesquisa de destino.  

- Uma unidade de pesquisa em seu serviço pode executar um indexador a qualquer momento. Criar vários indexadores conforme descrito acima será útil somente se eles realmente forem executados em paralelo. Para executar vários indexadores em paralelo, escale horizontalmente seu serviço de pesquisa criando um número apropriado de partições e réplicas. Por exemplo, se o serviço de pesquisa tiver seis unidades de pesquisa (por exemplo, 2 partições x 3 réplicas), seis indexadores poderão ser executados simultaneamente, resultando em um aumento de seis vezes a taxa de transferência da indexação. Para saber mais sobre dimensionamento e planejamento de capacidade, confira [dimensionar níveis de recursos para cargas de trabalho de consulta e indexação no Azure pesquisa cognitiva](search-capacity-planning.md).

## <a name="indexing-documents-along-with-related-data"></a>Indexação de documentos junto com os dados relacionados

Talvez você queira "montar" documentos de várias fontes em seu índice. Por exemplo, convém mesclar texto de blobs com outros metadados armazenados no Cosmos DB. Você pode até usar a API de indexação por push junto a vários indexadores para criar documentos de pesquisa de várias partes. 

Para que isso funcione, todos os indexadores e outros componentes precisam concordar com a chave de documento. Para obter detalhes adicionais sobre este tópico, consulte [indexar várias fontes de dados do Azure](https://docs.microsoft.com/azure/search/tutorial-multiple-data-sources). Para obter um passo a passo detalhado, consulte este artigo externo: [combinar documentos com outros dados no Azure pesquisa cognitiva](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html).

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>Indexação de texto sem formatação 

Se todos os seus blobs contiverem texto sem formatação na mesma codificação, você poderá melhorar significativamente o desempenho de indexação usando o **modo de análise de texto**. Para usar o modo de análise de texto, defina a propriedade de configuração `parsingMode` como `text`:

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
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
A tabela a seguir resume o processamento feito para cada formato de documento e descreve as propriedades de metadados extraídas pelo Pesquisa Cognitiva do Azure.

| Formato de documento/tipo de conteúdo | Propriedades de metadados específicas do tipo de conteúdo | Detalhes do processamento |
| --- | --- | --- |
| HTML (texto/HTML) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |Remoção da marcação HTML e extração do texto |
| PDF (aplicativo/PDF) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |Extração do texto, incluindo documentos incorporados (excluindo imagens) |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extração de texto, incluindo documentos incorporados |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extração de texto, incluindo documentos incorporados |
| DOCM (aplicativo/vnd.ms-word.document. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extração de texto, incluindo documentos incorporados |
| XML do WORD (application/vnd. ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Remoção da marcação XML e extração do texto |
| WORD 2003 XML (application/vnd. ms-WordML) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |Remoção da marcação XML e extração do texto |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extração de texto, incluindo documentos incorporados |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extração de texto, incluindo documentos incorporados |
| XLSM (application/vnd. MS-Excel. Sheet. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extração de texto, incluindo documentos incorporados |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extração de texto, incluindo documentos incorporados |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extração de texto, incluindo documentos incorporados |
| PPTM (application/vnd. ms-PowerPoint. Presentation. macroenabled. 12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |Extração de texto, incluindo documentos incorporados |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |Extração de texto, incluindo texto extraído de anexos. `metadata_message_to_email``metadata_message_cc_email`e `metadata_message_bcc_email` são coleções de cadeias de caracteres, o restante dos campos são cadeias.|
| ODT (application/vnd. Oasis. OpenDocument. Text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |Extração de texto, incluindo documentos incorporados |
| ODS (application/vnd. Oasis. OpenDocument. Spreadsheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |Extração de texto, incluindo documentos incorporados |
| ODP (application/vnd. Oasis. OpenDocument. Presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |Extração de texto, incluindo documentos incorporados |
| ZIP (application/zip) |`metadata_content_type` |Extração do texto de todos os documentos no arquivo |
| GZ (aplicativo/gzip) |`metadata_content_type` |Extração do texto de todos os documentos no arquivo |
| EPUB (Application/ePub + zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |Extração do texto de todos os documentos no arquivo |
| XML (application/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |Remoção da marcação XML e extração do texto |
| JSON (application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |Extrair texto<br/>OBSERVAÇÃO: se você precisar extrair vários campos de documento de um blob JSON, consulte [Como indexar blobs JSON](search-howto-index-json-blobs.md) para obter detalhes |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |Extração do texto, incluindo anexos |
| RTF (aplicativo/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | Extrair texto|
| Texto sem formatação (text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | Extrair texto|


## <a name="help-us-make-azure-cognitive-search-better"></a>Ajude-nos a tornar o Azure Pesquisa Cognitiva melhor
Caso você tenha solicitações de recursos ou ideias para melhorias, entre em contato conosco pelo [site UserVoice](https://feedback.azure.com/forums/263029-azure-search/).
