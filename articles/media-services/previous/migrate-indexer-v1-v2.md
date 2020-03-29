---
title: Migrar de Indexador v1 e v2 para Indexador de Vídeo do Azure Media Services | Microsoft Docs
description: Este tópico discute como migrar do Azure Media Indexer v1 e v2 para o Azure Media Services Video Indexer.
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
ms.date: 09/20/2019
ms.author: juliako
ms.openlocfilehash: 2268c074480f99ca23117ca2ffd2c87c1dbb10a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513228"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-indexer"></a>Migrar de Indexador de Mídia e Indexador de Mídia 2 para Indexador de Vídeo

O processador de mídia [Azure Media Indexer](media-services-index-content.md) e os processadores de mídia [Azure Media Indexer 2 Preview](media-services-process-content-with-indexer2.md) estão sendo aposentados. Para as datas de aposentadoria, consulte este tópico [de componentes legados.](legacy-components.md) [O Azure Media Services Video Indexer](https://docs.microsoft.com/azure/media-services/video-indexer/) substitui esses processadores de mídia legados.

O Azure Media Services Video Indexer é baseado no Azure Media Analytics, Azure Cognitive Search, Cognitive Services (como a API face, Microsoft Translator, API de visão computacional e Serviço de Fala Personalizada). Ele permite que você extraia os insights de seus vídeos usando os modelos de áudio e vídeo do Video Indexer. Para ver em que cenários o Video Indexer pode ser usado, quais recursos oferece e como começar, consulte [os modelos de vídeo indexador de vídeo e áudio](../video-indexer/video-indexer-overview.md). 

Você pode extrair insights de seus arquivos de vídeo e áudio usando as [predefinições do analisador V3 do Azure Media Services](../latest/analyzing-video-audio-files-concept.md) ou diretamente usando as [APIs do Indexador de Vídeo](https://api-portal.videoindexer.ai/). Atualmente, há uma sobreposição entre os recursos oferecidos pelas APIs do Indexador de Vídeo e pelos APIs v3 dos Serviços de Mídia.

> [!NOTE]
> Para entender quando você deseja usar predefinições do analisador de videoindexador vs. Media Services, confira o [documento de comparação](../video-indexer/compare-video-indexer-with-media-services-presets.md). 

Este artigo discute os passos para migrar do Azure Media Indexer e do Azure Media Indexer 2 para o Azure Media Services Video Indexer.  

## <a name="migration-options"></a>Opções de migração 

|Se você precisar  |e, em seguida, |
|---|---|
|uma solução que fornece uma transcrição de voz para texto para qualquer formato de arquivo de mídia em formatos de arquivo de legenda fechada: VTT, SRT ou TTML<br/>bem como insights adicionais de áudio, tais como: palavras-chave, inferência de tópicos, eventos acústicos, diarização de palestrantes, extração de entidades e tradução| atualize seus aplicativos para usar os recursos do Indexador de Vídeo Azure através da API do Video Indexer v2 REST ou da preconfiguração do Azure Media Services v3 Audio Analyzer.|
|recursos de fala para texto| use diretamente a API de fala de serviços cognitivos.|  

## <a name="getting-started-with-video-indexer"></a>Começando com indexador de vídeo

A seção a seguir aponta para links [relevantes: Como posso começar com o Indexador de Vídeo?](https://docs.microsoft.com/azure/media-services/video-indexer/video-indexer-overview#how-can-i-get-started-with-video-indexer) 

## <a name="getting-started-with-media-services-v3-apis"></a>Começando com ApIs de Media Services v3

A Api v3 do Azure Media Services permite extrair insights de seus arquivos de vídeo e áudio através das [predefinições do analisador V3 do Azure Media Services](../latest/analyzing-video-audio-files-concept.md). 

**AudioAnalyzerPreset** permite a extração de várias informações de áudio de um arquivo de áudio ou vídeo. A saída inclui um arquivo VTT ou TTML para a transcrição de áudio e um arquivo JSON (com todos os insights adicionais de áudio). Os insights de áudio incluem palavras-chave, indexação de alto-falantes e análise de sentimento da fala. AudioAnalyzerPreset também suporta detecção de idiomas para idiomas específicos. Para obter informações [detalhadas,](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#audioanalyzerpreset)consulte Transformações .

### <a name="get-started"></a>Introdução

Para iniciar, confira:

* [Tutorial](../latest/analyze-videos-tutorial-with-api.md)
* Amostras de audioanalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) ou [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* Amostras de VideoAnalyzerPreset: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) ou [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Começando com serviços cognitivos de fala

[O Azure Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/) fornece um serviço de fala para texto que transcreve fluxos de áudio para texto em tempo real que seus aplicativos, ferramentas ou dispositivos podem consumir ou exibir. Você pode usar o discurso-para-texto para [personalizar seu próprio modelo acústico, modelo de linguagem ou modelo de pronúncia](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md). Para obter mais informações, consulte [Serviços Cognitivos de fala a texto](../../cognitive-services/speech-service/speech-to-text.md). 

> [!NOTE] 
> O serviço de fala para texto não toma formatos de arquivo de vídeo e leva apenas [certos formatos de áudio](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#audio-formats). 

Para obter mais informações sobre o serviço de texto para fala e como começar, consulte [O que é fala-para-texto?](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-to-text)

## <a name="known-differences-from-deprecated-services"></a>Diferenças conhecidas de serviços depreciados 

Você verá que os serviços de Video Indexer, Azure Media Services v3 AudioAnalyzerPreset e Cognitive Services Speech Services são mais confiáveis e produzem uma produção de melhor qualidade do que os processadores azure Media Indexer 1 e Azure Media Indexer 2.  

Algumas diferenças conhecidas incluem: 

* Os Serviços Cognitivos de Fala não suportam a extração de palavras-chave. No entanto, o Video Indexer e o Media Services v3 AudioAnalyzerPreset oferecem um conjunto mais robusto de palavras-chave no formato de arquivo JSON. 

## <a name="need-help"></a>Precisa de ajuda?

Você pode abrir um ticket de suporte navegando para [nova solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-steps"></a>Próximas etapas

* [Componentes herdados](legacy-components.md)
* [Página de preços](https://azure.microsoft.com/pricing/details/media-services/#encoding)


