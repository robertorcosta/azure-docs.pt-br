---
title: Componentes legados do Azure Media Services | Microsoft Docs
description: Este tópico discute os componentes legados do Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2020
ms.author: juliako
ms.openlocfilehash: 94a70a1234d902787f248890f0cb538a4ba9c2f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77921072"
---
# <a name="azure-media-services-legacy-components"></a>Componentes legados do Azure Media Services

Ao longo do tempo, houve melhorias constantes e melhorias nos componentes do Media Service. Como resultado, alguns componentes legados foram aposentados. Você pode encontrar as instruções sobre como migrar seu aplicativo do componente legado para um componente atual nos artigos a seguir.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Planos de aposentadoria de componentes legados e orientação migratória

Estamos anunciando a depreciação dos processadores de mídia *Windows Azure Media Encoder* (WAME) e *Azure Media Encoder* (AME). Esses processadores serão aposentados em 31 de março de 2020.

* [Migrar do Windows Azure Media Encoder para o Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrar do Azure Media Encoder para o Media Encoder Standard](migrate-azure-media-encoder.md)

Também estamos anunciando a aposentadoria dos seguintes processadores de mídia media analytics: 
 
|Nome do processador de mídia|Data de baixa|Observações adicionais|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1º de janeiro de 2020|Este processador de mídia está sendo substituído pelo [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Para obter mais informações, consulte [Migrar do Azure Media Indexer 2 para o Azure Media Services Video Indexer](migrate-indexer-v1-v2.md).|
|[Indexador de Mídia do Azure](media-services-index-content.md)|1 de março de 2023|Este processador de mídia está sendo substituído pelo [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Para obter mais informações, consulte [Migrar do Indexador de Mídia do Azure para o Indexador de Vídeo do Azure Media Services](migrate-indexer-v1-v2.md)|
|[Detecção de movimento](media-services-motion-detection.md)|1 º de junho de 2020|Não há planos de substituição no momento.|
|[Sumário de vídeo](media-services-video-summarization.md)|1 º de junho de 2020|Não há planos de substituição no momento.|
|[Reconhecimento óptico de vídeo de caracteres](media-services-video-optical-character-recognition.md)|1 º de junho de 2020|Este processador de mídia está sendo substituído pelo [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Além disso, considere usar [o Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Veja [Compare Azure Media Services v3 predefinições e Indexador de vídeo](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Detector Facial](media-services-face-and-emotion-detection.md)|1 º de junho de 2020|Este processador de mídia está sendo substituído pelo [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Além disso, considere usar [o Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Veja [Compare Azure Media Services v3 predefinições e Indexador de vídeo](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|
|[Moderador de conteúdo](media-services-content-moderation.md)|1 º de junho de 2020|Este processador de mídia está sendo substituído pelo [Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/). Além disso, considere usar [o Azure Media Services v3 API](https://docs.microsoft.com/azure/media-services/latest/analyzing-video-audio-files-concept). <br/>Veja [Compare Azure Media Services v3 predefinições e Indexador de vídeo](https://docs.microsoft.com/azure/media-services/video-indexer/compare-video-indexer-with-media-services-presets)|

## <a name="next-steps"></a>Próximas etapas

[Diretrizes de migração dos Serviços de Mídia v2 para v3](../latest/migrate-from-v2-to-v3.md)
