---
title: Gerencie ativos nos Serviços de Mídia do Azure
titleSuffix: Azure Media Services
description: Um ativo onde você insere mídia (por exemplo, através de upload ou live ingest), mídia de saída (a partir de uma saída de trabalho) e publica mídia de (para streaming). Este tópico dá uma visão geral de como criar um novo ativo e carregar arquivos.
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
ms.openlocfilehash: 9136fd702fad5c12a8ec97a68ff8a592a203d7d2
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582209"
---
# <a name="manage-assets"></a>Gerenciar ativos

No Azure Media Services, um [ativo](https://docs.microsoft.com/rest/api/media/assets) é onde você 

* carregar arquivos de mídia em um ativo,
* ingerem e arquivam transmissões ao vivo em um ativo,
* produzir os resultados de uma codificação de trabalho de análise para um ativo,
* publicar mídia para streaming, 
* baixar arquivos de um ativo.

Este tópico dá uma visão geral de como carregar arquivos em um ativo e executar algumas outras operações comuns. Também fornece links para amostras de código e tópicos relacionados.

## <a name="prerequisite"></a>Pré-requisito 

Antes de iniciar o desenvolvimento, examine:

* [Conceitos](concepts-overview.md)
* [Desenvolvimento com APIs dos Serviços de Mídia v3](media-services-apis-overview.md) (inclui informações sobre como acessar APIs, convenções de nomenclatura etc.). 

## <a name="upload-media-files-into-an-asset"></a>Envie arquivos de mídia para um ativo

Depois que os arquivos digitais são carregados no armazenamento e associados a um Ativo, eles podem ser usados na codificação, streaming e análise de fluxos de trabalho de conteúdo. Um dos fluxos de trabalho dos Serviços de Mídia do Azure comuns é carregar, codificar e transmitir um arquivo. Esta seção descreve as etapas gerais.

1. Use a API dos Serviços de Mídia do Azure v3 para criar um novo ativo de "entrada". Esta operação cria um contêiner na conta de armazenamento associada com sua conta de Serviços de Mídia do Azure. A API retorna o nome do contêiner (por exemplo, `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    Se você já tem um recipiente blob que deseja associar a um Ativo, você pode especificar o nome do contêiner ao criar o Ativo. Os Serviços de Mídia do Azure atualmente suportam apenas blobs na raiz do contêiner e não com caminhos no nome do arquivo. Portanto, um contêiner com o nome do arquivo "input.mp4" funcionará. No entanto, um contêiner com o nome do arquivo "videos/inputs/input.mp4" não funcionará.

    Você pode usar a CLI do Azure para carregar diretamente em qualquer conta de armazenamento e contêiner que você tiver direitos na sua assinatura.

    O nome do contêiner deve ser exclusivo e seguir as diretrizes de nomeação de armazenamento. O nome não precisa seguir a formatação do nome do contêiner de ativo dos Serviços de Mídia do Azure (GUID do ativo).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Obter uma URL de SAS com permissões de leitura / gravação que serão usadas para carregar arquivos digitais em contêiner do ativo.

    Você pode usar a API de Serviços de Mídia do Azure [listará as URLs de contêiner do ativo](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).

    **AssetContainerSas.listContainerSas** leva um parâmetro [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) `expiryTime`no qual você define . O horário deve ser definido para < 24 horas.

    [ListContainerSasInput](https://docs.microsoft.com/rest/api/media/assets/listcontainersas#listcontainersasinput) retorna vários URLs SAS, pois existem duas chaves de conta de armazenamento para cada conta de armazenamento. Uma conta de armazenamento tem duas chaves porque ajuda com failover e rotação perfeita de chaves da conta de armazenamento. A primeira URL SAS representa a primeira chave da conta de armazenamento e a segunda URL SAS representa a segunda chave.
3. Use as APIs de armazenamento do Azure ou SDKs (por exemplo, a [API de armazenamento REST](../../storage/common/storage-rest-api-auth.md) ou [.NET SDK)](../../storage/blobs/storage-quickstart-blobs-dotnet.md)para carregar arquivos no contêiner Asset.
4. Use as APIs dos Serviços de Mídia v3 para criar uma transformação e um trabalho para processar seu ativo de "entrada". Para obter mais informações, consulte [Transformações e Empregos](transform-concept.md).
5. Transmitir o conteúdo do ativo de "saída".

### <a name="create-a-new-asset"></a>Criar um novo ativo

> [!NOTE]
> As propriedades de um ativo do tipo Datetime estão sempre no formato UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Para obter um exemplo REST, consulte [Criar um ativo com exemplo de REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples).

O exemplo mostra como criar o **Corpo de solicitação** onde você pode especificar descrição, nome do contêiner, conta de armazenamento e outras informações úteis.

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

## <a name="ingest-and-archive-live-streams-into-an-asset"></a>Ingere e arquive transmissões ao vivo em um ativo

Em Serviços de Mídia, um objeto [de saída ao vivo](https://docs.microsoft.com/rest/api/media/liveoutputs) é como um gravador de vídeo digital que capturará e gravará sua transmissão ao vivo em um ativo em sua conta de Serviços de Mídia. O conteúdo gravado é persistido no contêiner definido pelo recurso [Asset.](https://docs.microsoft.com/rest/api/media/assets)

Para obter mais informações, consulte:

* [Usando um DVR em nuvem](live-event-cloud-dvr.md)
* [Streaming de tutorial ao vivo](stream-live-tutorial-with-api.md)

## <a name="output-the-results-of-a-job-to-an-asset"></a>Produzir os resultados de um trabalho para um ativo

Nos Serviços de Mídia, ao processar seus vídeos (por exemplo, codificação ou análise) você precisa criar um [ativo](assets-concept.md) de saída para armazenar o resultado do seu [trabalho](transforms-jobs-concept.md).

Para obter mais informações, consulte:

* [Codificação de um vídeo](encoding-concept.md)
* [Criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md)

## <a name="publish-an-asset-for-streaming"></a>Publique um ativo para streaming

Para publicar um recurso para streaming, você precisa criar um [Localizador de Streaming](streaming-locators-concept.md). O localizador de streaming precisa saber o nome do ativo que você deseja publicar. 

Para obter mais informações, consulte:

[Tutorial: Enviar, codificar e transmitir vídeos com media services v3](stream-files-tutorial-with-api.md)

## <a name="download-results-of-a-job-from-an-output-asset"></a>Baixar resultados de um trabalho de um ativo de saída

Em seguida, você pode baixar esses resultados do seu trabalho para uma pasta local usando o Media Service e as APIs de armazenamento. 

Veja o exemplo dos [arquivos de download.](download-results-howto.md)

## <a name="filtering-ordering-paging"></a>Filtragem, classificação, paginação

Confira [Filtragem, classificação, paginação de entidades dos Serviços de Mídia](entities-overview.md).

## <a name="next-steps"></a>Próximas etapas

Veja os exemplos de código completo que demonstram como carregar, codificar, analisar, transmitir ao vivo e sob demanda: 

* [Java,](https://docs.microsoft.com/samples/azure-samples/media-services-v3-java/azure-media-services-v3-samples-using-java/) 
* [.NET,](https://docs.microsoft.com/samples/azure-samples/media-services-v3-dotnet/azure-media-services-v3-samples-using-net/) 
* [DESCANSE.](https://docs.microsoft.com/samples/azure-samples/media-services-v3-rest-postman/azure-media-services-postman-collection/)
