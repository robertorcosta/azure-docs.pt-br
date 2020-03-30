---
title: Tipos de LiveEvent do Azure Media Services | Microsoft Docs
description: No Azure Media Services, um evento ao vivo pode ser definido como uma *codificação* de passagem ou *ao vivo*. Este artigo mostra uma tabela detalhada que compara os tipos de Evento Ao Vivo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 06/13/2019
ms.author: juliako
ms.openlocfilehash: a28d4d96f643c12eeb6aa542db2c6af06f4fd954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78244635"
---
# <a name="live-event-types-comparison"></a>Comparação de tipos de Eventos ao Vivo

No Azure Media Services, um [Evento Ao Vivo](https://docs.microsoft.com/rest/api/media/liveevents) pode ser definido como um *pass-through* (um codificador ao vivo no local envia um fluxo de bitrate múltiplo) ou *codificação ao vivo* (um codificador ao vivo no local envia um único fluxo de bitrate). 

Este artigo compara características dos tipos de eventos ao vivo.

## <a name="types-comparison"></a>Comparação de tipos 

A tabela a seguir compara características dos tipos de Evento Ao Vivo. Os tipos são definidos durante a criação usando [LiveEventEncodingType](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType.None** - Um codificador ao vivo no local envia um fluxo de bitrate múltiplo. As transmissões ingeridas passam pelo Evento Ao Vivo sem qualquer processamento adicional. Também referido como um evento ao vivo de passagem.
* **LiveEventEncodingType.Standard** - Um codificador ao vivo no local envia um único fluxo de bitrate para o Live Event and Media Services cria vários fluxos de bitrate. Se a alimentação de contribuição for de 720p ou resolução superior, a predefinição **Default720p** codificará um conjunto de 6 pares de resolução/bitrate (detalhes seguem posteriormente no artigo).
* **LiveEventEncodingType.Premium1080p** - Um codificador ao vivo no local envia um único fluxo de bitrate para o Live Event and Media Services cria vários fluxos de bitrate. A predefinição Default1080p especifica o conjunto de saída de pares de resolução/taxa de bits (detalhes seguem posteriormente no artigo). 

| Recurso | Evento ao vivo de passagem | Evento ao vivo padrão ou premium1080p |
| --- | --- | --- |
| A entrada de taxa de bits única é codificada em várias taxas de bits na nuvem |Não |Sim |
| Resolução máxima de vídeo para feed de contribuição |4K (4096 x 2160 a 60 quadros / seg) |1080p de (1920 x 1088 em 30 quadros por segundo)|
| Camadas máximas recomendadas no feed de contribuição|Até 12|Um áudio|
| Camadas máximo na saída| Igual à entrada|Até 6 (ver Predefinições do Sistema abaixo)|
| Largura de banda agregada máxima de feed de contribuição|60 Mbps|N/D|
| Taxa de bits máxima para uma única camada de contribuição |20 Mbps|20 Mbps|
| Suporte para várias faixas de áudio de idioma|Sim|Não|
| Codecs de vídeo de entrada com suporte |H.264/AVC e H.265/HEVC|H.264/AVC|
| Codecs de vídeo de saída com suporte|Igual à entrada|H.264/AVC|
| Suporte para a profundidade de bits de vídeo, entrada e saída|Até 10 bits incluindo HDR 10/HLG|8 bits|
| Codecs de áudio de entrada com suporte|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Codecs de áudio de saída com suporte|Igual à entrada|AAC-LC|
| Resolução máxima de vídeo do vídeo de saída|Igual à entrada|Padrão - 720p, Premium1080p - 1080p|
| Taxa máxima de quadros de vídeo de entrada|60 quadros/segundo|Padrão ou Premium1080p - 30 quadros/segundo|
| Protocolos de entrada|RTMP, MP4 fragmentado (Smooth Streaming)|RTMP, MP4 fragmentado (Smooth Streaming)|
| Price|Confira a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique na guia “Vídeo ao vivo”|Confira a [página de preços](https://azure.microsoft.com/pricing/details/media-services/) e clique na guia “Vídeo ao vivo”|
| Tempo de execução máximo| 24 horas x 365 dias, linha ao vivo | 24 horas x 365 dias, linear ao vivo (visualização)|
| Capacidade de passar por meio do embedded CEA 608/708 legendas de dados|Sim|Sim|
| Capacidade de ativar a Transcrição Ao Vivo|Sim|Sim|
| Suporte para inserção de imagens fixas|Não|Não|
| Suporte para sinalização de anúncios via API| Não|Não|
| Suporte para o ad sinalização por meio de mensagens na faixa de SCTE-35|Sim|Sim|
| Capacidade de recuperação de interrupções breves no feed de contribuição|Sim|Parcial|
| Suporte para GOPs de entrada não uniforme|Sim|Não – entrada deve ter GOP duração fixa|
| Suporte para entrada de taxa de quadros variável|Sim|Não – a entrada deve ser uma taxa de quadros fixa. Pequenas variações são toleradas, por exemplo, durante cenas ricas em movimento. Mas o feed de contribuição não pode baixar a taxa de quadros (por exemplo, para 15 quadros/segundo).|
| Desligamento automático do Evento ao Vivo quando há perda do feed de entrada|Não|Após 12 horas, se não houver nenhum LiveOutput em execução|

## <a name="system-presets"></a>Predefinições do sistema

As resoluções e bits contidos na saída do codificador vivo são determinados pelo [predefinidoName](https://docs.microsoft.com/rest/api/media/liveevents/create#liveeventencoding). Se usar um **codificador** de vida padrão (LiveEventEncodingType.Standard), a predefinição *Default720p* especifica um conjunto de 6 pares de resolução/bitrate descritos abaixo. Caso contrário, se usar um codificador ao vivo **Premium1080p** (LiveEventEncodingType.Premium1080p), então a predefinição *Default1080p* especifica o conjunto de saída de pares de resolução/bitrate.

> [!NOTE]
> Não é possível aplicar a predefinição Default1080p a um Evento Ao Vivo se tiver sido configurada para codificação ao vivo Padrão - você terá um erro. Você também obterá um erro se tentar aplicar a predefinição Default720p a um codificador ao vivo Premium1080p.

### <a name="output-video-streams-for-default720p"></a>Fluxos de vídeo de saída para Default720p

Se a alimentação de contribuição for de 720p ou mais de resolução, a predefinição **Default720p** codificará o feed nas 6 camadas seguintes. Na tabela abaixo, Bitrate está em kbps, MaxFPS representa essa taxa de quadros máxima permitida (em quadros/segundo), Perfil representa o Perfil H.264 usado.

| Bitrate | Largura | Altura | MáxFPS | Perfil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Alta |
| 2200 |960 |540 |30 |Alta |
| 1350 |704 |396 |30 |Alta |
| 850 |512 |288 |30 |Alta |
| 550 |384 |216 |30 |Alta |
| 200 |340 |192 |30 |Alta |

> [!NOTE]
> Se você precisar personalizar a preconfiguração de codificação ao vivo, abra um bilhete de suporte através do Portal Azure. Você deve especificar a tabela desejada da resolução e das taxas de bits. Verifique se há apenas uma camada em 720p e no máximo seis camadas. Também especifique que você está solicitando uma predefinida para um codificador de live Padrão.
> Os valores específicos das taxas de bits e resoluções podem ser ajustados ao longo do tempo

### <a name="output-video-streams-for-default1080p"></a>Fluxos de vídeo de saída para Default1080p

Se a alimentação de contribuição for de resolução de 1080p, a predefinição **Default1080p** codificará o feed nas 6 camadas seguintes.

| Bitrate | Largura | Altura | MáxFPS | Perfil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Alta |
| 3000 |1280 |720 |30 |Alta |
| 1600 |960 |540 |30 |Alta |
| 800 |640 |360 |30 |Alta |
| 400 |480 |270 |30 |Alta |
| 200 |320 |180 |30 |Alta |

> [!NOTE]
> Se você precisar personalizar a preconfiguração de codificação ao vivo, abra um bilhete de suporte através do Portal Azure. Você deve especificar a tabela desejada da resolução e das taxas de bits. Verifique se há apenas uma camada em 1080p e no máximo 6 camadas. Também especifique que você está solicitando uma predefinida para um codificador ao vivo Premium1080p.
> Os valores específicos das taxas de bits e resoluções podem ser ajustados ao longo do tempo.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Fluxo de áudio de saída para Default720p e Default1080p

Para as predefinições *Default720p* e *Default1080p,* o áudio é codificado para estéreo AAC-LC a 128 kbps. A taxa de amostragem segue a da faixa de áudio na alimentação de contribuição.

## <a name="implicit-properties-of-the-live-encoder"></a>Propriedades implícitas do codificador vivo

A seção anterior descreve as propriedades do codificador vivo que podem ser controladas explicitamente, através da preset - como o número de camadas, resoluções e bitrates. Esta seção esclarece as propriedades implícitas.

### <a name="group-of-pictures-gop-duration"></a>Duração do grupo de imagens (GOP)

O codificador vivo segue a estrutura [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) do feed de contribuição - o que significa que as camadas de saída terão a mesma duração de GOP. Portanto, recomenda-se configurar o codificador local para produzir uma alimentação de contribuição que tenha duração fixa de GOP (tipicamente 2 segundos). Isso garantirá que os fluxos HLS e MPEG DASH de saída do serviço também tenha durações fixas de GOP. Pequenas variações nas durações do GOP provavelmente serão toleradas pela maioria dos dispositivos.

### <a name="frame-rate"></a>Taxa de quadros

O codificador ao vivo também segue as durações dos quadros de vídeo individuais no feed de contribuição - o que significa que as camadas de saída terão quadros com as mesmas durações. Portanto, recomenda-se configurar o codificador local para produzir uma alimentação de contribuição que tenha taxa de quadros fixa (no máximo 30 quadros/segundo). Isso garantirá que os fluxos HLS e MPEG DASH de saída do serviço também tenha durações de taxas de quadro sumidas. Pequenas variações nas taxas de quadros podem ser toleradas pela maioria dos dispositivos, mas não há garantia de que o codificador vivo produzirá uma saída que será reproduzida corretamente. Seu codificador ao vivo no local não deve estar soltando quadros (por exemplo. em condições de bateria fraca) ou variando a taxa de quadros de qualquer forma.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Resolução de camadas de alimentação e saída de contribuição

O codificador vivo é configurado para evitar a conversão da alimentação de contribuição. Como resultado, a resolução máxima das camadas de saída não excederá a da alimentação de contribuição.

Por exemplo, se você enviar um feed de contribuição a 720p para um Evento Vivo configurado para codificação ao vivo Default1080p, a saída terá apenas 5 camadas, começando com 720p a 3Mbps, descendo para 1080p a 200 kbps. Ou se você enviar um feed de contribuição a 360p em um Evento Ao Vivo configurado para codificação de vida padrão, a saída conterá 3 camadas (em resoluções de 288p, 216p e 192p). No caso degenerado, se você enviar uma transmissão de contribuição de, digamos, 160x90 pixels para um codificador vivo Padrão, a saída conterá uma camada na resolução 160x90 no mesmo bitrate que a do feed de contribuição.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Bitrate de camadas de alimentação e saída de contribuição

O codificador vivo é configurado para honrar as configurações de bitrate na predefinição, independentemente da taxa de bits da alimentação de contribuição. Como resultado, a taxa de bits das camadas de saída pode exceder a da alimentação de contribuição. Por exemplo, se você enviar um feed de contribuição em uma resolução de 720p a 1 Mbps, as camadas de saída permanecerão as mesmas da [tabela](live-event-types-comparison.md#output-video-streams-for-default720p) acima.

## <a name="next-steps"></a>Próximas etapas

[Visão geral da transmissão ao vivo](live-streaming-overview.md)
