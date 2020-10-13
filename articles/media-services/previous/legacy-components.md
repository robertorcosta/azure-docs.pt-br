---
title: Componentes herdados dos serviços de mídia do Azure | Microsoft Docs
description: Este tópico aborda os componentes herdados dos serviços de mídia do Azure.
services: media-services
documentationcenter: ''
author: myoungerman
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2020
ms.author: v-myoung
ms.openlocfilehash: d5a10ccc887124b2bc5595d66baad833a32275cc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/09/2020
ms.locfileid: "90600884"
---
# <a name="azure-media-services-legacy-components"></a>Componentes herdados dos serviços de mídia do Azure

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Com o tempo, aprimoramos os componentes do serviço de mídia e desativamos os componentes herdados. Este artigo ajuda você a migrar seu aplicativo de um componente herdado para um componente atual.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>Planos de aposentadoria de componentes herdados e diretrizes de migração

Os processadores de mídia do *Windows Azure Media Encoder* (WAME) e *do Azure Media Encoder* (ame) foram preteridos.

* [Migrar do codificador de mídia do Windows Azure para Media Encoder Standard](migrate-windows-azure-media-encoder.md)
* [Migrar do Azure Media Encoder para Media Encoder Standard](migrate-azure-media-encoder.md)

Os seguintes Análise de Mídia processadores de mídia foram preteridos ou em breve para serem preteridos:  
 
|Nome do processador de mídia|Data de baixa|Observações adicionais|
|---|---|
|Azure Media Indexer 2|1º de janeiro de 2020|Este processador de mídia foi substituído pelos [serviços de mídia do Azure Video indexer](../video-indexer/index.yml). Para obter mais informações, consulte [migrar do Azure Media indexer 2 para os serviços de mídia do Azure Video indexer](migrate-indexer-v1-v2.md).|
|[Indexador de Mídia do Azure](media-services-index-content.md)|1º de março de 2023|Esse processador de mídia será substituído pelos [serviços de mídia do Azure Video indexer](../video-indexer/index.yml). Para obter mais informações, consulte [migrar do Azure Media indexer para os serviços de mídia do Azure Video indexer](migrate-indexer-v1-v2.md).|
|Detecção de movimento|1º de junho, 2020|Não há planos de substituição no momento.|
|Resumo de vídeo] (Media-Services-Video-summarization.md|1º de junho, 2020|Não há planos de substituição no momento.|
|Reconhecimento óptico de caracteres em vídeo|1º de junho, 2020|Este processador de mídia foi substituído pelos [serviços de mídia do Azure Video indexer](../video-indexer/index.yml). Além disso, considere o uso da [API dos serviços de mídia do Azure v3](../latest/analyzing-video-audio-files-concept.md). <br/>Consulte [comparar as predefinições e video indexer dos serviços de mídia do Azure v3](../video-indexer/compare-video-indexer-with-media-services-presets.md).|
|Detector Facial|1º de junho, 2020|Este processador de mídia foi substituído pelos [serviços de mídia do Azure Video indexer](../video-indexer/index.yml). Além disso, considere o uso da [API dos serviços de mídia do Azure v3](../latest/analyzing-video-audio-files-concept.md). <br/>Consulte [comparar as predefinições e video indexer dos serviços de mídia do Azure v3](../video-indexer/compare-video-indexer-with-media-services-presets.md).|
|Content Moderator|1º de junho, 2020|Este processador de mídia foi substituído pelos [serviços de mídia do Azure Video indexer](../video-indexer/index.yml). Além disso, considere o uso da [API dos serviços de mídia do Azure v3](../latest/analyzing-video-audio-files-concept.md). <br/>Consulte [comparar as predefinições e video indexer dos serviços de mídia do Azure v3](../video-indexer/compare-video-indexer-with-media-services-presets.md).|

## <a name="next-steps"></a>Próximas etapas

[Diretrizes de migração dos Serviços de Mídia v2 para v3](../latest/migrate-from-v2-to-v3.md)
