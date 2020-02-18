---
title: Nuvens soberanas-serviço de fala
titleSuffix: Azure Cognitive Services
description: Saiba como usar nuvens soberanas
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: 00617fc09f471eaf3dc13a5aa691c4aab2e1e2ec
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/18/2020
ms.locfileid: "77424908"
---
# <a name="speech-services-with-sovereign-clouds"></a>Serviços de fala com nuvens soberanass

## <a name="azure-government-united-states"></a>Azure governamental (Estados Unidos)

Somente os governos federal, estadual, local e tribal dos EUA e seus parceiros têm acesso a essa instância dedicada com operações controladas por cidadãos selecionados dos EUA.
- Regiões: Gov. dos EUA – Virgínia
- SR em SpeechSDK:*config. FromHost ("WSS://Virginia.STT.Speech.Azure.us", "\<your-key\>");*
- TTS em SpeechSDK: *config. FromHost ("https[]()://Virginia.TTS.Speech.Azure.us", "\<your-key\>");*
- Tokens de autenticação[](): https://Virginia.API.cognitive.Microsoft.us/STS/v1.0/issueToken
- Portal do Azure: https://portal.azure.us  
- Portal de Fala Personalizada: https://virginia.cris.azure.us/Home/CustomSpeech
- SKUs disponíveis: S0
- Recursos compatíveis:
  - Conversão de fala em texto
  - Fala Personalizada (adaptação de linguagem/acústica)
  - Conversão de Texto em Fala
  - Conversor de fala
- Recursos sem suporte
  - Voz personalizada
  - Vozes neurais para conversão de texto em fala
- Localidades com suporte: Há suporte para localidades para os idiomas a seguir.
  - Árabe (ar-*)
  - Chinês (ZH-*)
  - Inglês (EN-*)
  - Francês (FR-*)
  - Alemão (de-*)
  - Híndi
  - Coreano
  - Russo
  - Espanhol (es-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure China

Localizada na China, uma data center do Azure com acesso direto à China Mobile, na China Telecom, na China Unicom e outra rede principal de backbone de carros, para que os usuários do chinês forneçam uma experiência de acesso à rede local estável e de alta velocidade.
- Regiões: Leste da China 2 (Xangai)
- SR em SpeechSDK: *config. FromHost ("WSS://chinaeast2.STT.Speech.Azure.cn", "\<your-key\>");*
- TTS em SpeechSDK: *config. FromHost ("https[]()://chinaeast2.TTS.Speech.Azure.cn", "\<your-key\>");*
- Tokens de autenticação[](): https://chinaeast2.API.cognitive.Azure.cn/STS/v1.0/issueToken
- Portal do Azure: https://portal.azure.cn
- Portal de Fala Personalizada: https://chinaeast2.cris.azure.cn/Home/CustomSpeech
- SKUs disponíveis: S0
- Recursos compatíveis:
  - Conversão de fala em texto
  - Fala Personalizada (adaptação de linguagem/acústica)
  - Conversão de Texto em Fala
  - Conversor de fala
- Recursos sem suporte
  - Voz personalizada
  - Vozes neurais para conversão de texto em fala
- Localidades com suporte: Há suporte para localidades para os idiomas a seguir.
  - Árabe (ar-*)
  - Chinês (ZH-*)
  - Inglês (EN-*)
  - Francês (FR-*)
  - Alemão (de-*)
  - Híndi
  - Coreano
  - Russo
  - Espanhol (es-*)

