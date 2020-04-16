---
title: Transcrição da conversa (Pré-visualização) - Serviço de fala
titleSuffix: Azure Cognitive Services
description: A Transcrição da Conversa é uma solução para reuniões, que combina reconhecimento, ID do alto-falante e diarização para fornecer transcrição de qualquer conversa.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: trbye
ms.openlocfilehash: dcc7721aec067c4de309e3fdd926245a9d240f0d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402507"
---
# <a name="what-is-conversation-transcription-in-meetings-preview"></a>O que é Transcrição de Conversação em reuniões (Preview)?

Transcription de conversação é uma solução [de fala para texto](speech-to-text.md) que combina reconhecimento de fala, identificação de oradores e atribuição de sentença a cada orador (também conhecido como _diarização_) para fornecer transcrição em tempo real e/ou assíncrona de qualquer conversa. A Transcrição da Conversa distingue os palestrantes em uma conversa para determinar quem disse o que e quando, e torna mais fácil para os desenvolvedores adicionar fala-a-texto aos seus aplicativos que realizam a diarização de vários alto-falantes.

## <a name="key-features"></a>Principais recursos

- **Carimbos de** tempo - cada enunciado do alto-falante tem um carimbo de data e hora, de modo que você pode facilmente encontrar quando uma frase foi dita.
- **Transcrições legíveis** - as transcrições têm formatação e pontuação adicionadas automaticamente para garantir que o texto corresponda de perto ao que estava sendo dito.
- **Perfis de usuário** - os perfis de usuários são gerados coletando amostras de voz do usuário e enviando-as para a geração de assinaturas.
- **Identificação dos alto-falantes** - os alto-falantes são identificados usando perfis de usuário e um _identificador de alto-falante_ é atribuído a cada um.
- **Diarização multi-alto-falante** - determine quem disse o que ao sintetizar o fluxo de áudio com cada identificador de alto-falante.
- **Transcrição em tempo real** – fornecer transcrições ao vivo de quem está dizendo o que e quando enquanto a conversa está acontecendo.
- **transcrição assíncrona** – forneça transcrições com maior precisão usando um fluxo de áudio multicanal.

> [!NOTE]
> Embora a Transcrição da Conversa não coloque um limite no número de alto-falantes na sala, ela é otimizada para 2-10 alto-falantes por sessão.

## <a name="use-cases"></a>Casos de uso

### <a name="inclusive-meetings"></a>Reuniões inclusivas

Para tornar os encontros inclusivos para todos, como os participantes surdos e com dificuldade de audição, é importante ter transcrição em tempo real. Transcrição da conversa em tempo real leva áudio de reunião e determina quem está dizendo o quê, permitindo que todos os participantes da reunião sigam a transcrição e participem da reunião sem demora.

### <a name="improved-efficiency"></a>Maior eficiência

Os participantes da reunião podem se concentrar na reunião e deixar a nota para transcrição de conversação. Os participantes podem participar ativamente da reunião e acompanhar rapidamente os próximos passos, usando a transcrição em vez de tomar notas e potencialmente perder algo durante a reunião.

## <a name="how-it-works"></a>Como ele funciona

Esta é uma visão geral de alto nível de como a Transcrição de Conversas funciona.

![O diagrama de transcrição da conversa de importação](media/scenarios/conversation-transcription-service.png)

## <a name="expected-inputs"></a>Entradas esperadas

- **Fluxo de áudio multicanal** – Para obter detalhes de especificação e design, consulte [o microfone SDK do Microsoft Speech Device](https://aka.ms/cts/microphone). Para saber mais ou comprar um kit de desenvolvimento, consulte [Obter O Microsoft Speech Device SDK](https://aka.ms/cts/getsdk).
- **Amostras de voz do usuário** – A transcrição da conversa precisa de perfis de usuários antes da conversa. Você precisará coletar gravações de áudio de cada usuário e, em seguida, enviar as gravações para o [Serviço de Geração de Assinaturas](https://aka.ms/cts/signaturegenservice) para validar o áudio e gerar perfis de usuário.

## <a name="real-time-vs-asynchronous"></a>Tempo real vs. assíncrono

A transcrição da conversa oferece três modos de transcrição:

### <a name="real-time"></a>Tempo real

Os dados de áudio são processados ao vivo para retornar o identificador do alto-falante + transcrição. Selecione este modo se o requisito da solução de transcrição for fornecer aos participantes da conversa uma exibição ao vivo de sua conversa em andamento. Por exemplo, construir um aplicativo para tornar as reuniões mais acessíveis aos participantes surdos e deficientes auditivos é um caso ideal de uso para transcrição em tempo real.

### <a name="asynchronous"></a>Assíncrona

Os dados de áudio são processados em lote para retornar o identificador do alto-falante e a transcrição. Selecione este modo se o requisito da solução de transcrição for fornecer maior precisão sem exibição de transcrição ao vivo. Por exemplo, se você quiser construir um aplicativo para permitir que os participantes da reunião se atualizem facilmente em reuniões perdidas, use o modo de transcrição assíncrona para obter resultados de transcrição de alta precisão.

### <a name="real-time-plus-asynchronous"></a>Em tempo real mais assíncrono

Os dados de áudio são processados ao vivo para retornar o identificador do alto-falante + transcrição, e, além disso, uma solicitação é criada para obter também uma transcrição de alta precisão através de processamento assíncrono. Selecione este modo se o aplicativo tiver necessidade de transcrição em tempo real, mas também requer uma transcrição de precisão mais alta para uso após a conversa ou reunião ocorrer.

## <a name="language-support"></a>Suporte ao idioma

Atualmente, a Transcrição da Conversa ção suporta "en-US" e "zh-CN" nas seguintes regiões: *centrale*  *eastasia*. Se você precisar de suporte local adicional, entre em contato com a [equipe de recursos de transcrição de conversação](mailto:CTSFeatureCrew@microsoft.com).

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Transcreva conversas em tempo real](how-to-use-conversation-transcription-service.md)
