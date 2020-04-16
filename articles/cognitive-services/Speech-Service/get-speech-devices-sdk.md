---
title: Obter SDK dos dispositivos de fala
titleSuffix: Azure Cognitive Services
description: O serviço Speech funciona com uma grande variedade de dispositivos e fontes de áudio. Agora, você pode levar os aplicativos de fala para o próximo nível com hardware e software correspondentes. Neste artigo, você aprenderá como obter acesso ao Speech Devices SDK e começar a desenvolver.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: 756ec976c4643c1cd80552b7fa552b70fd1f5d24
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81391263"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Obter o SDK de Dispositivos de Fala dos Serviços Cognitivos

O Speech Devices SDK é uma biblioteca pré-sintonizada projetada para trabalhar com kits de desenvolvimento construídos com propósito e diferentes configurações de matriz de microfone.

## <a name="choose-a-development-kit"></a>Escolha um kit de desenvolvimento

|Dispositivos|Especificação|Descrição|Cenários|
|--|--|--|--|
|[URbetter T11 Development Board](https://www.alibaba.com/product-detail/URBETTER-Dev-Kits-for-Microsoft-Speech_62358746402.html)![URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 Mic Array, ARM SOC, WIFI, Ethernet, HDMI, CÂMERA USB. <br>Linux|Um SDK de speech devices de nível do setor que adapta o array do Microsoft Mic e suporta I/O estendido, como HDMI/Ethernet e mais periféricos USB|Transcrição da conversação, educação, hospital, robôs, caixa OTT, agente de voz, Drive Thru|
|[Roobo Smart Audio Dev Kit](https://ddk.roobo.com)<br>[Configurar](speech-devices-sdk-roobo-v1.md)[Quickstart](speech-devices-sdk-android-quickstart.md)![Roobo Smart Audio Dev Kit / ](media/speech-devices-sdk/device-roobo-v1.jpg)|7 Conjunto de microfones, ARM SOC, WIFI, audio out, IO. <br>[Android](speech-devices-sdk-android-quickstart.md)|O primeiro Speech Devices SDK para adaptar o Microsoft Mic Array e o SDK de processamento frontal, para desenvolver cenários de transcrição e fala de alta qualidade|Transcrição da conversa, alto-falante inteligente, agente de voz, wearable|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Configuração](https://docs.microsoft.com/azure/Kinect-dk/set-up-azure-kinect-dk) / [Quickstart](speech-devices-sdk-windows-quickstart.md)![Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 Câmeras Mic Array RGB e Depth. <br>[Windows](speech-devices-sdk-windows-quickstart.md)/[Linux](speech-devices-sdk-linux-quickstart.md)|Um kit de desenvolvedores com sensores avançados de inteligência artificial (IA) para a construção de sofisticados modelos de visão computacional e de fala. Ele combina uma melhor matriz de microfone espacial e câmera de profundidade com uma câmera de vídeo e um sensor de orientação — tudo em um pequeno dispositivo com vários modos, opções e SDKs para acomodar uma gama de tipos de computação.|Transcrição de conversação, robótica, edifício inteligente|
|Roobo Smart Audio Dev Kit 2<br>[Instalação](speech-devices-sdk-roobo-v2.md)<br>![Roobo Smart Audio Dev Kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 Conjunto de microfones, ARM SOC, WIFI, Bluetooth, IO. <br>Linux|O SDK de 2ª geração de dispositivos de fala que fornece sO alternativo e mais recursos em um design de referência econômico.|Transcrição da conversa, alto-falante inteligente, agente de voz, wearable|


## <a name="download-the-speech-devices-sdk"></a>Fazer download do SDK de Dispositivos de Fala

Baixe o [Speech Devices SDK](https://aka.ms/sdsdk-download).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Introdução ao SDK de Dispositivos de Fala](https://aka.ms/sdsdk-quickstart)
