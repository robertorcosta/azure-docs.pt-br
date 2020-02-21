---
title: Ativos
titleSuffix: Azure Media Services
description: Saiba quais são os ativos e como eles são usados pelos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 3860823787b860f2504d6fb13b9479d1feec9d28
ms.sourcegitcommit: 934776a860e4944f1a0e5e24763bfe3855bc6b60
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2020
ms.locfileid: "77505804"
---
# <a name="assets-in-azure-media-services"></a>Ativos nos serviços de mídia do Azure

Nos serviços de mídia do Azure, um [ativo](https://docs.microsoft.com/rest/api/media/assets) contém informações sobre arquivos digitais armazenados no armazenamento do Azure (incluindo vídeo, áudio, imagens, coleções de miniaturas, faixas de texto e arquivos de legenda codificada).

Um ativo é mapeado para um contêiner de blob na [conta de Armazenamento do Microsoft Azure](storage-account-concept.md) e os arquivos no ativo são armazenados como blob de blocos nesse contêiner. Os Serviços de Mídia oferecem suporte a camadas de Blob quando a conta usa Armazenamento de uso geral v2 (GPv2). Com o GPv2, você pode mover arquivos para o [armazenamento esporádico ou para arquivar](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). O armazenamento de **arquivo** é adequado para arquivar arquivos de origem quando não for mais necessário (por exemplo, depois que eles tiverem sido codificados).

A camada de armazenamento de **Arquivamento** só é recomendada para arquivos de origem muito grandes que já tenham sido codificados e a saída do trabalho de codificação foi colocada em um contêiner de blobs de saída. Os BLOBs no contêiner de saída que você deseja associar a um ativo e usados para transmitir ou analisar seu conteúdo devem existir em uma camada de armazenamento **quente** ou **fria** .

### <a name="naming"></a>Nomenclatura 

#### <a name="assets"></a>Ativos

Os nomes dos ativos devem ser exclusivos. Os nomes de recursos dos serviços de mídia v3 (por exemplo, ativos, trabalhos, transformações) estão sujeitos a Azure Resource Manager restrições de nomenclatura. Para obter mais informações, consulte [convenções de nomenclatura](media-services-apis-overview.md#naming-conventions).

#### <a name="blobs"></a>Blobs

Os nomes de Arquivos/blobs em um ativo devem seguir os [requisitos de nome do blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) e os requisitos de nome do [NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). O motivo para esses requisitos é que os arquivos podem ser copiados do armazenamento de BLOBs para um disco NTFS local para processamento.

## <a name="upload-digital-files-into-assets"></a>Carregar os arquivos digitais em Ativos

Depois que os arquivos digitais são carregados no armazenamento e associados a um ativo, eles podem ser usados na codificação de serviços de mídia, no streaming e na análise de fluxos de trabalho de conteúdo. Um dos fluxos de trabalho dos Serviços de Mídia do Azure comuns é carregar, codificar e transmitir um arquivo. Esta seção descreve as etapas gerais.

> [!TIP]
> Antes de começar a desenvolver, examine o [desenvolvimento com as APIs dos serviços de mídia v3](media-services-apis-overview.md) (inclui informações sobre como acessar APIs, convenções de nomenclatura e assim por diante).

1. Use a API dos Serviços de Mídia do Azure v3 para criar um novo ativo de "entrada". Esta operação cria um contêiner na conta de armazenamento associada com sua conta de Serviços de Mídia do Azure. A API retorna o nome do contêiner (por exemplo, `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`).

    Se você já tiver um contêiner de BLOB que deseja associar a um ativo, poderá especificar o nome do contêiner ao criar o ativo. Os Serviços de Mídia do Azure atualmente suportam apenas blobs na raiz do contêiner e não com caminhos no nome do arquivo. Portanto, um contêiner com o nome do arquivo "input.mp4" funcionará. No entanto, um contêiner com o nome de arquivo "vídeos/entradas/Input. mp4" não funcionará.

    Você pode usar a CLI do Azure para carregar diretamente em qualquer conta de armazenamento e contêiner que você tiver direitos na sua assinatura.

    O nome do contêiner deve ser exclusivo e seguir as diretrizes de nomeação de armazenamento. O nome não precisa seguir a formatação do nome do contêiner de ativo dos Serviços de Mídia do Azure (GUID do ativo).

    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. Obter uma URL de SAS com permissões de leitura / gravação que serão usadas para carregar arquivos digitais em contêiner do ativo. Você pode usar a API de Serviços de Mídia do Azure [listará as URLs de contêiner do ativo](https://docs.microsoft.com/rest/api/media/assets/listcontainersas).
3. Use as APIs de armazenamento do Azure ou SDKs (por exemplo, a [API REST de armazenamento](../../storage/common/storage-rest-api-auth.md) ou o [SDK do .net](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) para carregar arquivos no contêiner de ativos.
4. Use as APIs dos Serviços de Mídia v3 para criar uma transformação e um trabalho para processar seu ativo de "entrada". Para obter mais informações, consulte [Transformações e Trabalhos](transform-concept.md).
5. Transmitir o conteúdo do ativo de "saída".

Para obter um exemplo .NET completo que mostra como criar o ativo, obter uma URL SAS gravável para o contêiner do ativo no armazenamento e carregar o arquivo no contêiner no armazenamento usando a URL SAS, consulte [criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md).

### <a name="create-a-new-asset"></a>Criar um novo ativo

> [!NOTE]
> As propriedades de um ativo do tipo DateTime estão sempre no formato UTC.

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

Para obter um exemplo REST, consulte [Criar um ativo com exemplo de REST](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples).

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

Para obter um exemplo completo, consulte [Criar uma entrada de trabalho de um arquivo local](job-input-from-local-file-how-to.md). Nos Serviços de Mídia do Azure v3, entrada de um trabalho também pode ser criada de URLs HTTPS (consulte [criar uma entrada de trabalho de uma URL HTTPS](job-input-from-http-how-to.md)).

## <a name="map-v3-asset-properties-to-v2"></a>Mapear Propriedades de ativos V3 para v2

A tabela a seguir mostra como as propriedades do [ativo](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)em v3 são mapeadas para as propriedades do ativo na v2.

|Propriedades v3|Propriedades de v2|
|---|---|
|`id`-(exclusivo) o caminho de Azure Resource Manager completo, consulte os exemplos no [ativo](https://docs.microsoft.com/rest/api/media/assets/createorupdate)||
|`name`-(exclusivo) consulte [convenções de nomenclatura](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id`-o valor (exclusivo) começa com o prefixo de `nb:cid:UUID:`.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (opções de criação)|
|`type`||

## <a name="storage-side-encryption"></a>Criptografia do armazenamento

Para proteger os Ativos em repouso, os ativos devem ser criptografados pela criptografia do armazenamento. A tabela a seguir mostra como a criptografia do armazenamento funciona nos Serviços de Mídia:

|Opção de criptografia|DESCRIÇÃO|Serviços de Mídia v2|Serviços de Mídia v3|
|---|---|---|---|
|Criptografia do Armazenamento dos Serviços de Mídia|Criptografia AES-256, chave gerenciada pelos serviços de mídia.|Com suporte<sup>(1)</sup>|Sem suporte<sup>(2)</sup>|
|[Criptografia do Serviço de Armazenamento para dados em repouso](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Criptografia do lado do servidor oferecida pelo armazenamento do Azure, chave gerenciada pelo Azure ou por cliente.|Suportado|Suportado|
|[Criptografia do cliente de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Criptografia do lado do cliente oferecida pelo armazenamento do Azure, chave gerenciada por cliente no Key Vault.|Sem suporte|Sem suporte|

<sup>1</sup> enquanto os serviços de mídia dão suporte ao tratamento de conteúdo em claro/sem qualquer forma de criptografia, fazer isso não é recomendado.

<sup>2</sup> Nos Serviços de Mídia v3, a criptografia de armazenamento (criptografia AES-256) somente terá suporte para compatibilidade com versões anteriores quando os Ativos tiverem sido criados com os Serviços de Mídia v2. O que significa v3 funciona com ativos criptografados de armazenamento existentes, mas não permitirá a criação de novos.

## <a name="filtering-ordering-paging"></a>Filtragem, classificação, paginação

Confira [Filtragem, classificação, paginação de entidades dos Serviços de Mídia](entities-overview.md).

## <a name="next-steps"></a>Próximas etapas

* [Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
* [Usar um DVR de nuvem](live-event-cloud-dvr.md)
* [Diferenças entre os Serviços de Mídia do Azure v2 e v3](migrate-from-v2-to-v3.md)
