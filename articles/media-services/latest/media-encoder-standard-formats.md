---
title: Codecs e formatos de codificador padrão-Azure
description: Este artigo contém uma lista dos formatos de arquivo de importação e exportação mais comuns que você pode usar com o StandardEncoderPreset.
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
ms.date: 02/10/2019
ms.author: juliako
ms.reviewer: anilmur
ms.openlocfilehash: f1d4d4f4006702ebe0d057e56cf24a022e73b83e
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888063"
---
# <a name="standard-encoder-formats-and-codecs"></a>Codecs e formatos de codificador padrão

Este artigo contém uma lista dos formatos de arquivo de importação e exportação mais comuns que você pode usar com [StandardEncoderPreset](https://docs.microsoft.com/rest/api/media/transforms/createorupdate#standardencoderpreset). Para obter informações sobre como criar predefinições personalizadas usando **StandardEncoderPreset**, veja [Criar uma transformação com uma predefinição personalizada](customize-encoder-presets-how-to.md).

## <a name="input-containerfile-formats"></a>Formatos de contêiner/arquivo de entrada

| Formatos de arquivo (extensões de arquivo) | Com suporte |
| --- | --- |
| FLV (com codecs H.264 e AAC) (.flv) |SIM |
| MXF    (.mxf) |SIM |
| GXF    (.gxf) |SIM |
| MPEG2-PS, MPEG2-TS, 3GP (.ts, .ps, .3gp, .3gpp, .mpg) |SIM |
| Vídeo do Windows Media (WMV)/ASF (.wmv, .asf) |SIM |
| AVI (8 bits/10 bits descompactado) (.avi) |SIM |
| MP4 (.mp4, .m4a, .m4v)/ISMV (.isma, .ismv) |SIM |
| [Gravação (DVR-MS) de vídeo Digital da Microsoft](https://msdn.microsoft.com/library/windows/desktop/dd692984) (.dvr-ms) |SIM |
| Matroska/WebM (.mkv) |SIM |
| WAVE/WAV (.wav) |SIM |
| QuickTime (.mov) |SIM |

### <a name="audio-formats-in-input-containers"></a>Formatos de áudio em contêineres de entrada

O codificador padrão dá suporte à manutenção dos seguintes formatos de áudio em contêineres de entrada:

* Arquivos do MXF, GXF e QuickTime que têm faixas de áudio com exemplos em estéreo intercalado ou de 5.1

ou

* Arquivos MXF, GXF e QuickTime onde o áudio é executado como faixas PCM separadas, mas o mapeamento de canal (para estéreo ou 5.1) pode ser deduzido dos metadados do arquivo

## <a name="input-video-codecs"></a>Codecs de vídeo de entrada
| Codecs de vídeo de entrada | Com suporte |
| --- | --- |
| AVC de 8 bits/10 bits até 4:2:2, incluindo AVCIntra |8 bits 4:2:0 e 4:2:2 |
| DNxHD ávido (em MXF) |SIM |
| DVCPro/DVCProHD (em MXF) |SIM |
| Vídeo digital (VD) (em arquivos AVI) |SIM |
| JPEG 2000 |SIM |
| MPEG-2 (até perfil e de alto nível 422; incluindo variantes como XDCAM, XDCAM HD, XDCAM IMX, CableLabs ® e D10) |Até perfil 422 |
| MPEG-1 |SIM |
| VC-1/WMV9 |SIM |
| Canopus HQ/HQX |Não |
| MPEG-4, parte 2 |SIM |
| [Theora](https://en.wikipedia.org/wiki/Theora) |SIM |
| YUV420 descompactado, ou mezzanine |SIM |
| Apple ProRes 422 |SIM |
| Apple ProRes 422 LT |SIM |
| Apple ProRes 422 HQ |SIM |
| Apple ProRes Proxy |SIM |
| Apple ProRes 4444 |SIM |
| Apple ProRes 4444 XQ |SIM |
| HEVC/H.265| Perfil Principal|

## <a name="input-audio-codecs"></a>Codecs de áudio de entrada
| Codecs de áudio de entrada | Com suporte |
| --- | --- |
| AAC (AAC-LC, AAC-HE e AAC-HEv2; até 5.1) |SIM |
| MPEG Layer 2 |SIM |
| MP3 (MPEG-1 Audio Layer 3) |SIM |
| Áudio do Windows Media |SIM |
| WAV/PCM |SIM |
| [FLAC](https://en.wikipedia.org/wiki/FLAC)</a> |SIM |
| [Opus](https://go.microsoft.com/fwlink/?LinkId=822667) |SIM |
| [Vorbis](https://en.wikipedia.org/wiki/Vorbis)</a> |SIM |
| AMR (multitaxa adaptável) |SIM |
| AES (SMPTE 331M e 302M, AES3-2003) |Não |
| Dolby® E |Não |
| Dolby® Digital (AC3) |Não |
| Dolby® Digital Plus (E-AC3) |Não |

## <a name="output-formats-and-codecs"></a>Codecs e formatos de saída
A tabela a seguir lista os codecs e formatos de arquivo com suporte para exportação.

| Formato de arquivo | Codec de vídeo | Codec de áudio |
| --- | --- | --- |
| MP4 <br/><br/>(incluindo contêineres MP4 de múltiplas taxas de bits) |H.264 (Perfis Alto, Principal e Linha de base) |AAC-LC, HE-AAC v1, HE-AAC v2 |
| MPEG2-TS |H.264 (Perfis Alto, Principal e Linha de base) |AAC-LC, HE-AAC v1, HE-AAC v2 |

## <a name="next-steps"></a>Próximos passos

[Criar uma transformação com uma predefinição personalizada](customize-encoder-presets-how-to.md)
