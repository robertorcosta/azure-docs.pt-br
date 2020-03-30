---
title: Identifique e transcreva automaticamente conteúdo em vários idiomas com indexador de vídeo
titleSuffix: Azure Media Services
description: Este tópico demonstra como identificar e transcrever automaticamente conteúdo em vários idiomas com o Indexador de Vídeo.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 09/01/2019
ms.author: juliako
ms.openlocfilehash: f0dede42891069bb5d01ddc33f3797c20c5493d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2020
ms.locfileid: "72968735"
---
# <a name="automatically-identify-and-transcribe-multi-language-content-preview"></a>Identifique e transcreva automaticamente conteúdo em vários idiomas (visualização)

O Video Indexer suporta identificação automática de idiomas e transcrição em conteúdo multi-idioma. Esse processo envolve identificar automaticamente a língua falada em diferentes segmentos do áudio, enviando cada segmento do arquivo de mídia para ser transcrito e combinar a transcrição de volta a uma transcrição unificada. 

## <a name="choosing-multilingual-identification-on-indexing-with-portal"></a>Escolhendo identificação multilíngüe na indexação com portal

Você pode escolher **a detecção de vários idiomas** ao carregar e indexar seu vídeo. Alternativamente, você pode escolher **a detecção de vários idiomas** ao reindexar seu vídeo. As seguintes etapas descrevem como reindexar:

1. Navegue até o site do [Video Indexer](https://vi.microsoft.com/) e entre.
1. Vá para a página **biblioteca** e disvirhe sobre o nome do vídeo que você deseja reindexar. 
1. No canto inferior direito, clique no botão **Reindexar vídeo.** 
1. Na **caixa de diálogo de vídeo Reindexe,** escolha a **detecção de vários idiomas** na caixa gotária do idioma de **origem** de vídeo.

    * Quando um vídeo é indexado como multi-idioma, a página de insights incluirá essa opção e um tipo de insight adicional aparecerá, permitindo que o usuário visualize qual segmento é transcrito em qual idioma "linguagem falada".
    * A tradução para todos os idiomas está totalmente disponível a partir da transcrição em vários idiomas.
    * Todos os outros insights aparecerão na linguagem de mestrado detectada – que é a linguagem que mais apareceu no áudio.
    * A legenda fechada no player também está disponível em vários idiomas.

![Experiência do Portal](./media/multi-language-identification-transcription/portal-experience.png)

## <a name="choosing-multilingual-identification-on-indexing-with-api"></a>Escolhendo identificação multilíndüe na indexação com API

Ao indexar ou [reindexar](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-Index-Video?) um vídeo usando `multi-language detection` a `sourceLanguage` API, escolha a opção no parâmetro.

### <a name="model-output"></a>Saída do modelo

O modelo recuperará todos os idiomas detectados no vídeo em uma lista

```json
"sourceLanguage": null,
"sourceLanguages": [
    "es-ES",
    "en-US"
],
```

Além disso, cada instância na seção de transcrição incluirá a linguagem em que foi transcrita

```json
{
  "id": 136,
  "text": "I remember well when my youth Minister took me to hear Doctor King I was a teenager.",
  "confidence": 0.9343,
  "speakerId": 1,
  "language": "en-US",
  "instances": [
    {
       "adjustedStart": "0:21:10.42",
       "adjustedEnd": "0:21:17.48",
       "start": "0:21:10.42",
       "end": "0:21:17.48"
    }
  ]
},
```

## <a name="guidelines-and-limitations"></a>Diretrizes e limitações

* Conjunto de idiomas suportados: inglês, francês, alemão, espanhol.
* Suporte para conteúdo em vários idiomas com até três idiomas suportados.
* Se o áudio contiver outros idiomas além da lista suportada acima, o resultado será inesperado.
* Comprimento mínimo do segmento para detectar para cada idioma – 15 segundos.
* O deslocamento de detecção de linguagem é de 3 segundos em média.
* Espera-se que o discurso seja contínuo. Alterações frequentes entre idiomas podem afetar o desempenho dos modelos.
* A fala de falantes não nativos pode afetar o desempenho do modelo (por exemplo, quando os falantes usam sua língua nativa e mudam para outra língua).
* O modelo foi projetado para reconhecer uma fala conversacional espontânea com acústica de áudio razoável (não comandos de voz, canto, etc.).
* Atualmente, a criação e edição de projetos não está disponível para vídeos em vários idiomas.
* Modelos de idioma personalizados não estão disponíveis ao usar detecção de várias linguagens.
* A adição de palavras-chave não é suportada.
* Ao exportar arquivos de legenda fechada, a indicação do idioma não aparecerá.
* A API de transcrição de atualização não suporta arquivos de vários idiomas.

## <a name="next-steps"></a>Próximas etapas

[Visão geral do indexador vídeo](video-indexer-overview.md)
