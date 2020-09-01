---
title: Codecs e formatos do Fluxo de Trabalho do Media Encoder Premium | Microsoft Docs
description: Este tópico fornece uma visão geral dos codecs e formatos do Fluxo de Trabalho do Media Encoder Premium
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
ms.date: 03/19/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: 38e794ce9e5a1cdc5aafeb1645f7a2b8c95520e2
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/01/2020
ms.locfileid: "89268332"
---
# <a name="media-encoder-premium-workflow-formats-and-codecs"></a>Codecs e formatos de fluxo de trabalho do Media Encoder Premium

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> O processador de mídia do Fluxo de Trabalho do Media Encoder Premium analisado neste tópico não está disponível na China. 

Este documento contém uma lista de formatos de arquivo de entrada e saída e codecs com suporte pela versão de demonstração pública do codificador de **Fluxo de trabalho do Media Encoder Premium** .

[Media Encoder Premium Workflow formatos de entrada e codecs](#input_formats)

Codecs e formatos de saída do Media Encoder Premium Workflow

**Fluxo de trabalho do Media Encoder Premium** dá suporte a legendas codificadas descritas [nesta](#closed_captioning) seção 

## <a name="media-encoder-premium-workflow-input-formats-and-codecs"></a><a id="input_formats"></a>Codecs e formatos de entrada de fluxo de trabalho do Media Encoder Premium

A seção a seguir lista os codecs e formatos de arquivo aos quais esse processador de mídia dá suporte como entrada.

### <a name="input-containerfile-formats"></a>Formatos de arquivo/contêiner de entrada

* Adobe® Flash® F4V
* MXF/SMPTE 377M
* GXF
* Fluxos de transporte de MPEG-2
* Fluxos de programa MPEG-2
* MPEG-4/MP4
* Windows Media/ASF
* AVI (8 bits/10 bits descompactado)

### <a name="input-video-codecs"></a>Codecs de vídeo de entrada

* AVC de 8 bits/10 bits até 4:2:2, incluindo AVCIntra
* DNxHD ávido (em MXF)
* DVCPro/DVCProHD (em MXF)
* HEVC/H.265, Main e Main 10 Profile
* JPEG2000
* MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10)
* MPEG-1
* Windows Media Video/VC-1

### <a name="input-audio-codecs"></a>Codecs de áudio de entrada

* AES (SMPTE 331M e 302M, AES3-2003)
* Dolby® E
* Dolby® Digital (AC3)
* AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Áudio do Windows Media
* WAV/PCM

## <a name="media-encoder-premium-workflow-output-formats-and-codecs"></a><a id="output_format"></a>Codecs e formatos de saída de fluxo de trabalho do Media Encoder Premium

A seção a seguir lista os codecs e formatos de arquivo com suporte como a saída desse processador de mídia.

### <a name="output-containerfile-formats"></a>Formatos de contêiner/arquivo de saída

* Adobe® Flash® F4V
* MXF (OP1a, XDCAM e AS02)
* DPP (incluindo AS11)
* GXF
* MPEG-4/MP4
* Windows Media/ASF
* AVI (8 bits/10 bits descompactado)
* Formato de arquivo do Smooth Streaming (PIFF 1.3)
* MPEG-TS 

### <a name="output-video-codecs"></a>Codecs de vídeo de saída

* AVC (H. 264; 8 bits; até perfil, nível elevado 5.2; 4K Ultra HD; Intra AVC)
* DNxHD ávido (em MXF)
* DVCPro/DVCProHD (em MXF)
* MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10)
* MPEG-1
* Windows Media Video/VC-1
* Criação de miniaturas JPEG
* HEVC (H.265; 8 e 10 bits, Main e Main 10 Profile)


### <a name="output-audio-codecs"></a>Codecs de áudio de saída

* AES (SMPTE 331M e 302M, AES3-2003)
* Dolby® Digital (AC3)
* Dolby ® Digital Plus (E-AC3) até 7.1
* AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1)
* MPEG Layer 2
* MP3 (MPEG-1 Audio Layer 3)
* Áudio do Windows Media

>[!NOTE]
>Se você codificar para Dolby® Digital (AC3), a saída só poderá ser gravada em um arquivo ISO MP4.

## <a name="support-for-closed-captioning"></a><a id="closed_captioning"></a>Suporte a legenda codificada

Na ingestão, o **fluxo de trabalho do Media Encoder Premium** dá suporte a:

1. Arquivos SCC
2. Arquivos SMPTE-TT
3. CEA-608/CEA-708 – transportados como dados de usuário (mensagens SEI de fluxos elementares H.264, ATSC/53, SCTE20) ou executados como dados complementares em arquivos MXF/GXF
4. Arquivos de legenda STL

Na saída, as seguintes opções estão disponíveis:

1. Tradução de CEA-608 para CEA-708
2. Transmissão CEA-608/CEA-708 (integrada em mensagens SEI para fluxos elementares H.264, ou carregada como dados auxiliares nos arquivos MXF)
3. SCC
4. Texto cronometrado de SMPTE (de origem CEA-608 por SMPTE RP2052, inclusive criação de arquivo DFXP)
5. Arquivo de legenda SRT
6. Fluxos de legenda DVB

> [!NOTE]
> Nem todos os formatos de saída acima têm suporte à entrega por meio de streaming nos Serviços de Mídia do Azure.

## <a name="known-issues"></a>Problemas conhecidos

Se o vídeo de entrada não contiver a legendagem oculta, o ativo de saída ainda conterá um arquivo TTML vazio. 

## <a name="need-help"></a>Precisa de ajuda?

Abra um tíquete de suporte navegando até [Nova solicitação de suporte](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)
## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

