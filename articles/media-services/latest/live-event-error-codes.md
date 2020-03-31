---
title: Códigos de erro de eventos ao vivo do Azure Media Services | Microsoft Docs
description: Este artigo lista códigos de erro de eventos ao vivo.
author: Juliako
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/25/2020
ms.author: juliako
ms.openlocfilehash: f9067562f67190b8bc04392f33078d4d3262f986
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77654572"
---
# <a name="media-services-live-event-error-codes"></a>Códigos de erro do Evento ao Vivo dos Serviços de Mídia

As tabelas nesta seção listam os códigos de erro [evento ao vivo.](live-events-outputs-concept.md)

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Ao se inscrever nos eventos da [Event Grid](https://docs.microsoft.com/azure/event-grid/) para um evento ao vivo, você poderá ver um dos seguintes erros do evento [LiveEventConnectionRejected.](media-services-event-schemas.md#liveeventconnectionrejected)

| Código de resultado | Descrição |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | URL de ingestão incorreta |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | O IP do codificador não está presente na lista de permissões de IP configurada |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | O codificador RTMP não enviou o comando setDataFrame. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | O codec especificado não é suportado. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |As informações de descrição da mídia não foram recebidas antes da entrega dos dados reais da mídia.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |A contagem de qualidades para o tipo de áudio ou vídeo excedeu o limite máximo permitido.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |A taxa total de entrada em um evento ao vivo ou serviço de canal excedeu o limite máximo permitido.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | O carimbo de data/hora para FLVTag de áudio ou vídeo é inválido a partir do codificador RTMP. |
| MPE_INGEST_FRAMERATE_EXCEEDED | O codificador de entrada ingerido por fluxos com framerates excedeu o máximo permitido de 30fps para codificação de eventos/canais ao vivo.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | Os fluxos de codificação de entrada ultrapassaram as seguintes resoluções permitidas: 1920x1088 para codificação de eventos/canais ao vivo e 4096 x 2160 para eventos/canais ao vivo de passagem.|

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Você pode ver um dos seguintes erros do evento [LiveEventEncoderDisconnected.](media-services-event-schemas.md#liveeventencoderdisconnected)

|Código de resultado|Descrição|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|A sessão RTMP atingiu o tempo limite depois de ficar ociosa por um limite de tempo permitido.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|O carimbo de data/hora para FLVTag de áudio ou vídeo é inválido a partir do codificador RTMP.|
|MPE_CAPACITY_LIMIT_REACHED|Envio muito rápido de dados pelo codificador.|
|Códigos de erro desconhecidos|Esses códigos de erro podem variar de erro de memória para entradas duplicadas no mapa de hash.|


## <a name="see-also"></a>Confira também

[Códigos de erro do Streaming Endpoint (Origin)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Próximas etapas

[Tutorial: Transmissão ao vivo com serviços de mídia](stream-live-tutorial-with-api.md)
