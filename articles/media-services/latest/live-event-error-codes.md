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
ms.date: 02/21/2020
ms.author: juliako
ms.openlocfilehash: bfbef771d33ad4d63ec8eaef83331e497d476071
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/25/2020
ms.locfileid: "77599464"
---
# <a name="media-services-live-event-error-codes"></a>Códigos de erro de eventos dinâmicos dos serviços de mídia

A tabela a seguir lista os códigos de erro de [evento ao vivo](live-events-outputs-concept.md) :

|Erro|DESCRIÇÃO|
|---|---| 
|MPE_INGEST_FRAMERATE_EXCEEDED|Esse erro ocorre quando o codificador de entrada está enviando fluxos que excedem o 30fps para codificar eventos/canais ao vivo.|
|MPE_INGEST_VIDEO_RESOLUTION_NOT_SUPPORTED|Esse erro ocorre quando o codificador de entrada está enviando fluxos que excedem as seguintes resoluções: 1920x1088 para codificar eventos/canais ao vivo e 4096 x 2160 para eventos/canais ao vivo de passagem.|

## <a name="see-also"></a>Confira também

[Códigos de erro de ponto de extremidade de streaming (origem)](streaming-endpoint-error-codes.md)

## <a name="next-steps"></a>Próximas etapas

[Tutorial: transmitir ao vivo com os serviços de mídia](stream-live-tutorial-with-api.md)
