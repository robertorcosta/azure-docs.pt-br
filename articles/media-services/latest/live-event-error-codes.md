---
title: Códigos de erro de eventos dinâmicos dos serviços de mídia do Azure | Microsoft Docs
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2020
ms.locfileid: "77654572"
---
# <a name="media-services-live-event-error-codes"></a>Códigos de erro de eventos dinâmicos dos serviços de mídia

As tabelas nesta seção listam os códigos de erro de [evento ao vivo](live-events-outputs-concept.md) .

## <a name="liveeventconnectionrejected"></a>LiveEventConnectionRejected

Ao assinar os eventos da [grade de eventos](https://docs.microsoft.com/azure/event-grid/) para um evento ao vivo, você poderá ver um dos erros a seguir do evento [LiveEventConnectionRejected](media-services-event-schemas.md#liveeventconnectionrejected) .

| Código de resultado | Descrição |
| ----------- | ----------- |
| MPE_RTMP_APPID_AUTH_FAILURE | URL de ingestão incorreta |
| MPE_INGEST_ENCODER_CONNECTION_DENIED | O IP do codificador não está presente na lista de permissões de IP configurada |
| MPE_INGEST_RTMP_SETDATAFRAME_NOT_RECEIVED | O codificador RTMP não enviou o comando setDataFrame. |
| MPE_INGEST_CODEC_NOT_SUPPORTED | O codec especificado não é suportado. |
| MPE_INGEST_DESCRIPTION_INFO_NOT_RECEIVED |As informações de descrição de mídia não foram recebidas antes de os dados de mídia reais serem entregues.|
| MPE_INGEST_MEDIA_QUALITIES_EXCEEDED |A contagem de qualidades para o tipo de áudio ou vídeo excedeu o limite máximo permitido.|
| MPE_INGEST_BITRATE_AGGREGATED_EXCEEDED |A taxa de bits de entrada total em um serviço de canal ou evento ao vivo excedeu o limite máximo permitido.|
| MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID | O carimbo de data/hora para FLVTag de áudio ou vídeo é inválido a partir do codificador RTMP. |
| MPE_INGEST_FRAMERATE_EXCEEDED | Os fluxos ingeridos do codificador de entrada com taxa de quadros excederam o máximo permitido de 30fps para codificar eventos/canais ao vivo.|
| MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED | Os fluxos ingeridos do codificador de entrada excederam as seguintes resoluções permitidas: 1920x1088 para codificação de eventos/canais ao vivo e 4096 x 2160 para eventos/canais ao vivo de passagem.|

## <a name="liveeventencoderdisconnected"></a>LiveEventEncoderDisconnected

Você pode ver um dos erros a seguir do evento [LiveEventEncoderDisconnected](media-services-event-schemas.md#liveeventencoderdisconnected) .

|Código de resultado|Descrição|
|---|---|
|MPE_RTMP_SESSION_IDLE_TIMEOUT|A sessão RTMP atingiu o tempo limite depois de ficar ociosa por um limite de tempo permitido.|
|MPE_RTMP_FLV_TAG_TIMESTAMP_INVALID|O carimbo de data/hora para FLVTag de áudio ou vídeo é inválido a partir do codificador RTMP.|
|MPE_CAPACITY_LIMIT_REACHED|Envio muito rápido de dados pelo codificador.|
|Códigos de erro desconhecidos|Esses códigos de erro podem variar de erro de memória para entradas duplicadas no mapa de hash.|


## <a name="see-also"></a>Confira também

[Códigos de erro de ponto de extremidade de streaming (origem)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Próximas etapas

[Tutorial: transmitir ao vivo com os serviços de mídia](stream-live-tutorial-with-api.md)
