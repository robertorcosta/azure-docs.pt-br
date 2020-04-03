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
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582685"
---
# <a name="streaming-locators"></a>Localizadores de Streaming

Para fazer vídeos na saída do ativo disponível para clientes de reprodução, você precisa criar um [Localizador de Streaming](https://docs.microsoft.com/rest/api/media/streaminglocators) e, em seguida, criar URLs de streaming. Para criar uma URL, você precisa concatenar o nome do host do Ponto de Extremidade de Streaming com o caminho do Localizador de Streaming. Para um exemplo de .NET, veja [Obter um Localizador de Streaming](stream-files-tutorial-with-api.md#get-a-streaming-locator).

O processo de criação de um **Localizador de Streaming** é chamado de publicação. Por padrão, o **Localizador de Streaming** é válido imediatamente após fazer as chamadas da API e durar até que seja excluído, a menos que você configure os horários opcionais de início e término. 

Ao criar um **localizador de streaming,** você deve especificar um nome **de ativo** e um nome **de política de streaming.** Para obter mais informações, consulte estes tópicos:

* [Ativos](assets-concept.md)
* [Políticas de Streaming](streaming-policy-concept.md)
* [Políticas da Chave de Conteúdo](content-key-policy-concept.md)

Você também pode especificar o tempo de início e término em seu Localizador de Streaming, que só permitirá que seu usuário reproduzisse o conteúdo entre esses tempos (por exemplo, entre 5/1/2019 a 5/5/2019).  

## <a name="considerations"></a>Considerações

* **Os localizadores de** streaming não são updatable. 
* As propriedades de **Localizadores de Streaming** que são do tipo Datetime estão sempre no formato UTC.
* Você deve criar um conjunto limitado de políticas para sua conta de serviço de mídia e reutilizá-los para os Localizadores de Streaming sempre que as mesmas opções forem necessárias. Para obter mais informações, consulte [Cotas e limites](limits-quotas-constraints.md).

## <a name="create-streaming-locators"></a>Criar localizadores de streaming  

### <a name="not-encrypted"></a>Não criptografado

Se você quiser transmitir seu arquivo in-the-clear (não criptografado), defina a política de streaming clara predefinida: para 'Predefined_ClearStreamingOnly' (em .NET, você pode usar o enum PredefinedStreamingPolicy.ClearStreamingOnly).

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

Se você precisar criptografar seu conteúdo com a criptografia cenc, defina sua política como 'Predefined_MultiDrmCencStreaming'. A criptografia Widevine será aplicada a um fluxo DASH e playReady to Smooth. A chave será entregue a um cliente de reprodução com base nas licenças DRM configuradas.

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

Se você também quiser criptografar seu fluxo HLS com CBCS (FairPlay), use 'Predefined_MultiDrmStreaming'.

> [!NOTE]
> O Widevine é um serviço fornecido pela Google Inc. e está sujeito aos termos de serviço e à política de privacidade da Google, Inc.

## <a name="associate-filters-with-streaming-locators"></a>Associar filtros com localizadores de streaming

Ver [Filtros: associar-se aos localizadores de streaming](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="filter-order-page-streaming-locator-entities"></a>Filtrar, encomendar, página Serviço de localização de streaming entidades

Confira [Filtragem, classificação, paginação de entidades dos Serviços de Mídia](entities-overview.md).

## <a name="list-streaming-locators-by-asset-name"></a>Listar localizadores de streaming pelo nome do ativo

Para obter localizadores de streaming com base no nome do Ativo associado, use as seguintes operações:

|Idioma|API|
|---|---|
|REST|[liststreaminglocators](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators)|
|CLI|[az ams lista de ativos-streaming-locatores](https://docs.microsoft.com/cli/azure/ams/asset?view=azure-cli-latest#az-ams-asset-list-streaming-locators)|
|.NET|[ListStreamingLocators](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.media.assetsoperationsextensions.liststreaminglocators?view=azure-dotnet#Microsoft_Azure_Management_Media_AssetsOperationsExtensions_ListStreamingLocators_Microsoft_Azure_Management_Media_IAssetsOperations_System_String_System_String_System_String_)|
|Java|[AssetStreamingLocator](https://docs.microsoft.com/rest/api/media/assets/liststreaminglocators#assetstreaminglocator)|
|Node.js|[listStreamingLocators](https://docs.microsoft.com/javascript/api/@azure/arm-mediaservices/assets#liststreaminglocators-string--string--string--msrest-requestoptionsbase-)|

## <a name="see-also"></a>Confira também

* [Ativos](assets-concept.md)
* [Políticas de Streaming](streaming-policy-concept.md)
* [Políticas da Chave de Conteúdo](content-key-policy-concept.md)
* [Tutorial: Envie, encode e transmita vídeos usando .NET](stream-files-tutorial-with-api.md)

## <a name="next-steps"></a>Próximas etapas

[Como criar um localizador de streaming e construir URLs](create-streaming-locator-build-url.md)
