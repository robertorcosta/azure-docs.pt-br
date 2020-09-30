---
title: Cenários de preços para chamada (voz/vídeo) e chat
titleSuffix: An Azure Communication Services concept document
description: Saiba mais sobre o modelo de preços dos Serviços de Comunicação.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0b82d7dc8f6aaaa28a5293966440f058fbf42dc1
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91460927"
---
# <a name="pricing-scenarios"></a>Cenários de preços

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Looks like other resources point to a /pricing page that is managed by Commerce or Marketing? https://azure.microsoft.com/pricing/details/functions/ Should we? FOLLOWING UP WITH KRISTIN TO FIND THE RIGHT ACS PAGE
-->

Os preços dos Serviços de Comunicação do Azure são baseados em um modelo pago conforme o uso sem valores antecipados. Você será cobrado apenas pelo consumo e pelo uso dos serviços.

## <a name="voicevideo-calling-and-screen-sharing"></a>Chamada de voz/vídeo e compartilhamento de tela

Os Serviços de Comunicação do Azure permitem adicionar chamadas de voz/vídeo e compartilhamento de tela aos seus aplicativos. Você pode inserir a experiência em seus aplicativos usando as bibliotecas de clientes JavaScript, Objective-C (Apple), Java (Android) ou .NET. Confira nossa [lista completa de bibliotecas de clientes disponíveis](./sdk-options.md).

### <a name="pricing"></a>Preços

Os serviços de chamada e de compartilhamento de tela são cobrados por minuto por participante a US$ 0,004 por participante por minuto para chamadas de grupo. Para entender os vários fluxos de chamada possíveis, confira [esta página](./call-flows.md).

Cada participante da chamada contará na cobrança de cada minuto em que estiver conectado à chamada. Isso se aplica independentemente de o usuário estar realizando uma chamada de vídeo, uma chamada de voz ou um compartilhamento de tela.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>Exemplo de preço: agrupar chamada de áudio/vídeo usando bibliotecas de clientes JS e iOS

Alice fez uma chamada em grupo com seus colegas, Bob e Charlie. Alice e Bob usaram as bibliotecas de clientes JS; Charlie usou as bibliotecas de clientes do iOS.

- A chamada dura um total de 60 minutos.
- Alice e Bob participaram de toda a chamada. Alice ativou o vídeo por cinco minutos e compartilhou a tela por 23 minutos. Bob ficou com o vídeo ativado durante toda a chamada (60 minutos) e compartilhou a tela por 12 minutos.
- Charlie saiu da chamada após 43 minutos. Charlie usou áudio e vídeo durante todo o tempo em que participou (43 minutos).

**Cálculos de custo**

- 2 participantes x 60 minutos x US$ 0,004 por participante por minuto = US$ 0,48 [vídeo e áudio são cobrados à mesma taxa]
- 1 participante x 43 minutos x US$ 0,004 por participante por minuto = US$ 0,172 [vídeo e áudio são cobrados à mesma taxa]

**Custo total para a chamada em grupo**: US$ 0,48 + US$ 0,172 = US$ 0,652

## <a name="chat"></a>Chat

Com os Serviços de Comunicação, você pode aprimorar seu aplicativo com a capacidade de enviar e receber mensagens de chat entre dois ou mais usuários. As bibliotecas de clientes de chat estão disponíveis para JavaScript, .NET, Python e Java. Confira [esta página para saber mais sobre bibliotecas de cliente](./sdk-options.md)

### <a name="price"></a>Preço

- Você será cobrado US$ 0,0008 para cada mensagem de bate-papo enviada.

### <a name="pricing-example-chat-between-two-users"></a>Exemplo de preço: chat entre dois usuários 

Geeta inicia uma conversa de chat com Emily para compartilhar uma atualização e envia cinco mensagens. O chat dura 10 minutos, em que Geeta e Emily enviam outras 15 mensagens cada.

**Cálculos de custo** 
- Número de mensagens enviadas (5 + 15 + 15) x US$ 0,0008 = US$ 0,028

### <a name="pricing-example-group-chat-with-multiple-users"></a>Exemplo de preço: chat em grupo com vários usuários 

Charlie inicia uma conversa de chat com seus amigos Casey e Jasmine para planejar as férias. Eles conversam por um tempo, em que Charlie, Casey e Jasmine enviam 20, 30 e 18 mensagens, respectivamente. Eles percebem que uma amiga deles, Rosa, pode estar interessada em participar da viagem também, então a adicionam à conversa de chat e compartilham todo o histórico de mensagens com ela. 

Rosa vê as mensagens e começa a conversar. Enquanto isso, Casey recebe uma chamada e decide acompanhar a conversa mais tarde. Charlie, Jasmine e Rosa decidem as datas da viagem e enviam outras 30, 25 e 35 mensagens, respectivamente.

**Cálculos de custo** 

- Número de mensagens enviadas (20 + 30 + 18 + 30 + 25 + 35) x US$ 0,0008 = US$ 0,1264
