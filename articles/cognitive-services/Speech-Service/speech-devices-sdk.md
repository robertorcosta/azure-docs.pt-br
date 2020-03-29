---
title: Dispositivos de fala SDK - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Introdução ao SDK de Dispositivos de Fala. O serviço Speech funciona com uma grande variedade de dispositivos e fontes de áudio. O SDK de Dispositivos de Fala é uma biblioteca pré-ajustada emparelhada com kits de desenvolvimento de matriz de microfone criados para um fim específico.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: add0d27ae6a612fed0320b5329d19236b7bbbd01
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370637"
---
# <a name="what-is-the-speech-devices-sdk"></a>O que é o Speech Devices SDK?

O [serviço Speech](overview.md) funciona com uma grande variedade de dispositivos e fontes de áudio. Agora, você pode levar os aplicativos de fala para o próximo nível com hardware e software correspondentes. O SDK de Dispositivos de Fala é uma biblioteca pré-ajustada emparelhada com kits de desenvolvimento de matriz de microfone criados para um fim específico.

O SDK de dispositivos de fala pode ajudá-lo:

- Teste rapidamente novos cenários de voz.
- Mais fácil integre o serviço de fala baseado em nuvem em seu dispositivo.
- Crie uma experiência de usuário excepcional para seus clientes.

O SDK de dispositivos de fala consome os [Speech SDK](speech-sdk.md). Usando nossos algoritmos avançados de processamento de áudio com o conjunto de microfones do dispositivo para enviar o áudio para o [serviço Speech](overview.md). Ele fornece reconhecimento preciso de [fala](speech-to-text.md) de campo distante através de supressão de ruído, cancelamento de eco, formação de feixes e desreverberação.

Você também pode usar o Speech Devices SDK para construir dispositivos ambientais que tenham sua própria [palavra-chave personalizada](speech-devices-sdk-create-kws.md). Uma palavra-chave personalizada fornece uma sugestão que inicia uma interação do usuário que é exclusiva da sua marca.

O Speech Devices SDK permite uma variedade de cenários habilitados para voz, como assistentes de [voz,](https://aka.ms/bots/speech/va)sistemas de pedidos drive-thru, [transcrição de conversas](conversation-transcription-service.md)e alto-falantes inteligentes. Você pode responder aos usuários com texto, falar com eles com uma [voz padrão](how-to-customize-voice-font.md) ou personalizada, apresentar resultados de pesquisa, [traduzir](speech-translation.md) para outros idiomas e muito mais. Estamos ansiosos para ver suas criações.

## <a name="get-the-speech-devices-sdk"></a>Obter SDK dos dispositivos de fala

### <a name="android"></a>Android

Para Android, os dispositivos baixam a versão mais recente do [Android Speech Devices SDK](https://aka.ms/sdsdk-download-android).

### <a name="windows"></a>Windows

Para windows, o aplicativo de exemplo é fornecido como um aplicativo Java multiplataforma. Baixe a versão mais recente do [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
O aplicativo é compilado com o pacote do SDK de Fala e o Java IDE do Eclipse (v4) no Windows de 64 bits. Ele é executado em um JRE (Java Runtime Environment) 8 de 64 bits.

### <a name="linux"></a>Linux

Para linux, o aplicativo de exemplo é fornecido como um aplicativo Java multiplataforma. Baixe a versão mais recente do [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
O aplicativo é construído com o pacote Speech SDK, e o Eclipse Java IDE (v4) em Linux de 64 bits (Ubuntu 16.04, Ubuntu 18.04, Debian 9, RHEL 8, CentOS 8). Ele é executado em um JRE (Java Runtime Environment) 8 de 64 bits.

Binários adicionais são fornecidos para suportar dispositivos próximos, [Roobo v2 DDK,](https://aka.ms/sdsdk-download-roobov2) [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter)e [alto-falante GGEC](https://aka.ms/sdsdk-download-speaker).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Escolha seu dispositivo de fala](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](get-started.md)
