---
title: Assistentes de voz - Serviço de fala
titleSuffix: Azure Cognitive Services
description: Uma visão geral dos recursos, recursos e restrições para assistentes de voz usando o Speech Software Development Kit (SDK).
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: 9219c9a72ce9e1cfba3504b0b8e16ade77f8a5e5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369889"
---
# <a name="what-is-a-voice-assistant"></a>O que é um assistente de voz?

Os assistentes de voz que usam o serviço Speech capacitam os desenvolvedores a criar interfaces de conversação naturais e humanas para seus aplicativos e experiências.

O serviço de assistente de voz fornece interação rápida e confiável entre um dispositivo e uma implementação de assistente que usa (1) o canal Direct Line Speech do Bot Framework ou (2) o serviço integrado de Comandos Personalizados (Preview) para conclusão da tarefa.

Os aplicativos se conectam ao serviço de assistente de voz com o Speech Software Development Kit (SDK).

   ![Diagrama conceitual do fluxo de serviço de orquestração do assistente de voz](media/voice-assistants/overview.png "O fluxo do assistente de voz")

## <a name="choosing-an-assistant-solution"></a>Escolhendo uma solução de assistente

O primeiro passo para criar um assistente de voz é decidir o que ele deve fazer. O serviço Speech fornece soluções múltiplas e complementares para elaborar interações com seus assistentes. Se você quer a flexibilidade e versatilidade que o canal [Direct Line Speech](direct-line-speech.md) do Bot Framework fornece ou a simplicidade de [Comandos Personalizados (Preview)](custom-commands.md) para cenários simples, selecionar as ferramentas certas fará com que você comece.

| Se você quiser... | Então considere... | Por exemplo... |
|-------------------|------------------|----------------|
|Conversa aberta com robusta integração de habilidades e controle total de implantação | O canal direct [line speech](direct-line-speech.md) do Bot Framework | <ul><li>"Eu preciso ir para Seattle"</li><li>"Que tipo de pizza posso pedir?"</li></ul>
|Conversa de comando e controle ou orientada a tarefas com autoria e hospedagem simplificadas | [Comandos personalizados (Visualização)](custom-commands.md) | <ul><li>"Ligue a luz aérea"</li><li>"Torná-lo 5 graus mais quente"</ul>

Recomendamos [o Direct Line Speech](direct-line-speech.md) como a melhor escolha padrão se você ainda não tiver certeza do que gostaria que seu assistente manuseasse. Oferece integração com um rico conjunto de ferramentas e auxílios de autoria, como a [Solução Assistente Virtual e o Modelo Corporativo](https://docs.microsoft.com/azure/bot-service/bot-builder-enterprise-template-overview) e o serviço [QnA Maker](https://docs.microsoft.com/azure/cognitive-services/QnAMaker/Overview/overview) para construir padrões comuns e usar suas fontes de conhecimento existentes.

[Custom Commands (Preview)](custom-commands.md) fornece uma experiência simplificada de autoria e hospedagem especificamente adaptada para cenários de comando e controle de linguagem natural.

   ![Comparação de soluções assistentes](media/voice-assistants/assistant-solution-comparison.png "Comparação de soluções assistentes")

## <a name="core-features"></a>Características principais

Se você escolher [direct line speech](direct-line-speech.md) ou custom [commands (Preview)](custom-commands.md) para criar interações com seu assistente, você pode usar um rico conjunto de recursos de personalização para personalizar seu assistente para sua marca, produto e personalidade.

| Categoria | Recursos |
|----------|----------|
|[palavras-chave Custom](speech-devices-sdk-create-kws.md) | Os usuários podem iniciar conversas com assistentes com uma palavra-chave personalizada como "Hey Contoso". Um aplicativo faz isso com um mecanismo de palavras-chave personalizado no Speech SDK, que pode ser configurado com uma palavra-chave personalizada [que você pode gerar aqui](speech-devices-sdk-create-kws.md). Os assistentes de voz podem usar a verificação de palavras-chave do lado do serviço para melhorar a precisão da ativação da palavra-chave (em comparação apenas com o dispositivo).
|[Fala ao texto](speech-to-text.md) | Assistentes de voz convertem áudio em tempo real em texto reconhecido usando [o serviço Fala-a-texto.](speech-to-text.md) Este texto está disponível, conforme transcrito, tanto para a implementação do seu assistente quanto para o seu aplicativo cliente.
|[Texto em fala](text-to-speech.md) | As respostas texais do seu assistente são sintetizadas usando [texto-para-voz](text-to-speech.md) do serviço Speech. Esta síntese é então disponibilizada para o seu aplicativo cliente como um fluxo de áudio. A Microsoft oferece a capacidade de construir sua própria voz Neural TTS personalizada e de alta qualidade que dá voz à sua marca. Para saber mais, [entre em contato conosco.](mailto:mstts@microsoft.com)

## <a name="getting-started-with-voice-assistants"></a>Começando com assistentes de voz

Oferecemos partidas rápidas projetadas para que você esteja executando o código em menos de 10 minutos. Esta tabela inclui uma lista de assistentes de voz quickstarts, organizada por linguagem.

| Guia de Início Rápido | Plataforma | Reference API |
|------------|----------|---------------|
| C#, UWP | Windows | [Procurar](https://aka.ms/csspeech/csharpref) |
| Java | Windows, macOS, Linux | [Procurar](https://aka.ms/csspeech/javaref) |
| Java | Android | [Procurar](https://aka.ms/csspeech/javaref) |

## <a name="sample-code"></a>Código de exemplo

O código de exemplo para a criação de um assistente de voz está disponível no GitHub. Essas amostras cobrem o aplicativo cliente para se conectar ao seu assistente em várias linguagens de programação populares.

* [Exemplos de assistente de voz (SDK)](https://aka.ms/csspeech/samples)
* [Tutorial: Habilitar a voz do seu assistente com o Speech SDK,C #](tutorial-voice-enable-your-bot-speech-sdk.md)

## <a name="tutorial"></a>Tutorial

Um tutorial sobre como [ativar a voz do seu assistente usando o canal Speech SDK e Direct Line Speech](tutorial-voice-enable-your-bot-speech-sdk.md).

## <a name="customization"></a>Personalização

Os assistentes de voz construídos usando o serviço Speech podem usar toda a gama de opções de personalização disponíveis para [a seleção de palavras-para-texto,](speech-to-text.md) [texto-para-voz](text-to-speech.md)e [seleção personalizada de palavras-chave.](speech-devices-sdk-create-kws.md)

> [!NOTE]
> As opções de personalização variam de acordo com o idioma/local (ver [idiomas suportados](supported-languages.md)).

## <a name="reference-docs"></a>Documentos de Referência

* [SDK de fala](speech-sdk-reference.md)
* [Serviço azure Bot](https://docs.microsoft.com/azure/bot-service/?view=azure-bot-service-4.0)

## <a name="next-steps"></a>Próximas etapas

* [Obter gratuitamente uma chave de assinatura dos Serviços de Fala](get-started.md)
* [Obter o SDK de Fala](speech-sdk.md)
* [Saiba mais sobre comandos personalizados (Pré-visualização)](custom-commands.md)
* [Saiba mais sobre o Direct Line Speech](direct-line-speech.md)
