---
title: Gerenciar pontos finais de streaming com o Azure Media Services v3
description: Este artigo demonstra como gerenciar pontos finais de streaming com o Azure Media Services v3.
services: media-services
documentationcenter: ''
author: Juliako
writer: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2020
ms.author: juliako
ms.openlocfilehash: 75ba2ad87eabd7ff6b0625ad95ab24a8ae58dd0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79461037"
---
# <a name="manage-streaming-endpoints-with--media-services-v3"></a>Gerenciar pontos finais de streaming com Serviços de Mídia v3

Quando sua conta de Serviços de Mídia é criada, um [ponto final de streaming](streaming-endpoint-concept.md) **padrão** é adicionado à sua conta no estado **Parado.** Para começar a transmitir seu conteúdo e aproveitar [a embalagem dinâmica](dynamic-packaging-overview.md) e a criptografia [dinâmica,](content-protection-overview.md)o ponto final de streaming a partir do qual você deseja transmitir conteúdo tem que estar no estado **em execução.**

Este artigo mostra como executar o comando [start](https://docs.microsoft.com/rest/api/media/streamingendpoints/start) em seu ponto final de streaming usando diferentes tecnologias. 
 
> [!NOTE]
> Você será cobrado apenas quando seu ponto de extremidade de streaming estiver em estado de execução.
    
## <a name="prerequisites"></a>Pré-requisitos

Revisão: 

* [Conceitos dos Serviços de Mídia](concepts-overview.md)
* [Conceito de Streaming Endpoint](streaming-endpoint-concept.md)
* [Empacotamento dinâmico](dynamic-packaging-overview.md)

## <a name="use-rest"></a>Usar o REST

```rest
POST https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/slitestmedia10/streamingEndpoints/myStreamingEndpoint1/start?api-version=2018-07-01
```

Para obter mais informações, consulte: 

* O iniciar uma documentação de referência [do StreamingEndpoint.](https://docs.microsoft.com/rest/api/media/streamingendpoints/start)
* Iniciar um ponto final de streaming é uma operação assíncrona. 

    Para obter informações sobre como monitorar operações de longa duração, consulte [operações de longo prazo](media-services-apis-overview.md).
* Esta [coleção do Carteiro](https://github.com/Azure-Samples/media-services-v3-rest-postman/blob/master/Postman/Media%20Services%20v3.postman_collection.json) contém exemplos de várias operações REST, incluindo sobre como iniciar um ponto final de streaming.

## <a name="use-the-azure-portal"></a>Use o Portal do Azure 
 
1. Faça login no [portal Azure](https://portal.azure.com/).
1. Vá para sua conta do Azure Media Services.
1. No painel esquerdo, selecione **Pontos finais de streaming**.
1. Selecione o ponto final de streaming que deseja iniciar e selecione **Iniciar**.

## <a name="use-the-azure-cli"></a>Usar a CLI do Azure

```cli
az ams streaming-endpoint start [--account-name]
                                [--ids]
                                [--name]
                                [--no-wait]
                                [--resource-group]
                                [--subscription]
```

Para obter mais informações, consulte [az ams streaming-endpoint start](https://docs.microsoft.com/cli/azure/ams/streaming-endpoint?view=azure-cli-latest#az-ams-streaming-endpoint-start).

## <a name="use-sdks"></a>Usar SDKs

### <a name="java"></a>Java
    
```java
if (streamingEndpoint != null) {
// Start The Streaming Endpoint if it is not running.
if (streamingEndpoint.resourceState() != StreamingEndpointResourceState.RUNNING) {
    manager.streamingEndpoints().startAsync(config.getResourceGroup(), config.getAccountName(), STREAMING_ENDPOINT_NAME).await();
}
```

Consulte a amostra completa [do código Java](https://github.com/Azure-Samples/media-services-v3-java/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/src/main/java/sample/StreamHLSAndDASH.java#L128).

### <a name="net"></a>.NET

```csharp
StreamingEndpoint streamingEndpoint = await client.StreamingEndpoints.GetAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);

if (streamingEndpoint != null)
{
    if (streamingEndpoint.ResourceState != StreamingEndpointResourceState.Running)
    {
        await client.StreamingEndpoints.StartAsync(config.ResourceGroup, config.AccountName, DefaultStreamingEndpointName);
    }
```

Consulte a amostra completa do [código .NET](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/master/DynamicPackagingVODContent/StreamHLSAndDASH/Program.cs#L112).

---

## <a name="next-steps"></a>Próximas etapas

* [Serviços de mídia v3 Especificação OpenAPI (Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)
* [Operações de streaming endpoint](https://docs.microsoft.com/rest/api/media/streamingendpoints)
