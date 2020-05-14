---
title: Palavras-chave personalizadas-serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos recursos, funcionalidades e restrições para palavras-chave personalizadas usando o kit de desenvolvimento de software de fala (SDK).
services: cognitive-services
author: hasyashah
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: hasshah
ms.openlocfilehash: 0a6df626f9567b51a8371c17004b454f9c5b4597
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202040"
---
# <a name="what-is-a-keyword"></a>O que é uma palavra-chave?

Uma palavra-chave é uma palavra ou frase curta que permite que seu produto seja ativado por voz. Por exemplo, "Ei Cortana" é a palavra-chave do assistente da Cortana. A ativação por voz permite que os usuários comecem a interagir com seu produto totalmente livre, simplesmente falando com a palavra-chave. À medida que o seu produto escuta a palavra-chave continuamente, todo o áudio é processado localmente no dispositivo do usuário até que ocorra uma detecção para garantir que seus dados permaneçam os mais privados possíveis.

## <a name="core-features-of-custom-keyword"></a>Principais recursos da palavra-chave personalizada

Com a personalização, o desempenho e os recursos de integração da palavra-chave personalizada, você pode personalizar a ativação de voz para melhor atender às necessidades dos usuários e da visão do seu produto.

| Recurso | Descrição |
|----------|----------|
| Personalização de palavra-chave | Como uma extensão de sua marca, uma palavra-chave reforça o patrimônio que você criou com seus clientes. O portal de palavra-chave personalizado no Speech Studio permite que você especifique qualquer palavra ou frase curta que melhor represente sua marca. Você pode personalizar ainda mais sua palavra-chave escolhendo as pronúncias certas, que serão respeitadas pelo modelo de palavra-chave gerado.
| Verificação de palavra-chave | Quando há alta confiança na palavra-chave que está sendo detectada localmente, o áudio é enviado para a nuvem para verificação adicional de que um usuário disse a palavra-chave. A verificação de palavra-chave fornece uma camada adicional de segurança, reduzindo o impacto de uma detecção local incorreta e protegendo a privacidade do usuário.
| Assistente de voz & integração do SDK de fala | Palavras-chave geradas com base na palavra-chaves personalizada no Speech Studio podem ser facilmente integradas em seu dispositivo ou aplicativo por meio do SDK de fala. Basta apontar o SDK para o modelo de palavra-chave fornecido pelo Speech Studio e seu produto será ativado por voz, apoiado pela verificação de palavra-chave. Você pode concluir as experiências de voz do seu produto criando seu próprio [Assistente de voz](voice-assistants.md).

## <a name="get-started-with-custom-keywords"></a>Introdução às palavras-chave personalizadas

* Tutorial: como [criar uma palavra-chave personalizada usando o Speech Studio](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws)
* Tutorial: como [Ativar voz para seu produto com o SDK de fala, usando C#](tutorial-voice-enable-your-bot-speech-sdk.md)
* Início rápido: [reconhecer palavras-chave com o SDK de fala, em plataforma universal do Windows usando C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/uwp/keyword-recognizer)
* Início rápido: [reconhecer palavras-chave com o SDK de fala, no Android usando Java](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/android/keyword-recognizer)

## <a name="next-steps"></a>Próximas etapas

* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](get-started.md)
* [Obter o SDK de Fala](speech-sdk.md)
* [Saiba mais sobre assistentes de voz](voice-assistants.md)
