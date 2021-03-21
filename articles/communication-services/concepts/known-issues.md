---
title: Serviços de comunicação do Azure-perguntas frequentes/problemas conhecidos
description: Saiba mais sobre os serviços de comunicação do Azure
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: 2c6ac34d8daf00578cb1d03833a28eb8535708b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/20/2021
ms.locfileid: "103493634"
---
# <a name="faq--known-issues"></a>Perguntas frequentes/problemas conhecidos
Este artigo fornece informações sobre problemas conhecidos e perguntas frequentes relacionadas aos serviços de comunicação do Azure.

## <a name="faq"></a>Perguntas frequentes

### <a name="why-is-the-quality-of-my-video-degraded"></a>Por que a qualidade do meu vídeo está degradada?

A qualidade dos fluxos de vídeo é determinada pelo tamanho do renderizador do lado do cliente que foi usado para iniciar esse fluxo. Ao assinar um fluxo remoto, um receptor determinará sua própria resolução com base nas dimensões do processador do lado do cliente do remetente.

### <a name="why-is-it-not-possible-to-enumerateselect-micspeaker-devices-on-safari"></a>Por que não é possível enumerar/selecionar dispositivos de Mic/viva-voz no Safari?

Os aplicativos não podem enumerar/selecionar dispositivos MIC/viva-voz (como Bluetooth) no Safari iOS/iPad. É uma limitação do sistema operacional-há sempre apenas um dispositivo.

Para o Safari no MacOS-o aplicativo não pode enumerar/selecionar o palestrante por meio dos serviços de comunicação Gerenciador de Dispositivos-eles precisam ser selecionados por meio do sistema operacional. Se você usar o Chrome no MacOS, o aplicativo poderá enumerar/selecionar dispositivos por meio do Gerenciador de Dispositivos de serviços de comunicação.

## <a name="known-issues"></a>Problemas conhecidos

Esta seção fornece informações sobre problemas conhecidos associados aos serviços de comunicação do Azure.

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Alternar repetidamente dispositivos de vídeo pode fazer com que o streaming de vídeo pare temporariamente

Alternar entre dispositivos de vídeo pode fazer com que o fluxo de vídeo seja pausado enquanto o fluxo é adquirido do dispositivo selecionado.

#### <a name="possible-causes"></a>Possíveis causas
A transmissão e a alternância entre os dispositivos de mídia são computacionalmente intensivas. Alternar com frequência pode causar degradação do desempenho. Os desenvolvedores são incentivados a interromper um fluxo de dispositivo antes de iniciar outro.
