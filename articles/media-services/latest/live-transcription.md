---
title: Transcrição ao vivo
titleSuffix: Azure Media Services
description: Saiba mais sobre a transcrição ao vivo do Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 11/19/2019
ms.author: juliako
ms.openlocfilehash: b364b6e70e3b5723c483bc3435f0c3a152c03aa9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499866"
---
# <a name="live-transcription-preview"></a>Transcrição ao vivo (pré-visualização)

O Azure Media Service fornece vídeo, áudio e texto em diferentes protocolos. Quando você publica sua transmissão ao vivo usando MPEG-DASH ou HLS/CMAF, em seguida, juntamente com vídeo e áudio, nosso serviço fornece o texto transcrito em TTML compatível com IMSC1.1. A entrega é embalada em fragmentos MPEG-4 Parte 30 (ISO/IEC 14496-30). Se usar a entrega via HLS/TS, o texto será entregue como VTT em pedaços.

Este artigo descreve como ativar a transcrição ao vivo ao transmitir um evento ao vivo com o Azure Media Services v3. Antes de continuar, certifique-se de estar familiarizado com o uso de APIs de Media Services v3 REST (veja [este tutorial](stream-files-tutorial-with-rest.md) para obter detalhes). Você também deve estar familiarizado com o conceito [de transmissão ao vivo.](live-streaming-overview.md) Recomenda-se completar o [tutorial de Stream live com Media Services.](stream-live-tutorial-with-api.md)

> [!NOTE]
> Atualmente, a transcrição ao vivo só está disponível como um recurso de pré-visualização na região oeste dos EUA 2. Suporta transcrição de palavras faladas em inglês para texto. A referência de API para este recurso está localizada abaixo — becasuse está em pré-visualização, os detalhes não estão disponíveis com nossos documentos REST.

## <a name="creating-the-live-event"></a>Criando o Evento Ao Vivo

Para criar o Evento Ao Vivo, envie a operação PUT para a versão de pré-visualização 2019-05-01, por exemplo:

```
PUT https://management.azure.com/subscriptions/:subscriptionId/resourceGroups/:resourceGroupName/providers/Microsoft.Media/mediaServices/:accountName/liveEvents/:liveEventName?api-version=2019-05-01-preview&autoStart=true 
```

A operação tem o seguinte corpo (onde um evento ao vivo de passagem é criado com RTMP como protocolo de ingestão). Note a adição de uma propriedade de transcrições. O único valor permitido para a linguagem é en-US.

```
{ 
  "properties": { 
    "description": "Demonstrate how to enable live transcriptions", 
    "input": { 
      "streamingProtocol": "RTMP", 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "preview": { 
      "accessControl": { 
        "ip": { 
          "allow": [ 
            { 
              "name": "Allow All", 
              "address": "0.0.0.0", 
              "subnetPrefixLength": 0 
            } 
          ] 
        } 
      } 
    }, 
    "encoding": { 
      "encodingType": "None" 
    }, 
    "transcriptions": [ 
      { 
        "language": "en-US" 
      } 
    ], 
    "vanityUrl": false, 
    "streamOptions": [ 
      "Default" 
    ] 
  }, 
  "location": "West US 2" 
} 
```

Consulte o status do Evento Ao Vivo até que ele entre no estado "Running", o que indica que agora você pode enviar uma contribuição feed RTMP. Agora você pode seguir os mesmos passos deste tutorial, como verificar o feed de visualização e criar saídas ao vivo.

## <a name="transcription-delivery-and-playback"></a>Entrega e reprodução de transcrição

Revise o artigo [de visão geral](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery) da embalagem dinâmica de como nosso serviço usa embalagens dinâmicas para fornecer vídeo, áudio e texto em diferentes protocolos. Quando você publica sua transmissão ao vivo usando MPEG-DASH ou HLS/CMAF, em seguida, juntamente com vídeo e áudio, nosso serviço fornece o texto transcrito em TTML compatível com IMSC1.1. Esta entrega é embalada em fragmentos MPEG-4 Parte 30 (ISO/IEC 14496-30). Se usar a entrega via HLS/TS, o texto será entregue como VTT em pedaços. Você pode usar um web player como o [Azure Media Player](use-azure-media-player.md) para reproduzir a transmissão.  

> [!NOTE]
> Se usar o Azure Media Player, use a versão 2.3.3 ou posterior.

## <a name="known-issues"></a>Problemas conhecidos

Para visualização, os seguintes problemas são conhecidos com transcrição ao vivo:

* O recurso está disponível apenas no West US 2.
* Os aplicativos precisam usar as APIs de visualização, descritas na [Especificação OpenAPI do Media Services v3](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/preview/2019-05-01-preview/streamingservice.json).
* A única língua suportada é o inglês (en-us).
* Com proteção de conteúdo, apenas a criptografia de envelope AES é suportada.

## <a name="next-steps"></a>Próximas etapas

* [Visão geral dos Serviços de Mídia](media-services-overview.md)
