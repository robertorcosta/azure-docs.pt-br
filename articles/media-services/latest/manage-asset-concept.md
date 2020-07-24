---
title: Gerenciar ativos nos serviços de mídia do Azure
titleSuffix: Azure Media Services
description: Um ativo no qual você insere a mídia (por exemplo, por meio de carregamento ou ingestão dinâmica), mídia de saída (de uma saída de trabalho) e publica mídia de (para streaming). Este tópico fornece uma visão geral de como criar um novo ativo e carregar arquivos.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 6accd303ba11c4c1406c7a157fa8176972fc7a3a
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/23/2020
ms.locfileid: "87022900"
---
# <a name="manage-assets"></a>Gerenciar ativos

Nos serviços de mídia do Azure, um [ativo](/rest/api/media/assets) é onde você 

* carregar arquivos de mídia em um ativo,
* ingerir e arquivar transmissões ao vivo em um ativo,
* gerar os resultados de uma codificação do trabalho de análise para um ativo,
* publicar mídia para streaming, 
* baixar arquivos de um ativo.

Este tópico fornece uma visão geral de como carregar arquivos em um ativo e executar outras operações comuns. Ele também fornece links para exemplos de código e tópicos relacionados.

## <a name="prerequisite"></a>Pré-requisito 

Antes de iniciar o desenvolvimento, examine:

* [Conceitos](concepts-overview.md)
* [Desenvolvimento com APIs dos Serviços de Mídia v3](media-services-apis-overview.md) (inclui informações sobre como acessar APIs, convenções de nomenclatura etc.). 

## <a name="upload-media-files-into-an-asset"></a>Carregar arquivos de mídia em um ativo

Depois que os arquivos digitais são carregados no armazenamento e associados a um ativo, eles podem ser usados na codificação de serviços de mídia, no streaming e na análise de fluxos de trabalho de conteúdo. Um dos fluxos de trabalho dos Serviços de Mídia do Azure comuns é carregar, codificar e transmitir um arquivo. Esta seção descreve as etapas gerais.

1. Use a API dos Serviços de Mídia do Azure v3 para criar um novo ativo de "entrada". Esta operação cria um contêiner na conta de armazenamento associada com sua conta de Serviços de Mídia do Azure. A API retorna o nome do contêiner (por exemplo, `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    Se você já tiver um contêiner de BLOB que deseja associar a um ativo, poderá especificar o nome do contêiner ao criar o ativo. Os Serviços de Mídia do Azure atualmente suportam apenas blobs na raiz do contêiner e não com caminhos no nome do arquivo. Portanto, um contêiner com o nome do arquivo "input.mp4" funcionará. No entanto, um contêiner com o nome de arquivo "vídeos/entradas/input.mp4" não funcionará.

    Você pode usar a CLI do Azure para carregar diretamente em qualquer conta de armazenamento e contêiner que você tiver direitos na sua assinatura.

    O nome do contêiner deve ser exclusivo e seguir as diretrizes de nomeação de armazenamento. O nome não precisa seguir a formatação do nome do contêiner de ativo dos Serviços de Mídia do Azure (GUID do ativo).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Obter uma URL de SAS com permissões de leitura / gravação que serão usadas para carregar arquivos digitais em contêiner do ativo.

    Você pode usar a API de Serviços de Mídia do Azure [listará as URLs de contêiner do ativo](/rest/api/media/assets/listcontainersas).

    **AssetContainerSas. listContainerSas** usa um parâmetro [ListContainerSasInput](/rest/api/media/assets/listcontainersas#listcontainersasinput) no qual você define `expiryTime` . A hora deve ser definida como < 24 horas.

    [ListContainerSasInput](/rest/api/media/assets/listcontainersas#listcontainersasinput) retorna várias URLs SAS, pois há duas chaves de conta de armazenamento para cada conta de armazenamento. Uma conta de armazenamento tem duas chaves porque ajuda com failover e rotação direta das chaves da conta de armazenamento. A primeira URL da SAS representa a primeira chave de conta de armazenamento e a segunda URL da SAS representa a segunda chave.
3. Use as APIs de armazenamento do Azure ou SDKs (por exemplo, a [API REST de armazenamento](../../storage/common/storage-rest-api-auth.md) ou o [SDK do .net](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) para carregar arquivos no contêiner de ativos.
4. Use as APIs dos Serviços de Mídia v3 para criar uma transformação e um trabalho para processar seu ativo de "entrada". Para obter mais informações, consulte [Transformações e Trabalhos](./transforms-jobs-concept.md).
5. Transmitir o conteúdo do ativo de "saída".

### <a name="create-a-new-asset"></a>Criar um novo ativo

> [!NOTE]
> As propriedades de um ativo do tipo DateTime estão sempre no formato UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Para obter um exemplo REST, consulte [Criar um ativo com exemplo de REST](/rest/api/media/assets/createorupdate#examples).

O exemplo mostra como criar o **corpo da solicitação** , onde você pode especificar a descrição, o nome do contêiner, a conta de armazenamento e outras informações úteis.

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

### <a name="see-also"></a>Confira também

* [Criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md)
* [Criar uma entrada de trabalho de uma URL HTTPS](job-input-from-http-how-to.md)

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Ingerir e arquivar transmissões ao vivo em um ativo

Nos serviços de mídia, um objeto de [saída ao vivo](/rest/api/media/liveoutputs) é como um gravador de vídeo digital que detectará e registrará seu fluxo ao vivo em um ativo em sua conta de serviços de mídia. O conteúdo gravado é mantido no contêiner definido pelo recurso de [ativo](/rest/api/media/assets) .

Para obter mais informações, consulte:

* [Usar um DVR de nuvem](live-event-cloud-dvr.md)
* [Tutorial de streaming ao vivo](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>Gerar os resultados de um trabalho para um ativo

Nos serviços de mídia, ao processar seus vídeos (por exemplo, codificação ou análise), você precisa criar um [ativo](assets-concept.md) de saída para armazenar o resultado de seu [trabalho](transforms-jobs-concept.md).

Para obter mais informações, consulte:

* [Codificando um vídeo](encoding-concept.md)
* [Criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Publicar um ativo para streaming

Para publicar um ativo para streaming, você precisa criar um [localizador de streaming](streaming-locators-concept.md). O localizador de streaming precisa saber o nome do ativo que você deseja publicar. 

Para obter mais informações, consulte:

[Tutorial: Fazer upload, codificar e transmitir vídeos com os Serviços de Mídia v3](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Baixar resultados de um trabalho de um ativo de saída

Em seguida, você pode baixar esses resultados de seu trabalho para uma pasta local usando o serviço de mídia e as APIs de armazenamento. 

Consulte o exemplo [baixar arquivos](download-results-howto.md) .

## <a name="filtering-ordering-paging"></a>Filtragem, classificação, paginação

Confira [Filtragem, classificação, paginação de entidades dos Serviços de Mídia](entities-overview.md).

## <a name="next-steps"></a>Próximas etapas

Consulte os exemplos de código completos que demonstram como carregar, codificar, analisar, transmitir ao vivo e sob demanda: 

* [Java](/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/), 
* [.Net](/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/), 
* [REST](/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/).
