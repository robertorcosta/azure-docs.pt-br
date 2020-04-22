---
title: Use o Video Indexer para identificar automaticamente as línguas faladas - Azure
titleSuffix: Azure Media Services
description: Este artigo descreve como o modelo de identificação de linguagem do Indexador de Vídeo é usado para identificar automaticamente a língua falada em um vídeo.
services: media-services
author: juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: 3a71a29fdf4af10162e2f7961fb457d0e99b18e8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687133"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>Identifique automaticamente a língua falada com o modelo de identificação de idiomas

O Video Indexer suporta a identificação automática de linguagem (LID), que é o processo de identificar automaticamente o conteúdo da língua falada a partir do áudio e enviar o arquivo de mídia para ser transcrito na linguagem identificada dominante. 

Atualmente, a LID suporta: inglês, espanhol, francês, alemão, italiano, mandarim chinês, japonês, russo e português (brasileiro). 

Certifique-se de revisar a seção [Diretrizes e limitações](#guidelines-and-limitations) abaixo.

## <a name="choosing-auto-language-identification-on-indexing"></a>Escolhendo a identificação do idioma automático na indexação

Ao indexar ou [reindexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) um vídeo usando `auto detect` a API, escolha a opção no `sourceLanguage` parâmetro.

Ao usar o portal, vá para os **vídeos da sua conta** na página inicial do [Indexador](https://www.videoindexer.ai/) de vídeo e dispe sobre o nome do vídeo que você deseja reindexar. No canto inferior direito clique no botão de reindexação. Na **caixa de diálogo De reindexação** de vídeo, escolha *'Detectar automaticamente'* na caixa de baixa do **idioma de origem** de vídeo.

![auto detectar](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>Saída do modelo

O Video Indexer transcreve o vídeo de acordo com `> 0.6`a linguagem mais provável se a confiança para essa linguagem for . Se o idioma não pode ser identificado com confiança, ele assume que a língua falada é o inglês. 

O modelo de linguagem dominante está disponível `sourceLanguage` nos insights JSON como atributo (sob raiz/vídeos/insights). Uma pontuação de confiança correspondente `sourceLanguageConfidence` também está disponível sob o atributo.

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>Diretrizes e limitações

* A identificação automática de idiomas (LID) suporta os seguintes idiomas: 

    Inglês, espanhol, francês, alemão, italiano, mandarim chines, japonês, russo e português (brasileiro).
* Embora o Video Indexer suporte árabe (Padrão Moderno e Levantino), hindi e coreano, essas línguas não são suportadas em LID.
* Se o áudio contiver outros idiomas além da lista suportada acima, o resultado será inesperado.
* Se o Video Indexer não consegue identificar`>0.6`o idioma com uma confiança alta o suficiente (), o idioma de recuo é o inglês.
* Não há suporte atual para arquivo com áudio de idiomas mistos. Se o áudio contém linguagens mistas, o resultado é inesperado. 
* O áudio de baixa qualidade pode afetar os resultados do modelo.
* O modelo requer pelo menos um minuto de fala no áudio.
* O modelo foi projetado para reconhecer uma fala conversacional espontânea (não comandos de voz, canto, etc.).

## <a name="next-steps"></a>Próximas etapas

* [Visão geral](video-indexer-overview.md)
* [Identifique e transcreva automaticamente conteúdo em vários idiomas](multi-language-identification-transcription.md)
