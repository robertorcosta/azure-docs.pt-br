---
title: Componentes herdados dos serviços de mídia do Azure | Microsoft Docs
description: Este tópico aborda os componentes herdados dos serviços de mídia do Azure.
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
ms.openlocfilehash: 1c3e50fd64c113aafd2d70cc76c7f789e080ad23
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89269515"
---
# <a name="azure-media-services-legacy-components"></a>Componentes herdados dos serviços de mídia do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Com o tempo, houve aprimoramentos e aprimoramentos contínuos nos componentes do serviço de mídia. Como resultado, alguns componentes herdados foram desativados. Você pode encontrar as instruções sobre como migrar seu aplicativo do componente herdado para um componente atual nos artigos a seguir.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Planos de aposentadoria de componentes herdados e diretrizes de migração

Estamos anunciando a substituição dos processadores de mídia *Codificador de Mídia do Azure para Windows* (WAME) e *Codificador de Mídia do Azure* (AME). Esses processadores estão sendo desativados em 31 de março de 2020.

* [Migrar do codificador de mídia do Windows Azure para Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrar do Azure Media Encoder para Media Encoder Standard](migrate-azure-media-encoder.md)

Também estamos anunciando a desativação dos seguintes Análise de Mídia processadores de mídia: 
 
|Nome do processador de mídia|Data de baixa|Observações adicionais|
|---|---|
|[Azure Media Indexer 2](media-services-process-content-with-indexer2.md)|1º de janeiro de 2020|Este processador de mídia está sendo substituído pelos [serviços de mídia do Azure Video indexer](../video-indexer/index.yml). Para obter mais informações, consulte [migrar do Azure Media indexer 2 para os serviços de mídia do Azure Video indexer](migrate-indexer-v1-v2.md).|
|[Indexador de Mídia do Azure](media-services-index-content.md)|1º de março de 2023|Este processador de mídia está sendo substituído pelos [serviços de mídia do Azure Video indexer](../video-indexer/index.yml). Para obter mais informações, consulte [migrar do Azure Media indexer para os serviços de mídia do Azure Video indexer](migrate-indexer-v1-v2.md)|
|[Detecção de movimento](media-services-motion-detection.md)|1º de junho, 2020|Não há planos de substituição no momento.|
|[Resumo de vídeo](media-services-video-summarization.md)|1º de junho, 2020|Não há planos de substituição no momento.|
|[Reconhecimento óptico de caracteres em vídeo](media-services-video-optical-character-recognition.md)|1º de junho, 2020|Este processador de mídia está sendo substituído pelos [serviços de mídia do Azure Video indexer](../video-indexer/index.yml). Além disso, considere o uso da [API dos serviços de mídia do Azure v3](../latest/analyzing-video-audio-files-concept.md). <br/>Consulte [comparar as predefinições do Azure Media Services V3 e video indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md)|
|[Detector Facial](media-services-face-and-emotion-detection.md)|1º de junho, 2020|Este processador de mídia está sendo substituído pelos [serviços de mídia do Azure Video indexer](../video-indexer/index.yml). Além disso, considere o uso da [API dos serviços de mídia do Azure v3](../latest/analyzing-video-audio-files-concept.md). <br/>Consulte [comparar as predefinições do Azure Media Services V3 e video indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md)|
|[Content Moderator](media-services-content-moderation.md)|1º de junho, 2020|Este processador de mídia está sendo substituído pelos [serviços de mídia do Azure Video indexer](../video-indexer/index.yml). Além disso, considere o uso da [API dos serviços de mídia do Azure v3](../latest/analyzing-video-audio-files-concept.md). <br/>Consulte [comparar as predefinições do Azure Media Services V3 e video indexer](../video-indexer/compare-video-indexer-with-media-services-presets.md)|

## <a name="next-steps"></a>Próximas etapas

[Diretrizes de migração dos Serviços de Mídia v2 para v3](../latest/migrate-from-v2-to-v3.md)
