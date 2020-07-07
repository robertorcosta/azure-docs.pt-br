---
title: Localizadores de Transmissão nos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo fornece uma explicação sobre o que são os Localizadores de Transmissão e como eles são usados pelos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/04/2020
ms.author: juliako
ms.openlocfilehash: 41b2d0ad1e072fb2bf5860ae80f8f25f886b37f7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/02/2020
ms.locfileid: "80582685"
---
# <a name="streaming-locators"></a>Localizadores de Streaming

Para fazer vídeos na saída do ativo disponível para clientes de reprodução, você precisa criar um [Localizador de Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) e, em seguida, criar URLs de streaming. Para criar uma URL, você precisa concatenar o nome do host do Ponto de Extremidade de Streaming com o caminho do Localizador de Streaming. Para um exemplo de .NET, veja [Obter um Localizador de Streaming](stream-files-tutorial-with-api.md#get-a-streaming-locator).

O processo de criação de um **Localizador de Streaming** é chamado de publicação. Por padrão, o **localizador de streaming** é válido imediatamente depois que você faz as chamadas à API e dura até que ela seja excluída, a menos que você configure as horas de início e término opcionais. 

Ao criar um **localizador de streaming**, você deve especificar um nome de **ativo** e um nome de **política de streaming** . Para obter mais informações, consulte estes tópicos:

* [Ativos](assets-concept.md)
* [Políticas de streaming](streaming-policy-concept.md)
* [Políticas de chave de conteúdo](content-key-policy-concept.md)

Você também pode especificar a hora de início e de término no seu localizador de streaming, o que permitirá que o usuário execute o conteúdo entre esses horários (por exemplo, entre 5/1/2019 e 5/5/2019).  

## <a name="considerations"></a>Considerações

* Os **localizadores de streaming** não são atualizáveis. 
* As propriedades de **Localizadores de Streaming** que são do tipo Datetime estão sempre no formato UTC.
* Você deve criar um conjunto limitado de políticas para sua conta de serviço de mídia e reutilizá-los para os Localizadores de Streaming sempre que as mesmas opções forem necessárias. Para obter mais informações, consulte [cotas e limites](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Criar localizadores de streaming  

### <a name="not-encrypted"></a>Não criptografado

Se você quiser transmitir o arquivo no-The-Clear (não criptografado), defina a política Clear streaming predefinida: para ' Predefined_ClearStreamingOnly ' (no .NET, você pode usar a enumeração PredefinedStreamingPolicy. ClearStreamingOnly).

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = PredefinedStreamingPolicy.ClearStreamingOnly
    });
```

### <a name="encrypted"></a>Criptografado 

Se você precisar criptografar o conteúdo com a criptografia CENC, defina sua política como ' Predefined_MultiDrmCencStreaming '. A criptografia Widevine será aplicada a um fluxo de DASH e a um PlayReady para Smooth. A chave será entregue a um cliente de reprodução com base nas licenças DRM configuradas.

```csharp
StreamingLocator locator = await client.StreamingLocators.CreateAsync(
    resourceGroup,
    accountName,
    locatorName,
    new StreamingLocator
    {
        AssetName = assetName,
        StreamingPolicyName = "Predefined_MultiDrmCencStreaming",
        DefaultContentKeyPolicyName = contentPolicyName
    });
```

Se você também quiser criptografar o fluxo do HLS com CBCS (FairPlay), use ' Predefined_MultiDrmStreaming '.

> [!NOTE]
> O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="associate-filters-with-streaming-locators"></a>Associar filtros a localizadores de streaming

Consulte [filtros: associar a localizadores de streaming](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Entidades do localizador de streaming, Order, Page

Confira [Filtragem, classificação, paginação de entidades dos Serviços de Mídia](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Listar localizadores de streaming por nome de ativo

Para obter localizadores de streaming com base no nome do ativo associado, use as seguintes operações:

|Language|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[AZ AMS Asset List – streaming-Locators](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Consulte também

* [Ativos](assets-concept.md)
* [Políticas de streaming](streaming-policy-concept.md)
* [Políticas de chave de conteúdo](content-key-policy-concept.md)
* [Tutorial: carregar, codificar e transmitir vídeos usando o .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Próximas etapas

[Como criar um localizador de streaming e URLs de compilação](create-streaming-locator-build-url.md)
