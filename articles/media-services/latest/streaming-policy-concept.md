---
title: Políticas de streaming nos Serviços de Mídia do Azure | Microsoft Docs
description: Este artigo fornece uma explicação sobre o que são as políticas de streaming e como são usados pelos Serviços de Mídia do Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 05/28/2019
ms.author: juliako
ms.openlocfilehash: 9c80056fd62173ff1e5a6ed3979adba71b7706cc
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/02/2020
ms.locfileid: "80582771"
---
# <a name="streaming-policies"></a>Políticas de Streaming

Nos Serviços de Mídia do Azure v3, as [Políticas de Streaming](https://docs.microsoft.com/rest/api/media/streamingpolicies) permitem definir protocolos de streaming e opções de criptografia para seus [Localizadores de Streaming](streaming-locators-concept.md). O Media Services v3 fornece algumas políticas de streaming predefinidas para que você possa usá-las diretamente para teste ou produção. 

As políticas de streaming predefinidas atualmente disponíveis:<br/>
* 'Predefined_DownloadOnly'
* 'Predefined_ClearStreamingOnly'
* 'Predefined_DownloadAndClearStreaming'
* 'Predefined_ClearKey'
* 'Predefined_MultiDrmCencStreaming' 
* 'Predefined_MultiDrmStreaming'

A seguinte "Árvore de decisão" ajuda você a escolher uma política de streaming predefinida para o seu cenário.

> [!IMPORTANT]
> * As propriedades de **Políticas de Streaming** que são do tipo Datetime estão sempre no formato UTC.
> * Você deve criar um conjunto limitado de políticas para sua conta de serviço de mídia e reutilizá-los para os Localizadores de Streaming sempre que as mesmas opções forem necessárias. Para obter mais informações, consulte [Cotas e limites](limits-quotas-constraints.md).

## <a name="decision-tree"></a>Árvore de decisão

Clique na imagem para exibi-la em tamanho normal.  

<a href="./media/streaming-policy/large.png" target="_blank"><img src="./media/streaming-policy/large.png"></a> 

Se criptografar seu conteúdo, você precisa criar uma [Política de Chave de Conteúdo](content-key-policy-concept.md), a Política de Chave de **Conteúdo** não é necessária para um streaming ou download claro. 

Se você tiver requisitos especiais (por exemplo, se você quiser especificar diferentes protocolos, precisar usar um serviço de entrega de chaves personalizado ou precisar usar uma faixa de áudio clara), você pode [criar](https://docs.microsoft.com/rest/api/media/streamingpolicies/create) uma Política de Streaming personalizada. 

## <a name="get-a-streaming-policy-definition"></a>Obtenha uma definição de Política de Streaming  

Se você quiser ver a definição de uma política de streaming, use [Obter](https://docs.microsoft.com/rest/api/media/streamingpolicies/get) e especificar o nome da diretiva. Por exemplo:

### <a name="rest"></a>REST

Solicitação:

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaServices/contosomedia/streamingPolicies/clearStreamingPolicy?api-version=2018-07-01
```

Resposta:

```
{
  "name": "clearStreamingPolicy",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/contoso/providers/Microsoft.Media/mediaservices/contosomedia/streamingPolicies/clearStreamingPolicy",
  "type": "Microsoft.Media/mediaservices/streamingPolicies",
  "properties": {
    "created": "2018-08-08T18:29:30.8501486Z",
    "noEncryption": {
      "enabledProtocols": {
        "download": true,
        "dash": true,
        "hls": true,
        "smoothStreaming": true
      }
    }
  }
}
```

## <a name="filtering-ordering-paging"></a>Filtragem, classificação, paginação

Confira [Filtragem, classificação, paginação de entidades dos Serviços de Mídia](entities-overview.md).

## <a name="next-steps"></a>Próximas etapas

* [Transmitir um arquivo por streaming](stream-files-dotnet-quickstart.md)
* [Usar criptografia dinâmica AES-128 e o serviço de entrega de chave](protect-with-aes128.md)
* [Usar o serviço de entrega de licença e criptografia dinâmica do DRM](protect-with-drm.md)
