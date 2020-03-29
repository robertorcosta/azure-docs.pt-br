---
title: Nuvens Soberanas - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Aprenda a usar nuvens soberanas
services: cognitive-services
author: cbasoglu
manager: xdh
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 1/14/2020
ms.author: cbasoglu
ms.openlocfilehash: b41967033b00144ca5bd52ce23cf8aabcea6749e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "78228089"
---
# <a name="speech-services-with-sovereign-clouds"></a>Serviços de fala com nuvens soberanas

## <a name="azure-government-united-states"></a>Governo azure (Estados Unidos)

Apenas os governos federal, estadual, local e tribal dos EUA e seus parceiros têm acesso a esta instância dedicada com operações controladas por cidadãos americanos selecionados.
- Regiões: Us Gov Virginia
- SR em SpeechSDK:*config. FromHost ("wss://virginia.stt.speech.azure.us",\<"\>your-key ");*
- TTS em SpeechSDK: *config. FromHost[]()("https://virginia.tts.speech.azure.us",\<"\>sua chave ");*
- Tokens de autenticação: https://virginia.api.cognitive.microsoft.us/sts/v1.0/issueToken[]()
- Portal do Azure: https://portal.azure.us  
- Portal de fala personalizado:https://virginia.cris.azure.us/Home/CustomSpeech
- Skus disponível: S0
- Recursos compatíveis:
  - Conversão de fala em texto
  - Fala Personalizada (adaptação acústica/idioma)
  - Conversão de texto em fala
  - Tradutor de Fala
- Recursos sem suporte
  - Voz personalizada
  - Vozes neurais para texto-para-fala
- Locais suportados: Locais para os seguintes idiomas são suportados.
  - Árabe (ar-*)
  - Chinês (zh-*)
  - Inglês (en-*)
  - Francês (fr-*)
  - Alemão (de-*)
  - Híndi
  - Coreano
  - Russo
  - Espanhol (es-*)

## <a name="microsoft-azure-china"></a>Microsoft Azure China

Localizado na China, um data center do Azure com acesso direto à China Mobile, China Telecom, China Unicom e outras grandes operadoras de rede backbone, para os usuários chineses fornecerem experiência de acesso à rede local de alta velocidade e estável.
- Regiões: China East 2 (Xangai)
- SR em SpeechSDK: *config. FromHost ("wss://chinaeast2.stt.speech.azure.cn",\<"\>your-key ");*
- TTS em SpeechSDK: *config. FromHost[]()("https://chinaeast2.tts.speech.azure.cn",\<"\>sua chave ");*
- Tokens de autenticação: https://chinaeast2.api.cognitive.azure.cn/sts/v1.0/issueToken[]()
- Portal do Azure: https://portal.azure.cn
- Portal de fala personalizado:https://speech.azure.cn/CustomSpeech
- Skus disponível: S0
- Recursos compatíveis:
  - Conversão de fala em texto
  - Fala Personalizada (adaptação acústica/idioma)
  - Conversão de texto em fala
  - Tradutor de Fala
- Recursos sem suporte
  - Voz personalizada
  - Vozes neurais para texto-para-fala
- Locais suportados: Locais para os seguintes idiomas são suportados.
  - Árabe (ar-*)
  - Chinês (zh-*)
  - Inglês (en-*)
  - Francês (fr-*)
  - Alemão (de-*)
  - Híndi
  - Coreano
  - Russo
  - Espanhol (es-*)

