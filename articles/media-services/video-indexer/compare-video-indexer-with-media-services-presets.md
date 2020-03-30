---
title: Comparação entre o Indexador de Vídeo e os Serviços de Mídia Azure v3 predefinições
description: Este artigo compara os recursos do Indexador de Vídeo e as predefinições v3 do Azure Media Services.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: dcfc6ea4afe23424e72c625518356be52f62bc81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77602194"
---
# <a name="compare-azure-media-services-v3-presets-and-video-indexer"></a>Comparar as predefinições do Azure Media Services v3 e o Video Indexer 

Este artigo compara os recursos das **APIs do Video Indexer** e **APIs do Media Services v3**. 

Atualmente, há uma sobreposição entre os recursos oferecidos pelas [APIs do Indexador](https://api-portal.videoindexer.ai/) de Vídeo e pelos [Serviços de Mídia v3 APIs](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json). A tabela a seguir oferece a diretriz atual para entender as diferenças e semelhanças. 

## <a name="compare"></a>Comparar

|Recurso|APIs do indexador de vídeo |Analisador de vídeo e áudio Analyzer predefinições<br/>nas APIs do Media Services v3|
|---|---|---|
|Insights de mídia|[Avançado](video-indexer-output-json-v2.md) |[Fundamentos](../latest/intelligence-concept.md)|
|Experiências|Consulte a lista completa de recursos com suporte: <br/> [Visão geral](video-indexer-overview.md)|Retorna somente os insights de vídeo|
|Cobrança|[Preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Preços dos Serviços de Mídia](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|Conformidade|Para obter as atualizações de conformidade mais atuais, visite [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) e procure por "Video Indexer" para ver se ele está em conformidade com um certificado de interesse.|Para obter as atualizações de conformidade mais atuais, visite [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf) e procure por "Serviços de Mídia" para ver se ele está em conformidade com um certificado de interesse.|
|Avaliação gratuita|Leste dos EUA|Não disponível|
|Disponibilidade de região|Veja [a disponibilidade de Serviços Cognitivos por região](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)|Consulte [a disponibilidade dos Serviços de Mídia por região](https://azure.microsoft.com/global-infrastructure/services/?products=media-services).|

## <a name="next-steps"></a>Próximas etapas

[Visão geral do indexador vídeo](video-indexer-overview.md)

[Visão geral dos Serviços de Mídia v3](../latest/media-services-overview.md)
