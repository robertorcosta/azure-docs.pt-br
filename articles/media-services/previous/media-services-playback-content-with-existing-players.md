---
title: Usar players existentes para reproduzir seu conteúdo - Azure | Microsoft Docs
description: Este artigo lista os players existentes que você pode usar para reproduzir o conteúdo.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2019
ms.author: juliako
ms.openlocfilehash: d2036fa056932f9bbd2f14b4005fa54321b1a382
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93095706"
---
# <a name="playing-your-content-with-existing-players"></a>Reprodução de seu conteúdo com players existentes

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Os serviços de mídia do Microsoft Azure suporta muitos formatos populares de streaming, como Smooth Streaming, HTTP Live Streaming e MPEG-Dash. Este tópico aponta para players existentes que você pode usar para testar os fluxos.

### <a name="the-azure-portal-media-services-content-player"></a>Player de conteúdo dos Serviços de Mídia do Portal do Azure
O portal **do Azure** fornece um player de conteúdo que você pode usar para testar seu vídeo.

Clique no vídeo desejado (verifique se ele foi [publicado](media-services-portal-publish.md)) e clique no botão **Reproduzir** na parte inferior do portal.

Algumas considerações se aplicam:

* O **PLAYER DE CONTEÚDO DOS SERVIÇOS DE MÍDIA** é reproduzido por meio do ponto de extremidade de streaming padrão. Se você deseja reproduzir por meio de um ponto de extremidade de streaming não padrão, use outro reprodutor. Por exemplo, [Player de Mídia do Azure](https://aka.ms/azuremediaplayer).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Player de Mídia do Azure

Use o [Player de Mídia do Azure](https://aka.ms/azuremediaplayer) para reproduzir conteúdo (protegido ou não) em qualquer um dos seguintes formatos:

* Smooth Streaming
* MPEG DASH
* HLS
* MP4 progressivo

### <a name="flash-player"></a>Flash Player

#### <a name="playready-with-token"></a>PlayReady com token

[http://reference.dashif.org/dash.js/nightly/samples/dash-if-reference-player/index.html](http://reference.dashif.org/dash.js/nightly/samples/dash-if-reference-player/index.html)

### <a name="dash-players"></a>Players DASH

"https://dashplayer.azurewebsites.net"

[https://dashif.org](https://dashif.org)

### <a name="other"></a>Outro
Para testar as URLs de HLS, você também pode usar:

* **Safari** em um dispositivo iOS ou
* **Player 3ivx HLS** no Windows.

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png
