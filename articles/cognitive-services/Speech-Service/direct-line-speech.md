---
title: Fala direta - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos recursos, recursos e restrições para assistentes de voz usando o Direct Line Speech com o Speech Software Development Kit (SDK).
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 714cdefa7100160a510d1922a2547ae53692d76c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367798"
---
# <a name="what-is-direct-line-speech"></a>O que é o Direct Line Speech?

**Direct Line Speech** é uma solução robusta e completa para criar um assistente de voz flexível e extensível. Ele é alimentado pelo Bot Framework e seu canal Direct Line Speech, que é otimizado para interação por voz e voz com bots.

[Assistentes de voz](voice-assistants.md) ouvem os usuários e tomam uma ação em resposta, muitas vezes falando de volta. Eles usam [a fala-para-texto](speech-to-text.md) para transcrever a fala do usuário e, em seguida, tomar medidas sobre a compreensão da linguagem natural do texto. Esta ação frequentemente inclui saída falada do assistente gerado com [texto-para-fala](text-to-speech.md).

O Direct Line Speech oferece os mais altos níveis de personalização e sofisticação para assistentes de voz. Ele foi projetado para cenários de conversação que são abertos, naturais ou híbridos dos dois com conclusão de tarefas ou uso de comando e controle. Esse alto grau de flexibilidade vem com uma maior complexidade, e cenários que são escopo para tarefas bem definidas usando entrada de linguagem natural podem querer considerar [Comandos Personalizados (Preview)](custom-commands.md) para uma experiência de solução simplificada.

## <a name="getting-started-with-direct-line-speech"></a>Começando com direct line speech

Os primeiros passos para criar um assistente de voz usando o Direct Line Speech são [obter uma chave de assinatura de voz,](get-started.md)criar um novo bot associado a essa assinatura e conectar o bot ao canal Direct Line Speech.

   ![Diagrama conceitual do fluxo de serviço de orquestração direct line speech](media/voice-assistants/overview-directlinespeech.png "O fluxo do Canal de Fala")

Para obter um guia completo e passo a passo sobre a criação de um assistente de voz simples usando o Direct Line Speech, consulte [o tutorial para habilitar a voz do seu bot com o Speech SDK e o canal Direct Line Speech](tutorial-voice-enable-your-bot-speech-sdk.md).

Também oferecemos partidas rápidas projetadas para que você execute código e aprenda as APIs rapidamente. Esta tabela inclui uma lista de assistentes de voz quickstarts organizados por linguagem e plataforma.

| Guia de Início Rápido | Plataforma | Reference API |
|------------|----------|---------------|
| C#, UWP | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Procurar](https://aka.ms/csspeech/javaref) |
| Java | Android | [Procurar](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Código de exemplo

O código de exemplo para a criação de um assistente de voz está disponível no GitHub. Essas amostras cobrem o aplicativo cliente para se conectar ao seu assistente em várias linguagens de programação populares.

* [Exemplos de assistente de voz (SDK)](https://aka.ms/csspeech/samples)
* [Tutorial: Habilitar a voz do seu assistente com o Speech SDK,C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="customization"></a>Personalização

Assistentes de voz construídos usando o serviço Speech podem usar toda a gama de opções de personalização disponíveis para [a seleção de palavras-para-texto,](speech-to-text.md) [texto-para-voz](text-to-speech.md)e [seleção personalizada de palavras-chave.](speech-devices-sdk-create-kws.md)

> [!NOTE]
> As opções de personalização variam de acordo com o idioma/local (ver [idiomas suportados](supported-languages.md)).

O Direct Line Speech e sua funcionalidade associada para assistentes de voz são um suplemento ideal para a [Solução Assistente Virtual e o Modelo Corporativo](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview). Embora o Direct Line Speech possa funcionar com qualquer bot compatível, esses recursos fornecem uma linha de base reutilizável para experiências conversacionais de alta qualidade, bem como habilidades e modelos comuns de suporte para começar rapidamente.

## <a name="reference-docs"></a>Documentos de Referência

* [SDK de fala](speech-sdk-reference.md)
* [Serviço azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Próximas etapas

* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](get-started.md)
* [Obter o SDK de Fala](speech-sdk.md)
* [Criar e implantar um bot básico](https://docs.microsoft.com/azure/bot-service/bot-builder-tutorial-basic-deploy?view=azure-bot-service-4.0)
* [Obtenha a solução assistente virtual e o modelo corporativo](https://github.com/Microsoft/AI)
